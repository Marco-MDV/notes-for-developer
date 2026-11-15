# Next.js

Next.js is a framework built on React. It provides file-based routing, shared layouts, and server rendering. This guide uses the App Router and builds a small notes site with a list page, a dynamic detail page, and an interactive button. If you are new to JSX, props, or state, start with the [React notes](React.md).

For the same example in plain JavaScript, see the [JavaScript version](Next.js-JavaScript.md).

## Create a project

Use a supported Node.js LTS release and check the [Next.js installation requirements](https://nextjs.org/docs/app/getting-started/installation).

```sh
npx create-next-app@latest next-notes --ts --app --no-src-dir --use-npm --yes
cd next-notes
npm run dev
```

Open `http://localhost:3000`. Stop the development server before running `npm run build` to check the production build. Run `npm start` after building to serve it locally.

## Pages, layouts, and navigation

With the App Router, a folder inside `app/` represents a URL segment. A `page.tsx` file makes the route available. A `layout.tsx` wraps pages and nested layouts below it, so it can hold shared navigation or a footer.

Add or replace these files in the generated project.

```text
app/
  layout.tsx
  page.tsx
  notes/
    data.ts
    like-button.tsx
    [slug]/
      page.tsx
```

| File | Role |
| --- | --- |
| `app/layout.tsx` | Root layout for every page |
| `app/page.tsx` | Home page at `/` |
| `app/notes/[slug]/page.tsx` | Detail page such as `/notes/routing` |
| `app/notes/data.ts` and `app/notes/like-button.tsx` | Modules imported by pages |

Replace `app/layout.tsx`. The root layout supplies the document's `html` and `body` elements. The exported metadata sets the default browser title and description.

```tsx
import type { Metadata } from 'next';
import Link from 'next/link';
import type { ReactNode } from 'react';

export const metadata: Metadata = {
    title: 'Next.js Notes',
    description: 'A small notes site built with the Next.js App Router.',
};

export default function RootLayout({ children }: { children: ReactNode }) {
    return (
        <html lang="en">
            <body>
                <header>
                    <nav aria-label="Main navigation">
                        <Link href="/">All notes</Link>
                    </nav>
                </header>
                <main>{children}</main>
            </body>
        </html>
    );
}
```

Create `app/notes/data.ts` with two sample notes.

```ts
export const notes = [
    {
        slug: 'routing',
        title: 'Routing',
        body: 'A page file makes a URL available in the App Router.',
    },
    {
        slug: 'components',
        title: 'Server and Client Components',
        body: 'Use a Client Component when you need state or event handlers.',
    },
];
```

Replace `app/page.tsx`. Use `Link` for navigation within the app. It accepts an `href` and supports client-side navigation between routes.

```tsx
import Link from 'next/link';
import { notes } from './notes/data';

export default function HomePage() {
    return (
        <>
            <h1>Next.js Notes</h1>
            <ul>
                {notes.map(note => (
                    <li key={note.slug}>
                        <Link href={`/notes/${note.slug}`}>{note.title}</Link>
                    </li>
                ))}
            </ul>
        </>
    );
}
```

## Server and Client Components

App Router pages and layouts are Server Components by default. They can access server-side data and keep credentials on the server. Add `'use client'` at the top of a module that needs state, event handlers, or browser APIs. Keep this boundary close to the interactive UI and pass values that React can serialize across it.

Create `app/notes/like-button.tsx`. The button's state lives in the browser for this component instance.

```tsx
'use client';

import { useState } from 'react';

export default function LikeButton() {
    const [liked, setLiked] = useState(false);

    return (
        <button type="button" aria-pressed={liked} onClick={() => setLiked(current => !current)}>
            Like this note
        </button>
    );
}
```

## Dynamic routes

Create `app/notes/[slug]/page.tsx`. Square brackets mark a variable URL segment. In current App Router pages, await the `params` promise to read `slug`. Use `notFound()` for an unknown note.

```tsx
import { notFound } from 'next/navigation';
import { notes } from '../data';
import LikeButton from '../like-button';

export function generateStaticParams() {
    return notes.map(({ slug }) => ({ slug }));
}

export default async function NotePage({ params }: { params: Promise<{ slug: string }> }) {
    const { slug } = await params;
    const note = notes.find(entry => entry.slug === slug);

    if (!note) notFound();

    return (
        <article>
            <h1>{note.title}</h1>
            <p>{note.body}</p>
            <LikeButton />
        </article>
    );
}
```

`generateStaticParams()` supplies the known slugs at build time. Visit `/notes/routing` to see a detail page and try the Like button. Visit `/notes/unknown` to see the 404 page.

## Data, loading states, and styles

- **Data fetching.** This example reads local data. For data from an external API, an async Server Component can call `fetch()` and check `response.ok` before reading the body. Decide whether the result should be fresh or cached based on how often the source changes.
- **Loading states.** Add a `loading.tsx` next to a page or layout to provide a fallback while its route content loads. For a smaller section of a page, wrap the async component in `Suspense`.
- **CSS.** Import global styles in the root layout. Use a `.module.css` file for classes scoped to a component. If the project was generated with Tailwind CSS, keep the scaffold's global CSS import in the layout to use its utilities.
- **Images.** Put local images in `public/` and reference them from `/`. For example, `public/cover.jpg` is served at `/cover.jpg`. The `Image` component from `next/image` supports optimization. Supply meaningful alternative text and dimensions for a path-based image, or use `fill` inside a positioned parent.

Official guides for [layouts and pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages), [Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components), [data fetching](https://nextjs.org/docs/app/getting-started/fetching-data), [CSS](https://nextjs.org/docs/app/getting-started/css), and [images](https://nextjs.org/docs/app/getting-started/images).
