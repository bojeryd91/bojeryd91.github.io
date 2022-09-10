---
layout: default
title: A stunning personal blog Jekyll theme
description: Journal is a stunning personal blog Jekyll theme with an image-focused design.
featured_image: /images/demo/demo-landscape.jpg
---

<section class="intro">

	<div class="wrap">

		<h1>{{ page.title }}</h1>
		<p>{{ page.subtitle }}</p>

	</div>

</section>

<section class="single">

	<div class="wrap">

		{{ include_relative test.md }}

	</div>

</section>
