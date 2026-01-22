# Introduction to Interpretable Machine Learning

This module deals with transparency as an important responsible AI consideration. Following the framework discussed in Chapter {doc}`0-2-framework`, transparency (or lack thereof) can also manifest in Problem, Data, Model, and Decision components. Specifically,

- **Problem** transparency:
- **Data** transparency: data provenance, data fact-sheet
- **Model** transparency: this primarily deals with interpretable machine learning.
- **Decision** transparency: interaction transparency (e.g., watermarking)

From a technical point of view, the following two chapters focus on interpretable machine learning. Many machine learning models are inherently very complex, and it is often unclear why a model makes certain predictions. The goal of Interpretable Machine Learning is to provide interpretations for a ML model's predictions. It is also referred to as Explainable Machine Learning or Explainable AI (XAI).

## What Does "Interpretation" Mean?

When people talk about making machine learning models interpretable / explainable, they don't always mean the same thing. Therefore, before we dive into the _how_ of interpretable machine learning, we need to first discuss the _what_ question.

At a high level, there are at least three different ways to interpret "interpretability" (pun intended):

1. **Feature Importance**: which features are most responsible / impactful for the model's predictions? This is perhaps the most common way people talk about interpretable machine learning.
2. **Model Complexity**: whether a model is simple enough to be understood by humans. This is often an intrinsic characteristic of the model's underlying learning technique. For example, a decision tree or a linear regression model is intrinsically not very complex (and is therefore more interpretable), where as a deep learning neural network is intrinsically more complex (and less interpretable). This is related to feature importance -- in general, it is easier to characterize feature importance for intrinsically less complex models.
3. **Model Transparency**: whether the process of building a machine learning model as well as its inner workings are properly disclosed. For example, what kinds of tranining data are used, what is the learning technique and training strategy, how is the model evaluated, etc. This is closely related to the idea of "open-source" in software development.

For our subsequent discussions, we will focus on the first way to understand interpretable machine learning, and cover different techniques to quantify feature importance. 

Within this scope, there are different types of intepretation methods that have been developed:

- **Global interpretation**: derive overall interpretation of a model, usually the relative importance of different input features.
- **Local interpretation**: derive specific interpretation for a given prediction.
- **Model-specific interpretation**: methods that only work for certain (class of) machine learning techniques.
- **Model-agnostic interpretation**: methods that are applicable regardless of the machine learning techniques being used.

The following table summarizes the interpretation methods that will be discussed in the next two chapters. All of them are model-agnostic (note that Chapter {doc}`2-2-global` will also cover a few model-specific techniques for completeness).

|                    | Global Interpretation | Local Interpretation |
| ------------------ | --------------------- | -------------------- |
| PDP                | &check;               |                      |
| M-Plot             | &check;               |                      |
| ICE                |                       | &check;              |
| ALE                | &check;               |                      |
| SHAP               | &check;               | &check;              |
| LIME               |                       | &check;              |

It is worth noting that interpretable machine learning is a fast-evloving field, and new interpretation methods are constantly being proposed (e.g., the "Integrated Gradients" approach {cite:p}`sundararajan2017axiomatic,enguehard2023sequential`). For a more comprehensive coverage of this topic, we recommend {cite:t}`molnar2022` (also available as an [online book](https://christophm.github.io/interpretable-ml-book/)).

## Why Is Interpretable ML Important?

In decision-making systems where machine learning models play an important role, having interpretable models (vs. opaque, "black-box" models) is instrumental for achieving _accountability_ and enhance _trust_. 

## Why is Interpretable ML Hard?

There is a proliferation of interpretable ML techniques and tools, but data scientists may not always understand them or use them in the intended manner {cite:p}`kaur2020interpreting`. 