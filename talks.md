---
layout: default
title: Talks & Media
description: Invited keynotes, workshop talks, academic mentoring, and media coverage of Francesco Fabbri's research.
permalink: /talks/
---

{%- comment -%}
  Month-name lookup macro — reused by both Talks and Media sections.
{%- endcomment -%}

<section class="section-pad">
  <div class="stitle">Talks</div>
  <div class="ssub">Invited keynotes, workshop talks, and academic mentoring. Most recent first.</div>

  {%- assign all_talks = site.data.timeline | where: "type", "talk" | sort: "date" | reverse -%}

  <ul class="pub-list">
    {%- for entry in all_talks -%}
      {%- assign date_parts = entry.date | split: "-" -%}
      {%- assign year = date_parts[0] -%}
      {%- assign month_num = date_parts[1] -%}
      {%- assign month_name = "" -%}
      {%- case month_num -%}
        {%- when "01" -%}{%- assign month_name = "Jan" -%}
        {%- when "02" -%}{%- assign month_name = "Feb" -%}
        {%- when "03" -%}{%- assign month_name = "Mar" -%}
        {%- when "04" -%}{%- assign month_name = "Apr" -%}
        {%- when "05" -%}{%- assign month_name = "May" -%}
        {%- when "06" -%}{%- assign month_name = "Jun" -%}
        {%- when "07" -%}{%- assign month_name = "Jul" -%}
        {%- when "08" -%}{%- assign month_name = "Aug" -%}
        {%- when "09" -%}{%- assign month_name = "Sep" -%}
        {%- when "10" -%}{%- assign month_name = "Oct" -%}
        {%- when "11" -%}{%- assign month_name = "Nov" -%}
        {%- when "12" -%}{%- assign month_name = "Dec" -%}
      {%- endcase -%}
      <li class="pub-row">
        <div class="pub-title">
          {{ entry.title }} ·
          {%- if entry.link %} <a href="{{ entry.link }}"><strong>{{ entry.venue }}</strong></a>
          {%- else %} <strong>{{ entry.venue }}</strong>{%- endif -%}
        </div>
        <div class="pub-meta">{{ month_name }} {{ year }}</div>
      </li>
    {%- endfor -%}
  </ul>
</section>

{%- assign all_media = site.data.timeline | where: "type", "media" | sort: "date" | reverse -%}
{%- if all_media.size > 0 -%}
<section class="section-pad">
  <div class="stitle">Media coverage</div>
  <div class="ssub">Research blog posts, articles, and press featuring my work.</div>

  <ul class="pub-list">
    {%- for entry in all_media -%}
      {%- assign date_parts = entry.date | split: "-" -%}
      {%- assign year = date_parts[0] -%}
      {%- assign month_num = date_parts[1] -%}
      {%- assign month_name = "" -%}
      {%- case month_num -%}
        {%- when "01" -%}{%- assign month_name = "Jan" -%}
        {%- when "02" -%}{%- assign month_name = "Feb" -%}
        {%- when "03" -%}{%- assign month_name = "Mar" -%}
        {%- when "04" -%}{%- assign month_name = "Apr" -%}
        {%- when "05" -%}{%- assign month_name = "May" -%}
        {%- when "06" -%}{%- assign month_name = "Jun" -%}
        {%- when "07" -%}{%- assign month_name = "Jul" -%}
        {%- when "08" -%}{%- assign month_name = "Aug" -%}
        {%- when "09" -%}{%- assign month_name = "Sep" -%}
        {%- when "10" -%}{%- assign month_name = "Oct" -%}
        {%- when "11" -%}{%- assign month_name = "Nov" -%}
        {%- when "12" -%}{%- assign month_name = "Dec" -%}
      {%- endcase -%}
      <li class="pub-row">
        <div class="pub-title">
          {%- if entry.link -%}<a href="{{ entry.link }}">{{ entry.title }}</a>{%- else -%}{{ entry.title }}{%- endif -%}
          {%- if entry.outlet %} · <strong>{{ entry.outlet }}</strong>{%- endif -%}
        </div>
        <div class="pub-meta">{{ month_name }} {{ year }}</div>
      </li>
    {%- endfor -%}
  </ul>
</section>
{%- endif -%}
