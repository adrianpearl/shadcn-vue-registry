# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a personal shadcn-vue registry containing custom Vue.js components that extend the shadcn-vue ecosystem. The primary component is a rich text editor built with TipTap and shadcn-vue components.

## Repository Structure

```
registry/
├── index.json                    # Registry index with component metadata
├── rich-text-editor.json         # Component definition file for shadcn-vue
├── new-york/
│   └── rich-text-editor/
│       └── RichTextEditor.vue     # Main component implementation
└── README.md                      # Usage documentation
```

## Component Architecture

### Rich Text Editor Component
- **Framework**: Vue 3 with Composition API and TypeScript
- **Editor Engine**: TipTap with custom extensions
- **UI Components**: shadcn-vue (Button, Separator) with Lucide icons
- **Styling**: Tailwind CSS with scoped styles and CSS custom properties
- **Features**: 
  - Bimodal operation (editable/read-only)
  - Inline toolbar with essential formatting tools
  - Custom TipTap extensions for lists with proper styling
  - Prose-style content rendering

### Key Dependencies
- `@tiptap/vue-3` - Core TipTap Vue integration
- `@tiptap/starter-kit` - Basic editor functionality
- `@tiptap/extension-*` - Specific formatting extensions (underline, lists)
- `lucide-vue-next` - Icon library
- shadcn-vue components (button, separator)

## Development Commands

### Registry Management
```bash
# Install component from registry
bunx shadcn-vue@latest add https://raw.githubusercontent.com/adrianpearl/shadcn-vue-registry/main/rich-text-editor.json

# Test registry structure
bunx shadcn-vue@latest add ./rich-text-editor.json  # Local testing
```

### Git Operations
```bash
# Standard workflow for registry updates
git add .
git commit -m "feat: update rich text editor component"
git push origin main
```

## Registry Component Format

Components must follow the shadcn-vue registry schema:
- `name`: Component identifier (kebab-case)
- `type`: Must be "registry:component" 
- `description`: Brief component overview
- `dependencies`: NPM packages required
- `registryDependencies`: Other shadcn-vue components needed
- `files`: Array with base64-encoded component content

## Component Development Guidelines

### TipTap Integration Patterns
- Use explicit extension configuration rather than defaults
- Configure HTML attributes for proper CSS styling
- Implement proper cleanup in `onBeforeUnmount`
- Watch reactive props to sync with editor instance

### Vue Composition API Patterns
- Use `withDefaults` for prop defaults
- Implement proper TypeScript interfaces for props/emits
- Leverage reactive watchers for prop synchronization
- Follow Vue 3 lifecycle patterns

### Styling Architecture
- Use CSS custom properties for theme integration
- Implement scoped styles with `:deep()` for editor content
- Maintain Tailwind class consistency
- Provide proper list styling workarounds for TipTap

### Component API Design
- Support v-model for content binding
- Provide editable/read-only modes
- Include accessibility attributes (aria-label)
- Use semantic HTML structure

## File Encoding Notes

Component files in the registry are base64-encoded in the JSON metadata files. When updating components:
1. Modify the actual `.vue` file
2. Base64 encode the updated content
3. Replace the `content` field in the corresponding JSON file
4. Update version/description if needed

## Testing Registry Components

To test components locally before publishing:
1. Use local file paths in shadcn-vue commands
2. Verify proper dependency resolution
3. Test in both editable and read-only modes
4. Ensure proper TypeScript integration

## Common Issues

### Registry Installation Failures
- Ensure `rich-text-editor.json` exists and has proper schema
- Verify base64 content encoding is valid
- Check that all dependencies are properly listed
- Confirm GitHub raw URLs are accessible

### TipTap Styling Issues  
- Lists may require custom CSS for proper display
- Use TipTap-specific HTML attributes for styling hooks
- Ensure prose styles don't conflict with component styles
- Test editor content rendering in both modes