# shadcn-cheat-sheet


# Install

<br><br>

## Vite
- https://ui.shadcn.com/docs/installation/vite









<br><br>
<br><br>
_____________________________________
_____________________________________
<br><br>
<br><br>

# components.json
- https://ui.shadcn.com/docs/components-json
- The components.json file holds configuration for your project.












<br><br>
<br><br>
_____________________________________
_____________________________________
<br><br>
<br><br>


## Dark Mode

<br><br>

### Vite

<details><summary>Click to expand..</summary>

1. Create a theme provider
- components/theme-provider.tsx
```typescript
import { createContext, useContext, useEffect, useState } from "react"

type Theme = "dark" | "light" | "system"

type ThemeProviderProps = {
  children: React.ReactNode
  defaultTheme?: Theme
  storageKey?: string
}

type ThemeProviderState = {
  theme: Theme
  setTheme: (theme: Theme) => void
}

const initialState: ThemeProviderState = {
  theme: "system",
  setTheme: () => null,
}

const ThemeProviderContext = createContext<ThemeProviderState>(initialState)

export function ThemeProvider({
  children,
  defaultTheme = "system",
  storageKey = "vite-ui-theme",
  ...props
}: ThemeProviderProps) {
  const [theme, setTheme] = useState<Theme>(
    () => (localStorage.getItem(storageKey) as Theme) || defaultTheme
  )

  useEffect(() => {
    const root = window.document.documentElement

    root.classList.remove("light", "dark")

    if (theme === "system") {
      const systemTheme = window.matchMedia("(prefers-color-scheme: dark)")
        .matches
        ? "dark"
        : "light"

      root.classList.add(systemTheme)
      return
    }

    root.classList.add(theme)
  }, [theme])

  const value = {
    theme,
    setTheme: (theme: Theme) => {
      localStorage.setItem(storageKey, theme)
      setTheme(theme)
    },
  }

  return (
    <ThemeProviderContext.Provider {...props} value={value}>
      {children}
    </ThemeProviderContext.Provider>
  )
}

export const useTheme = () => {
  const context = useContext(ThemeProviderContext)

  if (context === undefined)
    throw new Error("useTheme must be used within a ThemeProvider")

  return context
}
```

2. Wrap your root layout
- Add the ThemeProvider to your root layout.
  
App.tsx
```typescript
import { ThemeProvider } from "@/components/theme-provider"
 
function App() {
  return (
    <ThemeProvider defaultTheme="dark" storageKey="vite-ui-theme">
      {children}
    </ThemeProvider>
  )
}
 
export default App
```

3. Add a mode toggle
- Place a mode toggle on your site to toggle between light and dark mode.


components/mode-toggle.tsx
```typescript
import { Moon, Sun } from "lucide-react"

import { Button } from "@/components/ui/button"
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"
import { useTheme } from "@/components/theme-provider"

export function ModeToggle() {
  const { setTheme } = useTheme()

  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button variant="outline" size="icon">
          <Sun className="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
          <Moon className="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
          <span className="sr-only">Toggle theme</span>
        </Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent align="end">
        <DropdownMenuItem onClick={() => setTheme("light")}>
          Light
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("dark")}>
          Dark
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("system")}>
          System
        </DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  )
}
```



# Dark Mode Styling Guide
<details><summary>Click to expand..</summary>

## Grundlegende Farbklassen

### Text & Hintergrund
```css
/* Basis Text und Hintergrund */
bg-background dark:bg-background  /* Hintergrundfarbe */
text-foreground dark:text-foreground  /* Textfarbe */

/* Weiß/Schwarz Kontrast */
text-black dark:text-white  /* Maximaler Kontrast */
```

### Karten & Container
```css
/* Karten-Styling */
bg-card dark:bg-card/90  /* Kartenhintergrund mit leichter Transparenz im Dark Mode */
text-card-foreground dark:text-card-foreground  /* Kartentext */

/* Container-Styling */
bg-muted dark:bg-muted  /* Gedämpfter Hintergrund */
text-muted-foreground dark:text-muted-foreground  /* Gedämpfter Text */
```

### Interaktive Elemente
```css
/* Buttons */
hover:bg-accent dark:hover:bg-muted  /* Hover-Effekt */
active:bg-accent/90 dark:active:bg-muted/90  /* Aktiv-Zustand */

/* Inputs */
bg-background dark:bg-background  /* Input Hintergrund */
text-foreground dark:text-foreground  /* Input Text */
```

## Komponenten-spezifische Styles

### Kanban Board
```css
/* Hauptcontainer */
.kanban-board {
    @apply bg-background dark:bg-background text-foreground;
}

/* Spalten */
.kanban-column {
    @apply bg-card dark:bg-card/90 p-4 rounded-lg shadow-sm;
}

/* Spalten-Header */
.kanban-header {
    @apply bg-background dark:bg-card rounded p-2;
}

/* Karten */
.kanban-card {
    @apply bg-background dark:bg-muted rounded-lg shadow-sm;
}
```

### Text-Hierarchie
```css
/* Überschriften */
.heading {
    @apply text-foreground dark:text-white font-bold;
}

/* Normaler Text */
.text {
    @apply text-foreground dark:text-foreground;
}

/* Sekundärer Text */
.text-secondary {
    @apply text-muted-foreground dark:text-muted-foreground;
}
```

## Farbvariablen (aus index.css)

### Light Mode
```css
:root {
    --background: 0 0% 100%;      /* Weiß */
    --foreground: 222.2 84% 4.9%; /* Fast Schwarz */
    --card: 0 0% 100%;           /* Weiß */
    --muted: 210 40% 96.1%;      /* Helles Grau */
    --accent: 210 40% 96.1%;     /* Akzentfarbe */
}
```

### Dark Mode
```css
.dark {
    --background: 222.2 84% 4.9%; /* Fast Schwarz */
    --foreground: 210 40% 98%;    /* Fast Weiß */
    --card: 222.2 84% 4.9%;      /* Fast Schwarz */
    --muted: 217.2 32.6% 17.5%;  /* Dunkles Grau */
    --accent: 217.2 32.6% 17.5%; /* Akzentfarbe */
}
```

## Best Practices

1. **Immer Paare verwenden**: Jede Farb-Klasse sollte eine dark:-Variante haben
   ```css
   className="bg-white dark:bg-black"
   ```

2. **Kontrast beachten**: Im Dark Mode ausreichend Kontrast für Lesbarkeit sicherstellen
   ```css
   className="text-gray-800 dark:text-gray-100"
   ```

3. **Opacity nutzen**: Für subtile Unterschiede Opacity verwenden
   ```css
   className="bg-card/90 dark:bg-card/80"
   ```

4. **Hierarchie durch Farben**: Wichtige Elemente durch stärkeren Kontrast hervorheben
   ```css
   className="text-muted-foreground dark:text-white"
   ```






</details>

   
</details>

































<br><br>
<br><br>
_____________________________________
_____________________________________
<br><br>
<br><br>


# Third Party

<details><summary>Click to expand..</summary>

<br><br>
<br><br>


# Libs and Components


| Name                         | Beschreibung                                                                                                                                                     | Link                                                                                       |
|------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| aceternity-ui **HOT**               | Copy paste the most trending react components without having to worry about styling and animations.                                                              | [aceternity-ui](https://ui.aceternity.com/)  - https://github.com/CyberT33N/aceternity-ui-cheat-sheet/blob/main/README.md                                            |
| assistant-ui                 | React Components for AI Chat.                                                                                                                                   | [assistant-ui](https://github.com/Yonom/assistant-ui)                                    |
| autocomplete-select-shadcn-ui| Autocomplete component built with shadcn/ui and Fancy Multi Select by Maximilian Kaske.                                                                       | [autocomplete-select-shadcn-ui](https://www.armand-salle.fr/post/autocomplete-select-shadcn-ui) |
| auto-form                    | A React component that automatically creates a shadcn/ui form based on a zod schema.                                                                            | [auto-form](https://github.com/vantezzen/auto-form)                                        |
| capture-photo              | Capture-Photo is a versatile, browser-based React component designed to streamline the integration of camera functionalities directly into your web applications. | [capture-photo](https://github.com/UretzkyZvi/capture-photo)                                |
| clerk-elements                | Composable components that can be used to build custom UIs on top of Clerk’s APIs.                                                                                  | [clerk-elements](https://clerk.com/docs/elements/examples/shadcn-ui)                          |
| clerk-shadcn-theme           | Easily synchronize your Clerk `<SignIn />` and `<SignUp />` components with your shadcn/ui styles.                                                              | [clerk-shadcn-theme](https://github.com/stormynight9/clerk-shadcn-theme)                     |
| confirm-dialog               | A confirm dialog component built with shadcn/ui.                                                                                                                  | [confirm-dialog](https://github.com/Aslam97/react-confirm-dialog)                            |
| country-state-dropdown       | This Component is built with Nextjs, Tailwindcss, shadcn/ui & Zustand for state management.                                                                     | [country-state-dropdown](https://github.com/Jayprecode/country-state-dropdown)             |
| cult-ui                      | A well curated set of animated shadcn-style React components for more specific use-cases.                                                                      | [cult-ui](https://www.cult-ui.com/)                                                        |
| credenza                     | Ready-made responsive modal component for shadcn/ui.                                                                                                              | [credenza](https://github.com/redpangilinan/credenza)                                      |
| crypto-charts                | Crypto charts made for shadcn/ui using PythNetwork.                                                                                                              | [crypto-charts](https://github.com/jstnw10/crypto-charts)                                    |
| date-range-picker-for-shadcn | Includes multi-month views, text entry, preset ranges, responsive design, and date range comparisons.                                                           | [date-range-picker-for-shadcn](https://github.com/johnpolacek/date-range-picker-for-shadcn) |
| date-time-picker-shadcn      | Beautifully Crafted Datetime Picker for your shadNext Project.                                                                                                 | [date-time-picker-shadcn](https://shadcn-datetime-picker.vercel.app/)                       |
| downshift-shadcn-combobox    | Combobox/autocomplete component built with shadcn/ui and Downshift.                                                                                            | [downshift-shadcn-combobox](https://github.com/TheOmer77/downshift-shadcn-combobox)       |
| echo-editor                  | A modern WYSIWYG rich-text editor base on tiptap and shadcn/ui                                                                                               | [echo-editor](https://github.com/Seedsa/echo-editor)                                       |
| edil-ozi  **HOT**                   | React components for developers. Gsap + framer motion + tailwind.                                                                                                | [edil-ozi](https://edilozi.pro/) - https://github.com/CyberT33N/edilozi-cheat-sheet/blob/main/README.md                                                           |
| emblor                       | A highly customizable, accessible, and fully-featured tag input component built with shadcn/ui.                                                                | [emblor](https://github.com/JaleelB/emblor)                                                 |
| enhanced-button              | An enhanced version of the default shadcn-button component.                                                                                                    | [enhanced-button](https://github.com/jakobhoeg/enhanced-button)                              |
| fancy-area                   | The Textarea is inspired by GitHub’s PR comment section. The impressive part is the @mention support including hover cards in the preview.                   | [fancy-area](https://craft.mxkaske.dev/post/fancy-area)                                    |
| fancy-box                    | The Combobox is inspired by GitHub’s PR label selector and is powered by shadcn/ui.                                                                          | [fancy-box](https://craft.mxkaske.dev/post/fancy-box)                                     |
| fancy-multi-select           | The Multi Select Component is inspired by campsite.design’s and cal.com’s settings forms.                                                                   | [fancy-multi-select](https://craft.mxkaske.dev/post/fancy-multi-select)                     |
| fancy-switch                 | A fancy switch component built with shadcn/ui.                                                                                                                 | [fancy-switch](https://github.com/Aslam97/react-fancy-switch)                             |
| farmui                       | A shadcn and tailwindcss based beautifully styled and animated component library solution with its own npm package.                                           | [farmui](https://farmui.com/)                                                             |
| file-uploader                | A file uploader built with shadcn/ui and react-dropzone.                                                                                                      | [file-uploader](https://github.com/sadmann7/file-uploader)                                |
| file-vault                   | File upload component for React.                                                                                                                                | [file-vault](https://github.com/ManishBisht777/file-vault)                                 |
| fusion-ui                    | Fusion UI library combining shadcn/ui and MagicUI.                                                                                                               | [fusion-ui](https://github.com/nyxb-ui/ui)                                               |
| ibelick/background-snippet  | Ready to use collection of modern background snippets.                                                                                                        | [ibelick/background-snippet](https://bg.ibelick.com/)                                    |
| indie-ui                     | UI components with variants - Docs                                                                                                                            | [indie-ui](https://github.com/Ali-Hussein-dev/indie-ui)                                   |
| lukacho-ui                   | Next Generation UI Components                                                                                                                                   | [lukacho-ui](https://ui.lukacho.com/components)                                             |
| magicui                      | React components to build beautiful landing pages using tailwindcss + framer motion + shadcn/ui                                                                | [magicui](https://magicui.design/)                                                         |
| maily.to                     | Craft beautiful emails effortlessly with notion like powerful editor.                                                                                             | [maily.to](https://github.com/arikchakma/maily.to)                                         |
| minimal-tiptap               | A minimal WYSIWYG editor built with shadcn/ui and tiptap.                                                                                                    | [minimal-tiptap](https://github.com/Aslam97/shadcn-minimal-tiptap)                       |
| mixcnui                      | Mixui is a collection of animated or re-usable component currently for Nextjs.                                                                                   | [mixcnui](https://github.com/taqui-786/mixcnui)                                          |
| mynaui                       | TailwindCSS and shadcn/ui UI Kit for Figma and React.                                                                                                         | [mynaui](https://mynaui.com/)                                                             |
| neobrutalism-components      | Collection of neobrutalism-styled Tailwind React and shadcn/ui components.                                                                                       | [neobrutalism-components](https://github.com/ekmas/neobrutalism-components)                |
| nextjs-components            | A collection of Next.js components build with TypeScript, React, shadcn/ui, Craft UI, and Tailwind CSS.                                                           | [nextjs-components](https://components.bridger.to/)                                      |
| nextjs-dnd                   | Sortable Drag and Drop with Next.js, shadcn/ui, and dnd-kit.                                                                                                      | [nextjs-dnd](https://github.com/sujjeee/nextjs-dnd)                                        |
| novel                        | Novel is a Notion-style WYSIWYG editor with AI-powered autocompletion. Built with Tiptap + Vercel AI SDK.                                                      | [novel](https://github.com/steven-tey/novel)                                            |
| password-input               | shadcn/ui custom password input.                                                                                                                                  | [password-input](https://gist.github.com/mjbalcueva/b21f39a8787e558d4c536bf68e267398)    |
| phone-input-shadcn-ui        | Custom phone number component built with shadcn/ui.                                                                                                              | [phone-input-shadcn-ui](https://www.armand-salle.fr/post/phone-input-shadcn-ui)            |
| planner                      | Planner is a highly adaptable scheduling component tailored for React applications.                                                                                | [planner](https://github.com/UretzkyZvi/planner)                                        |
| plate                        | The rich-text editor for React.                                                                                                                                   | [plate](https://github.com/udecode/plate)                                                |
| pricing-page-shadcn          | Pricing Page made with shadcn/ui & Next.js 14. Completely customizable.                                                                                               | [pricing-page-shadcn](https://github.com/m4nute/pricing-page-shadcn)                      |
| progress-button              | An extension of shadcn/ui button component that uses a state machine to drive a progress UX.                                                                      | [progress-button](https://github.com/tomredman/ProgressButton)                              |
| react-dnd-kit-tailwind-shadcn-ui | Drag and drop Accessible kanban board implementing using React, dnd-kit, tailwind, and shadcn/ui.                                                                      | [react-dnd-kit-tailwind-shadcn-ui](https://github.com/Georgegriff/react-dnd-kit-tailwind-shadcn-ui)  |
| react-select               | Implementation of the react-select library with shadcn styling. Support for Select, Async-Select, Multi-Select with many configurable options    | [react-select](https://gist.github.com/ilkou/7bf2dbd42a7faf70053b43034fc4b5a4)                 |
| search-address               | The SearchAddress component provides a flexible and interactive search interface for addresses, utilizing the powerful Nominatim service from OpenStreetMap.   | [search-address](https://github.com/UretzkyZvi/search-address)                                 |
| shadcn-address-autocomplete  | An address autocomplete component built with Google Places API and shadcn components.                                                                           | [shadcn-address-autocomplete](https://github.com/NiazMorshed2007/shadcn-address-autocomplete)     |
| shadcn-blocks                | Blocks is the official shadcn/ui pre-made but customizable components that can be copied and pasted into your projects.                                            | [shadcn-blocks](https://ui.shadcn.com/blocks)                                            |
| shadcn-cal                   | A copy of the monthly calendar used by Cal.com with shadcn/ui, Radix Colors and React Aria.                                                                      | [shadcn-cal](https://shadcn-cal-com.vercel.app/?date=2024-04-29)                            |
| shadcn-calendar-heatmap       | Modern alternative to primitive react heatmaps built on top of shadcn/ui calendar component.                                                                    | [shadcn-calendar-heatmap](https://shadcn-calendar-heatmap.vercel.app/)                     |
| shadcn-calendar-component    | A calendar date picker component designed with shadcn/ui.                                                                                                        | [shadcn-calendar-component](https://github.com/sersavan/shadcn-calendar-component)          |
| shadcn-chat                 | Customizable and reusable chat component for you to use in your projects.                                                                                         | [shadcn-chat](https://github.com/jakobhoeg/shadcn-chat)                                   |
| shadcn-carousel-testimonials | shadcn UI Carousel Testimonials.                                                                                                                                  | [shadcn-carousel-testimonials](https://github.com/johanguse/shadcn-carousel-testimonials)    |
| shadcn-data-table-advanced-col-opions | Column-resizing option to shadcn/ui DataTable.                                                                                                       | [shadcn-data-table-advanced-col-opions](https://github.com/danielagg/shadcn-data-table-advanced-col-opions)   |
| shadcn-date-picker           | Advanced date picker with range selection, year and month selection, and more.                                                                                 | [shadcn-date-picker](https://date-picker.luca-felix.com/)                                   |
| shadcn-drag-table            | A drag-and-drop table component using shadcn/ui and Next.js.                                                                                                     | [shadcn-drag-table](https://github.com/zenoncao/shadcn-drag-table)                            |
| shadcn-extends               | Intended to be a collection of components built using shadcn/ui.                                                                                                  | [shadcn-extends](https://github.com/lucioew28/extends)                                     |
| shadcn-extension            | An open-source component collection that extends your UI library, built using shadcn/ui components.                                                              | [shadcn-extension](https://github.com/BelkacemYerfa/shadcn-extension)                      |
| shadcn-image-cropper         | A minimal image cropper UI built with shadcn and the react-image-crop library.                                                                                 | [shadcn-image-cropper](https://github.com/sujjeee/shadcn-image-cropper)                    |
| shadcn-linear-combobox      | A copy of the combobox that Linear uses to set the priority of a task.                                                                                         | [shadcn-linear-combobox](https://github.com/damianricobelli/shadcn-linear-combobox)         |
| shadcn-multi-select-component| A multi-select component designed with shadcn/ui.                                                                                                             | [shadcn-multi-select-component](https://github.com/sersavan/shadcn-multi-select-component) |
| shadcn-phone-input-2         | Simple and formatted phone input component built with shadcn/ui y libphonenumber-js.                                                                             | [shadcn-phone-input-2](https://github.com/damianricobelli/shadcn-phone-input)             |
| shadcn-phone-input           | Customizable phone input component with proper validation for any country.                                                                                        | [shadcn-phone-input](https://github.com/omeralpi/shadcn-phone-input)                          |
| shadcn-stepper               | A complete stepper component built with shadcn/ui.                                                                                                                | [shadcn-stepper](https://github.com/damianricobelli/shadcn-stepper)                        |
| shadcn-table-v2             | shadcn/ui table component with server-side sorting, filtering, and pagination.                                                                                 | [shadcn-table-v2](https://github.com/sadmann7/shadcn-table)                                |
| shadcn-timeline              | Customizable and re-usable timeline component for you to use in your projects. Built on top of shadcn.                                                           | [shadcn-timeline](https://github.com/timDeHof/shadcn-timeline)                              |
| shadcn-ui-blocks             | A collection of Over 10+ fully responsive, UI blocks you can drop into your shadcn/ui projects and customize to your heart’s content.                            | [shadcn-ui-blocks](https://shadcn-ui-blocks.vercel.app/)                                 |
| shadcn-ui-expansions         | A lots of useful components which shadcn/ui does not have out of the box.                                                                                      | [shadcn-ui-expansions](https://github.com/hsuanyi-chou/shadcn-ui-expansions)                |
| shadcn-ui-sidebar            | A stunning, functional and responsive retractable sidebar built on top of shadcn/ui.                                                                            | [shadcn-ui-sidebar](https://github.com/salimi-my/shadcn-ui-sidebar)                        |
| simplekit                    | Responsive connect wallet and account component built on top of Wagmi and shadcn/ui.                                                                           | [simplekit](https://github.com/vaunblu/SimpleKit)                                          |
| sortable                     | A sortable component built with shadcn/ui, radix ui, and dnd-kit.                                                                                                  | [sortable](https://github.com/sadmann7/sortable)                                           |
| stocks                       | Stock Picker using Next.js, React Server Components, and shadcn/ui charts.                                                                                      | [stocks](https://github.com/aryanvichare/stocks)                                          |
| time-picker                  | A simple TimePicker for your shadcn/ui project.                                                                                                                   | [time-picker](https://github.com/openstatusHQ/time-picker)                                |
| tremor-raw                   | Copy & paste React components to build modern web applications. Good for building charts.                                                                      | [tremor-raw](https://github.com/tremorlabs/tremor-raw)                                    |
| uixmat/onborda               | Give your application the onboarding it deserves with Onborda product tour for Next.js                                                                             | [uixmat/onborda](https://github.com/uixmat/onborda)                                         |
| zoom-charts                  | Zoomable Charts on top of shadcn/ui Charts.                                                                                                                        | [zoom-charts](https://github.com/shelwinsunga/zoom-chart-demo)                            |





<br><br>
<br><br>


# Plugins and Extensions


| Name                                  | Beschreibung                                                                                                                                          | Link                                                                                                         |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| **chat-with-youtube**                 | Chrome-Erweiterung zum effizienten Zusammenfassen von Videos, Durchsuchen spezifischer Teile und weiteren nützlichen Funktionen.                     | [chat-with-youtube](https://chat-with-youtube.vercel.app/)                                                   |
| **designgui**                         | Chrome-Browser-Erweiterung zum Verwalten von Farben in CSS-Variablen.                                                                                | [designgui](https://www.designgui.io/)                                                                       |
| **raycast-shadcn**                    | Raycast-Erweiterung, um die shadcn/ui-Dokumentation, Komponenten und Beispiele zu durchsuchen.                                                       | [raycast-shadcn](https://www.raycast.com/luisFilipePT/shadcn-ui)                                             |
| **shadcn-ui**                         | Komponenten direkt aus shadcn/ui in VS Code hinzufügen.                                                                                              | [shadcn-ui](https://marketplace.visualstudio.com/items?itemName=SuhelMakkad.shadcn-ui)                       |
| **shadcn/ui Components Manager**      | JetBrains-Plugin zur Verwaltung von shadcn/ui-Komponenten in Svelte-, React-, Vue- und Solid-Projekten.                                              | [shadcn/ui Components Manager](https://plugins.jetbrains.com/plugin/23479-shadcn-ui-components-manager)      |
| **vscode-shadcn-svelte**              | VS Code-Erweiterung für shadcn/ui-Komponenten in Svelte-Projekten.                                                                                   | [vscode-shadcn-svelte](https://marketplace.visualstudio.com/items?itemName=Selemondev.vscode-shadcn-svelte)  |
| **vscode-shadcn-ui-snippets**         | Snippets für die einfache Verwendung von shadcn-ui-Komponenten in VS Code (jsx/tsx).                                                                | [vscode-shadcn-ui-snippets](https://marketplace.visualstudio.com/items?itemName=VeroXyle.shadcn-ui-snippets) |
| **vscode-shadcn-vue**                 | VS Code-Erweiterung zur Integration von shadcn/ui-Komponenten in Vue.js-Projekte.                                                                    | [vscode-shadcn-vue](https://marketplace.visualstudio.com/items?itemName=Selemondev.vscode-shadcn-vue)        |






<br><br>
<br><br>



# Colors and Customizations



| Name                                           | Beschreibung                                                                                                 | Link                                                                                                     |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| **10000+Themes for shadcn/ui**                | Über 10.000 Themes für shadcn/ui.                                                                           | [10000+Themes for shadcn/ui](https://ui.jln.dev/)                                                        |
| **dizzy**                                     | Bootstrap für neue Next- oder Vite-Projekte mit shadcn/ui. Anpassung von Schriftarten, Icons, Farben, etc.  | [dizzy](https://dizzy.systems/)                                                                         |
| **gradient-picker**                           | Fancy Gradient Picker mit shadcn/ui, Radix UI und Tailwind CSS.                                             | [GitHub - gradient-picker](https://github.com/Illyism/gradient-picker)                                   |
| **navnote/rangeen**                           | Tool zur Erstellung einer Farbpallette für Websites.                                                        | [GitHub - navnote/rangeen](https://github.com/navnote/rangeen)                                           |
| **shadcn-ui-customizer**                      | POC für shadcn/ui-Themes mit Farbwählern.                                                                   | [GitHub - shadcn-ui-customizer](https://github.com/Railly/shadcn-ui-customizer)                          |
| **ui-colorgen**                               | Anwendung zur Unterstützung der Farbanpassung für shadcn/ui.                                                | [ui-colorgen](https://ui-colorgen.vercel.app/)                                                           |
| **zippy starter’s shadcn/ui theme generator** | Einfaches Erstellen von benutzerdefinierten Themes mit Farben, die in Apps eingefügt werden können.         | [zippy starter’s shadcn/ui theme generator](https://zippystarter.com/tools/shadcn-ui-theme-generator)    |


<br><br>
<br><br>



# Animations


| Name                | Beschreibung                                                                                          | Link                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| **magicui.design**  | Größte Sammlung von Open-Source-React-Komponenten zum Erstellen schöner Landingpages.                 | [magicui.design](https://magicui.design/)                                                     |
| **motionvariants**  | Wunderschöne Animationen mit Framer Motion.                                                          | [GitHub - motionvariants](https://github.com/chrisabdo/motionvariants)                        |


<br><br>
<br><br>



# Tools


| Name | Beschreibung | Link |
|------|--------------|------|
| **5devs** | Website für das Erstellen von Fake-Daten für Brasilien zu Testzwecken. | [5devs](https://www.5devs.com.br/) |
| **cut-it** | Linkverkürzer mit Next.js, Drizzle ORM, Turso und shadcn/ui. | [cut-it](https://github.com/mehrabmp/cut-it) |
| **DownDevDetector** | App, die aktuell ausgefallene Services auflistet (u. a. Atlassian Status Page). | [DownDevDetector](https://github.com/birobirobiro/downdevdetector) |
| **CV Forge** | Lebenslauf-Builder mit shadcn/ui, react-hook-form und react-pdf. | [CV Forge](https://cvforge.app/) |
| **form-builder** | UI-Codegenerator für sichere und schöne shadcn/ui-Formulare. | [form-builder](https://github.com/AlandSleman/FormBuilder) |
| **imgsrc** | Tool zur einfachen Erstellung von Open Graph Bildern. | [imgsrc](https://imgsrc.io/) |
| **invoify** | Rechnungs-Generator mit Next.js, TypeScript und shadcn/ui. | [invoify](https://github.com/aliabb01/invoify) |
| **JobSync** | Job-Management-Tool für effiziente Jobsuche. | [JobSync](https://github.com/Gsync/jobsync) |
| **MemFree** | Hybride Suchmaschine mit KI-Unterstützung, erstellt mit Next.js und shadcn/ui. | [MemFree](https://github.com/memfreeme/memfree) |
| **metro-sp** | Echtzeit-Updates zu Vorfällen im SP-Metro-System. | [metro-sp](https://github.com/birobirobiro/metro-sp-front) |
| **myinstants** | Größte Website für Sofort-Soundbuttons in Brasilien. | [myinstants](https://www.myinstants.xyz/) |
| **pastecode** | Pastebin-Alternative mit Next.js, TypeScript und Drizzle ORM. | [pastecode](https://github.com/Quorin/PasteCode.app) |
| **proxmox-helper-scripts** | Script-Katalog für Proxmox VE Homelabs. | [proxmox-helper-scripts](https://github.com/BramSuurdje/proxmox-helper-scripts) |
| **QuackDB** | Open-Source In-Browser DuckDB SQL Editor. | [QuackDB](https://github.com/mattf96s/QuackDB) |
| **qualmeuip** | Website zur Anzeige der eigenen IP-Adresse und Geschwindigkeitstests. | [qualmeuip](https://www.qualmeuip.xyz/) |
| **shadcn-pricing-page-generator** | Generator für Pricing Pages mit React und Tailwind CSS. | [shadcn-pricing-page-generator](https://shipixen.com/shadcn-pricing-page) |
| **translate-app** | Übersetzungs-App mit Next.js, Tailwind CSS und OpenAI SDK. | [translate-app](https://github.com/developaul/translate-app) |
| **typelabs** | MonkeyType-inspirierter Tipp-Test mit React und Zustand. | [typelabs](https://github.com/imsandeshpandey/typelabs) |
| **v0** | Generatives UI-System von Vercel für Text- und Bild-basierte UIs. | [v0](https://v0.dev/) |
| **Xuneix** | Linkrotation-Tool für Sicherheitsadministration, integriert mit Vercel KV. | [Xuneix](https://xuneix.theteleporter.me/) |



<br><br>
<br><br>


# Platform


| Name           | Beschreibung                                                                 | Link                                                                              |
|----------------|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| anonypost      | Share your thoughts and experiences anonymously by posting on the platform. Crafted using t3-stack. | [GitHub](https://github.com/avalynndev/anonypost)                                 |
| bolhadev       | The quickest path to learn English is speaking it regularly. Just find someone to chat with.         | [Website](https://bolhadev.chat/)                                                |
| enjoytown      | A free anime, manga, movie, TV-shows streaming platform. Built with Nextjs, shadcn/ui.               | [GitHub](https://github.com/avalynndev/enjoytown)                                 |
| Grade Calculator | A grade calculator/dashboard for students, aiming to provide a better overview of academic performance. | [Website](https://grades.nstr.dev/)                                              |
| infinitunes    | A Simple Music Player Web App built using Next.js, shadcn/ui, Tailwind CSS, DrizzleORM and more…      | [GitHub](https://github.com/rajput-hemant/infinitunes)                            |
| kd             | Ad-free Kdrama streaming app. Built with Nextjs, Drizzle ORM, NeonDB and shadcn/ui.                  | [GitHub](https://github.com/gneiru/kd)                                           |
| plotwist       | Easy management and reviews of your movies, series, and animes using Next.js, Tailwind CSS, Supabase and shadcn/ui. | [Website](https://plotwist.app/en-US)                                           |


<br><br>
<br><br>


# Ports

| Framework / Sprache      | Beschreibung                                                         | Link                                                                                     |
|---------------------------|---------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| Angular                  | Angular port of shadcn/ui                                          | [GitHub](https://github.com/goetzrobin/spartan)                                          |
| Flutter                  | Flutter port of shadcn/ui                                          | [GitHub](https://github.com/nank1ro/shadcn-ui)                                          |
| Franken UI               | HTML-first, framework-agnostic, beautifully designed components    | [Website](https://www.franken-ui.dev/)                                                  |
| JollyUI                  | shadcn/ui compatible React Aria components                        | [GitHub](https://github.com/jolbol1/jolly-ui)                                           |
| Kotlin                   | Kotlin port of shadcn/ui                                          | [GitHub](https://github.com/dead8309/shadcn-kotlin)                                     |
| Phoenix Liveview         | Phoenix Liveview port of shadcn/ui                                | [GitHub](https://github.com/bluzky/salad_ui)                                            |
| React Native             | React Native port of shadcn/ui                                    | [GitHub](https://github.com/Mobilecn-UI/nativecn-ui)                                    |
| React Native (empfohlen) | React Native port of shadcn/ui (recommended)                      | [GitHub](https://github.com/mrzachnugent/react-native-reusables)                        |
| Ruby                     | Ruby port of shadcn/ui                                            | [GitHub](https://github.com/aviflombaum/shadcn-rails)                                   |
| Solid                    | Solid port of shadcn/ui                                           | [GitHub](https://github.com/hngngn/shadcn-solid)                                        |
| Svelte                   | Svelte port of shadcn/ui                                          | [GitHub](https://github.com/huntabyte/shadcn-svelte)                                    |
| Swift                    | Swift port of shadcn/ui                                           | [GitHub](https://github.com/Mobilecn-UI/swiftcn-ui)                                     |
| Vue                      | Vue port of shadcn/ui                                             | [GitHub](https://github.com/radix-vue/shadcn-vue)                                       |





<br><br>
<br><br>


# Design System


| Name                          | Beschreibung                                                                                                             | Link                                                                                                          |
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| **shadcn-ui-components**      | Alle shadcn/ui-Komponenten in Figma nachgebaut.                                                                          | [Figma-Link](https://www.figma.com/community/file/1342715840824755935/shadcn-ui-components)                   |
| **shadcn-ui-storybook**       | Alle shadcn/ui-Komponenten im Storybook, erstellt von JheanAntunes.                                                      | [Storybook von JheanAntunes](https://65711ecf32bae758b457ae34-uryqbzvojc.chromatic.com/)                      |
| **shadcn-ui-storybook**       | Alle shadcn/ui-Komponenten im Storybook, erstellt von fellipeutaka.                                                      | [Storybook von fellipeutaka](https://fellipeutaka-ui.vercel.app/?path=%2Fdocs%2Fcomponents-accordion--docs)   |


<br><br>
<br><br>



# Boilerplates / Templates

| Name | Beschreibung | Link |
|------|--------------|------|
| **chadnext** | Starter-Template mit Next.js 14, shadcn/ui, LuciaAuth, Prisma, Stripe, Internationalisierung und mehr. | [chadnext](https://github.com/moinulmoin/chadnext) |
| **cloudflare-saas-stack** | Batteries-included Starterkit für SaaS-Produkte auf Cloudflare. | [cloudflare-saas-stack](https://github.com/Dhravya/cloudflare-saas-stack) |
| **design-system-template** | Template mit Turborepo, TailwindCSS, Storybook und shadcn/ui. | [design-system-template](https://github.com/arevalolance/design-system-template) |
| **electron-shadcn** | Electron-App-Template mit shadcn/ui und weiteren Tools. | [electron-shadcn](https://github.com/LuanRoger/electron-shadcn) |
| **horizon-ai-nextjs-shadcn-boilerplate** | Premium AI-Template mit Next.js, shadcn/ui, Stripe, Supabase und OAuth. | [horizon-ai-nextjs-shadcn-boilerplate](https://horizon-ui.com/boilerplate-shadcn) |
| **kirimase** | Boilerplate für Projekte mit shadcn/ui, Tailwind CSS und Next.js. | [kirimase](https://kirimase.dev/) |
| **magicui-startup-templates** **HOT** | Startup-Template mit shadcn/ui, TailwindCSS und framer-motion. | [magicui-startup-templates](https://magicui.design/docs/templates/startup) |
| **next-shadcn-dashboard-starter** | Admin-Dashboard-Template mit Next.js 14 und shadcn/ui. | [next-shadcn-dashboard-starter](https://github.com/Kiranism/next-shadcn-dashboard-starter) |
| **nextjs-mdx-blog** | Blog-Template mit Contentlayer, MDX, shadcn/ui und Tailwind CSS. | [nextjs-mdx-blog](https://github.com/ChangoMan/nextjs-mdx-blog) |
| **shadcn-landing-page** | Landing-Page-Template mit shadcn/ui, React, TypeScript und Tailwind CSS. | [shadcn-landing-page](https://github.com/leoMirandaa/shadcn-landing-page) |
| **shadcn-nextjs-free-boilerplate** | Kostenloses Next.js-Template mit ChatGPT-API-Dashboard. | [shadcn-nextjs-free-boilerplate](https://github.com/horizon-ui/shadcn-nextjs-boilerplate) |
| **shadcn-vue-landing-page** | Landing-Page-Template mit Vue, shadcn-vue, TypeScript und Tailwind CSS. | [shadcn-vue-landing-page](https://github.com/leoMirandaa/shadcn-vue-landing-page) |
| **t3-app-template** | Admin-Template für T3 Stack und shadcn/ui. | [t3-app-template](https://github.com/gaofubin/t3-app-template) |
| **taxonomy** | Open-Source-Anwendung mit Next.js-Features wie Server-Komponenten und neuem Router. | [taxonomy](https://github.com/shadcn/taxonomy) |
| **turborepo-shadcn-ui-tailwindcss** | Turborepo-Starter mit shadcn/ui und TailwindCSS für Shared UI-Komponenten. | [turborepo-shadcn-ui-tailwindcss](https://github.com/henriqpohl/turborepo-shadcn-ui-tailwindcss) |
| **turborepo-launchpad** | Monorepo-Boilerplate für shadcn-Projekte mit Turbo und Shared Components. | [turborepo-launchpad](https://github.com/JadRizk/turborepo-launchpad) |
| **Supabase authentication starter template** | Template für Supabase-Authentifizierung mit Next.js, TypeScript und shadcn/ui. | [Supabase authentication starter template](https://github.com/Sahil-Sharma-23/supa-next-shad-auth) |  







</details>
