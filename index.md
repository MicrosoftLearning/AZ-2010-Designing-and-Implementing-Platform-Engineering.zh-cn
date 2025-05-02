---
title: 练习说明
permalink: index.html
layout: home
---

# 练习

此页列出了与 [Microsoft Learn](https://learn.microsoft.com) 上的 Microsoft 技能内容关联的练习

<!-- You can edit the paragraph above to provide a more specific description and links to content on Learn.

Include the following note if an Azure subscription is required (or add something similar for any other requirements, such as a Microsoft 365 account).

> **Note**: To complete these exercises, you will need a [Microsoft Azure subscription](https://azure.microsoft.com/free) in which you have sufficient permissions to create and configure the required resources.

If a more complex setup is required, create a separate markdown file with setup instructions at \Instructions\Labs\00-setup.md - being sure to include "lab.title"" metadata at the top so it shows up the list below
-->

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}

