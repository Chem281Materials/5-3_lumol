# Implementation of a New Pair Potential in Lumol


## Background

Traditional molecular dynamics simulations represent interactions between atoms using simple, efficiently computable expressions.
For example, numerous important pair potentials in molecular dynamics are specific forms of the Mie potential:

```math
V(r) = \left( \frac{n}{n-m} \right) \left(\frac{n}{m}\right)^{\frac{m}{n-m}} \epsilon \left[\left(\frac{\sigma}{r}\right)^n - \left(\frac{\sigma}{r}\right)^m \right]
```

Although simple, this potential captures some of the key characteristics of intermolecular interactions.
At large internuclear separations, the second (negative) term in the square brackets dominates, and the atoms are attracted.
At very small internuclear separations, the first (positive) term dominates, and the atoms are repelled.
By far, the most common variation of the Mie potential is the Lennard-Jones potential, for which $n=12$ and $m=6$.
Lumol has implementations of both the Lennard-Jones potential and the more general Mie potential.

## Task 1: Implement the 9-6 Potential

Another potential that has sometimes been used in the context of molecular dynamics is the 9-6 potential (that is, the Mie potential with $n=9$ and $m=6$).
Because Lumol supports the more general Mie potential, it is obviously already possible to run calculations with the 9-6 potential in Lumol; however, the generality of the Mie potential comes at a performance cost.
There are numerous ways in which you could optimize the calculation of the potential if you only needed to support the case of $n=9$ and $m=6$ (the separate implementation of the Lennard-Jones potential is similarly motivated by performance reasons).
You will create a new pair potential in Lumol for the 9-6 potential, ensuring that it is highly efficient.
Take advantage of any possible optimization opportunities.

**Hints:** It is cheaper to call `powi` (the function to raise by an integer power) than `powf` (the more general function to raise by a floating point value).
The Rust compiler should automatically optimize `powi` calls that raise by the power of `2` (for example, `f64::powi(x, 2)`) to simple multiplication (i.e., `x * x`).
Calls to `powi` with an exponent larger than 2 will be more expensive than a single multiply.
You may find it helpful to examine the `LennardJones` implementation for ideas.
You may also find it useful to know that Lumol has a [tutorial](https://lumol.org/lumol/latest/book/advanced/potential/index.html) for adding new potentials; however, note that the example they give is for a case in which Lumol is used as a separate library, whereas this problem requires you to implement your potential in the core code.
You may also find it useful to search for `Mie` or a similar existing potential for the purpose of identifying where you might need to make changes to the code.
For example:

```bash
grep -r "Mie" .
```

You can run a calculation in Lumol with:

```bash
cd lumol
cargo run <path to .toml file>
```

For example, you could do `cargo run /work/argon/argon.toml`.

## Task 2: Add Testing

Add unit tests for your new pair potential, following a similar approach to that already used in lumol for testing.
Modify the GitHub actions workflow so that it runs an example simulation using your 9-6 potential.

Note that lumol is divided into several sub-packages.
In order to run tests for the `lumol-core` sub-package, you can do:

```bash
cargo test -p lumol-core
```

Note that the above may produce errors that are native to Lumol and unrelated to this excercise.
To run only tests in `lumol-core/src/energy/functions.rs`, you can do:

```bash
cargo test -p lumol-core functions
```

Example input files (`argon.toml` and `argon.xyz`) that run using the Mie potential are provided in the `argon` directory.
You can run them with:

```bash
cd lumol
cargo run -- /work/argon/argon.toml
```

## Task 3: Explain the Code

In 2-3 paragraphs, explain the strategy Lumol has used for implementing potentials.
In particular, compare and contrast how Lumol and LAMMPS are structured.
