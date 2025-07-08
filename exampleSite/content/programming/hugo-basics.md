---
title: "Hugo Basics"
date: 2025-07-08
tags: ["hugo", "static-site", "tutorial"]
---

# Hugo Basics

Hugo is a fast and modern static site generator written in Go, designed for building websites of all kinds, but with a focus on speed and flexibility.

## Key Features

1. **Speed** - Hugo is one of the fastest static site generators available, capable of regenerating an entire site in milliseconds.

2. **Cross-Platform** - Works on macOS, Windows, Linux, FreeBSD, and more.

3. **Versatile** - Hugo supports unlimited content types, taxonomies, menus, dynamic API-driven content, and more.

4. **Themes** - Numerous themes available in the Hugo Themes repository, or create your own.

## Basic Commands

```bash
# Create a new site
hugo new site mysite

# Create content
hugo new posts/my-first-post.md

# Run development server
hugo server -D

# Build site
hugo
```

## Directory Structure

A typical Hugo site structure:

```
├── archetypes/
├── content/
├── data/
├── layouts/
├── static/
├── themes/
└── config.toml
```
