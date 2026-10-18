## Perspective 1. Architectures and Training Techniques of Foundation Models: Understand Before Scaling Up, and Explore the Conditions for Applying Them

### Problem
- Papers report good results in the settings their authors chose, but real-world settings often differ from those settings because of data, scale, resources, and other factors.
- Every technique has premises and conditions it needs in order to work. So we must first check under what circumstances those premises hold and when they break, and in some cases verify the premises of the paper itself.
- However, resources are often too limited to simply start at foundation-model scale.

### Questions

**Stage 1: Understanding and Verification**
- When scaling down due to resource limits, how do we implement the technique at small scale while preserving its essence, and how do we verify that the implementation is valid?
- Can we design diagnostic experiments, ordered by cost, to confirm whether the paper's mechanism actually works?
- When our setting differs from the paper's, what baselines and evaluation metrics should we establish to judge that we have reproduced the paper's results?

**Stage 2: Exploring Conditions for Application**
- **When can this technique be transferred?** What signals tell us whether its premises hold, and, when they break, how and why it fails?
- Among alternatives serving the same purpose, under what conditions do we choose this technique, and under what conditions do we avoid it? Under the same conditions, what evidence justifies choosing it over the other alternatives?
- (Optional) When transferring results obtained at small scale to large scale, can we design experiments to estimate how the hyperparameters should change?

### Analysis Axes

| Analysis Axis | Representative Examples | Core Focus |
|---|---|---|
| Training techniques | Generative objectives, supervised objectives, policy optimization, distillation objectives | What signal each technique is designed to learn from and how, and whether it actually behaves that way |
| Training stages | Pretraining, SFT, RL, distillation | What each stage is responsible for, and what carries over or breaks down between stages |
| Architecture | Modules, blocks, the entire backbone | Dissect by unit, look inside, and intervene directly to reveal each unit's role and premises, then feed this into the design and improvement of the architecture |
| Training process and tuning | Optimizer, learning rate schedule, batch size | Analyze, using signals during training, how training behaves under different settings for a given run, and efficiently find the hyperparameters that determine the outcome |
| Scaling (optional) | Model, data, and compute scale | Based on results examined at small scale, find scale invariants, design hyperparameter scaling rules that preserve them, and form initial hypotheses through small-scale experiments |

---

## Perspective 2. Designing Practical Pipelines for Generative Models

> While Perspective 1 understands and verifies individual techniques at the paper level, Perspective 2 designs dataset construction and the entire training process as a pipeline tailored to real-world situations.

**1. Selection and Application Tailored to Practice**
- Problem: Real-world data and environments differ from those in papers or toy settings, so there is no guarantee that techniques understood at small scale will work in practice. Yet there are many candidates and limited resources, so we cannot run all of them to completion.
- Question: How do we establish validation criteria suited to the given task and data, quickly filter out techniques that don't fit, and adapt the remaining ones for application?

**2. Automated Pipelines**
- Problem: Dataset construction and training span multiple stages, so running them by hand each time makes it hard to repeat the same process. Also, whenever the pipeline needs to be modified or repurposed, we end up rebuilding it from scratch.
- Question: How do we structure the pipeline so that everything from dataset construction to training is automated, while still allowing only the necessary parts to be swapped out and reused when modifying or repurposing it?

**3. Design for Debugging**
- Problem: When results differ from expectations, it is hard to pinpoint which stage or component is the cause.
- Question: Into what units should we divide the pipeline so that components can be changed and compared one at a time?

---

## Perspective 3. Diagnosing and Controlling Generative Models Without Retraining

### Problem
- Large generative models are difficult to retrain. Yet generation defects can arise at inference time for many reasons, and we cannot retrain every time we want to change the output.
- Looking at the outputs alone does not tell us where a defect originates or where to intervene to produce the desired change.

### Questions
- When an output looks abnormal, what analysis methods should we use to examine the generation process leading to that output and the model's internals?
- Can we classify defects by type and diagnose the cause of each by alternating between internal observation and component-wise intervention?
- Among those, how do we distinguish defects that can be fixed at inference time from those that require retraining, and how do we fix them at inference time?
- Beyond fixing defects, how can we create desired changes through training-free editing or conditional control?

---

## Perspective 4. Inference Optimization of Generative Models

### Problem
- Generative models involve heavy iterative computation and large model sizes, so a single generation consumes significant time and resources.
- Each inference optimization technique targets a different bottleneck, and quality may be lost in proportion to the computation removed.
- Combining multiple techniques does not simply add up their effects. They may conflict with each other, or their quality losses may compound.

### Questions
- To find bottlenecks, what should we measure across the whole process or at the suspected stages?
- When applying a technique suited to the bottleneck, how much quality loss are we willing to accept in exchange for speed?
- When combining techniques causes conflicts or quality degradation, which technique or combination is responsible? Is the loss an inherent limit of the technique, or can it be recovered through tuning?

---

## Perspective 5. Serving and MLOps for Generative Models

> While Perspectives 1–4 deal with the model itself, Perspective 5 addresses deploying the model and operating it continuously.

**1. Serving Goals and Metrics**
- Problem: What matters differs across serving environments, and since generative models have no single correct answer, we must define for ourselves what a good result is.
- Question: In the target deployment environment, which of the many metrics should we aim for, and why that one?

**2. Serving Tailored to the Request Distribution**
- Problem: Even with the same model, bottlenecks vary with the request distribution. If the way requests are batched and dispatched doesn't fit, serving metrics will fall short of targets even when the model is fast.
- Question: How do we find the bottlenecks that hold back serving metrics under the real request distribution, and how do we design the serving architecture to meet the targets?

**3. Drift and Retraining**
- Problem: Input distributions and usage patterns change over time. As a result, a model that fit well at deployment gradually drifts away from actual requests.
- Question: How do we detect drift, and how do we decide when, and with what data, to retrain and redeploy?

**4. Deploying and Recovering from Model Changes**
- Problem: Every change to a model, adapter, or configuration risks degrading existing quality. If we cannot track which version is deployed where, it becomes hard to find the cause or roll back when problems occur.
- Question: How do we record and validate model versions and deploy them progressively, and when problems arise, how do we detect them, roll back, and prevent recurrence?
