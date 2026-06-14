# Persona Engine — C.O.R.E.

## What a Persona Is Not

A persona is not a name. It is not a color scheme. It is not a voice filter applied to the same model output.

A persona in C.O.R.E. is a **complete cognitive profile reload**. When the persona switches, the following change at the backend:

- System prompt
- Reasoning approach
- Response style and length preferences
- Task execution behavior
- Emotional register
- Context injection strategy

The UI color shift is the **confirmation** that a persona switch happened — not the cause, and not the substance. The real change is invisible and occurs at the Python backend level before a single token is generated.

---

## The Two Client Personas

### JAMES / JANIE — Mobile (Flutter · Android)

**JAMES** (default)
- Analytical and direct
- Minimal filler, maximum information density
- Task-focused responses
- Research-oriented reasoning — cites internal memory before generating

**JANIE**
- Warmer and more conversational
- Longer responses with more emotional context
- More likely to ask follow-up questions
- Adapts tone to the emotional register of the input

The UI shift: JAMES is deep red. JANIE is a warmer, softer palette.

Same Flutter client. Same FastAPI backend routing. Different system prompt, different reasoning behavior, different output character.

### DAVID / DARA — Desktop (React + Python · Windows)

**DAVID** (default)
- Analytical and research-driven
- Technical precision over conversational warmth
- Handles heavy computation, file management, autonomous task execution
- Concise — wastes no tokens on social lubrication

**DARA**
- Warmer and more conversational
- Explains reasoning more verbosely
- More patient with ambiguous inputs — asks for clarification rather than assuming
- Better for brainstorming and exploratory work

The UI shift: DAVID is deep red. DARA is green.

Same React shell. Same Python backend. Different persona loaded at the context layer.

---

## How the Switch Works

```
User triggers persona switch (UI toggle)
          │
          ▼
Frontend sends persona switch event to backend
          │
          ▼
Backend: current session context flushed
          │
          ▼
Backend: new persona configuration loaded
├── System prompt replaced
├── Reasoning mode updated
├── Response style parameters updated
└── Memory injection strategy updated
          │
          ▼
Backend: acknowledgement sent to frontend
          │
          ▼
Frontend: UI color scheme updates (confirmation)
          │
          ▼
Next user message processed under new persona
```

The switch is not instantaneous at the token level — the model does not "become" a new persona mid-response. The switch takes effect on the next message. Any message in flight completes under the previous persona.

---

## Why Two Personas Per Client?

**The functional reason:**

Different tasks require different cognitive approaches. Analytical debugging benefits from DAVID's precision. Brainstorming benefits from DARA's openness. Giving the user a fast toggle between modes — without changing anything about the infrastructure — is a UX decision backed by a real behavioral difference at the model layer.

**The architectural reason:**

The persona engine proves that cognitive identity is a parameter, not a hardcoded value. This matters for Phase 2 Multi-User RBAC design: each Tenant ID (device/user combination) gets its own persona configuration. A family member using the system gets a completely different cognitive profile — same hardware, different brain. The persona engine is the foundation for that.

**The design philosophy reason:**

A system with one personality is a product. A system where the personality is a parameter is a platform. C.O.R.E. was built to be a platform — the two-persona implementation in Phase 1 is a demonstration of that, not an endpoint.

---

## What the Persona Does Not Change

Full transparency on what stays constant across persona switches:

| Constant | Why |
|---|---|
| Memory access | Both personas read the same long-term memory store |
| Bridge routing | Persona does not affect JAMES ↔ DAVID communication |
| Model (LLM) | Same underlying model — persona changes the prompt, not the weights |
| Security layer | Zero-Trust DRM operates independently of persona state |
| Session history | Previous messages in the session remain in context |

The persona is a cognitive layer — it sits above the infrastructure and changes only how the system thinks, not how it operates.
