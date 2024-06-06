# cache.coherencyLAOCII
### MSI with caches and processors, MOESI state machines, and Directory protocols.

In a multiprocessor system with a shared memory/address-space, a need for coherence(i.e program order preserved, atomic access, serial writes)
between caches arises when one of its data is modified. There're diferent solutions that suit better each context.
1. Centrilized Shared-Memory Architectures:
    - bandwidth needs increase as cpus grows, implies non-scalable;
    - symmetrical multiprocessor(SMP);
    - snoopy(a.k.a bus watching) scheme.

- Implemented Protocols: 
  - MSI: Basic cache coherency protocol, cache states update accordingly to bus(broadcasting data and issuing new states) and cpu(writes/reads) requests.
  - MOESI: Adds more complexity to the possible assumed states; now the set of tag+address, which eventually translates into a virtual address, can be exclusive or owned by certain caches.   

  - Types of states:
    * M - store modifies cache(main memory is invalid), if re-writen a write-back happens.
    * O - a cache owns data, which can be shared as valid in other caches, the main memory isn't yet updated if there is read memory access is then aborted, if re-writen a write-back happens.
    * E - preserved in only one cache, exclusive to it, main memory is clean(data must have been writen-back, or there was a read miss in that cache level which the main memory had to supply, still clean). 
    * S - read modifies cache(address required are valid in one of the caches and then shared), memory access is aborted.
    * I - read miss if tag isn't present, write-invalidate if other caches overwrites it. 
2. Distributed:
    - physically distribuited;
    - tipically non-uniform accesses time, asymmetrical;
    - interconnection network.
  * Implemented Protocol: Directory scheme.

About Sync:
  - In a Thread-Level parallelism context sync is needed for criticaly shared memory addresses accesses.
  - All these protocols assume that there is syncronicy and atomicity between operations.
  - We can achive this by implementing a spin lock whithin the program code, if the ISA support such instructions(i.e. load-link and store-coditional).

Caches and processors were add to demonstrate MSI, you might want to add a different cache if using the MOESI state machine so then
the correct bus probing wire have a corresponding width to the new added states. How to connect/communicate caches and how to use the
implemented MOESI state machine can be figured out by looking at the inner working in the original msi_main.v and cache.v files.    

If more caches and cores are added you'll have to propagate the data writen on bus to all other caches and coordinate
the writes on bus since now you can't point the bus_reply of one cache(the new state for address) directly at the input of the other cache module
(you'll have many bus_reply's, one for each core), a bus arbitrer for a common data bus is necessary and eventually will became a bottle neck as the 
number caches increases(there you have it! the directory protocol!). You can substitute the MSI implementation with MOESI after some ajustments. 

### MSI fsm
![diagram1](https://github.com/MnoZombie956/cache.coherencyLAOCII/blob/main/imgs/fsm_msi.png?raw=false)
### MOESI fsm
![diagram2](https://github.com/MnoZombie956/cache.coherencyLAOCII/blob/main/imgs/fsm_moesi.png?raw=false)
### Directory fsm
![diagram3](https://github.com/MnoZombie956/cache.coherencyLAOCII/blob/main/imgs/fsm_dir.png?raw=false)

### Blocking vs. nonBlocking Matrix Multiplication

The blocking technique is welcome as it decreases L1 cache capacity failures and exploit other features which, in the long run, allow us to stop accumulating 
a huge amount of time spent only with the latency to access different levels of the hierarchy. No need to change hardware to perform a better matrix multiplications.
The submatrix B size(blocking factor) used in the multiplication must fit in your L1.
![graph1](https://github.com/MnoZombie956/cache.coherencyLAOCII/blob/main/imgs/aocIII_tp02.png?raw=false)

