# Types.json Documentation

## Overview

Types.json is the core configuration file for Junction blueprints, which integrates seamlessly with the Tribe ecosystem. It defines the database structure for your headless CMS, specifying the data types (called "tracks" in Junction UI) and their fields (called "modules" in the configuration). This file serves as a blueprint that Junction uses to automatically generate database tables, admin interfaces, and API endpoints.

## Purpose

The types.json file enables Junction to:

- Generate database tables and relationships automatically
- Create admin interfaces for content management
- Establish data interconnections through linked types
- Provide structured data that follows JSON-LD principles
- Build scalable, interconnected datasets

## File Structure

Types.json is a flat JSON object where each key represents a content type, and the value contains the type's configuration and modules (fields).

```json
{
  "type_name": {
    "type": "content",
    "slug": "type_name",
    "name": "type_name",
    "plural": "type_names",
    "description": "Description of this content type",
    "readonly": false,
    "sendable": false,
    "editable": true,
    "modules": [...]
  }
}
```

## Type Properties

### Core Properties

- `type`: Always set to "content" for standard content types
- `slug`: URL-friendly identifier (matches the key name, cannot be "type")
- `name`: Singular form of the type name (can use spaces, cannot be "type")
- `plural`: Plural form for UI display (can use spaces)
- `description`: Human-readable description of the type's purpose
- `readonly`: Boolean - if true, prevents editing existing records
- `sendable`: Boolean - always set to false per Junction requirements
- `editable`: Boolean - if true, allows creating and editing records

### Modules (Fields) Array

Each type contains a `modules` array that defines the individual fields for that content type.

## Module (Field) Structure

Each module in the modules array defines a single field with the following properties:

### Required Properties

- `input_slug`: Field identifier (cannot be "type", "slug", or "id")
- `input_type`: The type of input field
- `var_type`: Backend variable type ("string", "int", "float", "array", "bool")

### Common Properties

- `input_placeholder`: Placeholder text for the input field
- `input_primary`: Boolean - marks the primary field (usually title)
- `input_unique`: Boolean - ensures unique values across all records
- `input_multiple`: Boolean - allows multiple selections (only works with text, url, or select fields)
- `list_field`: Boolean - shows field in list views
- `list_searchable`: Boolean - enables search on this field
- `list_sortable`: Boolean - allows sorting by this field

### Relationship Properties

- `linked_type`: References another type for relationships
- `input_options`: Array of predefined options for select fields

## Input Types

### Valid Input Types

**HTML5 Input Types:**

- `text`: Single-line text input
- `url`: URL input with validation
- `email`: Email input with validation
- `tel`: Telephone number input
- `password`: Password input
- `number`: Numeric input
- `date`: Date picker
- `time`: Time picker
- `datetime-local`: Date and time picker
- `datetime`: Date and time input
- `month`: Month picker
- `week`: Week picker
- `color`: Color picker

**Junction-Specific Types:**

- `editorjs`: Rich text editor for structured content
- `file_uploader`: File upload interface
- `select`: Dropdown selection (requires `linked_type` or `input_options`)
- `textarea`: Multi-line text input (Note: treated as special case, not standard HTML5)

## Variable Types (var_type)

Junction uses these five variable types for backend processing:

- `string`: Text data, URLs, emails
- `int`: Whole numbers, IDs, counts
- `float`: Decimal numbers, prices, ratings
- `array`: Lists, multiple selections, JSON data
- `bool`: True/false values, toggles

## Interconnections and Relationships

### Linked Types

Use `linked_type` to create relationships between content types:

```json
{
  "input_slug": "categories",
  "linked_type": "category",
  "input_type": "select",
  "var_type": "array",
  "input_multiple": true
}
```

### Self-Referencing Relationships

Types can reference themselves for hierarchical structures:

```json
{
  "input_slug": "parent_category",
  "linked_type": "category",
  "input_type": "select",
  "var_type": "string",
  "input_multiple": false
}
```

## SEO and Social Media Fields

For public-facing content, include these standard modules:

### SEO Fields

```json
{
  "input_slug": "meta_title",
  "input_type": "text",
  "var_type": "string",
  "input_placeholder": "SEO title (recommended: 50-60 characters)"
},
{
  "input_slug": "meta_description",
  "input_type": "textarea",
  "var_type": "string",
  "input_placeholder": "Use this field for ARIA label, SEO description and social media description (recommended: 150-160 characters)"
},
{
  "input_slug": "meta_keywords",
  "input_type": "text",
  "var_type": "string",
  "input_placeholder": "Comma-separated keywords for SEO"
}
```

### Open Graph Protocol

```json
{
  "input_slug": "og_title",
  "input_type": "text",
  "var_type": "string",
  "input_placeholder": "Title for social media sharing (Facebook, LinkedIn)"
},
{
  "input_slug": "og_image_url",
  "input_type": "url",
  "var_type": "string",
  "input_placeholder": "Image URL for social media sharing (recommended: 1200x630px)"
}
```

### Twitter Cards

```json
{
  "input_slug": "twitter_title",
  "input_type": "text",
  "var_type": "string",
  "input_placeholder": "Title for Twitter large summary card"
},
{
  "input_slug": "twitter_description",
  "input_type": "textarea",
  "var_type": "string",
  "input_placeholder": "Description for Twitter card (recommended: under 200 characters)"
}
```

## Blueprint Rules and Best Practices

### Critical Junction Rules

1. **File Uploader Placement**: Place `file_uploader` modules at the end of the modules array
2. **File Uploader Requirements**: Only include when the type has at least one URL field AND requires photo/document uploads
3. **Sendable Property**: Always set `sendable: false` for all types
4. **Reserved Names**: `input_slug` cannot be "type", "slug", or "id" (auto-generated fields)
5. **Type Naming**: No type can have name/slug value "type"
6. **Multiple Input Restriction**: `input_multiple: true` only works with text, url, number or select fields

### Uniqueness Guidelines

- Set `input_unique: true` only when values should never repeat across all records
- Categories, tags, and identifiers can be unique
- Names, titles, and descriptions should typically be non-unique
- Consider real-world usage patterns (student names can repeat, category names typically cannot)

### Content Requirements

- Every blueprint must include at least one type with an `editorjs` module
- Use `select` fields with either `linked_type` or `input_options` (never both)
- Include comprehensive SEO fields for public content
- Design for accessibility with proper ARIA considerations

### Naming Conventions

- `name` and `plural` fields can use spaces instead of underscores
- Choose field names appropriate for target region (US vs UK English)
- Consider local content standards and requirements
- Adapt placeholder text for regional context

## Example Use Case

```
Study this example blueprint for a news media organization to understand the expected JSON structure:

{
  "category": {
    "type": "content",
    "slug": "category",
    "name": "category",
    "plural": "categories",
    "description": "List of categories.",
    "readonly": false,
    "sendable": false,
    "editable": true,
    "modules": [
      {
        "input_slug": "title",
        "input_primary": true,
        "input_type": "text",
        "var_type": "string",
        "input_placeholder": "Enter category title",
        "input_unique": false,
        "list_field": true,
        "list_searchable": true,
        "list_sortable": true
      },
      {
        "input_slug": "description",
        "input_placeholder": "Enter the category description",
        "input_type": "textarea",
        "var_type": "string",
        "list_field": true,
        "list_searchable": true
      }
    ]
  },
  "post": {
    "type": "content",
    "slug": "post",
    "name": "post",
    "plural": "posts",
    "description": "List of posts.",
    "readonly": false,
    "sendable": false,
    "editable": true,
    "modules": [
      {
        "input_slug": "title",
        "input_primary": true,
        "input_type": "text",
        "var_type": "string",
        "input_placeholder": "Enter post title",
        "input_unique": false,
        "list_field": true,
        "list_searchable": true,
        "list_sortable": true
      },
      {
        "input_slug": "strap",
        "input_placeholder": "Strap",
        "input_type": "textarea",
        "var_type": "string",
        "list_field": true,
        "list_searchable": true
      },
      {
        "input_slug": "authors",
        "linked_type": "author",
        "input_placeholder": "Authors",
        "input_type": "select",
        "var_type": "array",
        "input_multiple": true,
        "list_field": true,
        "list_searchable": true,
        "list_sortable": true
      },
      {
        "input_slug": "cover_url",
        "input_placeholder": "Cover image URL",
        "input_type": "url",
        "var_type": "string"
      },
      {
        "input_slug": "cover_caption",
        "input_placeholder": "Cover image caption",
        "input_type": "text",
        "var_type": "string"
      },
      {
        "input_slug": "body",
        "input_placeholder": "Type content here",
        "input_type": "editorjs",
        "var_type": "array"
      },
      {
        "input_slug": "format",
        "input_placeholder": "Select format",
        "input_options": [
          {
            "slug": "article",
            "title": "Article"
          },
          {
            "slug": "report",
            "title": "Report"
          },
          {
            "slug": "book_excerpt",
            "title": "Book Excerpt"
          },
          {
            "slug": "podcast",
            "title": "Podcast"
          },
          {
            "slug": "video",
            "title": "Video"
          }
        ],
        "input_type": "select",
        "var_type": "string",
        "input_multiple": false,
        "list_searchable": true,
        "list_field": true
      },
      {
        "input_slug": "categories",
        "linked_type": "category",
        "input_placeholder": "Select categories",
        "input_type": "select",
        "var_type": "array",
        "input_multiple": true
      },
      {
        "input_slug": "publishing_date",
        "input_placeholder": "Publishing date",
        "input_type": "date",
        "var_type": "string",
        "list_field": true,
        "list_searchable": true,
        "list_sortable": true
      },
      {
        "input_slug": "files",
        "input_type": "file_uploader",
        "var_type": "array",
        "input_placeholder": "File uploader"
      }
    ]
  },
  "page": {
    "type": "content",
    "slug": "page",
    "name": "page",
    "plural": "pages",
    "description": "List of pages.",
    "readonly": false,
    "sendable": false,
    "editable": true,
    "modules": [
      {
        "input_slug": "title",
        "input_primary": true,
        "input_type": "text",
        "var_type": "string",
        "input_placeholder": "Enter page title",
        "input_unique": false,
        "list_field": true,
        "list_searchable": true,
        "list_sortable": true
      },
      {
        "input_slug": "body",
        "input_placeholder": "Type content here",
        "input_type": "editorjs",
        "var_type": "array"
      },
      {
        "input_slug": "cover_url",
        "input_placeholder": "Cover image URL",
        "input_type": "url",
        "var_type": "string"
      },
      {
        "input_slug": "files",
        "input_type": "file_uploader",
        "var_type": "array",
        "input_placeholder": "File uploader"
      }
    ]
  },
  "author": {
    "type": "content",
    "slug": "author",
    "name": "author",
    "plural": "authors",
    "description": "List of authors.",
    "readonly": false,
    "sendable": false,
    "editable": true,
    "modules": [
      {
        "input_slug": "title",
        "input_primary": true,
        "input_type": "text",
        "var_type": "string",
        "input_placeholder": "Enter author name",
        "input_unique": false,
        "list_field": true,
        "list_searchable": true,
        "list_sortable": true
      },
      {
        "input_slug": "description",
        "input_placeholder": "About the author",
        "input_type": "textarea",
        "var_type": "string",
        "list_searchable": true
      },
      {
        "input_slug": "social_media_links",
        "input_placeholder": "Links to social media profiles",
        "input_type": "url",
        "var_type": "array",
        "input_multiple": true
      },
      {
        "input_slug": "cover_url",
        "input_placeholder": "Profile photo URL",
        "input_type": "url",
        "var_type": "string",
        "list_field": true
      },
      {
        "input_slug": "files",
        "input_type": "file_uploader",
        "var_type": "array",
        "input_placeholder": "File uploader"
      }
    ]
  }
}
```

## Tips for Manual Creation

1. **Start with Core Entities**: Identify the main content types your system needs
2. **Map Relationships**: Determine how entities connect using `linked_type`
3. **Plan for Growth**: Design interconnections that allow data expansion
4. **Consider User Experience**: Include fields that support rich frontend experiences
5. **SEO**: Add metadata fields for public-facing content, if required
6. **Validate Data Types**: Ensure `var_type` matches the field's intended use
7. **Test Interconnections**: Verify that relationships create meaningful data networks
8. **Follow Junction Rules**: Always validate against the critical rules (sendable=false, file_uploader placement, etc.)
9. **Design for Interconnection**: Embrace the "Library of Babel" philosophy with rich data relationships

## Junction AI Deliverables

When implementing Junction blueprints, AI responses must provide:

### 1. consultation.html

A three-paragraph HTML consultation summary with:

- **First two paragraphs**: Implementation strategy and initial steps for effective blueprint deployment
- **Third paragraph**: Data analysis possibilities and future value of the structured asset
- **Tone**: Polite and suggestive language (use "could" instead of "should")
- **Voice**: Professional "we" perspective representing Junction AI

### 2. types.json

Valid JSON blueprint file following all Junction rules and best practices

### AI Response Format

A single JSON response with "html" and "json" keys for programmatic parsing:

```json
{
  "html": "consultation content",
  "json": "blueprint content"
}
```
