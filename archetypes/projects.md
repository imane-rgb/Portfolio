---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
description: ""
tags: []
icon: "📄"
filetype: "c"
github: ""
draft: true
# Hardware specs sidebar — list of {key, value} pairs
hw_specs:
  - { key: "MCU",       value: "" }
  - { key: "Clock",     value: "" }
  - { key: "Flash",     value: "" }
  - { key: "RAM",       value: "" }
  - { key: "Protocol",  value: "" }
---

## Overview

<!-- Project description -->

## Architecture

<!-- System diagram or description -->

## Technical Highlights

<!-- Key implementation details -->

{{</* codeblock lang="c" filename="main.c" */>}}
// your code here
{{</* /codeblock */>}}

## Challenges

<!-- What was hard and how you solved it -->
