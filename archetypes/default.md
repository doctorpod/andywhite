---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
description: ""
date: {{ .Date }}
slug: {{ .File.ContentBaseName }}
draft: true
---
