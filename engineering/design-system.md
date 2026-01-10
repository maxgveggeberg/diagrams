# Design System (v3.1)

> **Audience:** Frontend developers
> **Purpose:** Component specifications and design tokens
> **Last updated:** January 9, 2026

Extracted from Figma:
- Current Tetra Design System: `W60u5Lbi4g2o6YTHzAGRkS`
- RepairBot Job Summary Screen: `kOxblDpHcb5SeIdxXpekyA?node-id=225:1070`
- RepairBot Review & Payment Screen: `kOxblDpHcb5SeIdxXpekyA?node-id=285:2256`

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
| `interactive.blueAlt` | `#1456B8` | Darker blue for links (terms, etc.) |

**Status**
| Token Name | Value | Usage |
|------------|-------|-------|
| `status.success` | `#B4EB47` | Success states (same as brand.primary) |
| `status.error` | `#BB0000` | Error states |
| `status.critical` | `#EB5D47` | Critical/urgent status (e.g., "No heat") |
| `status.criticalDark` | `#B82A14` | Critical border/hover state |
| `status.warning` | `#EBB447` | Warning/attention status (e.g., "Return visit") |
| `status.warningDark` | `#B88114` | Warning border/hover state |
| `status.minor` | `#81B814` | Minor severity indicator |

**Grey Extended (from PaymentForm)**
| Token Name | Value | Usage |
|------------|-------|-------|
| `grey.labels` | `#4F5B76` | Form field labels |
| `grey.placeholder` | `#A5ACB8` | Input placeholder text |
| `grey.border` | `#E0E0E0` | Input field borders |

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
      blueAlt: '#1456B8',
    },
    status: {
      success: '#B4EB47',
      error: '#BB0000',
      critical: '#EB5D47',
      criticalDark: '#B82A14',
      warning: '#EBB447',
      warningDark: '#B88114',
      minor: '#81B814',
    },
    grey: {
      labels: '#4F5B76',
      placeholder: '#A5ACB8',
      border: '#E0E0E0',
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

### StatusTag

#### Description

A colored badge indicating job status, urgency level, or special conditions. Uses solid background with darker border for emphasis.

#### Visual Variants

| Variant | Background | Border | Text Color | Usage |
|---------|------------|--------|------------|-------|
| `critical` | `#EB5D47` | 2px solid `#B82A14` | `#FFFFFF` | Urgent issues (No heat, No AC) |
| `warning` | `#EBB447` | 2px solid `#B88114` | `#FFFFFF` | Attention needed (Return visit) |
| `info` | `#1A6BE5` | 2px solid `#0b4884` | `#FFFFFF` | Informational status |
| `neutral` | `#F3F5F7` | 2px solid `#E1E5EA` | `#566376` | Default/inactive status |

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Border radius | 8px |
| | Padding | 4px vertical, 16px horizontal |
| | Border | 2px solid (variant color) |
| **Text** | Font | Sharp Grotesk Medium 20, 12px |
| | Line height | 1.6 (19.2px) |
| | Color | `#FFFFFF` |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'critical' \| 'warning' \| 'info' \| 'neutral'` | `'neutral'` | Visual style |
| `children` | `string` | - | Tag label text |

#### Implementation

```typescript
// Components/StatusTag/StatusTag.tsx
interface StatusTagProps {
  variant?: 'critical' | 'warning' | 'info' | 'neutral';
  children: string;
}

const variantStyles = {
  critical: {
    background: '#EB5D47',
    border: '#B82A14',
    text: '#FFFFFF',
  },
  warning: {
    background: '#EBB447',
    border: '#B88114',
    text: '#FFFFFF',
  },
  info: {
    background: '#1A6BE5',
    border: '#0b4884',
    text: '#FFFFFF',
  },
  neutral: {
    background: '#F3F5F7',
    border: '#E1E5EA',
    text: '#566376',
  },
};

export function StatusTag({ variant = 'neutral', children }: StatusTagProps) {
  const styles = variantStyles[variant];

  return (
    <span
      className="
        inline-flex items-center
        px-[16px] py-[4px]
        rounded-[8px] border-2
        text-[12px] font-medium leading-[1.6]
      "
      style={{
        backgroundColor: styles.background,
        borderColor: styles.border,
        color: styles.text,
      }}
    >
      {children}
    </span>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=269:1627',
  },
},
```

---

### TabNav

#### Description

Horizontal tab navigation with active state indicator. Used for switching between content sections within a page.

#### Visual States

```
+------------------------------------------------------------------+
|                                                                  |
|   Summary        Action Plan        History      [ Start visit ] |
|   --------                                                       |
|   (active)         (inactive)      (inactive)     (CTA button)   |
+------------------------------------------------------------------+
```

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Border bottom | 1px solid `#E1E5EA` |
| | Padding | 0 24px |
| **Tab (Active)** | Font | Sharp Grotesk Medium 21, 14px |
| | Color | `#81B814` |
| | Border bottom | 4px solid `#81B814` |
| | Border radius (top) | 8px |
| | Padding | 16px 0 |
| **Tab (Inactive)** | Font | Sharp Grotesk Book 20, 14px |
| | Color | `#566376` |
| | Padding | 16px 0 |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `tabs` | `Array<{ id: string; label: string }>` | - | Tab configuration |
| `activeTab` | `string` | - | Currently active tab id |
| `onTabChange` | `(tabId: string) => void` | - | Tab change handler |
| `action` | `{ label: string; onClick: () => void }` | - | Optional CTA button |

#### Implementation

```typescript
// Components/TabNav/TabNav.tsx
interface Tab {
  id: string;
  label: string;
}

interface TabNavProps {
  tabs: Tab[];
  activeTab: string;
  onTabChange: (tabId: string) => void;
  action?: {
    label: string;
    onClick: () => void;
  };
}

export function TabNav({ tabs, activeTab, onTabChange, action }: TabNavProps) {
  return (
    <nav className="border-b border-[#E1E5EA] flex items-center justify-between">
      <div className="flex items-end">
        {tabs.map((tab) => {
          const isActive = tab.id === activeTab;
          return (
            <button
              key={tab.id}
              onClick={() => onTabChange(tab.id)}
              className={`
                px-[24px] py-[16px] text-[14px] text-center
                ${isActive
                  ? 'font-medium text-[#81B814] border-b-4 border-[#81B814] rounded-t-[8px]'
                  : 'font-normal text-[#566376]'
                }
              `}
            >
              {tab.label}
            </button>
          );
        })}
      </div>

      {action && (
        <button
          onClick={action.onClick}
          className="
            bg-[#B4EB47] rounded-[8px] px-[12px] py-[8px]
            text-[14px] font-medium text-[#202E05]
          "
        >
          {action.label}
        </button>
      )}
    </nav>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=269:1655',
  },
},
```

---

### SeverityBadge

#### Description

Text-only badge indicating issue severity level. No background - just colored text for inline use.

#### Variants

| Variant | Color | Usage |
|---------|-------|-------|
| `minor` | `#81B814` | Low severity issues |
| `medium` | `#EBB447` | Moderate severity issues |
| `major` | `#B82A14` | High severity issues |

#### Styling

| Property | Value |
|----------|-------|
| Font | Sharp Grotesk Medium 20, 14px |
| Line height | 1.4 |
| Background | transparent |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `severity` | `'minor' \| 'medium' \| 'major'` | - | Severity level |

#### Implementation

```typescript
// Components/SeverityBadge/SeverityBadge.tsx
interface SeverityBadgeProps {
  severity: 'minor' | 'medium' | 'major';
}

const severityColors = {
  minor: '#81B814',
  medium: '#EBB447',
  major: '#B82A14',
};

const severityLabels = {
  minor: 'Minor',
  medium: 'Medium',
  major: 'Major',
};

export function SeverityBadge({ severity }: SeverityBadgeProps) {
  return (
    <span
      className="text-[14px] font-medium leading-[1.4]"
      style={{ color: severityColors[severity] }}
    >
      {severityLabels[severity]}
    </span>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=2346:2654',
  },
},
```

---

### InfoRow

#### Description

A label-value pair row used for displaying structured information. Commonly used in detail cards and summaries.

#### Layout

```
+------------------------------------------------------------------+
| Label text (grey)          | Value text (black)                  |
+------------------------------------------------------------------+
                             ^ border-bottom separator
```

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Padding | 4px vertical, 12px horizontal |
| | Border bottom | 1px solid `#E1E5EA` |
| | Background | `#FFFFFF` |
| **Label** | Font | Sharp Grotesk Book 20, 12px |
| | Color | `#566376` |
| | Width | 119px (fixed) |
| | Line height | 1.6 |
| **Value** | Font | Sharp Grotesk Book 20, 12px |
| | Color | `#000000` |
| | Line height | 1.6 |
| | Flex | 1 (fills remaining space) |

#### Variants

| Variant | Value Font | Usage |
|---------|------------|-------|
| `default` | Book 20 (400) | Standard information |
| `emphasis` | Medium 20 (500) | Important/highlighted values |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `label` | `string` | - | Row label (left side) |
| `value` | `string` | - | Row value (right side) |
| `emphasis` | `boolean` | `false` | Bold value text |
| `showBorder` | `boolean` | `true` | Show bottom border |

#### Implementation

```typescript
// Components/InfoRow/InfoRow.tsx
interface InfoRowProps {
  label: string;
  value: string;
  emphasis?: boolean;
  showBorder?: boolean;
}

export function InfoRow({
  label,
  value,
  emphasis = false,
  showBorder = true,
}: InfoRowProps) {
  return (
    <div
      className={`
        flex items-center gap-[24px]
        px-[12px] py-[4px] pb-[2px]
        bg-white
        ${showBorder ? 'border-b border-[#E1E5EA]' : ''}
      `}
    >
      <span className="w-[119px] text-[12px] leading-[1.6] text-[#566376] shrink-0">
        {label}
      </span>
      <span
        className={`
          flex-1 text-[12px] leading-[1.6] text-black
          ${emphasis ? 'font-medium' : 'font-normal'}
        `}
      >
        {value}
      </span>
    </div>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=2346:2619',
  },
},
```

---

### Checkbox

#### Description

A simple checkbox component for selection states in checklists.

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Size | 14px x 14px |
| | Border | 1.556px solid `#15191E` |
| | Border radius | 2px |
| | Background (unchecked) | transparent |
| | Background (checked) | `#15191E` |
| **Checkmark** | Color | `#FFFFFF` |
| | Stroke width | 2px |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `checked` | `boolean` | `false` | Checked state |
| `onChange` | `(checked: boolean) => void` | - | Change handler |
| `disabled` | `boolean` | `false` | Disabled state |
| `id` | `string` | - | Input ID for label association |

#### Implementation

```typescript
// Components/Checkbox/Checkbox.tsx
import { Check } from 'lucide-react';

interface CheckboxProps {
  checked?: boolean;
  onChange?: (checked: boolean) => void;
  disabled?: boolean;
  id?: string;
}

export function Checkbox({
  checked = false,
  onChange,
  disabled = false,
  id,
}: CheckboxProps) {
  return (
    <button
      id={id}
      role="checkbox"
      aria-checked={checked}
      disabled={disabled}
      onClick={() => onChange?.(!checked)}
      className={`
        w-[14px] h-[14px] rounded-[2px]
        border-[1.556px] border-[#15191E]
        flex items-center justify-center
        transition-colors duration-150
        ${checked ? 'bg-[#15191E]' : 'bg-transparent'}
        ${disabled ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer'}
      `}
    >
      {checked && (
        <Check className="w-[10px] h-[10px] text-white" strokeWidth={3} />
      )}
    </button>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=2346:2521',
  },
},
```

---

### SectionHeader

#### Description

A simple section header used to title content sections within a page.

#### Styling

| Property | Value |
|----------|-------|
| Font | Sharp Grotesk Medium 20, 16px |
| Font weight | 500 (bold) |
| Color | `#000000` |
| Line height | 1.6 (25.6px) |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `children` | `string` | - | Header text |

#### Implementation

```typescript
// Components/SectionHeader/SectionHeader.tsx
interface SectionHeaderProps {
  children: string;
}

export function SectionHeader({ children }: SectionHeaderProps) {
  return (
    <h2 className="
      text-[16px] font-medium leading-[1.6] text-black
    ">
      {children}
    </h2>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=2346:2432',
  },
},
```

---

### StepCounter

#### Description

A pill-shaped indicator showing the current step position within a sequence (e.g., "1/6", "2/6"). Used in action step cards to indicate progress through a multi-step diagnostic or repair process.

#### Layout

```
+-------+
|  1/6  |
+-------+
```

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Height | 19px |
| | Padding | 10px |
| | Background | `#E1E5EA` (background.border) |
| | Border radius | 100px (pill shape) |
| | Display | flex, centered |
| **Text** | Font | Sharp Grotesk Book 20, 12px |
| | Color | `#566376` (text.light) |
| | Line height | 1.6 |
| | White space | nowrap |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `current` | `number` | - | Current step number |
| `total` | `number` | - | Total number of steps |

#### Implementation

```typescript
// Components/StepCounter/StepCounter.tsx
interface StepCounterProps {
  current: number;
  total: number;
}

export function StepCounter({ current, total }: StepCounterProps) {
  return (
    <div className="
      h-[19px] px-[10px]
      bg-[#E1E5EA] rounded-full
      flex items-center justify-center
    ">
      <span className="
        text-[12px] leading-[1.6] text-[#566376]
        whitespace-nowrap
      ">
        {current}/{total}
      </span>
    </div>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=275:1839',
  },
},
```

---

### StickyFooter

#### Description

A sticky footer bar with primary action button. Fixed to bottom of viewport for always-accessible CTAs.

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Background | `#FFFFFF` |
| | Border top | 1px solid `rgba(0,0,0,0.1)` |
| | Padding | 24px vertical, 128px horizontal |
| | Position | sticky, bottom: 0 |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `buttonLabel` | `string` | - | Primary button text |
| `onButtonClick` | `() => void` | - | Button click handler |
| `disabled` | `boolean` | `false` | Disable button |

#### Implementation

```typescript
// Components/StickyFooter/StickyFooter.tsx
interface StickyFooterProps {
  buttonLabel: string;
  onButtonClick: () => void;
  disabled?: boolean;
}

export function StickyFooter({
  buttonLabel,
  onButtonClick,
  disabled = false,
}: StickyFooterProps) {
  return (
    <div className="
      sticky bottom-0
      bg-white border-t border-[rgba(0,0,0,0.1)]
      px-[128px] py-[24px]
    ">
      <button
        onClick={onButtonClick}
        disabled={disabled}
        className="
          w-full bg-[#B4EB47] rounded-[8px]
          px-[12px] py-[8px]
          text-[14px] font-medium leading-[20px] text-[#202E05]
          disabled:opacity-50 disabled:cursor-not-allowed
        "
      >
        {buttonLabel}
      </button>
    </div>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=275:1857',
  },
},
```

---

### ChatInput

#### Description

A floating input field for composing chat messages. Features a pill-shaped design with placeholder text and a circular send button. Uses the elevated shadow style to appear floating above content.

#### Layout

```
+------------------------------------------------------------------+
| Describe your issue                                         (up)  |
+------------------------------------------------------------------+
```

#### Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Background | `#F3F4F6` |
| | Border | 1px solid `#F3F5F7` |
| | Border radius | 24px |
| | Padding | 12px vertical, 24px left, 16px right |
| | Shadow | Elevated shadow (see tokens) |
| **Placeholder** | Font | Sharp Grotesk Book 20, 12px |
| | Color | `#A6B0BF` (text.disabled) |
| | Line height | 20px |
| **Send Button** | Size | 36px x 36px |
| | Background | `#15191E` (when has value) / `#E1E5EA` (empty) |
| | Border radius | 50% (circle) |
| **Arrow Icon** | Color | `#FFFFFF` (when has value) / `#A6B0BF` (empty) |
| | Size | 16px |

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | `''` | Input value |
| `onChange` | `(value: string) => void` | - | Change handler |
| `onSubmit` | `() => void` | - | Submit handler |
| `placeholder` | `string` | `'Describe your issue'` | Placeholder text |
| `disabled` | `boolean` | `false` | Disabled state |

#### Implementation

```typescript
// Components/ChatInput/ChatInput.tsx
import { ArrowUp } from 'lucide-react';

interface ChatInputProps {
  value?: string;
  onChange?: (value: string) => void;
  onSubmit?: () => void;
  placeholder?: string;
  disabled?: boolean;
}

export function ChatInput({
  value = '',
  onChange,
  onSubmit,
  placeholder = 'Describe your issue',
  disabled = false,
}: ChatInputProps) {
  const hasValue = value.trim().length > 0;

  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'Enter' && !e.shiftKey && hasValue) {
      e.preventDefault();
      onSubmit?.();
    }
  };

  return (
    <div className="
      bg-[#F3F4F6] border border-[#F3F5F7] rounded-[24px]
      pl-[24px] pr-[16px] py-[12px]
      shadow-[0px_12px_26px_rgba(0,0,0,0.1),0px_47px_47px_rgba(0,0,0,0.09),0px_105px_63px_rgba(0,0,0,0.05),0px_187px_75px_rgba(0,0,0,0.01),0px_292px_82px_rgba(0,0,0,0)]
      flex items-center justify-between
    ">
      <input
        type="text"
        value={value}
        onChange={(e) => onChange?.(e.target.value)}
        onKeyDown={handleKeyDown}
        placeholder={placeholder}
        disabled={disabled}
        className="
          flex-1 bg-transparent
          text-[12px] leading-[20px] text-[#15191E]
          placeholder:text-[#A6B0BF]
          outline-none border-none
          py-[4px]
        "
      />

      <button
        onClick={onSubmit}
        disabled={disabled || !hasValue}
        className={`
          w-[36px] h-[36px] rounded-full
          flex items-center justify-center
          transition-colors duration-150
          ${hasValue
            ? 'bg-[#15191E]'
            : 'bg-[#E1E5EA]'
          }
          disabled:cursor-not-allowed
        `}
      >
        <ArrowUp
          className={`
            w-[16px] h-[16px]
            ${hasValue ? 'text-white' : 'text-[#A6B0BF]'}
          `}
        />
      </button>
    </div>
  );
}
```

#### Figma Reference

```typescript
parameters: {
  design: {
    type: 'figma',
    url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=2036:2118',
  },
},
```

---

*Note: This document contains the core components. Additional components (PotentialCauseCard, ContactInfoItem, EquipmentCard, JobHeader, SummaryTextBlock, DiagnosisAccordionItem, WarrantyStatusPill, ChecklistCard, ActionStepCard, ChatHistoryContainer, SystemInfoList, SystemAlertBanner, ActionPlanPreviewSheet, FindingsCard, CompletedStepCard, LineItemRow, LineItemList, PricingFooter, PhotoUploadRow, NotesCard, PaymentForm, VisitSummaryCard, PaymentBreakdown, SecondaryActionCTA, CollapsibleSectionHeader) are documented in the supplementary design-system-components.md file.*

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
  --color-interactive-blue-alt: #1456B8;

  /* Colors - Semantic: Status */
  --color-status-success: var(--color-green-60);
  --color-status-error: var(--color-red);
  --color-status-critical: #EB5D47;
  --color-status-critical-dark: #B82A14;
  --color-status-warning: #EBB447;
  --color-status-warning-dark: #B88114;
  --color-status-minor: #81B814;

  /* Grey - Extended (from PaymentForm) */
  --color-grey-labels: #4F5B76;
  --color-grey-placeholder: #A5ACB8;
  --color-grey-border: #E0E0E0;

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

        // Status - Extended
        critical: {
          DEFAULT: '#EB5D47',
          dark: '#B82A14',
        },
        warning: {
          DEFAULT: '#EBB447',
          dark: '#B88114',
        },
        minor: '#81B814',

        // Grey - Extended (from PaymentForm)
        grey: {
          labels: '#4F5B76',
          placeholder: '#A5ACB8',
          border: '#E0E0E0',
        },

        // Interactive - Extended
        interactive: {
          blue: '#1A6BE5',
          blueLight: '#D1E1FA',
          blueExtraLight: '#E3EDFC',
          blueAlt: '#1456B8',
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

---

## Component Index

| Component | Category | Status |
|-----------|----------|--------|
| Header | Layout | Core |
| Input | Form | Core |
| Checkmark | Feedback | Core |
| ChatBubble | Data Display | Core |
| BottomSheet | Layout | Core |
| TimeSlotCard | Data Display | Core |
| Button | Form | Core |
| StatusTag | Feedback | v3.1 |
| TabNav | Navigation | v3.1 |
| SeverityBadge | Feedback | v3.1 |
| InfoRow | Data Display | v3.1 |
| Checkbox | Form | v3.1 |
| SectionHeader | Typography | v3.1 |
| StepCounter | Feedback | v3.1 |
| StickyFooter | Layout | v3.1 |
| ChatInput | Form | v3.1 |
| PotentialCauseCard | Data Display | v3.1 |
| ContactInfoItem | Data Display | v3.1 |
| EquipmentCard | Data Display | v3.1 |
| JobHeader | Layout | v3.1 |
| SummaryTextBlock | Data Display | v3.1 |
| DiagnosisAccordionItem | Data Display | v3.1 |
| WarrantyStatusPill | Feedback | v3.1 |
| ChecklistCard | Data Display | v3.1 |
| ActionStepCard | Data Display | v3.1 |
| ChatHistoryContainer | Layout | v3.1 |
| SystemInfoList | Data Display | v3.1 |
| SystemAlertBanner | Feedback | v3.1 |
| ActionPlanPreviewSheet | Layout | v3.1 |
| FindingsCard | Data Display | v3.1 |
| CompletedStepCard | Data Display | v3.1 |
| LineItemRow | Data Display | v3.1 |
| LineItemList | Data Display | v3.1 |
| PricingFooter | Layout | v3.1 |
| PhotoUploadRow | Form | v3.1 |
| NotesCard | Data Display | v3.1 |
| PaymentForm | Form | v3.1 |
| VisitSummaryCard | Data Display | v3.1 |
| PaymentBreakdown | Data Display | v3.1 |
| SecondaryActionCTA | Layout | v3.1 |
| CollapsibleSectionHeader | Navigation | v3.1 |
