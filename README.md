# 🛠️ **Complete Client Tools Definition for ElevenLabs Conversational AI**

## 1️⃣ **set_personality_profile**

### **ElevenLabs Agent Configuration:**
```json
{
  "name": "set_personality_profile",
  "description": "Store the user's personality assessment results after analyzing their responses to personality questions",
  "wait_for_response": true,
  "response_timeout_seconds": 2,
  "parameters": [
    {
      "data_type": "string",
      "identifier": "disc",
      "required": true,
      "value_type": "LLM Prompt",
      "description": "Primary DISC personality type. Must be exactly one of: D, I, S, C"
    },
    {
      "data_type": "string",
      "identifier": "enneagram",
      "required": false,
      "value_type": "LLM Prompt",
      "description": "Enneagram type number as string. Must be 1, 2, 3, 4, 5, 6, 7, 8, or 9. Leave empty if uncertain."
    },
    {
      "data_type": "number",
      "identifier": "confidence",
      "required": true,
      "value_type": "LLM Prompt",
      "description": "Confidence level between 0.7 and 1.0. Only call this tool if confidence is 0.7 or higher."
    }
  ]
}
```

### **Client-Side Implementation:**
```typescript
// In your startSession clientTools
set_personality_profile: ({ disc, enneagram, confidence }: {
  disc: 'D' | 'I' | 'S' | 'C',
  enneagram?: string,
  confidence: number
}): string => {
  // Validate input
  if (!['D', 'I', 'S', 'C'].includes(disc)) {
    return 'Error: Invalid DISC type';
  }
  if (confidence < 0.7 || confidence > 1.0) {
    return 'Error: Confidence must be between 0.7 and 1.0';
  }
  
  // Store in your app state
  setUserPersonality({
    disc,
    enneagram: enneagram || null,
    confidence,
    timestamp: new Date().toISOString()
  });
  
  return `Personality profile stored: ${disc} type with ${confidence * 100}% confidence`;
}
```

### **LLM Usage Example:**
```
User: "When I'm overwhelmed, I usually take charge and start making lists to fix everything."

Genesis analyzes: Direct action, control-focused, task-oriented = D type
Genesis calls: set_personality_profile({ disc: "D", enneagram: "8", confidence: 0.85 })
```

---

## 2️⃣ **set_ritual_preferences**

### **ElevenLabs Agent Configuration:**
```json
{
  "name": "set_ritual_preferences", 
  "description": "Save the user's personalized daily ritual template and voice selection after co-creating their preferences",
  "wait_for_response": true,
  "response_timeout_seconds": 2,
  "parameters": [
    {
      "data_type": "string",
      "identifier": "timing",
      "required": true,
      "value_type": "LLM Prompt",
      "description": "When they prefer sessions. Must be exactly: morning_person or evening_person"
    },
    {
      "data_type": "string",
      "identifier": "duration",
      "required": true,
      "value_type": "LLM Prompt", 
      "description": "Session length preference. Must be exactly: quick_focused or deeper_dive"
    },
    {
      "data_type": "string",
      "identifier": "style",
      "required": true,
      "value_type": "LLM Prompt",
      "description": "Interaction style. Must be exactly: guided_structure or open_conversation"
    },
    {
      "data_type": "string",
      "identifier": "voice_id",
      "required": true,
      "value_type": "LLM Prompt",
      "description": "Selected voice type. Must be exactly: confident_coach, warm_friend, gentle_guide, or wise_mentor"
    },
    {
      "data_type": "string", 
      "identifier": "focus_area",
      "required": true,
      "value_type": "LLM Prompt",
      "description": "Primary emotional focus. Must be exactly: stress_management, goal_achievement, relationships, self_worth, or emotional_regulation"
    }
  ]
}
```

### **Client-Side Implementation:**
```typescript
set_ritual_preferences: ({ timing, duration, style, voice_id, focus_area }: {
  timing: 'morning_person' | 'evening_person',
  duration: 'quick_focused' | 'deeper_dive', 
  style: 'guided_structure' | 'open_conversation',
  voice_id: 'confident_coach' | 'warm_friend' | 'gentle_guide' | 'wise_mentor',
  focus_area: 'stress_management' | 'goal_achievement' | 'relationships' | 'self_worth' | 'emotional_regulation'
}): string => {
  // Validate all enums
  const validTiming = ['morning_person', 'evening_person'];
  const validDuration = ['quick_focused', 'deeper_dive'];
  const validStyle = ['guided_structure', 'open_conversation'];
  const validVoice = ['confident_coach', 'warm_friend', 'gentle_guide', 'wise_mentor'];
  const validFocus = ['stress_management', 'goal_achievement', 'relationships', 'self_worth', 'emotional_regulation'];
  
  if (!validTiming.includes(timing) || !validDuration.includes(duration) || 
      !validStyle.includes(style) || !validVoice.includes(voice_id) || 
      !validFocus.includes(focus_area)) {
    return 'Error: Invalid ritual preference values';
  }
  
  // Create ritual template
  const ritualTemplate = {
    timing,
    duration: duration === 'quick_focused' ? '3min' : '7min',
    style,
    voice_id,
    focus_area,
    created_at: new Date().toISOString()
  };
  
  setUserRitual(ritualTemplate);
  
  return `Ritual template saved: ${timing} ${duration} ${style} sessions with ${voice_id} voice, focusing on ${focus_area}`;
}
```

---

## 3️⃣ **tag_knowledge_category**

### **ElevenLabs Agent Configuration:**
```json
{
  "name": "tag_knowledge_category",
  "description": "Tag emotional focus areas detected during conversation for knowledge base personalization",
  "wait_for_response": true,
  "response_timeout_seconds": 1,
  "parameters": [
    {
      "data_type": "array",
      "identifier": "categories", 
      "required": true,
      "value_type": "LLM Prompt",
      "description": "Array of detected emotional categories. Each must be exactly: stress_management, goal_achievement, relationships, self_worth, or emotional_regulation"
    }
  ]
}
```

### **Client-Side Implementation:**
```typescript
tag_knowledge_category: ({ categories }: {
  categories: string[]
}): string => {
  const validCategories = ['stress_management', 'goal_achievement', 'relationships', 'self_worth', 'emotional_regulation'];
  
  // Validate all categories
  const invalidCategories = categories.filter(cat => !validCategories.includes(cat));
  if (invalidCategories.length > 0) {
    return `Error: Invalid categories: ${invalidCategories.join(', ')}`;
  }
  
  // Store categories for knowledge base personalization
  setKnowledgeCategories(categories);
  
  return `Tagged emotional categories: ${categories.join(', ')}`;
}
```

### **LLM Usage Example:**
```
User: "I'm always stressed at work and can't seem to focus on my personal goals."

Genesis detects: "stressed" + "goals" = stress_management + goal_achievement
Genesis calls: tag_knowledge_category({ categories: ["stress_management", "goal_achievement"] })
```

---

## 4️⃣ **set_primary_goals**

### **ElevenLabs Agent Configuration:**
```json
{
  "name": "set_primary_goals",
  "description": "Store the user's stated emotional wellness objectives and aspirations",
  "wait_for_response": true,
  "response_timeout_seconds": 1,
  "parameters": [
    {
      "data_type": "array",
      "identifier": "goals",
      "required": true,
      "value_type": "LLM Prompt", 
      "description": "Array of user's emotional wellness goals as strings. Keep them concise and actionable."
    }
  ]
}
```

### **Client-Side Implementation:**
```typescript
set_primary_goals: ({ goals }: {
  goals: string[]
}): string => {
  // Validate input
  if (!Array.isArray(goals) || goals.length === 0) {
    return 'Error: Goals must be a non-empty array';
  }
  
  // Store goals
  setPrimaryGoals(goals.map(goal => ({
    text: goal,
    created_at: new Date().toISOString(),
    status: 'active'
  })));
  
  return `Primary goals stored: ${goals.join(', ')}`;
}
```

---

## 5️⃣ **complete_onboarding**

### **ElevenLabs Agent Configuration:**
```json
{
  "name": "complete_onboarding",
  "description": "Finalize the onboarding process and transition user to their first Arami session",
  "wait_for_response": true,
  "response_timeout_seconds": 3,
  "parameters": []
}
```

### **Client-Side Implementation:**
```typescript
complete_onboarding: (): string => {
  // Validate that minimum required data exists
  const personality = getUserPersonality();
  const ritual = getUserRitual();
  const categories = getKnowledgeCategories();
  
  if (!personality || !ritual || categories.length === 0) {
    return 'Error: Missing required onboarding data';
  }
  
  // Mark onboarding as complete
  setOnboardingComplete(true);
  
  // Prepare data for main app
  const onboardingData = {
    personality,
    ritual,
    categories,
    goals: getPrimaryGoals(),
    completed_at: new Date().toISOString()
  };
  
  // Trigger transition to main app
  transitionToMainApp(onboardingData);
  
  return 'Onboarding completed successfully. Transitioning to your personalized Arami experience.';
}
```

---

## 6️⃣ **clarify_user_input**

### **ElevenLabs Agent Configuration:**
```json
{
  "name": "clarify_user_input",
  "description": "Ask for clarification when user response is unclear or garbled",
  "wait_for_response": true,
  "response_timeout_seconds": 1,
  "parameters": [
    {
      "data_type": "string",
      "identifier": "question",
      "required": true,
      "value_type": "LLM Prompt",
      "description": "The clarifying question to ask the user"
    }
  ]
}
```

### **Client-Side Implementation:**
```typescript
clarify_user_input: ({ question }: {
  question: string
}): string => {
  // Log the clarification request
  console.log('Clarification requested:', question);
  
  // Could trigger UI indication that clarification is needed
  setNeedsClarification(true);
  
  return `Asked for clarification: ${question}`;
}
```

---

## 🎯 **Complete startSession Setup:**

```typescript
const conversation = useConversation({
  onConnect: () => console.log('Connected to Genesis'),
  onDisconnect: () => console.log('Disconnected'),
  onMessage: (message) => console.log('Genesis:', message),
  onError: (error) => console.error('Error:', error),
});

const startOnboarding = async () => {
  const signedUrl = await getSignedUrl();
  
  await conversation.startSession({
    signedUrl,
    dynamicVariables: {
      user_name: userName,
    },
    clientTools: {
      set_personality_profile,
      set_ritual_preferences, 
      tag_knowledge_category,
      set_primary_goals,
      complete_onboarding,
      clarify_user_input
    }
  });
};
```

This setup gives you **complete control** over the onboarding data collection while allowing Genesis to make intelligent decisions about when and how to call each tool!