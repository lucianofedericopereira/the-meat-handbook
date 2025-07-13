---
title: Introduction
---

# Introduction. Nice to Meat You

JavaScript is not short on frameworks. From the ubiquitous React and Vue to micro-libraries like Alpine and Nano, the modern developer has no shortage of architectural opinions competing for mindshare. Yet amid this noise, the simplest tools often fall through the cracks.

Meat, the Mitt Enhanced Application Toolkit, is a lightweight, reactive state manager and event bus with plugin extensibility. It's not trying to reinvent the DOM or abstract away your business logic. It doesn’t come with JSX, a virtual DOM, or custom compiler transforms. What it does offer is precision—a well-defined set of methods centered around predictable state updates, scoped event handling, and opt-in mutability. It’s roughly 300 lines of code, intentionally so.

This book is a practical guide to understanding Meat's internals, using it effectively, and extending it responsibly. If you want a deeper understanding of how small tools can yield big architectural wins, you're in the right place.

We’ll begin by exploring Meat’s architectural core: the state object and the eventBus system. From there, we’ll build up toward plugin authoring, state serialization, and debugging workflows. You’ll learn how to incorporate Meat into real applications with minimal friction—and how to do so without relying on build tools or transpilation. Each concept is grounded in annotated source and concrete examples.

You won’t find a toy framework or throwaway experiment here. Meat is opinionated, yes—but those opinions are rooted in runtime clarity and developer autonomy. If your favorite part of Redux was combineReducers, and your least favorite was everything else, you’ll probably enjoy what Meat has to offer.

This book assumes you’re comfortable with JavaScript and have built at least one client-side application using any framework. You don’t need experience with compiler internals or state machines—though you may develop an appreciation for both by the end.

Welcome to Meat. It’s not here to replace your stack. It’s here to sharpen it.

### Why Meat Exists

Meat was created to address a recurring frustration in frontend development: the growing complexity of state and event handling in small to mid-sized applications. While full-featured frameworks offer powerful abstractions, they often demand significant mental overhead, configuration, and runtime costs—especially when the problem at hand is modest.

In many cases, developers simply need a reactive store, scoped events, and a plugin system to extend functionality. That’s it. No templates, no JSX, no virtual DOM diffing. **Meat exists to be exactly that: a focused tool that does one thing well without introducing architectural debt.**

The inspiration behind Meat stems from the simplicity of libraries like [Mitt](https://github.com/developit/mitt)—a tiny event emitter—and a desire to apply that clarity to state mutation and data flow. Unlike more elaborate patterns such as Flux or Redux, Meat favors **direct API access** and **observable state transitions**, giving developers both power and visibility.

Meat is:

- **Minimal**: Under 300 lines of code with zero dependencies.
- **Modular**: You can extend it with plugins, but nothing is enforced.
- **Transparent**: Every method and mutation is traceable and inspectable.
- **Flexible**: Mutable or immutable state, runtime configurable.
- **Accessible**: Drop into a page via CDN, or install via npm—no build step required.

The goal isn’t to compete with established ecosystems. It’s to provide a precision tool for developers who want control without clutter. If your project needs state logic, event orchestration, and plugin extensibility—but not templating engines or hydration strategies—Meat may be the most practical choice.

### Core philosophy

Meat is built on three foundational values: **clarity**, **control**, and **minimalism**. Each principle informs the framework’s design decisions, internal architecture, and usage patterns. Collectively, they aim to provide developers with tools that are easy to reason about, powerful when needed, and unobtrusive by default.

#### Clarity

Meat’s API is deliberately small and semantically focused. Each method name reflects its intent, and there is no hidden behavior behind abstraction layers. State updates emit events, event handlers execute synchronously, and plugin APIs expose only what’s explicitly permitted.

Source code transparency is a core concern—Meat is designed to be fully readable in one sitting. Developers who use the framework should feel capable of understanding the entire codebase without needing external tooling or documentation.

#### Control

Meat favors explicit state transitions over magical reactivity. While reactive updates are supported, they are scoped and predictable. State is either mutable or immutable, depending on configuration. Plugins extend functionality, but cannot override internal behavior unless intentionally permitted.

Event listeners are user-defined and modular. Subscriptions can be scoped to keys, executed once, or globally intercepted. This provides granular control over how your application responds to data changes.

#### Minimalism

Meat avoids bundling features that are outside its scope. It doesn’t handle routing, templating, or UI rendering. Instead, it offers a reactive core that you can pair with your preferred view layer or use standalone in vanilla JavaScript environments.

With no external dependencies and no compilation step, Meat can be included directly via a `<script>` tag or installed via npm. Its footprint is minimal, and its cognitive overhead is intentionally low—allowing developers to focus on their application logic rather than framework intricacies.

By aligning with these principles, Meat aims to serve developers who value architectural simplicity and runtime transparency. It’s not the loudest tool in the room. It’s the clearest.


### What You'll Learn

This book is written for developers who want precise control over state, events, and data flow in JavaScript applications—without relying on large frameworks or toolchains. By working through the chapters, you’ll gain a thorough understanding of Meat’s architecture, methods, and plugin interface, enabling you to use it effectively in a range of contexts. You’ll learn:

- How Meat structures and isolates state, and how to configure it for mutable or immutable updates.
- How the event system works behind the scenes, including scoped listeners, wildcard emitters, and reactivity patterns.
- How to serialize, inspect, and persist application state with minimal overhead.
- How to use built-in plugins for DOM binding and localStorage persistence.
- How to write your own plugins that safely extend Meat’s functionality without creating naming conflicts.
- How to debug and audit your application state using Meat’s devtools and introspection methods.
- How to incorporate Meat into small JavaScript projects with no build step or transpilation.

Each chapter is self-contained and focused on a specific part of the framework, with annotated code examples and practical advice. By the end of this book, you’ll be fluent in the Meat API and equipped to decide when and how to integrate it into your own development workflow.

Meat is not a general-purpose solution, nor is it a teaching tool disguised as a framework. It’s a deliberate experiment in clarity and modularity. This book treats it as such—with attention to its design, its limitations, and its possibilities.
<br>
:::note[Open Source]  
<br><strong style="color:#255ac0">MEAT</strong> is an open-source tool, and you can contribute to the project by joining the <a href="https://github.com/lucianofedericopereira/meat">MEAT GitHub repository</a>.
:::
