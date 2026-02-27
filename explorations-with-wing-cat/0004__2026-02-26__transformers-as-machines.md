date: 2026-02-26
development: https://chatgpt.com/c/69a10bdb-f72c-832b-9dd0-0591cce2f112

# **Transformers as Machines**

## *The Optimization Space of Large Language Models*

*(Explorations with Wing-Cat)*

---

## **Abstract**

Large language models are commonly described as software systems running on powerful computers. This description is rapidly becoming obsolete. Modern transformers possess a degree of structural regularity, determinism, and computational predictability that allows them to migrate from flexible software workloads toward specialized physical machines. This essay explores the emerging optimization space of transformer inference: the historical progression from GPU execution to transformer-specific ASICs and model-compiled silicon; the central role of memory bandwidth and KV cache dynamics; the architectural suitability of transformers for deep pipelining; and the growing view of inference as signal propagation through a temporal medium rather than program execution.

Using contemporary DeepSeek-class models as a concrete reference point, we examine why runtime memory — not weights or arithmetic — dominates system design, and why future accelerators increasingly resemble memory fabrics surrounding deterministic transformation pipelines. The essay introduces the speculative lens of the *temporal waveguide*, framing transformer inference as a structured flow of tokens through accumulated contextual state.

Finally, we estimate the practical engineering promise of this transition: a likely 50×–100× reduction in effective token cost and order-of-magnitude increases in token throughput. Such improvements would transform language models from expensive query systems into continuously operating cognitive infrastructure, enabling persistent multi-agent reasoning systems and reshaping the architecture of computation itself.

---

## **1. The Inversion of Computing**

For most of computing history, performance improvements meant faster arithmetic. CPUs optimized instruction execution; GPUs optimized parallel computation. Programs were limited primarily by how quickly numbers could be manipulated.

Transformers invert this relationship.

In modern language model inference, arithmetic is cheap. What dominates cost, latency, and energy consumption is the movement of data — specifically, the repeated movement of contextual state through memory over time. A transformer spends far more effort remembering than calculating.

This inversion changes everything. Optimization no longer targets computation alone; it targets the physical flow of information.

The central question becomes:

> What happens when a computational workload becomes predictable enough to be embodied directly in hardware?

Transformers appear to be the first widely deployed software systems whose structure answers this question affirmatively.

---

## **2. Four Phases of Transformer Hardware Evolution**

### Phase I — General Compute (GPU Era)

The first generation of large language models ran on GPUs designed for graphics and scientific computing. GPUs offered massive parallel arithmetic capability but remained fundamentally general-purpose machines.

Transformers were treated as workloads rather than architectural assumptions. Execution involved kernel launches, scheduling overhead, and continual movement of weights through memory systems not designed specifically for language inference.

Flexibility dominated efficiency.

---

### Phase II — AI Accelerators

The second phase introduced specialized tensor hardware:

* Google TPUs
* Cerebras wafer-scale processors
* dataflow architectures such as SambaNova

These systems acknowledged that neural networks justified dedicated silicon. Matrix multiplication became a first-class hardware primitive. However, they still targeted broad classes of neural networks rather than transformers specifically.

The hardware adapted to machine learning, but not yet to language models as a distinct computational species.

---

### Phase III — Transformer-Specific ASICs (The Present Era)

We now occupy a transitional period in which the transformer itself is assumed as the canonical workload.

Companies illustrate different facets of this shift:

* **Groq** builds deterministic execution pipelines that remove scheduling overhead entirely, treating inference as a continuous dataflow process.
* **Etched** designs silicon capable of running transformers and little else, abandoning general neural network flexibility.
* Emerging inference accelerators optimize token streaming rather than batch computation.

Here, execution graphs are static, predictable, and pipelineable. The model is no longer merely executed — it is *flowed through*.

---

### Phase IV — Model-Compiled Hardware (Emerging)

The most radical direction is represented by approaches such as **Taalas**, where trained model weights are compiled directly into silicon.

Weights cease to be data loaded from memory and instead become physical structure. Inference becomes signal propagation through circuitry shaped by learning.

Hardware begins to crystallize intelligence itself.

---

## **3. The Real Bottleneck: Memory**

The dominant constraint in transformer inference is neither compute nor parameter count but memory bandwidth.

Energy costs scale approximately as:

* arithmetic operation: low
* data movement across chip: higher
* data movement to external memory: orders of magnitude higher

Transformers repeatedly access accumulated context. Each generated token requires consulting the history of previous tokens stored in memory. Thus inference resembles a streaming memory workload with incidental computation attached.

This reality explains the rise of High Bandwidth Memory (HBM), silicon interposers, and tightly coupled memory fabrics. The modern AI accelerator is less a processor than a memory system with embedded transformation logic.

---

## **4. DeepSeek as a Concrete Reference**

Consider a contemporary high-quality model such as DeepSeek-class systems.

A defining feature of transformer inference is the **Key-Value (KV) cache**, which stores attention state for every prior token so the model need not recompute history.

For models of this scale:

* roughly ~1 MiB of KV state per token (FP16 baseline)
* ~160k context length ⇒ approximately **162 GiB** of runtime memory for a single sequence

Notably, this memory requirement persists even if weights are baked into silicon. The KV cache represents living conversational memory rather than static knowledge.

Quantization and architectural innovations such as Multi-Head Latent Attention reduce this burden, but the fundamental truth remains:

> Inference memory grows with time, not model size.

---

## **5. Transformers as Pipelines**

Transformers are unusually compatible with pipelined execution because they are:

* deterministic
* structurally repetitive
* free of branching control flow
* composed of identical layer transformations

A token moves through layers like an object on an assembly line:

```
Embedding → Layer₁ → Layer₂ → … → Layerₙ → Output
```

Multiple tokens may occupy different stages simultaneously. After pipeline fill, hardware can emit tokens continuously.

Three nested pipelines emerge:

1. **Layer pipelines** — each layer as a stage
2. **Tensor pipelines** — staged operations within layers
3. **Token pipelines** — multiple tokens in flight

Inference begins to resemble digital signal processing rather than conventional computation.

---

## **6. Processing Near Memory**

Because data movement dominates cost, modern architectures increasingly move computation toward memory:

* logic layers within HBM stacks
* processing-in-memory concepts
* localized attention evaluation

Instead of memory serving passive storage, it becomes active computational substrate. The distinction between storage and processing weakens.

This direction mirrors biological computation, where memory and processing are inseparable.

---

## **7. The Temporal Waveguide (Speculative Lens)**

A useful speculative lens is to view transformer inference as a **temporal waveguide**.

In this view:

* tokens behave like signals
* layers act as transformation media
* the KV cache forms a persistent field encoding past interactions
* generation is a wave propagating through accumulated context

Each new token interacts with the entire prior field, reshaping it and continuing the propagation.

Inference is not merely calculation; it is structured evolution through time.

This perspective helps explain why memory locality, pipeline continuity, and deterministic flow matter more than raw compute power.

---

## **8. The Optimization Frontier — What Engineering Still Promises**

Large gains remain achievable without new algorithms:

* hardened execution pipelines
* transformer-specific silicon
* compiled weights
* KV compression and quantization
* near-memory compute
* static scheduling

These improvements remove inefficiencies rather than invent new intelligence.

### **What’s the Promise**

A conservative engineering projection suggests:

| Metric                 | Expected Improvement       |
| ---------------------- | -------------------------- |
| Token cost             | **50×–100× cheaper**       |
| Token speed            | **10×–30× faster**         |
| Energy per token       | ~100× lower                |
| Long-context usability | qualitative transformation |

Tokens begin to resemble CPU cycles rather than scarce resources.

For users building multi-agent systems, this implies a profound shift:

* agents no longer minimize thinking
* persistent reasoning becomes economical
* continuous cognitive processes replace discrete queries

Language models transition from expensive services into ongoing computational substrates.

---

## **9. Architectural Consequences**

As optimization progresses, machines may evolve toward:

* personal inference appliances
* memory-centric computers
* model-specific silicon devices

This mirrors earlier transitions:

* graphics pipelines replacing software rendering
* video codecs becoming fixed hardware
* network routing implemented as ASICs

Software stabilizes into structure once workloads become predictable.

Transformers appear to be reaching that stability.

---

## **10. Closing Reflection — When Software Becomes Structure**

The trajectory outlined here suggests a deeper transformation.

Traditional computing executes instructions stored in memory.

Transformer hardware increasingly embodies learned structure directly in matter. Computation becomes traversal rather than execution.

We may be witnessing a transition in which intelligence is no longer primarily software running on machines, but physical arrangements optimized for the flow of symbols through time.

When tokens become cheap enough, reasoning itself becomes continuous infrastructure — always running, always evolving, always present.

And at that point, the distinction between computation and cognition begins to blur.

---
