---
layout: default
title: Talks
description: Invited keynotes, workshop talks, and academic mentoring by Francesco Fabbri.
permalink: /talks/
---

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
        <div class="pub-title">{{ entry.title }} · <strong>{{ entry.venue }}</strong></div>
        <div class="pub-meta">{{ month_name }} {{ year }}</div>
      </li>
    {%- endfor -%}
  </ul>
</section>
