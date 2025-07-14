---
layout: default
title: The Compact Programming Language
parent: Hyperledger Improvement Proposals
---

# HIP Identifier
<mark>_**HIP identifier** a short description plus a serial number with a
version.
</mark>

# Sponsor(s)
- Kevin Millikin (Shielded) [<kevin.millikin@shielded.io>](mailto:kevin.millikin@shielded.io)
- Bob Blessing-Hartley (Shielded) [<bob.blessing-hartley@shielded.io>](mailto:bob.blessing-hartley@shielded.io)
- More?

# Abstract
**Compact** is a restricted programming language for privacy-preserving smart contracts in a decentralized blockchain.
It was designed for the Midnight Network but it is intended to be suitable for many different privacy-preserving blockchains.

# Context
The Compact programming language provides a concrete implementation of the [Kachina programming model](https://iohk.io/en/research/library/papers/kachina-foundations-of-private-smart-contracts/).
Compact is used to implement privacy-preserving smart contracts with these characteristics:

- contracts are compiled to off-chain code that implements the full contract with access to private data;
- contracts are compiled to on-chain code that implements (only) the contract's public state updates without access to private data;
- a zero-knowledge (ZK) proof of the off chain execution is constructed (off chain); and
- the on-chain code is executed on chain if the blockchain can verify the ZK proof.

The current implementation of Compact specifically targets the Midnight Network.
The characteristics listed above are concretely realized in the following ways:

- Compact contracts are compiled to off-chain JavaScript code;
- on-chain code is implemented by the Midnight Network's [Impact Virtual Machine](https://docs.midnight.network/develop/how-midnight-works/impact);
- an implementation of the BLS12-381 proving system is used for ZK proofs; and
- the Midnight Network's blockchain is used to verify ZK proofs and execute the public part of the contract on chain.

However, this is merely the specific implementation of Compact in the Midnight Network.
The language itself is designed to be generically used in any privacy-preserving smart contract system that is sufficiently similar,
and all of these implementation characteristics can be changed.

# Dependent Projects
Compact depends on an underlying blockchain system, but it does not depend on any specific blockchain.

# Motivation
Compact is designed to be a concrete realization of the abstract Kachina programming model.
Three important factors influenced Compact's design: it should be safe, familiar, and restricted.

## Safety
The execution of smart contracts can have real-world financial consequences.
The addition of data privacy can also raise issues of legal requirements of data protection and data disclosure.
While Compact itself cannot solve these issues, it is intended to make it easy for contract authors to address them.
It is therefore important that it is easy to develop correct smart contracts,
and possible to understand their behavior by reading their source code.

Compact is statically typed to help enforce correctness.
There is a very clear distinction between private data (called "witnesses" in Compact) and public data (called the "ledger" in Compact).
The language implementation tracks private data and requires it to be explicitly disclosed if it can be publicly observed.
Failure to explicitly disclose such such private data is a compilation error, preventing the contract from ever being executed.

## Familiarity
Compact is designed to be familiar to most developers.
Its syntax is based on TypeScript, which in turn is based on JavaScript, Java, and ultimately C and C++.
The goal is that the language should be unsurprising for developers who know any of these languages.
When features of Compact are similar or analogous to features of TypeScript we have used the same syntax.
When features are *different*, we have intentionally used different syntax.

## Restriction
The language is not a general purpose language, it is intentionally restricted.
In the context of a privacy-preserving blockchain, these restrictions come from two different sources.
First, there is an underlying zero-knowledge proving system, which will have some restrictions on the data types and computations it can represent.
Second, there is an intended blockchain which will have restrictions on the work that can be done as part of a transaction depending on characteristics such as block size, consensus model, etc.
Blockchain restrictions can also be required to accurately charge fees for transacdtions and to prevent denial of service attacks on the blockchain.

The current implementation of Compact has the specific restrictions of the Midnight Network blockchain and its underlying BLS proving system.
However, Compact itself is intended to be able to use "pragmas" in a contract's implementation to choose different restrictions.

# Status
The status of Compact is [incubation](https://toc.hyperledger.org/governing-documents/project-lifecycle.html#incubation)

# Solution
Compact is a language that is designed to support a range of privacy-preserving smart contract systems.
It has both an informal language reference (link) and an in-progress formal specification in Agda.
There is an implementation in the form of the Compact compiler, and a suite of developer tools forming a Compact software development kit (SDK).

## The Language Reference and Formal Specification
Compact is intended to have a complete, unambiguous, informal description in the Compact Language Reference.
This is paired with a formal specification in Agda.
Together, these two artifacts support a deep understanding of the language on the part of programmers and contract readers.
They additionally support development of tooling for the langauge and even separate implementations of the language itself.

## Witnesses for Private Data
Compact contracts have access to private data by calling so-called "witnesses".
Witnesses are declared in Compact and they are given Compact type signatures.
Compact witness type signatures are a kind of foreign function interface (FFI).
Witness implementations are intended to be provided in some general purpose programming language, which can be called from Compact programs.

An off-chain implementation of a Compact contract consists of the contract's implementation itself, along with some implementation of the witnesses that it uses.
The mapping from witnesses' Compact type signatures to foreign language type signatures will be predictable and well specified for each supported foreign language.

In the specific instantiation of Compact for the Midnight Network, witnesses have TypeScript foreign type signatures and they can be implemented in either TypeScript or JavaScript.
However, the intention is that other foreign langauges can be equally supported.

## Ledger ADTs
The public state of a Compact contract is its so-called "ledger".
Compact supports a number of builtin abstract data types (ADTs) for the ledger.
Examples include cells containing a Compact value, integer counters, sets of Compact values, maps whose keys and values are Compact values, etc.

The specific instantiation of Compact for the Midnight Network has specific ledger ADTs.
However, the design is general enough to allow other ADTs to be provided.

## The Compact Standard Library
Compact has a standard library which provides utilities that are generally useful in smart contracts.
Notable among these are cryptographic primitives such as hashing and elliptic curve arithmetic.
The ledger ADTs are also implemented by the standard library.

## The Compiler Implementation
The Compact project provides an implementation of the Compact programming language.
This implementation is a compiler implemented in the Scheme programming language.
Scheme is used, primarily, in order to use the [Nanopass compiler framework](https://nanopass.org/documentation.html) ([GitHub](https://github.com/nanopass)).
Nanopass is a framework intended to easily and correctly develop commercial-quality compilers.

The current implementation of the compiler supports the Midnight Network as the underlying blockchain.
Midnight uses a BLS-based proof system.
Together, we will refer to the underlying blockchain and proof system as the "backend".

The architecture of the compiler is intended to support a different backends.
The compiler has a sequence of compiler passes that are generic and do not depend on the backend.
Then, there are a relatively small number of backend-specific passes that would need to be implemented differently for a different underlying system.

### Contract and witness implementations
Specifically, in the Midnight network, there are backend passes to generate JavaScript code (with JavaScript wrappers for witnesses) implementing the contract.
This code uses a Midnight-specific JavaScript "Compact runtime" package.
The compiler generates Midnight-specific wrappers for the TypeScript or JavaScript witness implementations.

The compiler passes used to generate JavaScript, the specific implementation of a Compact runtime used, and the witness wrapper implementations could all be replaced with different backend-specific equivalents.

### ZK proofs
The Midnight network uses a BLS proving system.
The Compact compiler has backend passes to generate a specific intermediate representation for this proving system.
This representation is called ZKIR (the **Z**ero **K**nowledge **I**ntermediate **R**epresentation).

The compiler passes used to generate proofs, and the representation of proofs themselves could both be replaced with different backend-specific equivalents.
The Midnight Network-specific ZKIR representation has JSON and binary representations, but the proof representation could really be anything.
For example, it would be equally possible to generate Rust code implementing the proof using some proving library.

### Ledger ADTs and public state updates
Ledger ADTs have both an off-chain and an on-chain representation, and their operations have both off-chain and on-chain implementations.
In the Midnight Network we have chosen to make both off-chain and on-chain parts the same.
The Midnight-specific Compact runtime actually uses the same representation as the Midnight Network for ledger ADTs,
and it actually uses the Impact VM to implement ledger operations.

However, it is not necessary that Compact uses the same implementation off and on chain.
The Compact compiler itself simply needs to have a backend-specific implementation of off-chain code to use for the ledger,
and a backend-specifi implementation of the on-chain code to be deployed in a contract or else submitted in a transaction depending on how the backend blockchain works.

<mark>_**Effort and resources** committed (coders and any other resources
that are needed) and timeline._
</mark>

# How To
<mark>_**How to**: How to host and test the project. How to deploy and use.
How does one know that it works._
</mark>

# References
<mark>_**References**. See [citation guide](http://www.chicagomanualofstyle.org/tools_citationguide.html)._
</mark>

# Closure
### **Project Closure: Success Criteria for Compact**

The Compact project will be considered successfully completed when the following measurable criteria have been met. These criteria are organized by the major deliverables and goals of the project.

#### **I. Language Specification & Documentation**

The goal is to have a clear, unambiguous, and complete definition of the language that can be used by developers, auditors, and implementers.

* **Language Reference Completeness:** The informal Compact Language Reference document fully describes 100% of the language features implemented in the compiler and used in the Standard Library. There are no compiler features that lack corresponding documentation.  
* **Formal Specification Coverage:** The formal specification in Agda covers 100% of the language's static semantics (i.e., the complete type system). A formal proof exists demonstrating that the type system is sound.  
* **Specification Consistency:** An external review by at least two developers (not on the core Compact team) confirms they can build a functionally equivalent "hello world" style contract using *only* the Language Reference, with no ambiguities found.  
* **Standard Library Documentation:** Every function and module in the Compact Standard Library is documented with its type signature, purpose, parameters, and a usage example.

#### **II. Compiler & Software Development Kit (SDK)**

The goal is to provide a robust, correct, and usable compiler and set of tools for developers.

* **Compiler Correctness & Test Coverage:** The compiler's test suite achieves a minimum of 90% code coverage. This suite must include tests for all language features, error conditions, and optimizations.  
* **Reference Contracts:** At least 5 non-trivial reference smart contracts, covering key use cases (e.g., a private token, a sealed-bid auction, a multi-sig wallet), successfully compile, generate valid ZK proofs, and execute correctly on the target backend (Midnight Network).  
* **Safety Feature Validation:** The compiler correctly fails to compile 100% of test cases that attempt to implicitly leak private data to the public ledger, demonstrating the effectiveness of the mandatory disclosure safety feature.  
* **Developer SDK Usability:** A developer unfamiliar with the project can successfully set up the development environment, and build, test, and deploy one of the reference contracts within a 4-hour time-box, using only the provided SDK documentation.  
* **Performance Baseline:** The compiler meets defined performance benchmarks for a standard reference contract:  
  * **Compilation Time:** Compiles in under a specified time (e.g., 10 seconds) on standard developer hardware.  
  * **Proof Generation Time:** Generates a ZK proof in under a specified time (e.g., 30 seconds).  
  * **Proof Size:** Generates a ZK proof with a size below a specified threshold (e.g., 20 KB) to ensure on-chain feasibility.

#### **III. Initial Backend Implementation (Midnight Network)**

The goal is to prove the language is not just theoretical but has a concrete, working implementation on its primary target platform.

* **Full Feature Integration:** All specified Ledger ADTs and Standard Library cryptographic primitives are fully functional on the Midnight Network backend, with their on-chain and off-chain components interacting correctly.  
* **Witness FFI:** The Foreign Function Interface (FFI) for witnesses is fully implemented for TypeScript/JavaScript. It is possible to write, compile, and execute a contract that correctly calls a witness implemented in TypeScript and uses its private data.  
* **End-to-End Deployment:** A full end-to-end deployment and execution of a reference contract on a live Midnight testnet is successful. This includes submitting the transaction with its ZK proof, having the proof verified by the network, and seeing the public state updated correctly by the Impact VM.

#### **IV. Portability & Design Validation**

The goal is to validate the architectural claim that Compact is not intrinsically tied to a single backend.

* **Portability Proof-of-Concept (PoC):** A proof-of-concept is completed where the compiler is retargeted to a *different* backend. This could be a different ZK proving system (e.g., Groth16) or a mock blockchain environment.  
* **Architectural Validation:** The effort to build the PoC backend is documented. Success is defined as requiring modification to fewer than 25% of the compiler's total passes, confirming the effectiveness of the Nanopass architecture for creating a backend-agnostic core.
