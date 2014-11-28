# Hugo Syntax

```
{{ partial "header.html" . }}
{{ .Content }}
{{ .TableOfContents }}
{{ .Title }}
{{ .Params.bar }}
```

```
.Data.Singular The singular name of the taxonomy
.Data.Plural The plural name of the taxonomy
.Data.Terms The taxonomy itself
.Data.Terms.Alphabetical The Terms alphabetized
.Data.Terms.ByCount The Terms ordered by popularity
```


## Examples

```
{{ partial "header.html" . }}
    <div id="toc" class="well col-md-4 col-sm-6">
    {{ .TableOfContents }}
    </div>
    <h1>{{ .Title }}</h1>
    {{ .Content }}
{{ partial "footer.html" . }}
```

```
{{ if or (isset .Params "alt") (isset .Params "caption") }} Caption {{ end }}
```

```
{{ $address := "123 Main St."}}
{{ $address }}
```

{{range $element := array}}
    {{ $element }}
{{ end }}
Example 2: Declaring key and value variable name

{{range $index, $element := array}}
    {{ $index }}
    {{ $element }}
{{ end }}


Example 1: if

{{ if isset .Params "title" }}<h4>{{ index .Params "title" }}</h4>{{ end }}
Example 2: if … else

{{ if isset .Params "alt" }}
    {{ index .Params "alt" }}
{{else}}
    {{ index .Params "caption" }}
{{ end }}
Example 3: and & or

{{ if and (or (isset .Params "title") (isset .Params "caption")) (isset .Params "attr")}}
Example 4: with

An alternative way of writing “if” and then referencing the same value is to use “with” instead. with rebinds the context . within its scope, and skips the block if the variable is absent.

The first example above could be simplified as:

{{ with .Params.title }}<h4>{{ . }}</h4>{{ end }}
Example 5: if … else if

{{ if isset .Params "alt" }}
    {{ index .Params "alt" }}
{{ else if isset .Params "caption" }}
    {{ index .Params "caption" }}
{{ end }}
Pipes

One of the most powerful components of Go templates is the ability to stack actions one after another. This is done by using pipes. Borrowed from Unix pipes, the concept is simple, each pipeline’s output becomes the input of the following pipe.

Because of the very simple syntax of Go templates, the pipe is essential to being able to chain together function calls. One limitation of the pipes is that they only can work with a single value and that value becomes the last parameter of the next pipeline.

A few simple examples should help convey how to use the pipe.

Example 1:

{{ if eq 1 1 }} Same {{ end }}
is the same as

{{ eq 1 1 | if }} Same {{ end }}
It does look odd to place the if at the end, but it does provide a good illustration of how to use the pipes.

Example 2:

{{ index .Params "disqus_url" | html }}
Access the page parameter called “disqus_url” and escape the HTML.

Example 3:

{{ if or (or (isset .Params "title") (isset .Params "caption")) (isset .Params "attr")}}
Stuff Here
{{ end }}
Could be rewritten as

{{  isset .Params "caption" | or isset .Params "title" | or isset .Params "attr" | if }}
Stuff Here
{{ end }}
Internet Explorer conditional comments using Pipes

By default, Go Templates remove HTML comments from output. This has the unfortunate side effect of removing Internet Explorer conditional comments. As a workaround, use something like this:

{{ "<!--[if lt IE 9]>" | safeHtml }}
  <script src="html5shiv.js"></script>
{{ "<![endif]-->" | safeHtml }}
Context (a.k.a. the dot)

The most easily overlooked concept to understand about Go templates is that {{ . }} always refers to the current context. In the top level of your template this will be the data set made available to it. Inside of a iteration it will have the value of the current item. When inside of a loop the context has changed. . will no longer refer to the data available to the entire page. If you need to access this from within the loop, you will likely want to set it to a variable instead of depending on the context.

Example:

  {{ $title := .Site.Title }}
  {{ range .Params.tags }}
    <li> <a href="{{ $baseurl }}/tags/{{ . | urlize }}">{{ . }}</a> - {{ $title }} </li>
  {{ end }}
Notice how once we have entered the loop the value of {{ . }} has changed. We have defined a variable outside of the loop so we have access to it from within the loop.

Hugo Parameters

Hugo provides the option of passing values to the template language through the site configuration (for sitewide values), or through the meta data of each specific piece of content. You can define any values of any type (supported by your front matter/config format) and use them however you want to inside of your templates.


Using Content (page) Parameters

In each piece of content, you can provide variables to be used by the templates. This happens in the front matter.

An example of this is used in this documentation site. Most of the pages benefit from having the table of contents provided. Sometimes the TOC just doesn’t make a lot of sense. We’ve defined a variable in our front matter of some pages to turn off the TOC from being displayed.

Here is the example front matter:

---
title: "Permalinks"
date: "2013-11-18"
aliases:
  - "/doc/permalinks/"
groups: ["extras"]
groups_weight: 30
notoc: true
---
Here is the corresponding code inside of the template:

  {{ if not .Params.notoc }}
    <div id="toc" class="well col-md-4 col-sm-6">
    {{ .TableOfContents }}
    </div>
  {{ end }}
  
  
  ```
{{ range where .Data.Pages "Section" "post" }}
   {{ .Content}}
{{ end }}
where and first can be stacked

e.g.

{{ range first 5 (where .Data.Pages "Section" "post") }}
   {{ .Content}}
{{ end }}
```


```  
{{ % highlight html %}}
<section id="main">
  <div>
   <h1 id="title">{{ .Title }}</h1>
    {{ range .Data.Pages }}
        {{ .Render "summary"}}
    {{ end }}
  </div>
</section>
{{ % /highlight %}}
```

