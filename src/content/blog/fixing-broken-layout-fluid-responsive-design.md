---
title: 'Fixing a broken layout with fluid responsive design'
description: 'Using fluid responsive CSS variables to fix a broken layout and get rid of media queries.'
pubDate: '2025-08-11'
featured: true
released: true
heroImage: '../../images/2025-08-10-fixing-broken-fluid-hero.png'
---

I started this blog using a template I found in Astro's website. I really liked it (no wonder I chose it), but the top header and navbar layout was broken on small screens.
![Broken layout of the blog top header and navbar, on small screen sizes the name (Bruno Anken) overflows outside of it's parent and the menu and theme switcher buttons
become misaligned.](../../images/2025-08-10-broken-blog-header.gif)

We can see my name overflowing outside of its parent and the menu and theme switcher buttons become misaligned when the
width is lesser than `360px`.


This is important because it affects the user experience of the website. One could argue that there aren't many devices out there
with such small screens. Well, my own phone has such a small screen, and I am sure there are many other people out there using
devices with small screens, whether by option or because that's their only choice. And I want to be able to deliver all of them
a decent user experience (even though it's possible that none of them will ever access my blog).


The original code was the following (this project uses Tailwind):

```html
<a href="/" class="font-headings font-black text-3xl">Bruno Anken</a>
```

We could fix the issue by changing the `text-3xl` (`30px`) class to `text-2xl` (`24px`). This would make the text a bit smaller.
It would still break the layout, but only on even smaller screens.


Another approach could be using a media query utility class to shrink the text only on smaller screens, like the following:

```html
<a href="/" class="font-headings font-black text-xl sm:text-3xl">Bruno Anken</a>
```

This way the text size would be `20px` on small screens and `30px` on larger (`> 640px`) screens. This solves the problem!
But there is one other approach that I find very satisfying and elegant, and that's what I want to show you now.

## Fluid Responsive Design

Fluid responsive design is a technique to scale font size and space without using breakpoints. Put in other words, as the screen
size changes, the text and the spacing will scale up or down proportionally as well. Goodbye to media queries break points!

You can read more about it in the [Utopia website](https://utopia.fyi/). It's where I first discovered the concept of fluid
responsive design and it's also the tool I used to generate the fluid spacing and font sizes I used to fix my broken layout.

I generated type and space styles, added them to my Tailwind configuration and applied them to my code to get the following result:

![Fixed layout using the fluid responsive design approach.](../../images/2025-08-10-layout-fixed.gif)

See how the text and spacing scale up and down proportionally? It feels super satisfying! It does require a bit of setup,
but it removes the need for some media queries and breakpoints, which are a big pain to maintain and update (and I usually
forget about them too).

This is what I added to my Tailwind v4 configuration:

```css
/* @link https://utopia.fyi/type/calculator?c=360,16,1.2,1280,20,1.25,5,2,&s=0.75|0.5|0.25,1.5|2|3|4|6,s-l&g=s,l,xl,12 */
--text-fluid--2: clamp(0.6944rem, 0.6531rem + 0.1836vw, 0.8rem);
--text-fluid--1: clamp(0.8333rem, 0.7681rem + 0.2899vw, 1rem);
--text-fluid-0: clamp(1rem, 0.9022rem + 0.4348vw, 1.25rem);
--text-fluid-1: clamp(1.2rem, 1.0582rem + 0.6304vw, 1.5625rem);
--text-fluid-2: clamp(1.44rem, 1.2392rem + 0.8924vw, 1.9531rem);
--text-fluid-3: clamp(1.728rem, 1.4488rem + 1.2407vw, 2.4414rem);
--text-fluid-4: clamp(2.0736rem, 1.6908rem + 1.7011vw, 3.0518rem);
--text-fluid-5: clamp(2.4883rem, 1.9693rem + 2.3067vw, 3.8147rem);

/* @link https://utopia.fyi/space/calculator?c=360,16,1.2,1280,20,1.25,5,2,&s=0.75|0.5|0.25,1.5|2|3|4|6,s-l&g=s,l,xl,12 */
--spacing-fluid-3xs: clamp(0.25rem, 0.2255rem + 0.1087vw, 0.3125rem);
--spacing-fluid-2xs: clamp(0.5rem, 0.4511rem + 0.2174vw, 0.625rem);
--spacing-fluid-xs: clamp(0.75rem, 0.6766rem + 0.3261vw, 0.9375rem);
--spacing-fluid-s: clamp(1rem, 0.9022rem + 0.4348vw, 1.25rem);
--spacing-fluid-m: clamp(1.5rem, 1.3533rem + 0.6522vw, 1.875rem);
--spacing-fluid-l: clamp(2rem, 1.8043rem + 0.8696vw, 2.5rem);
--spacing-fluid-xl: clamp(3rem, 2.7065rem + 1.3043vw, 3.75rem);
--spacing-fluid-2xl: clamp(4rem, 3.6087rem + 1.7391vw, 5rem);
--spacing-fluid-3xl: clamp(6rem, 5.413rem + 2.6087vw, 7.5rem);
```

This allows me to use fluid text classes like

```html
<a href="/" class="font-headings font-black text-fluid-2">Bruno Anken</a>
```

And also spacing classes for gap

```html
<ul class="flex gap-fluid-xs h-full">
...
</ul>
```

And for padding

```html
<div
	id="main-header"
	class="fixed w-full z-50 transition duration-500 px-fluid-xs"
>
```

My implementation isn't perfect. The text continues to grow when the navbar/header already stopped growing. This is because the
fluid sizes are based on the viewport width. Ideally, the content should stop growing when it's parent does as well. Utopia can
generate values that adapt relative to the container and not the viewport, but I have yet to explore how this approach works.

[This is the commit](https://github.com/brunoanken/brunoanken.github.io/commit/cf5adb7574a8635590bb9f2f55997587f4bcd8bb) in which
I added the fluid classes to my blog in case you want to take a look at the entire code.
