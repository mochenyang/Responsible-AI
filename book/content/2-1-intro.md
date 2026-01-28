# Introduction to Interpretable Machine Learning

In 2015, IBM launched _Watson for Oncology_ and promoted it as a breakthrough in clinical decision support: an AI system that could help oncologists identify personalized cancer treatments by synthesizing patient records, clinical guidelines, and the medical literature {cite:p}`strickland2019ibm`. IBM invested billions of dollars in the development of this system, only to find that many clinicians and hospitals ultimately chose not to adopt the system. A recurring concern was not merely whether Watson's recommendations were accurate, but whether they were understandable. Clinicians reported difficulty assessing why particular treatments were suggested, how recommendations aligned with clinical evidence, and when the system might be unreliable {cite:p}`ross2018ibm`. In a domain where decisions carry life‑and‑death consequences and professional accountability rests with human experts, the absence of transparency proved to be a critical barrier to trust and adoption. 

> Black boxes are unacceptable: A CDSS (clinical decision support system) requires transparency so that users can understand the basis for any advice or recommendations that are offered. -- {cite:p}`shortliffe2018clinical`

## Transparency in AI / Augmented Decision Making

As discussed in Chapter {doc}`0-2-framework`, AI / augmented decision making systems do not act alone; they are inherently embedded as part of the larger socio-technical systems, where human judgment and algorithmic recommendations are intertwined. This gives rise to **transparency** as an important responsible AI consideration: how can stakeholders understand, scrutinize, and ultimately trust decisions shaped by complex ML / AI models? 

Like algorithmic fairness, the issue of transparency in AI / augmented decision making can also manifest in Problem, Data, Model, and Decision components. Specifically,

- **Problem** transparency: clarity about what decision problem the ML / AI system is designed to support and how it is framed. This includes explicit disclosure of the target outcome, optimization objective, constraints, assumptions, and key stakeholders in the problem formulation.
- **Data** transparency: visibility into where data come from, how they were collected, processed, and labeled, and what populations they represent. Common mechanisms include data provenance {cite:p}`buneman2001and`, data documentation, and datasheets {cite:p}`gebru2021datasheets`. Data transparency enables stakeholders to assess data quality, relevance, bias, and limitations, and to reason about when a model’s predictions may or may not generalize.
- **Model** transparency: this primarily deals with interpretable machine learning (more on this later).
- **Decision** transparency: clarity about how model outputs are communicated, contextualized, and used in downstream decision processes. This includes interaction transparency (e.g., explanations shown to users, uncertainty communication, confidence indicators), disclosure that AI assistance is being used (e.g., watermarking or labeling), and governance mechanisms that specify when human judgment can override or contest algorithmic recommendations.

From a technical point of view, the following two chapters focus on interpretable machine learning. Many machine learning models are inherently very complex, and it is often unclear why a model makes certain predictions. The goal of Interpretable Machine Learning is to provide interpretations for a ML model's predictions. It is also referred to as Explainable Machine Learning or Explainable AI (XAI).

## What Does "Interpretation" Mean?

When people talk about making machine learning models interpretable / explainable, they don't always mean the same thing {cite:p}`kaur2020interpreting`. Therefore, before we dive into the _how_ of interpretable machine learning, we need to first discuss the _what_ question.

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

It is worth noting that interpretable machine learning is a fast-evloving field, and new interpretation methods are constantly being proposed (e.g., the "Integrated Gradients" approach {cite:p}`sundararajan2017axiomatic,enguehard2023sequential`). For a more comprehensive coverage of this topic, we recommend {cite:t}`molnar2025` (also available as an [online book](https://christophm.github.io/interpretable-ml-book/)).

## From Interpretation to Decision

Consider the following two (fictitous) scenarios:
1. Scenario 1: a cancer detection algorithm predicts potential skin cancer using images of skin surfaces. User X received a positive diagnosis along with an explanation that "there is a large dark spot". X went on to cover the dark spot with makeup. With a new skin image, X received a negative diagnosis.
2. Scenario 2: a bank relies on a predictive model to approve / reject loans. The model predicts default risk based on an applicant's credit score and financial standing. Suppose applicant A received a rejection decision with an explanation "credit score lower than 600" (implying that A's application would have been approved if credit score was higher than 600). The bank next recommended A to prioritize paying off credit card debts and refrain from opening new lines of credit.

Even without deep knowledge of interpretable machine learning techniques, most people would intuitive think that user X's decision in scenario 1 is ineffective (and comical), whereas the bank's recommendation in scenario 2 is reasonable. In the first scenario, the action of covering up the dark spot follows from the interpretation, and this action, technically speaking, does change the model's prediction from positive to negative. However, this action would not do anything to cure the underlying skin condition (if there is one), because it does not causally affect the underlying ground truth (the actual skin condition). It simply "tricks" the model to give a different prediction. In the second scenario, the action taken based on the interpretation has a decent chance of not only change the model's prediction but also meaningfully change the ground truth default risk of the individual. 

Comparing these two scenarios, we can draw some takeaway lessons about making decisions based on interpretations of machine learning predictions. In both scenarios, the action is taken based on the interpretation, and the action can in fact change the model's prediction. This is not surprising, because interpretation methods are often mechanically designed to measure how much a feature can change a model's prediction. So if we change the value of features, we can "causally" change the predictions. However, changing model's predictions may not be what we truly care about in most cases. Instead, we care about taking actions to change the ground truth, such as curing the actual skin condition or to reduce the actual default risk. Here, interpretation typically is not the same as causality, meaning that decisions based on interpretation may not effectively change the ground truth (such as what we saw in scenario 1). The key missing element to go from interpretation to effective decisions here is the causal understanding of "how things work". In both of the scenarios discussed before, our common sense tells us that merely covering up a dark spot is not going to cure skin conditions, but paying off one’s debt can reduce default risk. In more complex decision environments where common sense is insufficient, we need to rely on causal inference methods, such as running experiments. to deal with this issue.

Please keep in mind that: making effective decisions often requires causal understanding, and causality is hard to claim. It needs domain knowledge and expertise. Do not treat data-driven interpretations as causal evidence and directly start making prescriptive decisions. Instead, treat them as possible hypotheses, questions to ask, factors to test, and an intermediate step to take before making the final decisions.
