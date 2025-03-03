# Prompt Memorization and Improvement Tool - PRD

## Overview

The Prompt Memorization and Improvement Tool is a web-based application designed to help users extract, analyze, and refine AI prompts used with Anthropic's Claude and OpenAI's ChatGPT. Over time, users will improve the quality and effectiveness of their prompts through structured feedback, sentiment analysis, and iterative refinement.

This tool enables users to retrieve prompts from chat history, manually add prompts, categorize them, and refine them based on historical interactions. By organizing prompts into a structured format and facilitating iterative improvements, users will gain better control over AI-generated responses and optimize their interactions.

## Requirements

### Core Functionality

The core function of the tool revolves around prompt extraction, refinement, and structured storage. To accomplish this, the following features will be implemented:

- **Support for Claude and ChatGPT:** The tool will initially focus on Anthropic's Claude but will support OpenAI's ChatGPT shortly after. This ensures users can refine prompts for multiple AI models. Other providers may be added later.
- **Prompt Extraction from Chat History:** Users will be able to pull chat history from Claude and ChatGPT through their respective APIs. The system will extract the first message from each conversation as the initial prompt. Later, we will try to extract the prompt from the entire conversation.
- **Manual Prompt Addition:** Users can manually add prompts, which will be processed in the same manner as extracted prompts to maintain uniformity.
- **Scheduled and Manual Sync:** The system will automatically retrieve chat history from the last 24 hours at scheduled intervals. Additionally, a "Sync Now" button will allow users to manually fetch chat data for immediate processing during development and testing.
- **JSON-Based Chat History Processing:** The tool will process chat logs in JSON format as provided by the AI APIs, ensuring smooth parsing and data consistency.
- **Time-Based Extraction Limitation:** By default, only conversations from the past 24 hours will be processed to keep data relevant and manageable. Users may later configure this window.

### Prompt Extraction & Sentiment Analysis

Once a prompt is extracted or added, the system will analyze it for usability and effectiveness. A key feature is sentiment analysis, which helps determine how well a prompt performed based on user feedback.

- **Focus on First Message in a Chat:** The system will initially extract only the first user message in each chat as a prompt. Future versions will support extracting mid-conversation prompts by identifying key transitions in the conversation.
- **Sentiment Analysis on Responses:** Immediately following a prompt, the system will evaluate the sentiment of the next user message. Sentiment is scored on a scale of 1-5:
  - **1-2:** Negative sentiment (e.g., "No, that’s wrong" or "That’s not what I meant")
  - **3:** Neutral (e.g., no explicit feedback, just continuing the conversation)
  - **4-5:** Positive (e.g., "Great! Now do..." or "This is exactly what I needed")
- **Handling No Feedback:** A neutral score (3) will be assigned if the system detects a new user message without explicit feedback. This is considered better than negative but not as strong as positive reinforcement.

### Prompt Processing & Structuring

To ensure consistency and usability, prompts will be transformed into a standardized structure. This format will allow for better comparison, reusability, and iteration.

Each processed prompt will follow this format:

1. **High-Level Task** - A concise one-sentence summary of the prompt’s goal.
2. **Detailed Task** - A bullet list outlining key actions, considerations, or requirements.
3. **Output Formatting** - A structured list specifying the expected format of the AI response.
4. **Procedure Guidance** - A list detailing the steps the AI should take to complete the task correctly.
5. **Task Specifics** - Freeform user-supplied details, limited to this section to keep all other sections template-driven.

- **Standardized Sections:** The structured format ensures that every prompt maintains a logical and organized approach, improving clarity and consistency.
- **Minimal Edits Beyond Structuring:** The system will only make minor grammar and spelling corrections to maintain user intent while improving readability.

### Prompt Storage & Similarity Matching

To facilitate prompt refinement and categorization, all prompts will be stored in a PostgreSQL database with vector embeddings for similarity matching.

- **Single Embedding Model:** Each prompt will be processed with a single embedding model to generate a vector representation for comparison.
- **Similarity Threshold:** Prompts that share an 80% similarity (based on goal and topic, not structure) will be considered related.
- **Parent-Child Relationships:**
  - New prompts are compared to existing ones in the database.
  - If a sufficiently similar prompt exists, the new prompt is marked as a child.
  - Otherwise, the new prompt is stored as a standalone parent.
  - Users can manually adjust parent-child relationships in a review interface.
- **Versioning and Storage:**
  - **Original Prompt:** The raw extracted or manually entered prompt.
  - **Processed Prompt:** The structured version of the original prompt.
  - **Current Prompt:** The most recent iteration of the prompt, incorporating applied feedback.
  - **Prompt History:** All past iterations will be stored and viewable with diffs.

### Applying Feedback & AI-Assisted Refinement
One of the primary functions of this tool is to improve prompts over time by applying feedback from child prompts. This process will be AI-assisted to ensure structured and meaningful improvements.

- **AI-Driven Refinement Process:**
  - When applying feedback, an AI (e.g., ChatGPT API) will compare the current parent prompt, the child prompt, and the sentiment score.
  - The AI will attempt to improve the parent prompt by incorporating insights from the child prompt.
  - Depending on the sentiment score, the AI may add, remove, or modify specific sections of the parent prompt.
- **User Interaction with AI-Generated Changes:**
  - The AI-generated refinements will be displayed as diffs in a side-by-side comparison.
  - Users will be able to interact with the AI, iterating on the refinements through a chat interface.
  - When satisfied, the user can accept the new version, marking the child prompt as "feedback applied."
- **Version Tracking:**
  - All previous versions of prompts will be stored, allowing users to revert changes if needed.
  - The applied feedback process will ensure that prompts evolve systematically while maintaining a history of iterations.

### User Interface (UI/UX)

The application will provide a clear and structured UI to facilitate prompt review, refinement, and usage.

#### **Main Prompt Review Panel**

- **Left Sidebar:**
  - Displays a list of saved prompts by short name.
  - Clicking a short name loads the corresponding prompt into the main panel.
- **Left Pane (Main Prompt View):**
  - Displays the current version of the selected prompt.
  - Lists all child prompts, marking those with unapplied feedback with a red indicator.
  - Allows manual prompt editing, with changes saved to history.
- **Right Pane (Diff View):**
  - Displays git-style diffs of prompt changes.
  - Toggle option to view chat history to understand context.
- **Apply Feedback Button:** Updates the parent prompt with changes from child prompts.

#### **Parent Identification Panel**

- **Table View:**
  - Lists all recent prompts, their short names, and their parent relationships.
  - Allows users to review and assign parent-child relationships.
- **Parent Selection View:**
  - Displays the full chat history where the prompt was extracted.
  - Shows top 5 matching candidates with similarity scores.
  - Users can confirm a parent or mark the prompt as standalone.

### Prompt Utilization

- **Direct AI Integration:** Each prompt will have a direct-use link to populate templates in Claude and ChatGPT for immediate use.
- **Manual Copying:** Users can copy prompts to the clipboard for export to external tools.

## Constraints & Future Work

- **No retention policy** will be enforced initially.
- **No bulk actions or user roles** in early versions.
- **No visualization tools** such as tree diagrams at this stage.
- **No API access** for third-party integration in early versions.
- **No Git storage** for now, but a diff tool will be used for tracking prompt changes.

## Markdown & Standardization

- **Raw Markdown Format:** All outputs will be generated as Markdown for easy copying and integration.
- **Standardized Terminology:**
  - **Parent Prompt:** The original evolving prompt.
  - **Child Prompt:** A variant used for refinement.
  - **Feedback Applied:** A flag indicating feedback has been integrated into the parent prompt.

## Deployment & Access

- **Web-Based Only:** No local or desktop versions.
- **Personal Tool:** No multi-user access initially.

This PRD provides a comprehensive foundation for the tool. Next steps will involve defining API integration details, database schema design, and UI wireframing.
