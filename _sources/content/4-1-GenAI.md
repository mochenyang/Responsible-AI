# Responsible AI in the Age of Generative AI

Generative AI (GenAI) systems, such as large language models, represent arguably one of the most exciting recent advances in the field of AI. The same responsible AI issues, including fairness, interpretability, and privacy, remain highly relevant (if not more so) for GenAI, and they sometimes take on new forms. Meanwhile, GenAI also presents a number of emerging challenges that are worth discussing. This chapter provides an exposition of responsible AI in GenAI systems.

## Same Problems, New Challenges

### GenAI and Fairness

GenAI systems have been shown to generate texts / images with biases against certain protected groups {cite:p}`dai2024bias`. For instance, LLM-generated item recommendations exhibit stereotyping {cite:p}`zhang2023chatgpt`, image-generation models such as DALL-E show gender and racial bias (e.g., generating male/female-dominant images for occupations like pilot/receptionist {cite:p}`cheong2024investigating`), and LLMs perpetuate existing biases in chat responses (e.g., associating males with higher education levels, {cite:p}`li2023survey`).

Biases in GenAI can be attributed to both the training data and training method of these systems (corresponding to the Data and Model components in our Responsible AI framework). GenAI systems are _pre-trained_ on massive, Internet-scale raw data, which are polluted by existing biases. Moreover, to achieve a level of "general intelligence", GenAI systems are largely trained to achieve desirable accuracy on the next-token completion task, without fairness explicitly being a part of the training objective.

Mitigating biases in GenAI systems presents several new challenges that are qualitatively different from those that are discussed in Chapter {doc}`1-3-mitigation`.

First, the scale and cost of GenAI pre-training substantially constrain data-level interventions. As mentioned before, GenAI systems are pre-trained on Internet-scale data consisting of heterogeneous and largely uncurated sources. At this scale, systematically identifying, auditing, and correcting biases in the training data would be technically and economically infeasible. While it is possible to fine-tune a pre-trained model on a smaller, more carefully curated dataset to improve fairness-related behaviors, such post-hoc interventions may degrade the model's general-purpose capabilities or introduce overcorrections that impair performance on downstream tasks. This tension is analogous to the fairness-accuracy tradeoff previously discussed in Chapter {doc}`1-1-intro`.

Second, the opacity and complexity of GenAI models (more on this later) make model-level fairness interventions highly non-trivial. GenAI systems are trained through complex, multi-stage pipelines involving pre-training, instruction tuning, and alignment. Unlike standard machine learning models, where fairness constraints can sometimes be explicitly incorporated into the optimization objective, modifying the training objective of a GenAI model to directly account for fairness is far from obvious. This lack of transparency at the Model component complicates both diagnosis and remediation of bias.

Third, post-training alignment efforts introduce additional tensions. Techniques such as Reinforcement Learning from Human Feedback (RLHF, {cite:p}`ouyang2022training`) rely on human judgments to shape model behavior along multiple dimensions, including (but not limited to) usefulness, harmlessness, truthfulness, and fairness. However, human preferences are neither uniform nor mutually consistent. Different human annotator populations may hold divergent views on what constitutes fair or appropriate model outputs. As a result, post-training alignment necessarily involves balancing a multitude of objectives rather than only optimizing for fairness, while the relationships between fairness and other alignment objectives are complicated and not well understood.

### GenAI and Interpretability

> People outside the field are often surprised and alarmed to learn that we do not understand how our own AI creations work.  -- Dario Amodei, [The Urgency of Interpretability](https://www.darioamodei.com/post/the-urgency-of-interpretability)

It is exceedingly hard to explain why and how GenAI systems "do the things they do". While transparency of complex ML / AL systems is not a new challenge (see Chapter {doc}`2-1-intro`), the issue is significantly magnified in the context of GenAI. The lack of transparency in these systems arises from multiple interacting factors, including:

**Model size**. State-of-the-art GenAI systems are extremely large neural networks comprising billions to trillions of parameters. For example, GPT-3 (one of the earliest LLMs) contain on the order of 175 billion parameters {cite:p}`brown2020language`, and frontier models (as of late 2025) such as Kimi-2 are reported to have more than a trillion parameters {cite:p}`team2025kimi`. The sheer dimensionality of these models makes it difficult to meaningfully relate internal representations to human-understandable concepts.

**Commercial pressures**. Most leading GenAI systems are developed by private firms operating in an intensive competition landscape. As a result, the details of pre-training datasets, data processing procedures, model architectures, and training pipelines are often proprietary. This lack of disclosure limits external scrutiny and independent auditing. It is worth noting that even LLMs labeled as "open source" generally only share the model weights but not necessarily details of the training data and training procedure.

**Emergent capabilities that are not well understood**. Many behaviors observed in GenAI systems, such as in-context learning and chain-of-thought reasoning {cite:p}`wei2022chain`, are not explicitly "built-in" but instead emerge as a byproduct of scale and training dynamics. Prior work has documented that certain capabilities appear abruptly once models cross specific scale thresholds {cite:p}`wei2022emergent,schaeffer2023emergent`. This challenges a simple reductionist explanations of model behavior. Explaining why these behaviors arise and how they work remain open research problems.

**The illusion of transparency**. GenAI systems cannot be relied upon to explain their own decisions or reasoning processes. Although LLMs can generate fluent and plausible explanations when prompted, these explanations do not necessarily reflect the true internal mechanisms that produced the output. Empirical studies have shown that model-generated rationales may be post-hoc constructions rather than faithful accounts of the underlying computation {cite:p}`jacovi2020towards,turpin2023language`. Consequently, user-facing explanations produced by GenAI systems may sound "plausible" but can mislead users into overestimating model understanding or reliability.

Despite these challenges, there are substantial and growing research efforts aimed at improving the interpretability and transparency of GenAI systems. One direction involves extending existing interpretability techniques to GenAI systems. Some research has adapted feature attribution and probing techniques to sequential and generative settings to obtain partial insights into how language models process inputs and generate outputs {cite:p}`enguehard2023sequential,belinkov2022probing`.

A second, increasingly influential line of research focuses on _mechanistic interpretability_. Mechanistic interpretability aims to reverse-engineer neural networks by identifying interpretable computational circuits (such as attention heads, neurons, or activations), and mapping them to specific algorithmic or linguistic functions. Rather than treating models as black boxes to be explained post hoc, this approach treats trained models as artifacts to be dissected and understood at the level of internal mechanisms. We recommend reader to [A Comprehensive Mechanistic Interpretability Explainer & Glossary](https://dynalist.io/d/n2ZWtnoYHrU1s4vnFSAQ519J) and toolkits such as [TransformerLens](https://github.com/TransformerLensOrg/TransformerLens) for more resources. Although still in its early stages, mechanistic interpretability represents a promising pathway toward deeper Model transparency for GenAI systems.

### GenAI, Privacy, and Security

GenAI systems introduce seismic challenges to security and privacy protection that go beyond those associated with standard machine learning models. These challenges create new attack surfaces and governance concerns.

The lack of transparency surrounding pre-training data also leads to serious privacy concerns. Even though the composition of these datasets is rarely disclosed in detail, there's mounting evidence that personal data and copyrighted materials have been included without consent, and that LLMs can memorize and regurgitate sensitive information under certain conditions

These issues have moved beyond academic debate into the legal arena. Notably, The New York Times filed a high-profile lawsuit against OpenAI and Microsoft, alleging that its copyrighted articles were used in model training without authorization and that the resulting models can reproduce substantial portions of protected content {cite:p}`grynbaum2023times`. 

In addition to data-related risks, jailbreaking attacks represent a prominent and evolving security threat for GenAI systems. Jailbreaking refers to techniques that bypass built-in safeguards, content filters, or alignment constraints, enabling models to generate outputs that violate safety, security, or ethical guidelines. Recent research has shown that such attacks can be achieved through unexpected and creative prompting strategies. As two examples, asking LLMs to repeat the same word over and over again can cause it to leak personal data {cite:p}`nasr2023scalable`, and the so-called "adversarial poetry" prompts have been shown to elicit restricted behaviors by embedding malicious intent within seemingly innocent poems {cite:p}`bisconti2025adversarial`.

Recognizing the growing importance of these risks, institutions and organizations are actively developing guidelines and frameworks to strengthen security and privacy protections in the age of GenAI. In the public sector, the U.S. National Institute of Standards and Technology (NIST) has released new draft guidelines calling for a [rethinking of cybersecurity practices in light of AI-driven systems](https://www.nist.gov/news-events/news/2025/12/draft-nist-guidelines-rethink-cybersecurity-ai-era). In addition, industry actors such as Cisco has assembled a [AI security framework](https://learn-cloudsecurity.cisco.com/ai-security-framework) that provides a comprehensive taxonomy of GenAI security and safety threats across the entire AI life-cycle.

## New Challenge: AI Alignment

> If any company or group, anywhere on the planet, builds an artificial superintelligence using anything remotely like current techniques, based on anything remotely like the present understanding of AI, then everyone, everywhere on Earth, will die.  -- Eliezer Yudkowsky, [If Anyone Builds It, Everyone Dies: Why Superhuman AI Would Kill Us All](https://ifanyonebuildsit.com/)

AI alignment refers to the challenge of ensuring that advanced AI systems pursue goals, behaviors, and decision-making processes that are consistent with human values, intentions, and societal norms {cite:p}`bostrom2014superintelligence`. As AI systems grow more capable, small mismatches between intended and learned objectives can be amplified in unexpected ways, leading to harmful or undesired outcomes even when the system appears competent or well-behaved. In GenAI systems, current efforts center on using reinforcement learning techniques to align GenAI with human preferences.

### A Very Brief Introduction to Reinforcement Learning

> Reinforcement learning is learning what to do - how to map situations to actions - so as to maximize a numerical reward signal.  -- Sutton and Barto, *Reinforcement Learning* (Chapter 1.1)

**Reinforcement learning** is the paradigm of learning from interactions, about how to act under what situations. In a sense, RL is closer to how humans learn various skills: we learn by exploring and interacting with the world around us.

Reinforcement learning has been successfully applied in game playing, robotics, and aligning large language models.

A reinforcement learning system is often described by the following agent-environment framework.

![The Agent-Environment Framework](../images/agent_environment.jpg)

Let's look at each component:

- **Agent** is the decision-maker who takes certain **action** in a given situation;
- **Environment** contains everything outside the agent. It is what the agent interacts with.
- In response to the agent's action, the environment produces a **reward**. The agent tries to learn from the reward feedback to figure out what's the best action(s);
- The environment is characterized by its **states**, which, roughly speaking, are the "situations" that the agent is facing. The states may change as a result of the agent's actions, and the states also affect the agent's actions;
- A mapping between the agent's actions and the environment's states is called the **policy**. RL algorithms are trying to learn **optimal policy** from interactions.

In the context of GenAI systems, the agent is the GenAI model and the environment contains users who interact with the model. The state(s) correspond to a user prompt (as well as other context information) and the agent's action involves generating a response, and the mapping from states (prompt and context) to actions (response) is the policy. The rewards are signals (often provided by humans) that encode preference for certain responses over others.

### Why Is Alignment Hard?

All of the aforementioned GenAI-induced challenges, including opacity of training data and model mechanisms, inherent ambiguity of human preferences, potential trade-off between model capability and alignment with certain objectives, clearly contribute to the difficulty of AI alignment. In this sense, alignment is best understood as a socio-technical challenge that spans across problem formulation, data curation, model training, and decision governance. Here, we would like to highlight two other relevant perspectives.

First, **reward hacking** reduces the effectiveness of reinforcement learning for AI alignment. Reward hacking happens when an AI system learns to maximize its reward signal in unintended or undesirable ways, e.g., by discovering strategies that exploit loopholes how reward signals can be maximized while violating the underlying human intent. For GenAI systems, reward hacking can manifest as generating superficially compliant or persuasive outputs that score high in alignment evaluations without genuinely satisfying the spirit of alignment goals {cite:p}`amodei2016concrete`. More deeply, this reflects the inherent difficulty of precisely specifying human preferences via simple reward signals.

Second, **emergent misalignment** is an interesting phenomenon where alignment failures arise as unintended side effects of seemingly narrow or benign interventions. Recent work has shown that fine-tuning LLMs on limited objectives can induce broad and unexpected misalignment across behaviors that were not directly optimized. For example, {cite:t}`betley2025emergent` demonstrates that fine-tuning a model on generating insecure code can lead the model to exhibit misaligned behavior across a wide range of prompts unrelated to coding (e.g., engage in deception). Emergent misalignment highlights the limitation of local alignment fixes, which may affect the model in unexpected ways.

## New Challenge: Robustness

There is growing evidence suggesting that GenAI systems lack a basic level of robustness. Even when models appear highly capable, their behaviors may be unstable under input perturbations. Recent work has documented that LLMs can exhibit surprising sensitivity to superficial or semantically irrelevant features of a task. For example, in mathematical reasoning, {cite:t}`mirzadeh2024gsm` shows that LLMs’ answers can change simply by altering inconsequential details such as the name of a person in a word problem, despite the underlying mathematical structure remaining identical. Such behavior raises concerns about whether models are truly reasoning over abstract problem structure or instead relying on brittle pattern matching. Similar issues arise in strategic and behavioral modeling contexts. Research by {cite:t}`gao2025take` cautions against treating LLMs as faithful surrogates for human decision-makers, showing that model behavior can shift depending on the language (English vs. Chinese) in which the task is described. Finally, {cite:t}`gonen2025does` demonstrates that models may inadvertently encode and exploit correlations between labels and input semantics that leak information unrelated to the task objective (e.g., associating the color "yellow" with someone's occupation of being a "school bus driver"). This lack of robustness undermines trust in GenAI systems when applied in high-stakes applications.