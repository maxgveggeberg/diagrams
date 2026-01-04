# Design System (v3)

> **Audience:** Frontend developers
> **Purpose:** Component specifications and design tokens
> **Last updated:** January 2, 2026

Extracted from Figma:
- Current Tetra Design System: `W60u5Lbi4g2o6YTHzAGRkS`

---

## Foundations

### Colors

#### Primitives (from Current Tetra Design System)

| Token Name | Value |
|------------|-------|
| `black-10` | `#15191E` |
| `black-40` | `#566376` |
| `white-100` | `#FFFFFF` |
| `white-90` | `#E1E5EA` |
| `white-70` | `#A6B0BF` |
| `green-30` | `#618A0F` |
| `green-60` | `#B4EB47` |
| `green-80` | `#DAF5A3` |
| `green-90` | `#ECFAD1` |
| `blue-50` | `#1A6BE5` |
| `blue-90` | `#D1E1FA` |
| `blue-94` | `#E3EDFC` |
| `red` | `#BB0000` |

#### Semantic Tokens

**Background**
| Token Name | Value | Usage |
|------------|-------|-------|
| `background.white` | `#FFFFFF` | Primary backgrounds |
| `background.grey` | `#F3F5F7` | Secondary backgrounds, input fills |
| `background.border` | `#E1E5EA` | Borders, dividers |

**Text**
| Token Name | Value | Usage |
|------------|-------|-------|
| `text.primary` | `#15191E` | Primary text |
| `text.light` | `#566376` | Secondary text, helper text |
| `text.disabled` | `#A6B0BF` | Disabled text, placeholders |

**Brand**
| Token Name | Value | Usage |
|------------|-------|-------|
| `brand.primary` | `#B4EB47` | Primary CTA, success states |
| `brand.light` | `#DAF5A3` | Light brand accent |
| `brand.extraLight` | `#ECFAD1` | Extra light brand accent |
| `brand.dark` | `#618A0F` | Dark brand accent, text on light green |

**Interactive**
| Token Name | Value | Usage |
|------------|-------|-------|
| `interactive.blue` | `#1A6BE5` | Links, interactive elements |
| `interactive.blueLight` | `#D1E1FA` | Light blue backgrounds |
| `interactive.blueExtraLight` | `#E3EDFC` | Extra light blue backgrounds |

**Status**
| Token Name | Value | Usage |
|------------|-------|-------|
| `status.success` | `#B4EB47` | Success states (same as brand.primary) |
| `status.error` | `#BB0000` | Error states |

```typescript
// tokens/colors.ts
export const colors = {
  primitives: {
    black: {
      10: '#15191E',
      40: '#566376',
    },
    white: {
      70: '#A6B0BF',
      90: '#E1E5EA',
      100: '#FFFFFF',
    },
    green: {
      30: '#618A0F',
      60: '#B4EB47',
      80: '#DAF5A3',
      90: '#ECFAD1',
    },
    blue: {
      50: '#1A6BE5',
      90: '#D1E1FA',
      94: '#E3EDFC',
    },
    red: '#BB0000',
  },
  semantic: {
    background: {
      white: '#FFFFFF',
      grey: '#F3F5F7',
      border: '#E1E5EA',
    },
    text: {
      primary: '#15191E',
      light: '#566376',
      disabled: '#A6B0BF',
    },
    brand: {
      primary: '#B4EB47',
      light: '#DAF5A3',
      extraLight: '#ECFAD1',
      dark: '#618A0F',
    },
    interactive: {
      blue: '#1A6BE5',
      blueLight: '#D1E1FA',
      blueExtraLight: '#E3EDFC',
    },
    status: {
      success: '#B4EB47',
      error: '#BB0000',
    },
  },
} as const;
```

---

### Typography

#### Font Family

| Token | Value |
|-------|-------|
| `fontFamily.primary` | `"Sharp Grotesk", sans-serif` |

#### Font Weights

| Token | Value | CSS |
|-------|-------|-----|
| `fontWeight.regular` | Book 20 | `400` |
| `fontWeight.medium` | Medium 20 | `500` |

#### Type Scale

| Token | Size | Line Height | Weight | Usage |
|-------|------|-------------|--------|-------|
| `typography.title.xl` | 40px | 48px | 500 | Page headlines |
| `typography.label.md` | 16px | 24px | 500 | Labels, emphasis |
| `typography.body.lg` | 16px | 28px | 400 | Body text |
| `typography.body.sm` | 12px | 20px | 400 | Input labels, captions |
| `typography.body.xs` | 10px | 16.5px | 400 | Helper text |
| `typography.button` | 12.5px | 17.5px | 500 | Button labels |

```typescript
// tokens/typography.ts
export const typography = {
  fontFamily: {
    primary: '"Sharp Grotesk", sans-serif',
  },
  fontWeight: {
    regular: 400,
    medium: 500,
  },
  title: {
    xl: {
      fontSize: '40px',
      lineHeight: '48px',
      fontWeight: 500,
    },
  },
  label: {
    md: {
      fontSize: '16px',
      lineHeight: '24px',
      fontWeight: 500,
    },
  },
  body: {
    lg: {
      fontSize: '16px',
      lineHeight: '28px',
      fontWeight: 400,
    },
    sm: {
      fontSize: '12px',
      lineHeight: '20px',
      fontWeight: 400,
    },
    xs: {
      fontSize: '10px',
      lineHeight: '16.5px',
      fontWeight: 400,
    },
  },
  button: {
    fontSize: '12.5px',
    lineHeight: '17.5px',
    fontWeight: 500,
  },
} as const;
```

---

### Spacing

| Token | Value | Usage |
|-------|-------|-------|
| `spacing.xs` | 8px | Tight padding |
| `spacing.sm` | 10px | Component gaps |
| `spacing.md` | 16px | Standard padding |
| `spacing.lg` | 24px | Section padding |
| `spacing.xl` | 36px | Large gaps |
| `spacing.2xl` | 50px | Section spacing |

```typescript
// tokens/spacing.ts
export const spacing = {
  xs: '8px',
  sm: '10px',
  md: '16px',
  lg: '24px',
  xl: '36px',
  '2xl': '50px',
} as const;
```

---

### Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| `radius.sm` | 6px | Buttons |
| `radius.lg` | 24px | Inputs, cards |
| `radius.full` | 9999px | Pills, avatars |

```typescript
// tokens/radius.ts
export const radius = {
  sm: '6px',
  lg: '24px',
  full: '9999px',
} as const;
```

---

### Shadows

| Token | Value | Usage |
|-------|-------|-------|
| `shadow.elevated` | See below | Floating inputs, modals |

```typescript
// tokens/shadows.ts
export const shadows = {
  elevated: `
    0px 12px 26px rgba(0, 0, 0, 0.1),
    0px 47px 47px rgba(0, 0, 0, 0.09),
    0px 105px 63px rgba(0, 0, 0, 0.05),
    0px 187px 75px rgba(0, 0, 0, 0.01),
    0px 292px 82px rgba(0, 0, 0, 0)
  `,
} as const;
```

---

### Motion

#### Duration Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `duration.fast` | 150ms | Micro-interactions (hover, focus) |
| `duration.normal` | 300ms | Standard transitions |
| `duration.slow` | 500ms | Complex animations (float, reveal) |

#### Easing Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `easing.default` | `cubic-bezier(0.4, 0, 0.2, 1)` | Standard ease |
| `easing.enter` | `cubic-bezier(0, 0, 0.2, 1)` | Elements entering |
| `easing.exit` | `cubic-bezier(0.4, 0, 1, 1)` | Elements leaving |
| `easing.bounce` | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Playful emphasis |

```typescript
// tokens/motion.ts
export const motion = {
  duration: {
    fast: '150ms',
    normal: '300ms',
    slow: '500ms',
  },
  easing: {
    default: 'cubic-bezier(0.4, 0, 0.2, 1)',
    enter: 'cubic-bezier(0, 0, 0.2, 1)',
    exit: 'cubic-bezier(0.4, 0, 1, 1)',
    bounce: 'cubic-bezier(0.34, 1.56, 0.64, 1)',
  },
} as const;
```

---

## Components

### Header

#### Description

The header is persistent across all screens and provides navigation and key actions. It uses a three-column layout with left action, center logo, and right action.

#### Layout

```
+-----------------------------------------------------------------+
|  +----------+          [Logo] Brand          +-------------+    |
|  | [icon] Left |                             | Right >     |    |
|  +----------+                                +-------------+    |
+-----------------------------------------------------------------+
```

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Background | `#FFFFFF` |
| | Border bottom | 1px solid `#F3F5F7` |
| | Padding | 8px vertical, 16px horizontal |
| **Buttons** | Background | `#F3F5F7` |
| | Border radius | 6.26px |
| | Padding | 9.39px |
| | Font | Sharp Grotesk Medium 20, 12.5px |
| | Text color | `#15191E` |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `onLeftClick` | `() => void` | - | Handler for left button |
| `onRightClick` | `() => void` | - | Handler for right button |
| `leftLabel` | `string` | - | Left button label |
| `rightLabel` | `string` | - | Right button label |
| `leftIcon` | `ReactNode` | - | Left button icon |
| `rightIcon` | `ReactNode` | - | Right button icon |

#### Implementation

```typescript
// Components/Header/Header.tsx
interface HeaderProps {
  onLeftClick?: () => void;
  onRightClick?: () => void;
  leftLabel?: string;
  rightLabel?: string;
  leftIcon?: ReactNode;
  rightIcon?: ReactNode;
}

export function Header({
  onLeftClick,
  onRightClick,
  leftLabel,
  rightLabel,
  leftIcon,
  rightIcon,
}: HeaderProps) {
  return (
    <header className="
      flex items-center justify-between
      px-[16px] py-[8px]
      bg-white border-b border-[#F3F5F7]
    ">
      {/* Left Button */}
      <div className="flex-1">
        {leftLabel && (
          <button
            onClick={onLeftClick}
            className="
              flex items-center gap-[6px]
              bg-[#F3F5F7] rounded-[6.26px] p-[9.39px]
              text-[12.5px] font-medium text-[#15191E]
            "
          >
            {leftIcon}
            <span>{leftLabel}</span>
          </button>
        )}
      </div>

      {/* Center: Logo */}
      <div className="flex-1 flex justify-center">
        <Logo className="h-[19.5px] w-[70px]" />
      </div>

      {/* Right Button */}
      <div className="flex-1 flex justify-end">
        {rightLabel && (
          <button
            onClick={onRightClick}
            className="
              flex items-center gap-[6px]
              bg-[#F3F5F7] rounded-[6.26px] p-[9.39px]
              text-[12.5px] font-medium text-[#15191E]
            "
          >
            <span>{rightLabel}</span>
            {rightIcon}
          </button>
        )}
      </div>
    </header>
  );
}
```

---

### Input

#### Description

A versatile input component that supports multiple visual states including idle, filled, loading, success, and error. The elevated shadow option makes it feel like a floating card.

#### Visual States

```
+-----------------------------------------------------------------+
|  STATE: IDLE (Empty)                                            |
|  +---------------------------------------------------------+   |
|  | Placeholder text                                    (^)  |   |
|  +---------------------------------------------------------+   |
|  Text: text.disabled (#A6B0BF)                                  |
|  Icon: Arrow up, grey fill, grey circle background              |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: FILLED (Has Value)                                      |
|  +---------------------------------------------------------+   |
|  | User entered value                                  (^)  |   |
|  +---------------------------------------------------------+   |
|  Text: text.primary (#15191E)                                   |
|  Icon: Arrow up, white fill, black circle background            |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: LOADING (Processing)                                    |
|  +---------------------------------------------------------+   |
|  | Loading message...                                  (o)  |   |
|  +---------------------------------------------------------+   |
|  Text: text.disabled (#A6B0BF)                                  |
|  Icon: Spinner, grey stroke                                     |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: SUCCESS (Complete)                                      |
|  +---------------------------------------------------------+   |
|  | Success message                                     [check]  |   |
|  +---------------------------------------------------------+   |
|  Text: text.primary (#15191E), medium weight                    |
|  Icon: Checkmark, white stroke, brand.primary (#B4EB47) circle  |
+-----------------------------------------------------------------+
```

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `label` | `string` | - | Placeholder text when empty |
| `value` | `string` | `''` | Input value |
| `onChange` | `(value: string) => void` | - | Change handler |
| `status` | `'idle' \| 'filled' \| 'loading' \| 'success' \| 'error'` | `'idle'` | Visual state |
| `statusText` | `string` | `undefined` | Overrides label during loading/success |
| `onSubmit` | `() => void` | - | Arrow button click handler |
| `elevated` | `boolean` | `false` | Apply shadow |
| `disabled` | `boolean` | `false` | Disabled state |
| `readOnly` | `boolean` | `false` | Prevents editing (used in loading/success) |

#### Icon States

| Status | Icon Component | Background | Foreground |
|--------|----------------|------------|------------|
| `idle` | `ArrowUp` | `#F3F5F7` (grey) | `#A6B0BF` (grey) |
| `filled` | `ArrowUp` | `#15191E` (black) | `#FFFFFF` (white) |
| `loading` | `Spinner` | transparent | `#A6B0BF` (grey) |
| `success` | `Checkmark` | `#B4EB47` (brand green) | `#FFFFFF` (white) |
| `error` | `AlertCircle` | `#BB0000` (red) | `#FFFFFF` (white) |

---

### Checkmark

#### Variants

| Variant | Background | Icon Color | Usage |
|---------|------------|------------|-------|
| `success` | Brand Green (`#B4EB47`) | White | Completion states |
| `neutral` | Grey (`#F3F5F7`) | Grey (`#A6B0BF`) | Inactive/pending |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'success' \| 'neutral'` | `'success'` | Visual variant |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Size (16/24/32px) |
| `animated` | `boolean` | `true` | Animate checkmark draw on mount |

---

### ChatBubble

#### Description

A simple chat message component with two styles: user messages in grey bubbles (right-aligned) and assistant messages without bubbles (left-aligned).

#### Variants

| Variant | Background | Border Radius | Padding | Text Color |
|---------|------------|---------------|---------|------------|
| `user` | `#F3F5F7` | 12px | 12px horizontal, 8px vertical | `#2B313B` |
| `assistant` | transparent | - | 0 | `#2B313B` |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'user' \| 'assistant'` | `'user'` | Visual style |
| `children` | `ReactNode` | - | Message content |
| `isStreaming` | `boolean` | `false` | Shows typing cursor during streaming |

#### Streaming Behavior

Assistant messages can stream token-by-token with a blinking cursor.

```typescript
// Components/ChatBubble/StreamingText.tsx
interface StreamingTextProps {
  content: string;
  isComplete: boolean;
}

export function StreamingText({ content, isComplete }: StreamingTextProps) {
  return (
    <span>
      {content}
      {!isComplete && <span className="animate-pulse">|</span>}
    </span>
  );
}
```

#### Typing Indicator

```css
@keyframes typing-dot {
  0%, 60%, 100% { opacity: 0.3; }
  30% { opacity: 1; }
}

.typing-indicator span:nth-child(1) { animation-delay: 0ms; }
.typing-indicator span:nth-child(2) { animation-delay: 150ms; }
.typing-indicator span:nth-child(3) { animation-delay: 300ms; }
```

#### Styling

```typescript
// User message bubble
const userBubbleStyles = {
  background: '#F3F5F7',
  borderRadius: '12px',
  padding: '8px 12px',
  maxWidth: '306px', // ~78% of container
  alignSelf: 'flex-end',
};

// Assistant message (no bubble)
const assistantStyles = {
  background: 'transparent',
  padding: '0',
  alignSelf: 'flex-start',
};

// Shared text styles
const messageTextStyles = {
  fontFamily: '"Sharp Grotesk", sans-serif',
  fontWeight: 400,
  fontSize: '12px',
  lineHeight: '20px',
  color: '#2B313B',
};
```

---

### BottomSheet

#### Description

A draggable bottom sheet component that shows summary information in a collapsed "peek" state and expands to reveal more detail.

#### Visual States

```
+-----------------------------------------------------------------+
|  STATE: COLLAPSED (Peek)                                        |
|  +---------------------------------------------------------+   |
|  |                    -----------                           |   |  <- Drag handle
|  |               Title text here                            |   |
|  |             Subtitle or counter                          |   |  <- Italic, grey
|  +---------------------------------------------------------+   |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: EXPANDED                                                |
|  +---------------------------------------------------------+   |
|  |                    -----------                           |   |
|  |               Title text here                            |   |
|  |             Subtitle or counter                          |   |
|  |  -------------------------------------------------------|   |
|  |  Item title                                              |   |  <- Medium weight
|  |  Item description text...                                |   |  <- Book weight, smaller
|  |  -------------------------------------------------------|   |
|  |  Another item title                                      |   |
|  |  Another item description...                             |   |
|  +---------------------------------------------------------+   |
+-----------------------------------------------------------------+
```

#### Styling Specification

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Background | `#FFFFFF` |
| | Border radius | 24px (top-left, top-right only) |
| | Border | 1px solid `#F3F5F7` |
| | Shadow | `0px -6px 12px rgba(0,0,0,0.08)` |
| **Drag Handle** | Size | 44px x 4px |
| | Background | `#DEDEDE` |
| | Border radius | 4px |
| | Margin | 6px top, 8px bottom |
| **Title** | Font | Sharp Grotesk Book 20, 12px |
| | Line height | 20px |
| | Color | `#15191E` |
| **Subtitle** | Font | Sharp Grotesk Book Italic, 10px |
| | Line height | 16.5px |
| | Color | `#566376` |
| **Divider** | Height | 2px |
| | Background | `#DEDEDE` |
| | Border radius | 4px |
| **Item Title** | Font | Sharp Grotesk Medium 20, 12px |
| | Line height | 20px |
| | Color | `#000000` |
| **Item Description** | Font | Sharp Grotesk Book 20, 10px |
| | Line height | 16.5px |
| | Color | `#2B313B` |

#### Interaction

| Platform | Gesture | Behavior |
|----------|---------|----------|
| Mobile | Drag up | Expands sheet to show item list |
| Mobile | Drag down | Collapses sheet to peek state |
| Desktop | Click drag handle | Toggle expand/collapse |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `isExpanded` | `boolean` | `false` | Whether sheet is expanded |
| `onExpand` | `() => void` | - | Called when dragged up past threshold |
| `onCollapse` | `() => void` | - | Called when dragged down past threshold |
| `title` | `string` | - | Primary title text |
| `subtitle` | `string` | - | Subtitle or counter text |
| `items` | `Array<{ title: string; description: string }>` | `[]` | List of items (shown when expanded) |

#### Implementation

```typescript
// Components/BottomSheet/BottomSheet.tsx
interface BottomSheetItem {
  title: string;
  description: string;
}

interface BottomSheetProps {
  isExpanded: boolean;
  onToggle: () => void;
  title: string;
  subtitle?: string;
  items: BottomSheetItem[];
}

export function BottomSheet({
  isExpanded,
  onToggle,
  title,
  subtitle,
  items,
}: BottomSheetProps) {
  return (
    <div className={`
      bg-white
      rounded-t-[24px]
      border border-[#F3F5F7]
      shadow-[0px_-6px_12px_rgba(0,0,0,0.08)]
    `}>
      {/* Drag Handle */}
      <button
        onClick={onToggle}
        className="w-full flex flex-col items-center pt-[6px] pb-[8px]"
      >
        <div className="w-[44px] h-[4px] bg-[#DEDEDE] rounded-[4px]" />
      </button>

      {/* Header */}
      <div className="text-center px-[8px]">
        <p className="text-[12px] leading-[20px] text-[#15191E]">
          {title}
        </p>
        {subtitle && (
          <p className="text-[10px] leading-[16.5px] text-[#566376] italic">
            {subtitle}
          </p>
        )}
      </div>

      {/* Expandable Item List */}
      {isExpanded && (
        <div className="px-[12px] pt-[10px] pb-[36px]">
          {items.map((item, index) => (
            <div key={index}>
              {index > 0 && (
                <div className="h-[2px] bg-[#DEDEDE] rounded-[4px] my-[6px]" />
              )}
              <div className="py-[4px]">
                <p className="text-[12px] leading-[20px] font-medium text-black">
                  {item.title}
                </p>
                <p className="text-[10px] leading-[16.5px] text-[#2B313B]">
                  {item.description}
                </p>
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}
```

---

### TimeSlotCard

#### Description

A selectable card component for displaying time slots with pricing information.

#### Visual States

| State | Background | Border | Shadow | Time Font Weight |
|-------|------------|--------|--------|------------------|
| `default` | `#FFFFFF` | 1.2px solid `#E1E5EA` | `0px 4px 4px rgba(0,0,0,0.25)` | Book 20 (400) |
| `selected` | `#ECFAD1` | 2px solid `#B4EB47` | `0px 4px 4px rgba(0,0,0,0.25)` | Medium 20 (500) |

#### Styling Details

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Min width | 200px |
| | Padding | 12px |
| | Border radius | 8px |
| | Content padding | 8px horizontal |
| **Time range** | Font | Sharp Grotesk Book/Medium 20, 14px |
| | Color | `#202E05` |
| | Line height | 20px |
| **Price** | Font | Sharp Grotesk Medium 20, 20px |
| | Color | `#202E05` |
| | Line height | 24px |
| **Note** | Font | Sharp Grotesk Book 20, 10px |
| | Color | `#202E05` |
| | Line height | 16.5px |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `timeRange` | `string` | - | e.g., "8am - 12pm" |
| `price` | `number` | - | Price in dollars |
| `isSelected` | `boolean` | `false` | Green selected state |
| `note` | `string \| null` | `null` | Optional note text |
| `onClick` | `() => void` | - | Selection handler |

#### Implementation

```typescript
// Components/TimeSlotCard/TimeSlotCard.tsx
interface TimeSlotCardProps {
  timeRange: string;
  price: number;
  isSelected?: boolean;
  note?: string | null;
  onClick?: () => void;
}

export function TimeSlotCard({
  timeRange,
  price,
  isSelected = false,
  note,
  onClick,
}: TimeSlotCardProps) {
  return (
    <button
      onClick={onClick}
      className={`
        w-full min-w-[200px] p-[12px] rounded-[8px]
        shadow-[0px_4px_4px_rgba(0,0,0,0.25)]
        ${isSelected
          ? 'bg-[#ECFAD1] border-2 border-[#B4EB47]'
          : 'bg-white border-[1.2px] border-[#E1E5EA]'
        }
      `}
    >
      <div className="flex items-center justify-between px-[8px]">
        <div className="flex flex-col items-start gap-[4px]">
          <span className={`
            text-[14px] leading-[20px] text-[#202E05]
            ${isSelected ? 'font-medium' : 'font-normal'}
          `}>
            {timeRange}
          </span>
          {note && (
            <span className="text-[10px] leading-[16.5px] text-[#202E05]">
              {note}
            </span>
          )}
        </div>
        <span className="text-[20px] leading-[24px] font-medium text-[#202E05]">
          ${price}
        </span>
      </div>
    </button>
  );
}
```

---

### Button

#### Description

Primary action button used for CTAs throughout the application.

#### Styling

| Property | Value |
|----------|-------|
| Background | `#B4EB47` |
| Border radius | 8px |
| Padding | 8px |
| Font | Sharp Grotesk Medium, 14px |
| Text color | `#202E05` |
| Line height | 20px |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `children` | `ReactNode` | - | Button label |
| `onClick` | `() => void` | - | Click handler |
| `disabled` | `boolean` | `false` | Disabled state |
| `fullWidth` | `boolean` | `false` | Full width button |

---

## Design Token Export

### CSS Custom Properties

```css
/* tokens.css */
:root {
  /* Colors - Primitives */
  --color-black-10: #15191E;
  --color-black-40: #566376;
  --color-white-100: #FFFFFF;
  --color-white-90: #E1E5EA;
  --color-white-70: #A6B0BF;
  --color-green-30: #618A0F;
  --color-green-60: #B4EB47;
  --color-green-80: #DAF5A3;
  --color-green-90: #ECFAD1;
  --color-blue-50: #1A6BE5;
  --color-blue-90: #D1E1FA;
  --color-blue-94: #E3EDFC;
  --color-red: #BB0000;

  /* Colors - Semantic: Background */
  --color-background-white: var(--color-white-100);
  --color-background-grey: #F3F5F7;
  --color-background-border: var(--color-white-90);

  /* Colors - Semantic: Text */
  --color-text-primary: var(--color-black-10);
  --color-text-light: var(--color-black-40);
  --color-text-disabled: var(--color-white-70);

  /* Colors - Semantic: Brand */
  --color-brand-primary: var(--color-green-60);
  --color-brand-light: var(--color-green-80);
  --color-brand-extra-light: var(--color-green-90);
  --color-brand-dark: var(--color-green-30);

  /* Colors - Semantic: Interactive */
  --color-interactive-blue: var(--color-blue-50);
  --color-interactive-blue-light: var(--color-blue-90);
  --color-interactive-blue-extra-light: var(--color-blue-94);

  /* Colors - Semantic: Status */
  --color-status-success: var(--color-green-60);
  --color-status-error: var(--color-red);

  /* Typography */
  --font-family-primary: "Sharp Grotesk", sans-serif;
  --font-weight-regular: 400;
  --font-weight-medium: 500;

  --font-size-xs: 10px;
  --font-size-sm: 12px;
  --font-size-md: 16px;
  --font-size-xl: 40px;

  --line-height-xs: 16.5px;
  --line-height-sm: 20px;
  --line-height-md: 28px;
  --line-height-xl: 48px;

  /* Spacing */
  --spacing-xs: 8px;
  --spacing-sm: 10px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  --spacing-xl: 36px;
  --spacing-2xl: 50px;

  /* Border Radius */
  --radius-sm: 6px;
  --radius-lg: 24px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-elevated:
    0px 12px 26px rgba(0, 0, 0, 0.1),
    0px 47px 47px rgba(0, 0, 0, 0.09),
    0px 105px 63px rgba(0, 0, 0, 0.05);

  /* Motion */
  --duration-fast: 150ms;
  --duration-normal: 300ms;
  --duration-slow: 500ms;

  --easing-default: cubic-bezier(0.4, 0, 0.2, 1);
  --easing-enter: cubic-bezier(0, 0, 0.2, 1);
  --easing-exit: cubic-bezier(0.4, 0, 1, 1);
  --easing-bounce: cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

### Tailwind Config

```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        black: {
          10: '#15191E',
          40: '#566376',
        },
        white: {
          70: '#A6B0BF',
          90: '#E1E5EA',
          100: '#FFFFFF',
        },
        green: {
          30: '#618A0F',
          60: '#B4EB47',
          80: '#DAF5A3',
          90: '#ECFAD1',
        },
        blue: {
          50: '#1A6BE5',
          90: '#D1E1FA',
          94: '#E3EDFC',
        },
        red: '#BB0000',

        // Semantic aliases
        brand: {
          DEFAULT: '#B4EB47',
          light: '#DAF5A3',
          'extra-light': '#ECFAD1',
          dark: '#618A0F',
        },
        surface: {
          DEFAULT: '#FFFFFF',
          secondary: '#F3F5F7',
          border: '#E1E5EA',
        },
        content: {
          DEFAULT: '#15191E',
          light: '#566376',
          disabled: '#A6B0BF',
        },
      },
      fontFamily: {
        sharp: ['"Sharp Grotesk"', 'sans-serif'],
      },
      fontSize: {
        'title-xl': ['40px', { lineHeight: '48px', fontWeight: '500' }],
        'body-lg': ['16px', { lineHeight: '28px', fontWeight: '400' }],
        'body-sm': ['12px', { lineHeight: '20px', fontWeight: '400' }],
        'body-xs': ['10px', { lineHeight: '16.5px', fontWeight: '400' }],
      },
      borderRadius: {
        sm: '6px',
        lg: '24px',
      },
      boxShadow: {
        elevated: '0px 12px 26px rgba(0,0,0,0.1), 0px 47px 47px rgba(0,0,0,0.09), 0px 105px 63px rgba(0,0,0,0.05)',
      },
      transitionDuration: {
        fast: '150ms',
        normal: '300ms',
        slow: '500ms',
      },
      transitionTimingFunction: {
        enter: 'cubic-bezier(0, 0, 0.2, 1)',
        exit: 'cubic-bezier(0.4, 0, 1, 1)',
        bounce: 'cubic-bezier(0.34, 1.56, 0.64, 1)',
      },
      animation: {
        'spin-slow': 'spin 1.5s linear infinite',
      },
    },
  },
}
```
