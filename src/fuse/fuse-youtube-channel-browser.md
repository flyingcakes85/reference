# Using FUSE to write a YouTube channel browser

The main idea here is to return data from the YouTube API instead of our SQLite database. Once you decide upon the way this filesystem should function, you can proceed to implementing the relevant functions.

The complete code is on GitHub: [flyingcakes85/fuse-yt](https://github.com/flyingcakes85/fuse-yt/).

### How would this filesystem work?

For our very simple demonstration, lets assume the follewing

- at the root, there shall be multiple directories
- each of these directories will have names corresponding to the id of the channel they should be connected to
- each directory should list videos from the corresponding channel

As a rough guideline, we can chart the following process

- when user opens a directory, we can extract the channel id from the directory path
- this channel id is then used to fetch videos via the YouTube API
- `readdir()` returns these videos

### Enumerating channels

This is the simplest part. All you've to do is to yield a list of strings which match channel names.

```py
def readdir(self, path: str, _offset: int):
    contents = [".", ".."]
    if path == "/":
        contents.extend(self._channel_list())

    for r in contents:
        yield fuse.Direntry(r)
```

### Listing video files in channel folders

We extend our earlier `readdir()` function to return "video files" if the path isn't at root. We append the names of videos to `content` array, which is finally returned by the function.

```py
def readdir(self, path: str, _offset: int):
    # --- snip ---
    else:
        channel_name = path.split("/")[1]
        videos = self._get_videos(channel_name)
        for v in videos:
            contents.append(
                v["snippet"]["resourceId"]["videoId"]
                + "_"
                + v["snippet"]["title"].replace("/", " ")
                + ".desktop"
            )
    # --- snip ---
```

### But do we download the videos?

Not really! It will be a huge wastage of bandwidth to download each video when opening the directory. Moreover, this will make filesystem operations very slow.

Videos can be played via `mpv` by using `yt-dlp` as a provider

```sh
mpv https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

This can be shortened to

```sh
mpv ytdl://dQw4w9WgXcQ
```

So, we could return "files", which are just shell scripts with the following content:

```sh
#!/usr/bin/env bash
mpv ytdl://$VIDEO_ID
```

Sufficient?

![](/images/py-yt-shell-files.png)

Well, nope! While executing these scripts may play the corresponding video, but they're NOT resembling video files AT ALL!

So how do you get icons to a file?

### Presenting video files to user

You create what's called a [desktop entry](https://specifications.freedesktop.org/desktop-entry-spec/latest/). These a way to create shortcuts to commands and these files can specify their own icons too.

Here's what a simple desktop entry for the above shell script should look like:

```
[Desktop Entry]

Type=Application

Name=Rick Astley - Never Gonna Give You Up (Official Music Video)
Exec=mpv --ytdl-raw-options=paths=/tmp ytdl://dQw4w9WgXcQ
Icon=/tmp/dQw4w9WgXcQ.jpg

Comment=

Categories=Video;
Keywords=youtube;

NoDisplay=false
```

Now it looks much better

![](/images/py-yt-rick.png)

Finally, we use this as file contents for each video, replacing the video id and title everytime.

Out `read()` function now extracts video name from path

```py
def read(self, path: str, _size: int, _offset: int) -> bytes:
    try:
        video_name = path.split("/")[2]
        video_id = video_name[:11]
        file_contents = f"""[Desktop Entry]

Type=Application

Name={video_name[12:-8]}
Exec=mpv --ytdl-raw-options=paths=/tmp ytdl://{video_id}
Icon={self.CACHE_FOLDER}/{video_id}.jpg

Comment=

Categories=Video;
Keywords=youtube;

RunInTerminal=true
NoDisplay=false
"""
        return bytes(file_contents, "utf-8")
    except ValueError:
        return -errno.ENOENT
```

### Adding new "channels" (i.e. directories)

By now, we have everything implemented that will fetch videos given the channel id. So, logically speaking, in order to add a new channel, all we need to do is to add that channel id to our array `CHANNEL_LIST`.

We will need to implement two functions: `mkdir` and `rename`. While you can create a folder of your preferred name via `mkdir` command at the shell, many file explorers create folder with a default name and then rename it to your desired one. Thus, keeping in mind our use case (i.e. usability from file explorers), its important to implement both `mkdir` and `rename`.

```py
def mkdir(self, path: str, mode: str):
    parent_dir, new_channel = os.path.split(path)

    # sanity checks
    if parent_dir != "/":
        return -errno.ENOENT

    if new_channel in self._channel_list():
        return errno.EEXIST

    # append to channel list
    self.CHANNEL_LIST.append(new_channel)

def rename(self, pathfrom: str, pathto: str):
    parent_dir, old_name = os.path.split(pathfrom)

    # sanity checks
    if parent_dir != "/":
        return -errno.ENOENT

    parent_dir, new_name = os.path.split(pathto)

    if parent_dir != "/":
        return -errno.ENOENT

    # rename
    for i in range(len(self.CHANNEL_LIST)):
        if self.CHANNEL_LIST[i] == old_name:
            self.CHANNEL_LIST[i] = new_name
            break
        i = i + 1
```

![](/images/channel_list_fuse.png)

![](/images/video-list-fuse.png)
