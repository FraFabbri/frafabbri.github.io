---
layout: default
title: Publications
description: Full list of Francesco Fabbri's peer-reviewed papers, preprints, and thesis.
permalink: /publications/
---

<section class="section-pad">
  <div class="stitle">Publications</div>
  <div class="ssub">Peer-reviewed papers, preprints, and thesis. Newest first.</div>

  {%- assign all_pubs = site.data.timeline | where: "type", "publication" | sort: "date" | reverse -%}
  {%- assign current_year = "" -%}

  <ol class="pub-list">
    {%- for entry in all_pubs -%}
      {%- assign date_parts = entry.date | split: "-" -%}
      {%- assign year = date_parts[0] -%}
      {%- if year != current_year -%}
        {%- assign current_year = year -%}
        <li class="pub-year-marker">{{ year }}</li>
      {%- endif -%}
      <li class="pub-row">
        <div class="pub-title">
          {%- if entry.award == "Best Paper" %}<span class="pub-award">★ Best Paper</span> · {% endif -%}
          {%- if entry.links.arxiv -%}<a href="{{ entry.links.arxiv }}">{{ entry.title }}</a>
          {%- elsif entry.links.spotify_research -%}<a href="{{ entry.links.spotify_research }}">{{ entry.title }}</a>
          {%- elsif entry.links.paper -%}<a href="{{ entry.links.paper }}">{{ entry.title }}</a>
          {%- else -%}{{ entry.title }}{%- endif -%}
        </div>
        <div class="pub-meta">
          {%- for author in entry.authors -%}
            {%- if author == "F. Fabbri" -%}<strong>{{ author }}</strong>{%- else -%}{{ author }}{%- endif -%}
            {%- unless forloop.last %}, {% endunless -%}
          {%- endfor -%}
          · <em>{{ entry.venue }}</em>
        </div>
      </li>
    {%- endfor -%}
  </ol>
</section>
