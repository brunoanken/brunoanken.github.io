---
title: 'Automatically clearing flash messages in Phoenix LiveView '
description: 'A simple and maintainable way to automatically clear flash messages in Phoenix LiveView.'
pubDate: '2025-08-10'
featured: true
released: true
# TODO: add a hero image?
heroImage: '../../images/2025-08-09-auto-clearing-liveview-flash-messages.png'
---

Phoenix LiveView is an awesome and elegant way to create web apps with a simple stack. Its generators are super capable and can get a lot done with simple commands, but one thing that has always bothered me is that flash messages don't disappear on their own after a few seconds.

To address this, I created a straightforward hook that fades the message after 5 seconds and also clears the flash message from the LiveView channel connection. Let’s dive into it!

```javascript
// app.js
let liveSocket = new LiveSocket("/live", Socket, {
  // ...
  hooks: {
    AutoClearFlash: {
      mounted() {
        let ignoredIDs = ["client-error", "server-error"];
        if (ignoredIDs.includes(this.el.id)) return;

        let hideElementAfter = 5000; // ms
        let clearFlashAfter = hideElementAfter + 500; // ms

        // first hide the element
        setTimeout(() => {
          this.el.style.opacity = 0;
        }, hideElementAfter);

        // then clear the flash
        setTimeout(() => {
          this.pushEvent("lv:clear-flash");
        }, clearFlashAfter);
      },
    },
  },
});
```

```elixir
# core_components.ex
def flash(assigns) do
  # ...
  phx-hook="AutoClearFlash"
  {@rest}
  # ...
end
```

Since the `client-error` and `server-error` messages display important information about the app's status and connectivity I prefer to ignore them.

The first step is to set a timeout to change the message's opacity to 0, making the message disappear from the UI. Combine that with transition effects for more elegant user experience (in my flash messages I use the following Tailwind classes: transition-opacity duration-300).

Then we set another timeout, but this time to send an event ("lv:clear-flash") to the server in order to clear the flash message. It is triggered some milliseconds after the hide message timeout in order to give the transition effect enough time to complete.

And that's it!

*This was originally published on https://dev.to/brunoanken/automatically-clearing-flash-messages-in-phoenix-liveview-2g7n*
