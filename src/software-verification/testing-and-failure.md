# Testing and failure

## Ensuring program correctness

Conventional method: **testing**

How to do testing?

- obtain test cases
    - inputs
    - outputs
- apply to implementation
- record outputs
- check if o/p match expected o/p

## Limitations with testing

- can't do exhaustive search
    - eg. for 4 input, each 16bit, number of testcases is \\(2^{4\times16}\\), i.e. \\(2^{64}\\)
    - real programs will have much more and broader inputs
- \\(\therefore\\) infeasible in practice
- testing shows **presence of bugs**, but not their *absence*

For I&C systems however, we are also interested in showing absence of bugs.

## Past examples of failure in I&C systems

- **Ariane-5**: failed launch
    - 64bit signed integer overflow
    - report said: *Software should be assumed to be faulty until current best practice methods can prove it to be correct.*
- **Therac-25**: 100 times radiation to patients


