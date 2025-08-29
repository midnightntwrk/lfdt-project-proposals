---
layout: default
title: Project To Be Named (TBN)
parent: LFDT Labs
grand_parent: Active Labs
---
# Project TBN

# Short Description
TBN is an open-source initiative developing a post-quantum zero-knowledge proving system that balances performance, security, and practical deployment constraints. The project focuses on creating a transparent, lattice-based architecture that provides realistic performance improvements while maintaining post-quantum security guarantees.

# Scope of Lab
TBN aims to advance the state of zero-knowledge proof technology by developing a system that addresses the fundamental trade-offs between prover time, proof size, and verifier cost. The lab's scope encompasses:

**Core Research and Development:**
- Development of a lattice-based folding protocol (NEO, LatticeFold+ or similar) for post-quantum security
- Implementation of a lookup-centric arithmetization system for computational efficiency
- Creation of a unified compilation target supporting R1CS, PLONKish, and AIR circuits
- Research into practical aggregation techniques for multiple proofs

**Performance Optimization:**
- Hardware-software co-design for CPU and GPU acceleration
- Small-field arithmetic optimization using the 64-bit Goldilocks field
- GPU-friendly Residue Number System (RNS) modular arithmetic 
- Memory-efficient proving strategies for large-scale computations
- Incrementally Verifiable Computation (IVC) support for long-running processes

**Developer Experience:**
- Rust-native SDK with comprehensive tooling
- Migration paths from existing proof systems (Halo 2, PLONK, STARKs)
- Transpiler tools for converting Plonkish circuits to CCS
- Debugging and development tools for circuit construction
- Documentation and learning resources for the ZK community

**Practical Deployment:**
- Multi-platform verification support (EVM, Move, WASM, Plutus, Compact (AKA Project Mitra))
- On-chain integration strategies with realistic gas cost targets
- Proof aggregation for blockchain scalability applications
- Data availability proof generation for rollup systems

**Security and Standards:**
- Post-quantum cryptographic parameter selection and validation
- Formal security analysis of the NEO and other related protocols
- Community-driven security audits and peer review
- Compliance with emerging post-quantum standards

The lab operates within LF Decentralized Trust's mission by advancing open-source cryptographic infrastructure that enables privacy, scalability, and trust in decentralized systems. TBN focuses on practical, deployable solutions that can be adopted by the broader blockchain and Web3 ecosystem.

# Initial Committers
- https://github.com/bobblessinghartley (Shielded Techonlogies)
- https://github.com/SebastienGllmt (Midnight Foundation)
- https://github.com/iquerejeta (Shielded Technologies)
- IOG (Aggelos?)
- Google 
- https://github.com/djetchev (Dimitar Jetchev - CyAIber Sarl and Microsoft Azure)
- https://github.com/dabo (Stanford University - Prof. Dan Boneh)
- https://github.com/nicarq (Shinkai)
- ...

# Sponsor
- https://github.com/bobblessinghartley  - Role: LFDT Board Member and TSC member for Mitra 

# Pre-existing repository
Not applicable

# Technical Approach and Realistic Goals

## Core Architecture
TBN will employ a hybrid approach that combines the best aspects of existing proof systems while acknowledging practical constraints:

**LatticeFold, Neo, or Similar Protocol (Lattice-Based Folding):**
- Employs the lattice-based Ajtai hash for efficient folding
- Based on the Module-SIS assumption for post-quantum security
- Uses sum-check folding for efficient proof aggregation
- Implements pay-per-bit commitments for granular efficiency
- Supports incremental verification for long computations

**Lookup-Centric Arithmetization:**
- Arithmetization should take advantage of repeated structure in the target computation, beyond what is supported by the CCS format (for example, repeated structure inside the SHA256 circuit)
- Unifies diverse computational models into a single lookup-based format
- Enables efficient compilation from R1CS, PLONKish, and AIR
- Optimizes for GPU acceleration and parallel processing
- Reduces circuit complexity for common operations

**Performance Targets (Realistic):**
- **Comment: if the goal is a folding-based architecture, then perhaps all performance targets should be stated in terms of a single folding step.**
- Proof generation: 2-5 seconds for 2^14 gates on consumer hardware 
- Proof size: 10-50 KB depending on security level and complexity
- Verification cost: <50,000 gas on Ethereum, scalable for other platforms
- Aggregation: Support for 100-1000 proofs with logarithmic growth

## Security Considerations
TBN acknowledges the fundamental trade-offs in zero-knowledge proof systems:

**Post-Quantum Security:**
- Lattice-based cryptography with configurable security levels (L1, L3, L5)
- Conservative parameter selection based on NIST standards
- Regular security audits and community review processes

**Practical Constraints:**
- Accepts larger proof sizes for enhanced security
- Balances performance with cryptographic soundness
- Provides migration paths from existing systems

## Development Timeline
The lab intends, as an early work item, to define a realistic, phased development approach:

**Phase 1 (n months):**
- Core cryptographic research and protocol specification
- Basic Rust SDK framework and development tools
- Community engagement and initial documentation

**Phase 2 (n months):**
- Reference implementation of the LatticeFold/NEO protocol
- Circuit compilation adapters for major arithmetizations
- Performance benchmarking and optimization

**Phase 3 (n months):**
- Production-ready SDK with comprehensive tooling
- Multi-platform verification support
- Security audits and formal verification

**Phase 4 (n months):**
- Advanced features (proof aggregation, IVC)
- Hardware acceleration and optimization
- Production deployment and ecosystem integration

## Community and Governance
TBN operates as an open, community-driven project following the these key principals:

**Open Development:**
- All code and specifications are open source
- Regular community calls and technical discussions
- Transparent decision-making processes
- Active engagement with the broader ZK community

**Quality Assurance:**
- Comprehensive testing and benchmarking
- Security-focused development practices
- Regular code reviews and audits
- Community-driven validation and feedback

**Ecosystem Integration:**
- Collaboration with existing LFDT projects
- Integration with major blockchain platforms
- Support for developer tools and frameworks
- Educational resources and training materials

This approach ensures that TBN delivers practical value to the decentralized trust ecosystem while maintaining realistic expectations about performance, security, and deployment complexity.

**Future Growth**
As the project matures and its community grows, we will follow the guidelines to transition from a Labs project to a full, incubating LFDT project, given the scope of the labs, potentially multiple, related projects. Our goal is to build a foundation that can scale and become a long-term, self-sustaining part of the LFDT ecosystem.
