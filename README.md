# Personal shadcn-vue Registry

A collection of custom shadcn-vue components.

## Components

### Rich Text Editor

A compact rich text editor built with TipTap and shadcn-vue components.

**Features:**
- Bimodal support (editable/read-only)
- Compact inline toolbar
- Essential formatting tools (bold, italic, underline, strike)
- Headings (H1, H2, paragraph)
- Lists (bullet, numbered, blockquote)
- Clean shadcn-vue integration

**Installation:**

```bash
bunx shadcn-vue@latest add https://raw.githubusercontent.com/adrianpearl/shadcn-vue-registry/main/rich-text-editor.json
```

**Usage:**

```vue
<template>
  <RichTextEditor 
    v-model="content" 
    :editable="true"
    placeholder="Start writing..." 
  />
</template>

<script setup>
import { ref } from 'vue'
import RichTextEditor from '@/components/ui/RichTextEditor.vue'

const content = ref('<p>Hello world!</p>')
</script>
```

## Registry Structure

```
registry/
├── README.md
├── rich-text-editor.json          # Registry metadata
└── new-york/
    └── rich-text-editor/
        └── RichTextEditor.vue      # Component file
```