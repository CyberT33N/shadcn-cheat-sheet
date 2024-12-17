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
