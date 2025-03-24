# Fine-Tuning LLMs for Socratic Interactions

## Selecting a Base Model
- We can fine-tune *any* large language model (LLM) for Socratic interaction.
- **Phi-3 Mini** is used here as an example due to its suitability and efficiency.

### Why Phi-3 Mini?
- **Small Model Size:**
  - Compact compared to larger models (e.g., GPT-4 or GPT-3), allowing fast and local deployments.
- **Cost & Accessibility:**
  - Open-source and suitable for users with limited resources.
- **Reasoning Capabilities:**
  - Optimized for logical reasoning and concise dialogue, ideal for Socratic interactions.

---

## Understanding Socratic Interaction
- A **Socratic interaction** is a type of dialogue where questions challenge a participant’s implicit assumptions, uncover inconsistencies, and guide toward deeper understanding.
- **Educational Use Today:**
  - Encourages deep conceptual learning over rote memorization.
  - Allows students to **discover solutions independently** through guided questions.

---

## Can Current LLMs Engage Socratically?
- Typically, no—most LLMs provide direct answers unless explicitly prompted to behave otherwise. For example:
  - **Student:** *"Professor, how can I solve \( x^2 - 1 = 0 \)?"*
  - **Typical LLM Response:** *"To solve the equation, follow these steps..."*

---

## Limitations of Prompt Engineering Alone
- Prompt engineering alone can instruct LLMs to mimic Socratic interactions, but effectiveness is limited, especially for smaller models.

---

## Fine-Tuning Pipeline for Socratic Interaction

- To enable natural Socratic interactions, we can fine-tune models using this pipeline:
1. **Data Preparation:**
   - Select seed interactions.

2. **Generate Interaction Prompts:**
   - Prompt a chosen LLM to produce teacher-student dialogues.

3. **Rank Generated Interactions:**
   - Use a more advanced ("Judge") LLM to rank generated dialogues against a predefined rubric.

4. **Fine-Tune via Direct Preference Optimization (DPO):**
   - Input the highest- and lowest-ranked examples into DPO for training.

---

## Available Datasets for Training
- ***TutorChat***
  - Each instance is a set of simulated GPT-3.5-turbo (Teacher) vs. GPT-3.5-turbo (Student) interactions based on one or more science textbooks.
- ***MathDial***
  - Each instance is a problem statement, a student's incorrect answer, a correct answer, and human (Teacher) vs. GPT-3.5-turbo (Student) interaction.
- ***Debugging***
  - Each instance is a problem statement, a student's code, a description of a bug, and human (Teacher) vs. human (Student) interaction.

| Dataset   | Description                                             | Interaction Participants              |
|-----------|---------------------------------------------------------|---------------------------------------|
| **TutorChat** | Based on science textbooks                               | GPT-3.5 (Teacher) vs GPT-3.5 (Student)|
| **MathDial**  | Math problems and solutions                             | Human (Teacher) vs GPT-3.5 (Student)  |
| **Debugging** | Coding bugs and fixes                                   | Human (Teacher) vs Human (Student)    |

---

## Assessing Interaction Quality

Interaction quality is evaluated by a **Summary Score** derived from four uniformly-weighted and normalized sub-categories:

1. **Question (0-1):** Does the response include a question?
2. **Reveal (0-1):** Does it explicitly reveal the solution?
3. **On-Topic (1–5):** How relevant is the response?
4. **Helpful (1–5):** How effectively does it guide the student toward the solution?

(*Except for Reveal, **higher** scores indicate better interactions.*)

---

## Validating Judge LLM Assessments

- Compare scores from a human evaluator vs. GPT-4o (Judge LLM).
- A study of 100 examples yielded a strong Pearson correlation (**0.78**), suggesting GPT-4o effectively approximates human assessment.
- Further analysis may reveal category-specific differences between human and LLM scoring.

---

## Choosing an Optimization Algorithm

After dataset selection and evaluation criteria setup, we select an optimization strategy:

- **Reinforcement Learning from Human Feedback (RLHF)**
- **Supervised Learning**
- **Direct Preference Optimization (DPO)** *(recommended here)*

---

## Why DPO?

DPO is preferred because it:
- Is **stable**, requiring less hyperparameter tuning.
- Eliminates the need to separately train a **reward model**.
- Requires fewer computational resources (**no GPU-intensive reward model** needed).

---

## Assembling the Fine-Tuning Pipeline

**Step-by-step process:**

1. Provide seed interactions from chosen datasets.
2. Prompt the LLM to generate **five separate, independent responses** per student query.
3. Evaluate responses with GPT-4o, calculating **summary scores**.
4. Rank responses; select the **best and worst** responses.
5. Construct training triplets:
   - Student Question
   - High-ranked (good) Teacher Response
   - Low-ranked (poor) Teacher Response
6. Fine-tune using the **DPO algorithm**.

<p align="center">
  <img src="training-pipeline.png" alt="Training Pipeline" width="616"/>
</p>

---

## Testing the Fine-Tuned Model

Two common methods:

- Reserve a portion of data beforehand as **test and validation sets**.
- Fine-tune on one dataset (e.g., TutorChat) and test on others (MathDial, Debugging).

---

## Evaluating the Results

- **Red:** Phi-3 Mini (prompt-engineered only)
- **Blue:** Phi-3 Mini fine-tuned
- **Purple:** GPT-4o (prompt-engineered only)

<p align="center">
  <img src="example-results.png" alt="Model Evaluation" width="616"/>
</p>

### Evaluating Performance by Scoring Component

<p align="center">
  <img src="example-results-breakdown.png" alt="Component Scores" width="616"/>
</p>

---

## Potential Contributions and Impact

- These methods allow us to create **custom Socratic-style interactions** for any community or topic.
- They provide a reliable **baseline assessment** for evaluating Socratic performance.
- Research has shown that this approach delivers state-of-the-art conversational interactions with low-cost, accessible models.

---
