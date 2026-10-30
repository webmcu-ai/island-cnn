# island-cnn
Webpage and possibly on-device cnn with island isolations for understandable ML


Live webpage at https://webmcu-ai.github.io/island-cnn/index.html




# Using Vision CNN SD Trainer to Understand and Improve a CNN

Treat the webpage as a CNN research and learning laboratory, not just a tool for training a model.

## 1. Examine Activations

After training a model:

- Open the Activations section.
- Use several validation images, not just one.
- Examine the activation maps for each Conv1 and Conv2 filter.
- Look for filters that appear to detect similar patterns.
- Look for filters that respond to different regions or features of an image.

Questions to investigate:
- Are some filters learning nearly identical features?
- Are some filters rarely activating?
- Do different filters respond to different visual patterns?
- Can we identify simple features such as edges, corners, textures, or shapes?

Remember: an activation map shows where a filter responds. It does not automatically tell us what the filter has learned.

## 2. Use Trace & Explain

This is one of the most valuable tools for understanding the internal workings of the CNN.

- Select a validation image.
- Run Trace & Explain.
- Follow the image through each stage:
  1. Input image
  2. Conv1
  3. Pooling
  4. Conv2
  5. Flatten
  6. Classifier

Pay particular attention to the Conv2 features and their contributions to the final class scores.

Investigate:
- Which Conv2 features contribute most to the correct classification?
- Which features contribute to the strongest competing class?
- Are positive and negative contributions balanced?
- Can we identify which internal features actually influence the final decision?

Try several images from the same class and several images from different classes.

## 3. Perform Ablation Experiments

Ablation means temporarily disabling part of a trained network to see what happens.

Start by recording the original validation accuracy and loss.

Then experiment with disabling:

- Individual Conv1 channels or filters.
- Individual Conv2 filters.
- Individual islands.
- Individual bridges between islands.

Record how each experiment changes:
- Validation accuracy.
- Validation loss.
- The predicted class for individual images.
- The confidence or class-score differences.

Questions to investigate:
- Which filters are essential?
- Which filters appear redundant?
- Which islands contribute most to classification?
- Are some bridges unnecessary?
- Can we remove parts of the network without significantly reducing performance?

Important: A filter may appear unimportant when tested individually because another filter can compensate for its removal. Test combinations of filters when appropriate.

## 4. Explore the Model Graph

Use the Model Graph to understand the network as a connected system.

Investigate:
- The number of nodes and connections.
- How filters are connected within islands.
- How islands communicate through bridges.
- Which connections carry the strongest weights.
- How activation information flows through the network.

Compare:
- A: Fully connected architecture.
- B: Isolated islands.
- C: Islands with fixed bridges.
- D: Islands with learned-gate bridges.
- E: Dense model pruned to provide a fair comparison.

Remember that a large weight does not necessarily mean a connection is important. Combine graph information with activation tracing and ablation results.

## 5. Compare Different Architectures

Train and compare Models A through E.

Use multiple random seeds where possible so that results are not based on a single lucky training run.

Experiment with different numbers of bridges (K).

Compare:
- Training accuracy.
- Validation accuracy.
- Held-out test accuracy.
- Number of active connections.
- MAC operations.
- Model size.
- Estimated memory requirements.
- Measured microcontroller inference time.

Model E is particularly important because it provides a comparison against ordinary network pruning.

Do not assume that islands are automatically better than a dense network. A dense network can represent the same connectivity as a sparse network by setting unused weights to zero. The interesting question is whether the different architectures affect learning, generalization, interpretability, or computational cost.

Model F explores spatial islands and is currently a browser-only research model, not an ESP32 firmware-exportable model.

## 6. Use the Coach and Tuning Tools

Use the Coach to investigate possible improvements to an already-trained model.

A possible workflow:

1. Train a baseline model.
2. Analyse its activations and graph.
3. Examine the Coach suggestions.
4. Select one suggested change.
5. Continue training or fine-tune.
6. Compare the result against the original control model.
7. Keep the change only if it produces a meaningful improvement.
8. Record the experiment in the journal.

Try reducing, freezing, or pruning selected channels, islands, or connections.

Make one or two changes at a time so that you can understand what caused the result.

## 7. Follow a Repeatable Research Workflow

Use this sequence for each experiment:

1. BASELINE: Train and save the original model.
2. UNDERSTAND: Examine activation maps.
3. INTERROGATE: Use Trace & Explain to follow individual images.
4. TEST: Perform ablation experiments.
5. VISUALIZE: Examine the network graph and connectivity.
6. REORGANIZE: Experiment with islands, bridges, and pruning.
7. OPTIMIZE: Use the Coach and tuning tools.
8. VERIFY: Compare validation results and finally evaluate on the held-out test set.

Save:
- Original model bundles.
- Interpretability JSON.
- Tuning session JSON.
- Architecture comparison results.
- Experiment notes and conclusions.

Use validation data while experimenting. Keep the held-out test set untouched until you have selected your final model. Ideally, evaluate it only once for each final candidate.

## 8. Investigate Real Efficiency

Distinguish three different types of efficiency:

**Computational efficiency**
- How many operations does the network actually perform?
- How many connections are active?
- Can unnecessary calculations be skipped?

**Memory efficiency**
- How much space is required for weights?
- How much RAM is needed during inference?
- Can intermediate feature maps be stored more efficiently?

**Architectural efficiency**
- Can the network achieve similar accuracy with fewer filters, islands, or connections?
- Can its structure be made easier for students and researchers to understand?

Important ESP32-S3 consideration:

Setting unused connections to zero does not automatically make the firmware faster. If the firmware still calculates every connection, the operations are still being performed.

To achieve real speed improvements, the firmware must skip disabled connections using an appropriate mask or skip-list implementation.

Always distinguish estimated MAC reductions from measured microcontroller inference time.

# Suggested New Feature for Version v010: Filter Discovery

Add a new section called **Filter Discovery** to help students and researchers understand what individual CNN filters have learned.

The goal is to connect the mathematical weights inside a CNN with the actual visual patterns in the images that activate those filters.

Potential features:

1. Display the learned Conv1 filters as visual 3×3 weight patterns, showing RGB channels separately.
2. Display Conv2 filter information and explain how Conv2 combines earlier feature channels.
3. Find and display the image patches that produce the strongest activation for each filter.
4. Show which filters activate most frequently across a collection of validation images.
5. Measure similarity between filters to identify potentially redundant filters.
6. Compare filter activation strength with classifier contribution.
7. Connect Filter Discovery results with the existing ablation tools.

The most important goal is to distinguish between:

- Filters that activate frequently.
- Filters that activate strongly.
- Filters that contribute significantly to the final classification.
- Filters that can be removed without hurting performance.

These are not necessarily the same thing. A filter might activate frequently but contribute very little to the final decision. Another filter might activate rarely but be essential for recognizing a particular class.

The objective is to help students understand not just what a CNN predicts, but how its internal features work together to produce that prediction.
