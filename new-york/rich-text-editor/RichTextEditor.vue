<script setup lang="ts">
import { ref, computed, onBeforeUnmount, onMounted, watch } from 'vue'
import { Editor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import Underline from '@tiptap/extension-underline'
import BulletList from '@tiptap/extension-bullet-list'
import OrderedList from '@tiptap/extension-ordered-list'
import ListItem from '@tiptap/extension-list-item'
import { type HTMLAttributes } from 'vue'

import { Button } from '@/components/ui/button'
import { Separator } from '@/components/ui/separator'
import { cn } from '@/lib/utils'

import { 
  Bold, 
  Italic, 
  Strikethrough, 
  Underline as UnderlineIcon,
  Heading1,
  Heading2,
  Pilcrow,
  List,
  ListOrdered,
  Quote
} from 'lucide-vue-next'

interface Props {
  modelValue?: string
  editable?: boolean
  placeholder?: string
  class?: HTMLAttributes['class']
}

interface Emits {
  (e: 'update:modelValue', value: string): void
}

const props = withDefaults(defineProps<Props>(), {
  modelValue: '',
  editable: true,
  placeholder: 'Start writing...'
})

const emit = defineEmits<Emits>()

const editor = ref<Editor | null>(null)

const createEditor = () => {
  editor.value = new Editor({
    content: props.modelValue,
    editable: props.editable,
    extensions: [
      StarterKit.configure({
        // Disable default lists to configure them explicitly
        bulletList: false,
        orderedList: false,
        listItem: false,
      }),
      Underline,
      BulletList.configure({
        HTMLAttributes: {
          class: 'tiptap-bullet-list',
        },
      }),
      OrderedList.configure({
        HTMLAttributes: {
          class: 'tiptap-ordered-list',
        },
      }),
      ListItem.configure({
        HTMLAttributes: {
          class: 'tiptap-list-item',
        },
      }),
    ],
    onUpdate: ({ editor }) => {
      const html = editor.getHTML()
      if (html !== props.modelValue) {
        emit('update:modelValue', html)
      }
    },
    editorProps: {
      attributes: {
        class: 'prose prose-sm max-w-none focus:outline-none',
        'data-placeholder': props.placeholder,
      },
    },
  })
}

onMounted(() => {
  createEditor()
})

onBeforeUnmount(() => {
  if (editor.value) {
    editor.value.destroy()
  }
})

// Watch for changes to modelValue
watch(() => props.modelValue, (newValue) => {
  if (editor.value && editor.value.getHTML() !== newValue) {
    editor.value.commands.setContent(newValue, false)
  }
})

// Watch for changes to editable prop
watch(() => props.editable, (newEditable) => {
  if (editor.value) {
    editor.value.setEditable(newEditable)
  }
})
</script>

<template>
  <div :class="cn('rich-text-editor', props.class)">
    <!-- Toolbar - only visible when editable -->
    <div 
      v-if="editable" 
      class="flex items-center gap-1 border-b p-2"
    >
      <!-- Text formatting -->
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleBold().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('bold') }"
        aria-label="Bold"
      >
        <Bold class="h-4 w-4" />
      </Button>
      
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleItalic().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('italic') }"
        aria-label="Italic"
      >
        <Italic class="h-4 w-4" />
      </Button>
      
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleStrike().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('strike') }"
        aria-label="Strikethrough"
      >
        <Strikethrough class="h-4 w-4" />
      </Button>
      
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleUnderline().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('underline') }"
        aria-label="Underline"
      >
        <UnderlineIcon class="h-4 w-4" />
      </Button>
      
      <Separator orientation="vertical" class="mx-1 h-6" />
      
      <!-- Headings -->
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleHeading({ level: 1 }).run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('heading', { level: 1 }) }"
        aria-label="Heading 1"
      >
        <Heading1 class="h-4 w-4" />
      </Button>
      
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleHeading({ level: 2 }).run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('heading', { level: 2 }) }"
        aria-label="Heading 2"
      >
        <Heading2 class="h-4 w-4" />
      </Button>
      
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().setParagraph().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('paragraph') }"
        aria-label="Paragraph"
      >
        <Pilcrow class="h-4 w-4" />
      </Button>
      
      <Separator orientation="vertical" class="mx-1 h-6" />
      
      <!-- Lists -->
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleBulletList().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('bulletList') }"
        aria-label="Bullet List"
      >
        <List class="h-4 w-4" />
      </Button>
      
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleOrderedList().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('orderedList') }"
        aria-label="Numbered List"
      >
        <ListOrdered class="h-4 w-4" />
      </Button>
      
      <Button
        variant="ghost"
        size="sm"
        @click="() => editor?.chain().focus().toggleBlockquote().run()"
        :class="{ 'bg-accent text-accent-foreground': editor?.isActive('blockquote') }"
        aria-label="Quote"
      >
        <Quote class="h-4 w-4" />
      </Button>
    </div>
    
    <!-- Editor content -->
    <div 
      :class="cn(
        'min-h-32 p-4 focus-within:outline-none',
        editable ? 'border rounded-b-md' : 'rounded-md',
        !editable && 'bg-muted/30'
      )"
    >
      <EditorContent 
        :editor="editor" 
        class="prose prose-sm max-w-none focus:outline-none"
      />
    </div>
  </div>
</template>

<style scoped>
/* TipTap styles */
:deep(.ProseMirror) {
  outline: none;
}

:deep(.ProseMirror p.is-editor-empty:first-child::before) {
  color: hsl(var(--muted-foreground));
  content: attr(data-placeholder);
  float: left;
  height: 0;
  pointer-events: none;
}

/* Prose styles using CSS custom properties */
:deep(.prose) {
  color: hsl(var(--foreground));
}

:deep(.prose h1) {
  color: hsl(var(--foreground));
  font-size: 1.5rem;
  font-weight: 700;
  margin-top: 1.5rem;
  margin-bottom: 1rem;
}

:deep(.prose h2) {
  color: hsl(var(--foreground));
  font-size: 1.25rem;
  font-weight: 600;
  margin-top: 1.25rem;
  margin-bottom: 0.75rem;
}

:deep(.prose p) {
  margin-top: 0.5rem;
  margin-bottom: 0.5rem;
}

:deep(.prose ul) {
  margin-top: 0.5rem;
  margin-bottom: 0.5rem;
  padding-left: 1.5rem;
  list-style-type: disc;
}

:deep(.prose ol) {
  margin-top: 0.5rem;
  margin-bottom: 0.5rem;
  padding-left: 1.5rem;
  list-style-type: decimal;
}

:deep(.prose ul ul) {
  list-style-type: circle;
}

:deep(.prose ul ul ul) {
  list-style-type: square;
}

:deep(.prose li) {
  margin-top: 0.25rem;
  margin-bottom: 0.25rem;
  display: list-item;
}

:deep(.prose blockquote) {
  border-left: 4px solid hsl(var(--border));
  padding-left: 1rem;
  font-style: italic;
  color: hsl(var(--muted-foreground));
  margin-top: 1rem;
  margin-bottom: 1rem;
}

:deep(.prose strong) {
  font-weight: 600;
  color: hsl(var(--foreground));
}

:deep(.prose em) {
  font-style: italic;
}

/* TipTap-specific list styling */
:deep(.tiptap-bullet-list) {
  list-style-type: disc !important;
  display: block !important;
}

:deep(.tiptap-ordered-list) {
  list-style-type: decimal !important;
  display: block !important;
}

:deep(.tiptap-list-item) {
  display: list-item !important;
  margin-left: 0 !important;
}

/* Ensure lists have proper spacing and visibility */
:deep(.ProseMirror ul[data-type="bulletList"]) {
  list-style-type: disc;
  padding-left: 1.5rem;
  margin: 0.5rem 0;
}

:deep(.ProseMirror ol[data-type="orderedList"]) {
  list-style-type: decimal;
  padding-left: 1.5rem;
  margin: 0.5rem 0;
}

:deep(.ProseMirror li[data-type="listItem"]) {
  display: list-item;
  margin: 0.25rem 0;
}
</style>
