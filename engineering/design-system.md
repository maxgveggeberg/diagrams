# RepairBot Design System - Storybook Structure (v3)

> **Audience:** Frontend developers implementing the RepairBot web app  
> **Purpose:** Component specifications, design tokens, and user journey context  
> **Last updated:** January 2, 2026

Extracted from Figma: 
- RepairBot: `kOxblDpHcb5SeIdxXpekyA`
- Current Tetra Design System: `W60u5Lbi4g2o6YTHzAGRkS`

---

## User Journey Overview

This design system supports the **RepairBot** web app - an AI-powered HVAC diagnostic tool that helps homeowners describe their issue and receive a preliminary diagnosis before scheduling a repair visit.

### The Happy Path

```
+-----------------------------------------------------------------+
|  1. LANDING                                                     |
|     Homeowner lands on RepairBot, sees a single input field     |
|     prompting them to enter their address.                      |
+-----------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------+
|  2. ADDRESS ENTRY                                               |
|     User types their address. Google Places autocomplete        |
|     helps them select a formatted address. When they click      |
|     the arrow (or press enter), the Digital Twin creation       |
|     begins.                                                     |
|                                                                 |
|     [!] PARALLEL CHECK: Backend checks if user has an existing   |
|     scheduled appointment. If yes, header button changes        |
|     from "Schedule" -> "My visit"                                |
+-----------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------+
|  3. DIGITAL TWIN LOADING (10-20 seconds)                        |
|     The address input transforms into a loading state that      |
|     cycles through status messages:                             |
|       * "Checking service territory."                           |
|       * "Looking at MLS images"                                 |
|       * "Searching for building permits."                       |
|       * "Generating geospatial data."                           |
|       * "Checking Streetview."                                  |
|       * "Digital twin complete" [check]                               |
|                                                                 |
|     Meanwhile, a second input appears below asking the user     |
|     to "Describe your issue..."                                 |
+-----------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------+
|  4. ISSUE DESCRIPTION                                           |
|     User types their HVAC issue in natural language. When       |
|     they press enter, they're taken to the Chat Screen.         |
+-----------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------+
|  5. DIGITAL TWIN COMPLETE + EQUIPMENT CONFIRMATION              |
|     Once the Digital Twin finishes loading:                     |
|       * The loader fades away (only chat remains)               |
|       * AI displays what it knows about the house:              |
|         - Heating system: Furnace (high confidence)             |
|         - Heating fuel: Gas (medium confidence)                 |
|         - Central AC: True (medium confidence)                  |
|                                                                 |
|     CONDITIONAL FLOW:                                           |
|       * High confidence -> AI acknowledges and continues         |
|       * Medium/Low -> AI asks user to confirm each uncertain     |
|         field before proceeding                                 |
|                                                                 |
|     Example: "First question, how do you heat your home?"       |
+-----------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------+
|  6. CHAT / DIAGNOSIS                                            |
|     The user's issue appears as a chat bubble. The AI responds  |
|     with follow-up questions to narrow down the diagnosis.      |
|                                                                 |
|     A bottom sheet shows the current diagnosis:                 |
|       "Top diagnosis: Refrigerant leak"                         |
|       "23 of 200 possible causes"                               |
|                                                                 |
|     As the user answers questions, the AI narrows down:         |
|       23 -> 12 -> 4 possible causes                               |
|                                                                 |
|     The user can drag up the bottom sheet to see ranked         |
|     possible causes with explanations.                          |
+-----------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------+
|  7. SCHEDULING                                                  |
|     Once diagnosis is complete, user clicks "Schedule" to       |
|     book a repair visit.                                        |
|                                                                 |
|     SCREEN FEATURES:                                            |
|       * Time slots grouped by day (today, tomorrow, etc.)       |
|       * 4-hour windows with pricing ($99 standard, $149 after   |
|         hours)                                                  |
|       * "Urgent?" button for emergency calls                    |
|       * "Show more" reveals additional days (max 3 clicks)      |
|       * "Want a different time?" prompts phone call             |
|                                                                 |
|     INTERACTION:                                                |
|       * Click slot -> turns green (selected)                     |
|       * Click "Next" -> advances to confirmation                 |
+-----------------------------------------------------------------+
```

### Returning User Flow

If the user already has a scheduled appointment (detected after entering their address):

```
+-----------------------------------------------------------------+
|  Header button: "Schedule" -> "My visit"                         |
|                                                                 |
|  User can:                                                      |
|  * Tap "My visit" to view upcoming appointment details          |
|  * Continue describing a new issue (proceeds normally)          |
|  * Call for support                                             |
+-----------------------------------------------------------------+
```

---

## Recommended Storybook Hierarchy

```
stories/
+-- Introduction.mdx
+-- Foundations/
|   +-- Colors.mdx
|   +-- Typography.mdx
|   +-- Spacing.mdx
|   +-- Shadows.mdx
|   +-- BorderRadius.mdx
|   +-- Motion.mdx
+-- Components/
|   +-- Button/
|   +-- Header/
|   |   +-- Header.tsx
|   |   +-- Header.stories.tsx
|   +-- Input/
|   |   +-- Input.tsx
|   |   +-- Input.stories.tsx
|   +-- ChatBubble/
|   +-- BottomSheet/
|   +-- TimeSlotCard/
|   |   +-- TimeSlotCard.tsx
|   |   +-- TimeSlotCard.stories.tsx
|   +-- Logo/
|   +-- Spinner/
|   +-- Checkmark/
|   +-- HelperText/
+-- Patterns/
|   +-- AddressEntry/
|   +-- DigitalTwinLoader/
|   +-- IssueDescription/
|   +-- EquipmentConfirmation/
|   |   +-- EquipmentConfirmation.tsx
|   |   +-- EquipmentConfirmation.stories.tsx
|   +-- ChatScreen/
|   +-- ScheduleScreen/
|       +-- ScheduleScreen.tsx
|       +-- ScheduleScreen.stories.tsx
+-- Pages/
    +-- RepairHomePage/
    +-- RepairHomePageStatusUpdate/
```

---

## Foundations

### Colors (`Foundations/Colors.mdx`)

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

### Typography (`Foundations/Typography.mdx`)

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

### Spacing (`Foundations/Spacing.mdx`)

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

### Border Radius (`Foundations/BorderRadius.mdx`)

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

### Shadows (`Foundations/Shadows.mdx`)

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

### Motion (`Foundations/Motion.mdx`)

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

### Header (`Components/Header`)

#### Narrative

The header is persistent across all screens and provides three key actions:
- **Call** - Direct phone support (left)
- **Logo** - Tetra branding (center)
- **Schedule / My Visit** - Primary CTA (right)

**Conditional state:** After the user enters their address, our backend checks if they already have a scheduled appointment. If they do, the right button changes from "Schedule" to "My Visit". This provides a quick way for returning users to access their upcoming visit details.

#### Layout

```
+-----------------------------------------------------------------+
|  +----------+          [Tetra] Tetra          +-------------+       |
|  | [phone] Call  |                            | Schedule >  |       |
|  +----------+                            +-------------+       |
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

#### Right Button Variants

| State | Label | Icon | Trigger |
|-------|-------|------|---------|
| `schedule` | "Schedule" | Send arrow (rotated 90 degrees) | Default state, no existing appointment |
| `myVisit` | "My visit" | Send arrow (rotated 90 degrees) | User has a scheduled appointment |

#### Props (Args)

| Arg | Type | Default | Description |
|-----|------|---------|-------------|
| `onCallClick` | `() => void` | - | Handler for Call button |
| `onScheduleClick` | `() => void` | - | Handler for Schedule/My Visit button |
| `hasScheduledVisit` | `boolean` | `false` | If true, shows "My visit" instead of "Schedule" |

#### Implementation

```typescript
// Components/Header/Header.tsx
interface HeaderProps {
  onCallClick?: () => void;
  onScheduleClick?: () => void;
  hasScheduledVisit?: boolean;
}

export function Header({ 
  onCallClick, 
  onScheduleClick,
  hasScheduledVisit = false 
}: HeaderProps) {
  return (
    <header className="
      flex items-center justify-between
      px-[16px] py-[8px]
      bg-white border-b border-[#F3F5F7]
    ">
      {/* Left: Call Button */}
      <div className="flex-1">
        <button 
          onClick={onCallClick}
          className="
            flex items-center gap-[6px]
            bg-[#F3F5F7] rounded-[6.26px] p-[9.39px]
            text-[12.5px] font-medium text-[#15191E]
          "
        >
          <PhoneIcon className="w-[18px] h-[17px]" />
          <span>Call</span>
        </button>
      </div>
      
      {/* Center: Logo */}
      <div className="flex-1 flex justify-center">
        <TetraLogo className="h-[19.5px] w-[70px]" />
      </div>
      
      {/* Right: Schedule / My Visit */}
      <div className="flex-1 flex justify-end">
        <button 
          onClick={onScheduleClick}
          className="
            flex items-center gap-[6px]
            bg-[#F3F5F7] rounded-[6.26px] p-[9.39px]
            text-[12.5px] font-medium text-[#15191E]
          "
        >
          <span>{hasScheduledVisit ? 'My visit' : 'Schedule'}</span>
          <SendIcon className="w-[17px] h-[18px] rotate-90" />
        </button>
      </div>
    </header>
  );
}
```

#### Story Structure

```typescript
// Components/Header/Header.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Header } from './Header';

const meta: Meta<typeof Header> = {
  title: 'Components/Header',
  component: Header,
  parameters: {
    layout: 'fullscreen',
    design: {
      type: 'figma',
      url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=291:2592',
    },
  },
};

export default meta;
type Story = StoryObj<typeof Header>;

export const Default: Story = {
  args: {
    hasScheduledVisit: false,
  },
};

export const WithScheduledVisit: Story = {
  args: {
    hasScheduledVisit: true,
  },
};
```

---

### Input (`Components/Input`) - COMPLETE STATE REFERENCE

#### Narrative

The Input component is the workhorse of RepairBot's interface. It serves multiple purposes:
- **Address entry** - With Google Places autocomplete
- **Issue description** - Multi-line text for describing the HVAC problem
- **Chat input** - For follow-up questions during diagnosis
- **Status display** - Shows Digital Twin loading progress

The component transitions through several states, each with distinct visual treatment. The elevated shadow makes it feel like a floating card, drawing focus to the primary action.

#### Visual States

```
+-----------------------------------------------------------------+
|  STATE: IDLE (Empty)                                            |
|  +---------------------------------------------------------+   |
|  | Address                                            (^)  |   |
|  +---------------------------------------------------------+   |
|  Text: text.disabled (#A6B0BF)                                  |
|  Icon: Arrow up, grey fill, grey circle background              |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: FILLED (Has Value)                                      |
|  +---------------------------------------------------------+   |
|  | 22 Ober Rd, Newton, MA 02459                       (^)  |   |
|  +---------------------------------------------------------+   |
|  Text: text.primary (#15191E)                                   |
|  Icon: Arrow up, white fill, black circle background            |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: LOADING (Processing)                                    |
|  +---------------------------------------------------------+   |
|  | Checking service territory.                        (o)  |   |
|  +---------------------------------------------------------+   |
|  Text: text.disabled (#A6B0BF)                                  |
|  Icon: Spinner, grey stroke                                     |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: SUCCESS (Complete)                                      |
|  +---------------------------------------------------------+   |
|  | Digital twin complete                              ([check])  |   |
|  +---------------------------------------------------------+   |
|  Text: text.primary (#15191E), medium weight                    |
|  Icon: Checkmark, white stroke, brand.primary (#B4EB47) circle  |
+-----------------------------------------------------------------+
```

#### Props (Args)

| Arg | Type | Default | Description |
|-----|------|---------|-------------|
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

#### Story Structure

```typescript
// Components/Input/Input.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Input } from './Input';

const meta: Meta<typeof Input> = {
  title: 'Components/Input',
  component: Input,
  parameters: {
    layout: 'centered',
    design: {
      type: 'figma',
      url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=2026:2394',
    },
  },
  tags: ['autodocs'],
  argTypes: {
    status: {
      control: 'select',
      options: ['idle', 'filled', 'loading', 'success', 'error'],
    },
  },
};

export default meta;
type Story = StoryObj<typeof Input>;

export const Idle: Story = {
  args: {
    label: 'Address',
    status: 'idle',
    elevated: true,
  },
};

export const Filled: Story = {
  args: {
    label: 'Address',
    value: '22 Ober Rd, Newton, MA 02459',
    status: 'filled',
    elevated: true,
  },
};

export const Loading: Story = {
  args: {
    label: 'Address',
    status: 'loading',
    statusText: 'Checking service territory.',
    elevated: true,
    readOnly: true,
  },
};

export const Success: Story = {
  args: {
    label: 'Address',
    status: 'success',
    statusText: 'Digital twin complete',
    elevated: true,
    readOnly: true,
  },
};

export const Error: Story = {
  args: {
    label: 'Address',
    status: 'error',
    statusText: 'Address not in service area',
    elevated: true,
  },
};
```

---

### Checkmark (`Components/Checkmark`)

#### Variants

| Variant | Background | Icon Color | Usage |
|---------|------------|------------|-------|
| `success` | Brand Green (`#B4EB47`) | White | Completion states |
| `neutral` | Grey (`#F3F5F7`) | Grey (`#A6B0BF`) | Inactive/pending |

#### Props (Args)

| Arg | Type | Default | Description |
|-----|------|---------|-------------|
| `variant` | `'success' \| 'neutral'` | `'success'` | Visual variant |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Size (16/24/32px) |
| `animated` | `boolean` | `true` | Animate checkmark draw on mount |

---

## Patterns

### DigitalTwinLoader (`Patterns/DigitalTwinLoader`)

#### Narrative

When the user submits their address, our backend begins creating a "Digital Twin" of their home - aggregating data from MLS listings, building permits, geospatial sources, and Street View imagery. This process takes **10-20 seconds**.

Rather than showing a generic spinner, we keep the user engaged by cycling through status messages that explain what's happening. This serves two purposes:
1. **Transparency** - Users understand we're doing real work, not just making them wait
2. **Trust-building** - Showing the data sources reinforces that our diagnosis will be informed and accurate

The loader stays visible at the top of the screen even after the user moves to the chat interface, providing continuity and showing when the Digital Twin is ready.

#### Completion & Fade Away

Once the Digital Twin is complete:
1. The status text changes to **"Digital twin complete"** with a green checkmark (`#B4EB47`)
2. The loader container gains a semi-transparent white overlay (`rgba(255,255,255,0.4)`)
3. After a brief pause (500ms), the entire loader **fades away** so only the chat window remains
4. The AI then responds with the equipment confirmation message

```typescript
// Fade animation for Digital Twin complete
const fadeOutAnimation = {
  opacity: [1, 0],
  transition: {
    duration: 500,
    delay: 500, // Show "complete" state briefly before fading
    easing: 'ease-out',
  },
};
```

#### Loading Sequence

```typescript
const DIGITAL_TWIN_STEPS = [
  { id: 'service_territory', message: 'Checking service territory.' },
  { id: 'mls_images', message: 'Looking at MLS images' },
  { id: 'building_permits', message: 'Searching for building permits.' },
  { id: 'geospatial', message: 'Generating geospatial data.' },
  { id: 'streetview', message: 'Checking Streetview.' },
  { id: 'complete', message: 'Digital twin complete' },
] as const;

type DigitalTwinStep = typeof DIGITAL_TWIN_STEPS[number]['id'];
```

#### Timing Specification

| Step | Typical Duration | Cumulative |
|------|------------------|------------|
| `service_territory` | 2-3s | 2-3s |
| `mls_images` | 3-5s | 5-8s |
| `building_permits` | 2-4s | 7-12s |
| `geospatial` | 2-3s | 9-15s |
| `streetview` | 1-3s | 10-18s |
| `complete` | - | 10-20s total |

#### State Machine

```typescript
type DigitalTwinLoaderState =
  | { status: 'idle' }
  | { status: 'loading'; step: DigitalTwinStep; progress: number }
  | { status: 'complete' }
  | { status: 'error'; message: string };
```

#### Props (Args)

| Arg | Type | Default | Description |
|-----|------|---------|-------------|
| `address` | `string` | - | The address being processed |
| `onComplete` | `(digitalTwin: DigitalTwin) => void` | - | Callback when complete |
| `onError` | `(error: Error) => void` | - | Callback on failure |
| `onStepChange` | `(step: DigitalTwinStep) => void` | - | Callback when step changes |

#### Implementation Notes

```typescript
// Patterns/DigitalTwinLoader/DigitalTwinLoader.tsx
import { useEffect, useState } from 'react';
import { Input } from '@/components/Input';
import { useDigitalTwinCreation } from '@/hooks/useDigitalTwinCreation';

interface DigitalTwinLoaderProps {
  address: string;
  onComplete: (digitalTwin: DigitalTwin) => void;
  onError?: (error: Error) => void;
}

export function DigitalTwinLoader({ 
  address, 
  onComplete, 
  onError 
}: DigitalTwinLoaderProps) {
  const { 
    status, 
    currentStep, 
    digitalTwin, 
    error 
  } = useDigitalTwinCreation(address);

  useEffect(() => {
    if (status === 'complete' && digitalTwin) {
      onComplete(digitalTwin);
    }
  }, [status, digitalTwin, onComplete]);

  useEffect(() => {
    if (status === 'error' && error) {
      onError?.(error);
    }
  }, [status, error, onError]);

  const statusText = status === 'complete' 
    ? 'Digital twin complete'
    : DIGITAL_TWIN_STEPS.find(s => s.id === currentStep)?.message;

  return (
    <Input
      label="Address"
      value={address}
      status={status === 'complete' ? 'success' : 'loading'}
      statusText={statusText}
      elevated
      readOnly
    />
  );
}
```

#### Hook Implementation

```typescript
// hooks/useDigitalTwinCreation.ts
import { useEffect, useState, useRef } from 'react';

interface UseDigitalTwinCreationResult {
  status: 'idle' | 'loading' | 'complete' | 'error';
  currentStep: DigitalTwinStep | null;
  digitalTwin: DigitalTwin | null;
  error: Error | null;
}

export function useDigitalTwinCreation(
  address: string
): UseDigitalTwinCreationResult {
  const [status, setStatus] = useState<'idle' | 'loading' | 'complete' | 'error'>('idle');
  const [currentStep, setCurrentStep] = useState<DigitalTwinStep | null>(null);
  const [digitalTwin, setDigitalTwin] = useState<DigitalTwin | null>(null);
  const [error, setError] = useState<Error | null>(null);
  
  const eventSourceRef = useRef<EventSource | null>(null);

  useEffect(() => {
    if (!address) return;

    setStatus('loading');
    setCurrentStep('service_territory');

    // Server-Sent Events for real-time progress updates
    const eventSource = new EventSource(
      `/api/digital-twin/create?address=${encodeURIComponent(address)}`
    );
    eventSourceRef.current = eventSource;

    eventSource.addEventListener('step', (event) => {
      const { step } = JSON.parse(event.data);
      setCurrentStep(step);
    });

    eventSource.addEventListener('complete', (event) => {
      const { digitalTwin } = JSON.parse(event.data);
      setDigitalTwin(digitalTwin);
      setStatus('complete');
      eventSource.close();
    });

    eventSource.addEventListener('error', (event) => {
      setError(new Error('Digital twin creation failed'));
      setStatus('error');
      eventSource.close();
    });

    return () => {
      eventSource.close();
    };
  }, [address]);

  return { status, currentStep, digitalTwin, error };
}
```

#### Story Structure

```typescript
// Patterns/DigitalTwinLoader/DigitalTwinLoader.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { DigitalTwinLoader } from './DigitalTwinLoader';

const meta: Meta<typeof DigitalTwinLoader> = {
  title: 'Patterns/DigitalTwinLoader',
  component: DigitalTwinLoader,
  parameters: {
    layout: 'centered',
    design: {
      type: 'figma',
      url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=2026:2394',
    },
  },
  tags: ['autodocs'],
};

export default meta;
type Story = StoryObj<typeof DigitalTwinLoader>;

export const Step1_ServiceTerritory: Story = {
  args: {
    address: '22 Ober Rd, Newton, MA 02459',
  },
  parameters: {
    mockData: {
      currentStep: 'service_territory',
    },
  },
};

export const Step2_MLSImages: Story = {
  args: {
    address: '22 Ober Rd, Newton, MA 02459',
  },
  parameters: {
    mockData: {
      currentStep: 'mls_images',
    },
  },
};

export const Step3_BuildingPermits: Story = {
  args: {
    address: '22 Ober Rd, Newton, MA 02459',
  },
  parameters: {
    mockData: {
      currentStep: 'building_permits',
    },
  },
};

export const Step4_GeospatialData: Story = {
  args: {
    address: '22 Ober Rd, Newton, MA 02459',
  },
  parameters: {
    mockData: {
      currentStep: 'geospatial',
    },
  },
};

export const Step5_Streetview: Story = {
  args: {
    address: '22 Ober Rd, Newton, MA 02459',
  },
  parameters: {
    mockData: {
      currentStep: 'streetview',
    },
  },
};

export const Complete: Story = {
  args: {
    address: '22 Ober Rd, Newton, MA 02459',
  },
  parameters: {
    mockData: {
      status: 'complete',
    },
  },
};

// Animated story that cycles through all steps
export const FullSequence: Story = {
  args: {
    address: '22 Ober Rd, Newton, MA 02459',
  },
  play: async ({ canvasElement }) => {
    // This story demonstrates the full loading sequence
    // Uses MSW or custom mock to simulate real API timing
  },
};
```

---

### AddressEntry (`Patterns/AddressEntry`)

#### Narrative

The address entry is the first interaction a homeowner has with RepairBot. We keep it intentionally simple - a single, prominent input field with Google Places autocomplete.

**Why address first?** The address unlocks everything:
- Service territory validation (are we available in this area?)
- **Existing appointment check** (does this user already have a scheduled visit?)
- Digital Twin creation (property data, MLS images, permits)
- Personalized diagnosis (knowing the home's age, size, and equipment history)

**Returning user flow:** After the user enters their address, our backend checks if they already have a scheduled appointment. If they do:
1. The header's "Schedule" button changes to "My visit"
2. User can tap "My visit" to see their upcoming appointment details
3. They can still proceed with a new issue if needed

Once the user selects an address and submits, an animated transition occurs:
1. The arrow icon morphs into a spinner
2. The input floats up toward the top of the screen
3. A second input fades in below: "Describe your issue..."
4. Helper text appears explaining that detailed descriptions lead to better diagnoses

This layered reveal keeps the user engaged during the 10-20 second Digital Twin creation process.

#### State Machine

```typescript
type AddressEntryState =
  | { phase: 'address_input'; inputStatus: 'idle' | 'filled' }
  | { phase: 'checking_appointment' }  // New: checking for existing visit
  | { phase: 'digital_twin_loading'; step: DigitalTwinStep; hasScheduledVisit: boolean }
  | { phase: 'digital_twin_complete'; digitalTwin: DigitalTwin; hasScheduledVisit: boolean }
  | { phase: 'issue_description' }
  | { phase: 'error'; message: string };
```

#### Complete Interaction Flow

```
+-----------------------------------------------------------------+
|  STEP 1: ADDRESS INPUT (Idle)                                   |
|  User sees empty address input                                  |
|  +---------------------------------------------------------+   |
|  | Address                                            (^)  |   |
|  +---------------------------------------------------------+   |
+-----------------------------------------------------------------+
                              |
                              | User types, Google Places shows suggestions
                              v
+-----------------------------------------------------------------+
|  STEP 2: ADDRESS FILLED                                         |
|  User has selected a formatted address                          |
|  +---------------------------------------------------------+   |
|  | 22 Ober Rd, Newton, MA 02459                       (^)  |   |
|  +---------------------------------------------------------+   |
|                                                                 |
|  User clicks arrow button (or auto-triggers on selection)       |
+-----------------------------------------------------------------+
                              |
                              | Trigger Digital Twin creation
                              v
+-----------------------------------------------------------------+
|  STEP 3: DIGITAL TWIN LOADING (10-20 seconds)                   |
|                                                                 |
|  Animation: Input floats to top, Issue input appears below      |
|                                                                 |
|  +---------------------------------------------------------+   |
|  | Checking service territory.                       (o)   |   |
|  +---------------------------------------------------------+   |
|                              v cycles through                   |
|  | Looking at MLS images                             (o)   |   |
|                              v                                  |
|  | Searching for building permits.                   (o)   |   |
|                              v                                  |
|  | Generating geospatial data.                       (o)   |   |
|                              v                                  |
|  | Checking Streetview.                              (o)   |   |
|                                                                 |
|  +---------------------------------------------------------+   |
|  | Describe your issue                               [^]   |   |
|  +---------------------------------------------------------+   |
|                                                                 |
|  Helper text: "There are over 200 possible ways..."             |
+-----------------------------------------------------------------+
                              |
                              | Digital Twin API returns success
                              v
+-----------------------------------------------------------------+
|  STEP 4: DIGITAL TWIN COMPLETE                                  |
|                                                                 |
|  +---------------------------------------------------------+   |
|  | Digital twin complete                             ([check])   |   |
|  +---------------------------------------------------------+   |
|                                                                 |
|  +---------------------------------------------------------+   |
|  | Describe your issue                               [^]   |   |
|  +---------------------------------------------------------+   |
|                                                                 |
|  Spinner disappears, user can now describe their issue          |
+-----------------------------------------------------------------+
```

#### State Machine

```typescript
type AddressEntryState =
  | { phase: 'address_input'; inputStatus: 'idle' | 'filled' }
  | { phase: 'digital_twin_loading'; step: DigitalTwinStep }
  | { phase: 'digital_twin_complete'; digitalTwin: DigitalTwin }
  | { phase: 'issue_description' }
  | { phase: 'error'; message: string };
```

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

---

## API Contract: Digital Twin Creation

```typescript
// Expected SSE endpoint: /api/digital-twin/create

// Request
GET /api/digital-twin/create?address={encoded_address}
Accept: text/event-stream

// Response events
event: step
data: {"step": "service_territory"}

event: step
data: {"step": "mls_images"}

event: step
data: {"step": "building_permits"}

event: step
data: {"step": "geospatial"}

event: step
data: {"step": "streetview"}

event: complete
data: {
  "digitalTwin": {
    "id": "dt_abc123",
    "address": "22 Ober Rd, Newton, MA 02459",
    "serviceTerritory": "boston_metro",
    "propertyData": { ... },
    "hvacEstimate": { ... }
  }
}

// Error event
event: error
data: {"message": "Address not in service area"}
```

---

## Patterns (continued)

### EquipmentConfirmation (`Patterns/EquipmentConfirmation`)

#### Narrative

Once the Digital Twin loading completes, the loader fades away and the AI presents what it has learned about the home's HVAC equipment. This is a critical moment - the quality of our diagnosis depends on accurate equipment information.

**The confidence system drives the conversation:**
- **High confidence** -> AI acknowledges the information and moves on
- **Medium or low confidence** -> AI guides the homeowner through confirming the uncertain details

This conditional flow ensures we don't waste the user's time asking questions we already know the answers to, while making sure we get clarity on anything uncertain.

**Example flow for medium confidence:**
1. AI displays: "Heating system: **Furnace** (high confidence)"
2. AI displays: "Heating fuel: **Gas** (medium confidence)"
3. AI displays: "Central AC: **True** (medium confidence)"
4. AI asks: "First question, how do you heat your home?"
5. User responds -> AI confirms or corrects the data
6. AI asks next question if needed
7. Once all confirmed -> AI proceeds to regular diagnosis chat

#### Visual Design

The equipment information message uses a structured format with bullet points and bold values:

```
We've compiled the following information on your 
heating and cooling system:

* Heating system: **Furnace** (high confidence)
* Heating fuel: **Gas** (medium confidence)  
* Central AC: **True** (medium confidence)

Next, we'll ask you to confirm any information we do 
not have high confidence in.

**First question, how do you heat your home?** Type 
"I'm not sure" if you'd like me to help you figure it out.
```

#### Typography in Message

| Element | Font | Weight | Color |
|---------|------|--------|-------|
| Body text | Sharp Grotesk Book 20 | 400 | `#2B313B` |
| Equipment values | Sharp Grotesk Medium 20 | 500 | `#2B313B` |
| Follow-up question | Sharp Grotesk Medium 20 | 500 | `#2B313B` |
| Confidence label | Sharp Grotesk Book 20 | 400 | `#2B313B` |

#### Equipment Data Structure

```typescript
interface EquipmentInfo {
  heatingSystem: {
    value: 'furnace' | 'boiler' | 'heat_pump' | 'other' | null;
    confidence: 'high' | 'medium' | 'low';
  };
  heatingFuel: {
    value: 'gas' | 'oil' | 'electric' | 'propane' | 'other' | null;
    confidence: 'high' | 'medium' | 'low';
  };
  centralAC: {
    value: boolean | null;
    confidence: 'high' | 'medium' | 'low';
  };
}

// Derived from Digital Twin
function needsConfirmation(equipment: EquipmentInfo): boolean {
  return (
    equipment.heatingSystem.confidence !== 'high' ||
    equipment.heatingFuel.confidence !== 'high' ||
    equipment.centralAC.confidence !== 'high'
  );
}
```

#### State Machine

```typescript
type EquipmentConfirmationState =
  | { phase: 'displaying_info' }
  | { phase: 'confirming'; field: 'heatingSystem' | 'heatingFuel' | 'centralAC' }
  | { phase: 'complete' };

// Flow logic
function getNextConfirmationField(
  equipment: EquipmentInfo,
  confirmedFields: Set<string>
): string | null {
  const fieldsToConfirm = ['heatingSystem', 'heatingFuel', 'centralAC']
    .filter(field => equipment[field].confidence !== 'high')
    .filter(field => !confirmedFields.has(field));
  
  return fieldsToConfirm[0] || null;
}
```

#### AI Response Templates

```typescript
const confirmationTemplates = {
  heatingSystem: {
    question: "First question, how do you heat your home?",
    helper: 'Type "I\'m not sure" if you\'d like me to help you figure it out.',
  },
  heatingFuel: {
    question: "What type of fuel does your heating system use?",
    helper: 'Common options are gas, oil, electric, or propane.',
  },
  centralAC: {
    question: "Do you have central air conditioning?",
    helper: 'This would be cooling that comes through vents, not window units.',
  },
};

// High confidence response (no questions needed)
const highConfidenceResponse = `
Great! Based on your property records, I have high confidence in your HVAC setup:
* Heating system: **Furnace**
* Heating fuel: **Gas**
* Central AC: **Yes**

Let's continue with diagnosing your issue.
`;
```

#### Story Structure

```typescript
// Patterns/EquipmentConfirmation/EquipmentConfirmation.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';

const meta: Meta = {
  title: 'Patterns/EquipmentConfirmation',
  parameters: {
    layout: 'fullscreen',
    viewport: { defaultViewport: 'mobile1' },
    design: {
      type: 'figma',
      url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=291:2741',
    },
  },
};

export default meta;

export const MixedConfidence: Story = {
  args: {
    equipment: {
      heatingSystem: { value: 'furnace', confidence: 'high' },
      heatingFuel: { value: 'gas', confidence: 'medium' },
      centralAC: { value: true, confidence: 'medium' },
    },
  },
};

export const AllHighConfidence: Story = {
  args: {
    equipment: {
      heatingSystem: { value: 'furnace', confidence: 'high' },
      heatingFuel: { value: 'gas', confidence: 'high' },
      centralAC: { value: true, confidence: 'high' },
    },
  },
};

export const LowConfidence: Story = {
  args: {
    equipment: {
      heatingSystem: { value: null, confidence: 'low' },
      heatingFuel: { value: null, confidence: 'low' },
      centralAC: { value: null, confidence: 'low' },
    },
  },
};
```

---

## Components (continued)

### ChatBubble (`Components/ChatBubble`)

#### Narrative

The chat interface uses a simple two-style approach:
- **User messages** appear in grey bubbles, right-aligned - clearly "from me"
- **AI responses** have no bubble, just text on the left - feels more like guidance than messaging

This asymmetry is intentional. We want the AI to feel like a knowledgeable advisor explaining things, not a chatbot having a back-and-forth conversation. The user's messages are inputs; the AI's responses are expert guidance.

**Streaming:** AI responses appear token-by-token as they're generated, with a blinking cursor to indicate more is coming. This creates a sense of the AI "thinking" and responding naturally, rather than dumping a wall of text.

#### Variants

| Variant | Background | Border Radius | Padding | Text Color |
|---------|------------|---------------|---------|------------|
| `user` | `#F3F5F7` | 12px | 12px horizontal, 8px vertical | `#2B313B` |
| `assistant` | transparent | - | 0 | `#2B313B` |

#### Props (Args)

| Arg | Type | Default | Description |
|-----|------|---------|-------------|
| `variant` | `'user' \| 'assistant'` | `'user'` | Visual style |
| `children` | `ReactNode` | - | Message content |
| `isStreaming` | `boolean` | `false` | Shows typing cursor during streaming |

#### Streaming Behavior

AI responses stream **token-by-token** for a natural conversational feel.

```typescript
// Components/ChatBubble/StreamingText.tsx
import { useEffect, useState } from 'react';

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

// Usage with SSE or WebSocket
function useMessageStream(messageId: string) {
  const [content, setContent] = useState('');
  const [isComplete, setIsComplete] = useState(false);
  
  useEffect(() => {
    const eventSource = new EventSource(`/api/chat/stream/${messageId}`);
    
    eventSource.addEventListener('token', (e) => {
      const { token } = JSON.parse(e.data);
      setContent((prev) => prev + token);
    });
    
    eventSource.addEventListener('done', () => {
      setIsComplete(true);
      eventSource.close();
    });
    
    return () => eventSource.close();
  }, [messageId]);
  
  return { content, isComplete };
}
```

#### Typing Indicator

While waiting for first token, show a typing indicator:

```
+-----------------------------------------------------------------+
|  ***                                                            |
+-----------------------------------------------------------------+
```

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

### BottomSheet (`Components/BottomSheet`)

#### Narrative

The bottom sheet is where the AI's diagnostic intelligence becomes visible to the user. As the conversation progresses, the AI continuously refines its diagnosis based on the user's answers.

**The "narrowing down" experience is key to our value proposition.** We start with "200 possible causes" - representing the full universe of things that could be wrong with an HVAC system. With each answer, we visibly reduce this number: 200 -> 23 -> 12 -> 4. This creates a sense of progress and demonstrates that the AI is actually reasoning, not just pattern-matching keywords.

The user can drag up the bottom sheet at any time to see the ranked list of possible causes with plain-English explanations. This transparency helps them understand what might be wrong and sets appropriate expectations for the repair visit.

#### Visual States

```
+-----------------------------------------------------------------+
|  STATE: COLLAPSED (Peek)                                        |
|  +---------------------------------------------------------+   |
|  |                    -----------                           |   |  <- Drag handle
|  |         Top diagnosis: Refrigerant leak                  |   |
|  |           23 of 200 possible causes                      |   |  <- Italic, grey
|  +---------------------------------------------------------+   |
+-----------------------------------------------------------------+

+-----------------------------------------------------------------+
|  STATE: EXPANDED                                                |
|  +---------------------------------------------------------+   |
|  |                    -----------                           |   |
|  |         Top diagnosis: Refrigerant leak                  |   |
|  |           23 of 45 possible causes                       |   |
|  |  -------------------------------------------------------|   |
|  |  Refrigerant leak                                        |   |  <- Medium weight
|  |  Similar to a battery that helps the motor start...      |   |  <- Book weight, smaller
|  |  -------------------------------------------------------|   |
|  |  Condenser fan motor                                     |   |
|  |  The fan outside isn't spinning, causing the compressor..|   |
|  |  -------------------------------------------------------|   |
|  |  Compressor issue                                        |   |
|  |  The 'heart' of the AC condenser may be struggling...    |   |
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
| **Drag Handle** | Size | 44px × 4px |
| | Background | `#DEDEDE` |
| | Border radius | 4px |
| | Margin | 6px top, 8px bottom |
| **Title** | Font | Sharp Grotesk Book 20, 12px |
| | Line height | 20px |
| | Color | `#15191E` |
| **Counter** | Font | Sharp Grotesk Book Italic, 10px |
| | Line height | 16.5px |
| | Color | `#566376` |
| **Divider** | Height | 2px |
| | Background | `#DEDEDE` |
| | Border radius | 4px |
| **Cause Title** | Font | Sharp Grotesk Medium 20, 12px |
| | Line height | 20px |
| | Color | `#000000` (black) |
| **Cause Description** | Font | Sharp Grotesk Book 20, 10px |
| | Line height | 16.5px |
| | Color | `#2B313B` |

#### Interaction

| Platform | Gesture | Behavior |
|----------|---------|----------|
| Mobile | Drag up | Expands sheet to show cause list |
| Mobile | Drag down | Collapses sheet to peek state |
| Desktop | Click drag handle | Toggle expand/collapse |

**Recommended library:** `framer-motion` or `react-spring` for gesture handling with spring physics.

```typescript
// Example with framer-motion
import { motion, useDragControls, useMotionValue, useTransform } from 'framer-motion';

const COLLAPSED_HEIGHT = 68;  // Peek height
const EXPANDED_HEIGHT = 320;  // Full height with cause list

function BottomSheet({ ... }) {
  const y = useMotionValue(0);
  const dragControls = useDragControls();
  
  return (
    <motion.div
      drag="y"
      dragControls={dragControls}
      dragConstraints={{ top: -(EXPANDED_HEIGHT - COLLAPSED_HEIGHT), bottom: 0 }}
      dragElastic={0.2}
      onDragEnd={(_, info) => {
        // Snap to expanded or collapsed based on velocity/position
        if (info.velocity.y < -500 || info.offset.y < -100) {
          onExpand();
        } else if (info.velocity.y > 500 || info.offset.y > 100) {
          onCollapse();
        }
      }}
      style={{ y }}
    >
      {/* ... content ... */}
    </motion.div>
  );
}
```

#### Props (Args)

| Arg | Type | Default | Description |
|-----|------|---------|-------------|
| `isExpanded` | `boolean` | `false` | Whether sheet is expanded |
| `onExpand` | `() => void` | - | Called when dragged up past threshold |
| `onCollapse` | `() => void` | - | Called when dragged down past threshold |
| `topDiagnosis` | `string` | - | Primary diagnosis text |
| `possibleCauses` | `{ current: number; total: number }` | - | Counter values |
| `causes` | `Array<{ title: string; description: string }>` | `[]` | List of causes (shown when expanded) |

#### Implementation Notes

```typescript
// Components/BottomSheet/BottomSheet.tsx
interface Cause {
  title: string;
  description: string;
}

interface BottomSheetProps {
  isExpanded: boolean;
  onToggle: () => void;
  topDiagnosis: string;
  possibleCauses: { current: number; total: number };
  causes: Cause[];
}

export function BottomSheet({
  isExpanded,
  onToggle,
  topDiagnosis,
  possibleCauses,
  causes,
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
          Top diagnosis: {topDiagnosis}
        </p>
        <p className="text-[10px] leading-[16.5px] text-[#566376] italic">
          {possibleCauses.current} of {possibleCauses.total} possible causes
        </p>
      </div>
      
      {/* Expandable Cause List */}
      {isExpanded && (
        <div className="px-[12px] pt-[10px] pb-[36px]">
          {causes.map((cause, index) => (
            <div key={index}>
              {index > 0 && (
                <div className="h-[2px] bg-[#DEDEDE] rounded-[4px] my-[6px]" />
              )}
              <div className="py-[4px]">
                <p className="text-[12px] leading-[20px] font-medium text-black">
                  {cause.title}
                </p>
                <p className="text-[10px] leading-[16.5px] text-[#2B313B]">
                  {cause.description}
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

## Patterns (continued)

### ChatScreen (`Patterns/ChatScreen`)

#### Narrative

After the homeowner enters their issue description and presses enter, they're taken to the Chat Screen. This is where the diagnostic conversation happens.

**Key design decisions:**

1. **Digital Twin loader fades away** - Once the Digital Twin completes, the loader shows "Digital twin complete" with a green checkmark, then fades away. This keeps the chat interface clean and focused on the conversation.

2. **AI responses stream token-by-token** - Rather than waiting for the full response, we stream text as it's generated. This feels more conversational and responsive, especially for longer explanations.

3. **User messages are visually distinct** - Grey bubbles on the right for user, no bubble (just text) on the left for AI. This keeps the focus on the AI's diagnostic guidance.

4. **Bottom sheet is always accessible** - The diagnosis summary is persistent, so users always know where they stand. Dragging up reveals more detail without leaving the conversation.

#### Screen Layout

```
+-----------------------------------------------------------------+
|  +---------------------------------------------------------+   |
|  |  [phone] Call          [Tetra] Tetra          Schedule >          |   |  <- Header (sticky)
|  +---------------------------------------------------------+   |
+-----------------------------------------------------------------+
|  +---------------------------------------------------------+   |
|  | Checking service territory.                        (o)  |   |  <- Digital Twin Loader
|  +---------------------------------------------------------+   |     (persists at top)
+-----------------------------------------------------------------+
|                                                                 |
|                            +-------------------------------+   |
|                            | User's issue description...   |   |  <- User message
|                            +-------------------------------+   |     (right-aligned)
|                                                                 |
|  AI response text without bubble, explaining the diagnosis      |  <- Assistant message
|  and next steps...                                              |     (left-aligned)
|                                                                 |
|                            +-------------------------------+   |
|                            | User's follow-up answer...    |   |
|                            +-------------------------------+   |
|                                                                 |  <- Chat area (scrollable)
+-----------------------------------------------------------------+
|  +---------------------------------------------------------+   |
|  |                    -----------                           |   |
|  |         Top diagnosis: Refrigerant leak                  |   |  <- Bottom Sheet
|  |           23 of 200 possible causes                      |   |     (draggable)
|  +---------------------------------------------------------+   |
|  +---------------------------------------------------------+   |
|  | Describe your issue                               (^)   |   |  <- Input (fixed)
|  +---------------------------------------------------------+   |
+-----------------------------------------------------------------+
```

#### State Machine

```typescript
interface ChatMessage {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  timestamp: Date;
}

interface DiagnosisState {
  topDiagnosis: string | null;
  possibleCauses: {
    current: number;  // Narrows down as questions are answered
    total: number;    // Starts at 200
  };
  causes: Array<{
    title: string;
    description: string;
    probability?: number;
  }>;
}

interface ChatScreenState {
  digitalTwin: {
    status: 'loading' | 'complete' | 'error';
    step?: DigitalTwinStep;
  };
  messages: ChatMessage[];
  diagnosis: DiagnosisState;
  input: {
    value: string;
    isSubmitting: boolean;
  };
  bottomSheet: {
    isExpanded: boolean;
  };
}
```

#### Diagnosis Counter Logic

The "X of Y possible causes" counter demonstrates AI narrowing down the diagnosis:

```typescript
// Initial state (before any conversation)
{ current: 200, total: 200 }  // "200 of 200 possible causes"

// After user describes issue
{ current: 23, total: 200 }   // "23 of 200 possible causes"

// After answering follow-up question 1
{ current: 12, total: 200 }   // "12 of 200 possible causes"

// After answering follow-up question 2
{ current: 4, total: 200 }    // "4 of 200 possible causes"

// Note: 'total' can also decrease if causes are definitively ruled out
// e.g., "23 of 45 possible causes" in expanded view
```

#### Component Composition

```typescript
// Patterns/ChatScreen/ChatScreen.tsx
import { Header } from '@/components/Header';
import { DigitalTwinLoader } from '@/patterns/DigitalTwinLoader';
import { ChatBubble } from '@/components/ChatBubble';
import { BottomSheet } from '@/components/BottomSheet';
import { Input } from '@/components/Input';

export function ChatScreen() {
  const [state, dispatch] = useReducer(chatReducer, initialState);
  
  return (
    <div className="flex flex-col h-screen bg-white">
      {/* Sticky Header */}
      <Header />
      
      {/* Digital Twin Loader (persists) */}
      <div className="px-[8px] pt-[8px]">
        <DigitalTwinLoader 
          address={state.address}
          status={state.digitalTwin.status}
          step={state.digitalTwin.step}
        />
      </div>
      
      {/* Scrollable Chat Area */}
      <div className="flex-1 overflow-y-auto p-[24px] space-y-[16px]">
        {state.messages.map((msg) => (
          <ChatBubble key={msg.id} variant={msg.role}>
            {msg.content}
          </ChatBubble>
        ))}
      </div>
      
      {/* Bottom Section */}
      <div className="px-[8px] pb-[24px] pt-[8px]">
        {/* Bottom Sheet */}
        <BottomSheet
          isExpanded={state.bottomSheet.isExpanded}
          onToggle={() => dispatch({ type: 'TOGGLE_BOTTOM_SHEET' })}
          topDiagnosis={state.diagnosis.topDiagnosis}
          possibleCauses={state.diagnosis.possibleCauses}
          causes={state.diagnosis.causes}
        />
        
        {/* Input */}
        <Input
          label="Describe your issue"
          value={state.input.value}
          onChange={(v) => dispatch({ type: 'SET_INPUT', value: v })}
          onSubmit={() => dispatch({ type: 'SUBMIT_MESSAGE' })}
          status={state.input.isSubmitting ? 'loading' : 'idle'}
          elevated
        />
      </div>
    </div>
  );
}
```

#### Story Structure

```typescript
// Patterns/ChatScreen/ChatScreen.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { ChatScreen } from './ChatScreen';

const meta: Meta<typeof ChatScreen> = {
  title: 'Patterns/ChatScreen',
  component: ChatScreen,
  parameters: {
    layout: 'fullscreen',
    viewport: { defaultViewport: 'mobile1' },
    design: {
      type: 'figma',
      url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=97:400',
    },
  },
};

export default meta;
type Story = StoryObj<typeof ChatScreen>;

export const InitialConversation: Story = {
  args: {
    initialState: {
      digitalTwin: { status: 'loading', step: 'service_territory' },
      messages: [
        {
          id: '1',
          role: 'user',
          content: "Hi, I'm hoping you can help me because I'm kind of panicking. My AC unit outside (the big box thing) is making this really loud, angry buzzing sound-like a giant electric bee?",
        },
        {
          id: '2',
          role: 'assistant',
          content: "Based on the 'grinding' noise and the temperature rising, this sounds like a compressor or fan motor issue. I am flagging this job as Priority Dispatch so we can get someone out to you as soon as possible.",
        },
      ],
      diagnosis: {
        topDiagnosis: 'Refrigerant leak',
        possibleCauses: { current: 23, total: 200 },
        causes: [],
      },
    },
  },
};

export const BottomSheetExpanded: Story = {
  args: {
    initialState: {
      // ...same as above
      bottomSheet: { isExpanded: true },
      diagnosis: {
        topDiagnosis: 'Refrigerant leak',
        possibleCauses: { current: 23, total: 45 },
        causes: [
          {
            title: 'Refrigerant leak',
            description: 'Similar to a battery that helps the motor start. When the capacitor fails, the system hums, but won\'t run.',
          },
          {
            title: 'Condenser fan motor',
            description: 'The fan outside isn\'t spinning, causing the compressor to overheat and buzz.',
          },
          {
            title: 'Compressor issue',
            description: 'The \'heart\' of the AC condenser may be struggling. Requires professional testing.',
          },
        ],
      },
    },
  },
  parameters: {
    design: {
      type: 'figma',
      url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=208:983',
    },
  },
};

export const NarrowedDiagnosis: Story = {
  args: {
    initialState: {
      diagnosis: {
        topDiagnosis: 'Condenser fan motor',
        possibleCauses: { current: 4, total: 200 },
        causes: [/* ... */],
      },
    },
  },
};
```

---

### ScheduleScreen (`Patterns/ScheduleScreen`)

#### Narrative

Once the homeowner completes the diagnostic chat, RepairBot prompts them to schedule a repair visit. Clicking the "Schedule" button in the header (or in the chat) takes them to this screen.

**Key interactions:**

1. **Urgent button** -> Prompts the user to call (for emergencies that can't wait)
2. **Time slot selection** -> Clicking a slot changes it to the green "selected" state, but does NOT advance to the next screen. User must click "Next" to proceed.
3. **Show more** -> Reveals availability for additional days. Can only be clicked **3 times** maximum, then the button disappears.
4. **"Want a different time?"** -> Prompts the user to call for custom scheduling

The 4-hour window is intentional - we explain this upfront so users understand we're allocating extra time for thorough diagnosis and repair.

#### Screen Layout

```
+-----------------------------------------------------------------+
|  <- Back            [Tetra] Tetra            [ Urgent? [phone] ]          |
+-----------------------------------------------------------------+
|                                                                 |
|  Availability for today                                         |
|  Why 4-hour window? We allocate extra time so our experts       |
|  never have to rush your diagnosis and repair.                  |
|                                                                 |
|  +---------------------------------------------------------+   |
|  | 8am - 12pm                                        $99   |   |  <- Selected (green)
|  +---------------------------------------------------------+   |
|  +---------------------------------------------------------+   |
|  | 12pm - 4pm                                        $99   |   |  <- Default (white)
|  +---------------------------------------------------------+   |
|  +---------------------------------------------------------+   |
|  | After 6pm                                         $149  |   |
|  | Includes additional dispatch fee.                       |   |
|  +---------------------------------------------------------+   |
|                                                                 |
|  Availability for tomorrow                                      |
|                                                                 |
|  +---------------------------------------------------------+   |
|  | Before 8am                                        $149  |   |
|  | Includes additional dispatch fee.                       |   |
|  +---------------------------------------------------------+   |
|  +---------------------------------------------------------+   |
|  | 8am - 12pm                                        $99   |   |
|  +---------------------------------------------------------+   |
|                                                                 |
|                      [ Show more ]                              |
|                                                                 |
|  +---------------------------------------------------------+   |
|  | [user] Want a different time? Give us a call.          ->   |   |
|  +---------------------------------------------------------+   |
|                                                                 |
+-----------------------------------------------------------------+
|  +---------------------------------------------------------+   |
|  |                        Next                              |   |  <- Primary CTA
|  +---------------------------------------------------------+   |
+-----------------------------------------------------------------+
```

#### Header Styling

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Background | `#FFFFFF` |
| | Height | 60px |
| | Border | none (no bottom border on this screen) |
| **Back button** | Font | Sharp Grotesk Medium 20, 12px |
| | Color | `#566376` |
| | Icon | Chevron left, `#566376` |
| **Urgent button** | Background | `#F3F5F7` |
| | Border radius | 6.17px |
| | Padding | 9.25px |
| | Text color | `#202E05` |
| | Font | Sharp Grotesk Medium 20, 12.3px |
| | Icon | Phone, `#202E05` |

#### Section Headers

| Element | Property | Value |
|---------|----------|-------|
| **Day title** | Font | Sharp Grotesk Medium 20, 14px |
| | Color | black |
| | Example | "Availability for today" |
| **Explanation text** | Font | Sharp Grotesk Book 20, 10px |
| | Color | `#566376` |
| | Line height | 16.5px |

#### TimeSlotCard Component

##### Visual States

| State | Background | Border | Shadow | Time Font Weight |
|-------|------------|--------|--------|------------------|
| `default` | `#FFFFFF` | 1.2px solid `#E1E5EA` | `0px 4px 4px rgba(0,0,0,0.25)` | Book 20 (400) |
| `selected` | `#ECFAD1` | 2px solid `#B4EB47` | `0px 4px 4px rgba(0,0,0,0.25)` | Medium 20 (500) |

##### Styling Details

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

##### Props (Args)

| Arg | Type | Default | Description |
|-----|------|---------|-------------|
| `timeRange` | `string` | - | e.g., "8am - 12pm", "After 6pm", "Before 8am" |
| `price` | `number` | - | Price in dollars |
| `isSelected` | `boolean` | `false` | Green selected state |
| `note` | `string \| null` | `null` | Optional note like "Includes additional dispatch fee." |
| `onClick` | `() => void` | - | Selection handler |

##### Implementation

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

#### Show More Button

| Property | Value |
|----------|-------|
| Background | `#FFFFFF` |
| Border | 2px solid `#E1E5EA` |
| Border radius | 8px |
| Padding | 4px vertical, 16px horizontal |
| Font | Sharp Grotesk Medium 20, 12px |
| Text color | `#15191E` |

**Behavior:** 
- Initially visible
- Each click reveals the next day's availability
- After **3 clicks**, the button disappears (maximum 5 days shown: today + 4 more)

#### "Want a different time?" Card

| Element | Property | Value |
|---------|----------|-------|
| **Container** | Background | `#F3F5F7` |
| | Border radius | 6px |
| | Padding | 7px |
| | Gap | 8px |
| **Avatar** | Size | 37px |
| | Border | 1.23px solid white |
| | Border radius | full |
| | Phone badge | 13px, bottom-right |
| **Text** | Font | Sharp Grotesk Book 20, 13px |
| | Color | black |
| | Line height | 1.7 |
| **Arrow button** | Background | `#E1E5EA` |
| | Size | 38px |
| | Border radius | 4px |
| | Icon | Arrow right |

**Behavior:** Clicking triggers a phone call prompt (same as Urgent button).

#### Primary CTA (Next Button)

| Property | Value |
|----------|-------|
| Background | `#B4EB47` |
| Border radius | 8px |
| Padding | 8px |
| Font | Sharp Grotesk Medium, 14px |
| Text color | `#202E05` |
| Line height | 20px |

**Behavior:** 
- Disabled (visually same, but non-interactive) until a time slot is selected
- When clicked with a selection, advances to confirmation screen

#### Sticky Footer

| Property | Value |
|----------|-------|
| Background | `#FFFFFF` |
| Border top | 1px solid `rgba(0,0,0,0.1)` |
| Padding | 8px top, 48px bottom (safe area), 16px horizontal |

#### State Machine

```typescript
interface TimeSlot {
  id: string;
  date: string;          // ISO date
  dayLabel: string;      // "today", "tomorrow", or formatted date
  timeRange: string;     // "8am - 12pm"
  price: number;
  note?: string;
  available: boolean;
}

interface ScheduleScreenState {
  selectedSlotId: string | null;
  visibleDays: number;        // Starts at 2 (today + tomorrow)
  maxDays: number;            // Total available days (e.g., 5)
  showMoreClicks: number;     // Max 3
  slots: TimeSlot[];
}

type ScheduleAction =
  | { type: 'SELECT_SLOT'; slotId: string }
  | { type: 'SHOW_MORE' }
  | { type: 'CALL_URGENT' }
  | { type: 'CALL_DIFFERENT_TIME' }
  | { type: 'GO_BACK' }
  | { type: 'SUBMIT' };

function scheduleReducer(state: ScheduleScreenState, action: ScheduleAction): ScheduleScreenState {
  switch (action.type) {
    case 'SELECT_SLOT':
      return { ...state, selectedSlotId: action.slotId };
    
    case 'SHOW_MORE':
      if (state.showMoreClicks >= 3) return state;
      return {
        ...state,
        visibleDays: Math.min(state.visibleDays + 1, state.maxDays),
        showMoreClicks: state.showMoreClicks + 1,
      };
    
    case 'CALL_URGENT':
    case 'CALL_DIFFERENT_TIME':
      // Trigger phone call
      window.location.href = 'tel:+1-XXX-XXX-XXXX';
      return state;
    
    case 'GO_BACK':
      // Navigate back to chat
      return state;
    
    case 'SUBMIT':
      if (!state.selectedSlotId) return state;
      // Navigate to confirmation screen
      return state;
    
    default:
      return state;
  }
}
```

#### Slot Grouping Logic

```typescript
// Group slots by day for rendering
function groupSlotsByDay(slots: TimeSlot[], visibleDays: number): Map<string, TimeSlot[]> {
  const grouped = new Map<string, TimeSlot[]>();
  const visibleDates = new Set(
    slots.map(s => s.date).slice(0, visibleDays)
  );
  
  for (const slot of slots) {
    if (!visibleDates.has(slot.date)) continue;
    
    const existing = grouped.get(slot.dayLabel) || [];
    grouped.set(slot.dayLabel, [...existing, slot]);
  }
  
  return grouped;
}
```

#### Story Structure

```typescript
// Patterns/ScheduleScreen/ScheduleScreen.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { ScheduleScreen } from './ScheduleScreen';

const meta: Meta<typeof ScheduleScreen> = {
  title: 'Patterns/ScheduleScreen',
  component: ScheduleScreen,
  parameters: {
    layout: 'fullscreen',
    viewport: { defaultViewport: 'mobile1' },
    design: {
      type: 'figma',
      url: 'https://www.figma.com/design/kOxblDpHcb5SeIdxXpekyA/RepairBot?node-id=199:695',
    },
  },
};

export default meta;
type Story = StoryObj<typeof ScheduleScreen>;

export const Default: Story = {
  args: {
    slots: [
      { id: '1', date: '2026-01-02', dayLabel: 'today', timeRange: '8am - 12pm', price: 99, available: true },
      { id: '2', date: '2026-01-02', dayLabel: 'today', timeRange: '12pm - 4pm', price: 99, available: true },
      { id: '3', date: '2026-01-02', dayLabel: 'today', timeRange: 'After 6pm', price: 149, note: 'Includes additional dispatch fee.', available: true },
      { id: '4', date: '2026-01-03', dayLabel: 'tomorrow', timeRange: 'Before 8am', price: 149, note: 'Includes additional dispatch fee.', available: true },
      { id: '5', date: '2026-01-03', dayLabel: 'tomorrow', timeRange: '8am - 12pm', price: 99, available: true },
    ],
    selectedSlotId: null,
  },
};

export const WithSelection: Story = {
  args: {
    ...Default.args,
    selectedSlotId: '1',
  },
};

export const ShowMoreMaxed: Story = {
  args: {
    ...Default.args,
    showMoreClicks: 3,
    visibleDays: 5,
  },
  parameters: {
    docs: {
      description: {
        story: 'After clicking Show More 3 times, the button disappears.',
      },
    },
  },
};
```

---

## Open Questions

| # | Question | Context | Owner |
|---|----------|---------|-------|
| 1 | **Backend API Pattern for Digital Twin Creation** | The current implementation assumes Server-Sent Events (SSE) for real-time step progress updates. Does the backend already emit step events, or is it a single long-running request that returns after 10-20s? Alternative approaches: polling, WebSocket, or simulated client-side step progression with a single completion callback. | Engineering |
| 2 | **Error State Screens** | What happens when an address is outside the service area? Need Figma screens for error states to document the error UI pattern. | Design |
