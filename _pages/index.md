---
layout: landing
title: TeXt Theme
excerpt: >
  A super customizable Jekyll theme for personal site, team site, blog, project, documentation, etc.
permalink: /index.html

article_header:
  actions:
    - text: Getting Started
      type: error
      url: /docs/en/quick-start
    - text: Demo
      type: outline-theme-dark
      url: /test/
  height: 100vh
  theme: dark
  background_color: "#367a9a"
  background_image:
    gradient: "linear-gradient(rgba(0, 0, 0, .2), rgba(0, 0, 0, .6))"
    src: /docs/assets/images/cover4.jpg
data:
  sections:
    - title: Fully Responsive
      excerpt: This theme will look great on any device, no matter the size!
      theme: dark
      image:
        src: /screenshots/TeXt-responsive.png
      background_color: "#515151"
    - title: Super Customizable
      excerpt: Everything from the menus, sidebars, comments, and more can be configured or set with YAML Front Matter.
      actions:
        - text: See Examples
          url: /samples.html
        - text: Learn More
          url: /docs/en/configuration
      image:
        src: /screenshots/TeXt-layouts.png
        is_row: true
        full_width: true
        style: "max-width: 1200px;"