---
title: Printing Data Structures in OCaml
description: One of the first things developers want to do in any language is print
  out the value of a variable. In languages with dynamic typing or extensive reflection,
  this is often a trivial, built-in operation. In OCaml, the story is a bit different.
  Its strong, static type system requires you to be explicit about how to turn a data
  structure into a string representation. There is no universal print or console.log
  that works on any type.
url: https://batsov.com/articles/2025/07/19/printing-data-in-ocaml/
date: 2025-07-19T15:00:00-00:00
preview_image:
authors:
- ""
source:
ignore:
---

<p>One of the first things developers want to do in any language is print out the value of a variable. In languages with dynamic typing or extensive reflection, this is often a trivial, built-in operation. In OCaml, the story is a bit different. Its strong, static type system requires you to be explicit about how to turn a data structure into a string representation. There is no universal <code class="language-plaintext highlighter-rouge">print</code> or <code class="language-plaintext highlighter-rouge">console.log</code> that works on any type.</p>

<p>While this might seem like a limitation, it’s a direct consequence of OCaml’s design philosophy: be explicit and type-safe. The good news is that the community has developed powerful tools to make this process painless. Let’s explore the common approaches.</p>

<p>For our examples, we’ll use the <code class="language-plaintext highlighter-rouge">person</code> record from my previous post and a simple list of integers.</p>

<div class="language-ocaml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"><pre class="lineno">1
2
3
4
5
6
7
8
9
10
</pre></td><td class="rouge-code"><pre><span class="k">type</span> <span class="n">person</span> <span class="o">=</span> <span class="p">{</span>
  <span class="n">name</span><span class="o">:</span> <span class="kt">string</span><span class="p">;</span>
  <span class="n">age</span><span class="o">:</span> <span class="kt">int</span><span class="p">;</span>
  <span class="n">is_developer</span><span class="o">:</span> <span class="kt">bool</span><span class="p">;</span>
<span class="p">}</span>

<span class="k">let</span> <span class="n">people</span> <span class="o">=</span> <span class="p">[</span>
  <span class="p">{</span> <span class="n">name</span> <span class="o">=</span> <span class="s2">"Bozhidar Batsov"</span><span class="p">;</span> <span class="n">age</span> <span class="o">=</span> <span class="mi">42</span><span class="p">;</span> <span class="n">is_developer</span> <span class="o">=</span> <span class="bp">true</span> <span class="p">};</span>
  <span class="p">{</span> <span class="n">name</span> <span class="o">=</span> <span class="s2">"Jane Doe"</span><span class="p">;</span> <span class="n">age</span> <span class="o">=</span> <span class="mi">34</span><span class="p">;</span> <span class="n">is_developer</span> <span class="o">=</span> <span class="bp">false</span> <span class="p">};</span>
<span class="p">]</span>
</pre></td></tr></tbody></table></code></pre></div></div>

<h3>Approach 1: Manual Printer Functions</h3>

<p>The most fundamental approach is to write a dedicated function to print your data structure. This gives you complete control over the output format.</p>

<p>For our <code class="language-plaintext highlighter-rouge">person</code> record, the function would look like this:</p>

<div class="language-ocaml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"><pre class="lineno">1
2
3
4
</pre></td><td class="rouge-code"><pre><span class="k">let</span> <span class="n">print_person</span> <span class="n">p</span> <span class="o">=</span>
  <span class="nn">Printf</span><span class="p">.</span><span class="n">printf</span>
    <span class="s2">"{ name = </span><span class="se">\"</span><span class="s2">%s</span><span class="se">\"</span><span class="s2">; age = %d; is_developer = %b }"</span>
    <span class="n">p</span><span class="o">.</span><span class="n">name</span> <span class="n">p</span><span class="o">.</span><span class="n">age</span> <span class="n">p</span><span class="o">.</span><span class="n">is_developer</span>
</pre></td></tr></tbody></table></code></pre></div></div>

<p>Here, we use the <code class="language-plaintext highlighter-rouge">Printf</code> module to create a formatted string. For a list, you typically combine a printer for the element type with <code class="language-plaintext highlighter-rouge">List.iter</code>.</p>

<div class="language-ocaml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"><pre class="lineno">1
2
3
4
5
6
</pre></td><td class="rouge-code"><pre><span class="k">let</span> <span class="n">print_int_list</span> <span class="n">lst</span> <span class="o">=</span>
  <span class="n">print_string</span> <span class="s2">"["</span><span class="p">;</span>
  <span class="nn">List</span><span class="p">.</span><span class="n">iter</span> <span class="p">(</span><span class="k">fun</span> <span class="n">i</span> <span class="o">-&gt;</span> <span class="nn">Printf</span><span class="p">.</span><span class="n">printf</span> <span class="s2">"%d; "</span> <span class="n">i</span><span class="p">)</span> <span class="n">lst</span><span class="p">;</span>
  <span class="n">print_string</span> <span class="s2">"]"</span>

<span class="n">print_int_list</span> <span class="p">[</span><span class="mi">1</span><span class="p">;</span> <span class="mi">2</span><span class="p">;</span> <span class="mi">3</span><span class="p">];</span> <span class="c">(* [1; 2; 3; ] *)</span>
</pre></td></tr></tbody></table></code></pre></div></div>

<p>To print our list of people, we can compose these two ideas:</p>

<div class="language-ocaml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"><pre class="lineno">1
2
3
4
5
6
7
8
9
10
</pre></td><td class="rouge-code"><pre><span class="k">let</span> <span class="n">print_people</span> <span class="n">lst</span> <span class="o">=</span>
  <span class="n">print_string</span> <span class="s2">"[</span><span class="se">\n</span><span class="s2">"</span><span class="p">;</span>
  <span class="nn">List</span><span class="p">.</span><span class="n">iter</span> <span class="p">(</span><span class="k">fun</span> <span class="n">p</span> <span class="o">-&gt;</span>
    <span class="n">print_string</span> <span class="s2">"  "</span><span class="p">;</span>
    <span class="n">print_person</span> <span class="n">p</span><span class="p">;</span>
    <span class="n">print_string</span> <span class="s2">";</span><span class="se">\n</span><span class="s2">"</span>
  <span class="p">)</span> <span class="n">lst</span><span class="p">;</span>
  <span class="n">print_string</span> <span class="s2">"]"</span>

<span class="n">print_people</span> <span class="n">people</span><span class="p">;</span>
</pre></td></tr></tbody></table></code></pre></div></div>

<p><strong>Trade-offs:</strong></p>

<ul>
  <li><strong>Pros:</strong> No external dependencies. You have full control over the formatting, which is great for producing user-facing output. It forces you to think about the structure of your data.</li>
  <li><strong>Cons:</strong> It’s incredibly verbose. Writing these printers for every new type is tedious and error-prone. If you add a field to your record, you must remember to update the printer, or it will be silently ignored.</li>
</ul>

<h3>Approach 2: Automatic Derivation with PPX</h3>

<p>To eliminate the boilerplate of manual printers, the OCaml community relies on PPX (Preprocessor eXtensions). A PPX is a tool that rewrites the abstract syntax tree (AST) of your code at compile time. We can use this to automatically generate printer functions from our type definitions.</p>

<p>The most popular choice for this is <code class="language-plaintext highlighter-rouge">ppx_deriving_show</code>.</p>

<p>First, you need to have it installed (<code class="language-plaintext highlighter-rouge">opam install ppx_deriving_show</code>) and configured in your build system (e.g., Dune).</p>

<p>Once set up, you simply annotate your type definition with <code class="language-plaintext highlighter-rouge">[@@deriving show]</code>.</p>

<div class="language-ocaml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"><pre class="lineno">1
2
3
4
5
</pre></td><td class="rouge-code"><pre><span class="k">type</span> <span class="n">person</span> <span class="o">=</span> <span class="p">{</span>
  <span class="n">name</span><span class="o">:</span> <span class="kt">string</span><span class="p">;</span>
  <span class="n">age</span><span class="o">:</span> <span class="kt">int</span><span class="p">;</span>
  <span class="n">is_developer</span><span class="o">:</span> <span class="kt">bool</span><span class="p">;</span>
<span class="p">}</span> <span class="p">[</span><span class="o">@@</span><span class="n">deriving</span> <span class="n">show</span><span class="p">]</span>
</pre></td></tr></tbody></table></code></pre></div></div>

<p>This single line of code does two things:</p>

<ol>
  <li>It generates a “pretty-printer” function named <code class="language-plaintext highlighter-rouge">pp_person</code>. This function takes a formatter and a value (e.g., <code class="language-plaintext highlighter-rouge">pp_person Format.std_formatter bbatsov</code>).</li>
  <li>It generates a <code class="language-plaintext highlighter-rouge">show_person</code> function that returns the string representation directly (e.g., <code class="language-plaintext highlighter-rouge">show_person bbatsov</code>).</li>
</ol>

<p>Now, printing is trivial:</p>

<div class="language-ocaml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"><pre class="lineno">1
2
3
4
</pre></td><td class="rouge-code"><pre><span class="k">let</span> <span class="n">bbatsov</span> <span class="o">=</span> <span class="p">{</span> <span class="n">name</span> <span class="o">=</span> <span class="s2">"Bozhidar Batsov"</span><span class="p">;</span> <span class="n">age</span> <span class="o">=</span> <span class="mi">42</span><span class="p">;</span> <span class="n">is_developer</span> <span class="o">=</span> <span class="bp">true</span> <span class="p">};;</span>

<span class="n">print_endline</span> <span class="p">(</span><span class="n">show_person</span> <span class="n">bbatsov</span><span class="p">);</span>
<span class="c">(* Output: { name = "Bozhidar Batsov"; age = 42; is_developer = true } *)</span>
</pre></td></tr></tbody></table></code></pre></div></div>

<p>The real power of <code class="language-plaintext highlighter-rouge">ppx_deriving_show</code> is that it’s compositional. It automatically knows how to print lists, options, and other standard types if the element type has a <code class="language-plaintext highlighter-rouge">show</code> function.</p>

<div class="language-ocaml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"><pre class="lineno">1
2
3
4
5
6
7
8
9
10
11
12
13
14
</pre></td><td class="rouge-code"><pre><span class="c">(* We need to tell the PPX how to show a list of people *)</span>
<span class="c">(* The generated function will be `show_list` *)</span>
<span class="p">[</span><span class="o">@@@</span><span class="n">deriving</span> <span class="n">show</span> <span class="p">{</span> <span class="n">with_path</span> <span class="o">=</span> <span class="bp">false</span> <span class="p">}]</span>

<span class="k">let</span> <span class="n">people</span> <span class="o">=</span> <span class="p">[</span>
  <span class="p">{</span> <span class="n">name</span> <span class="o">=</span> <span class="s2">"Bozhidar Batsov"</span><span class="p">;</span> <span class="n">age</span> <span class="o">=</span> <span class="mi">42</span><span class="p">;</span> <span class="n">is_developer</span> <span class="o">=</span> <span class="bp">true</span> <span class="p">};</span>
  <span class="p">{</span> <span class="n">name</span> <span class="o">=</span> <span class="s2">"Jane Doe"</span><span class="p">;</span> <span class="n">age</span> <span class="o">=</span> <span class="mi">34</span><span class="p">;</span> <span class="n">is_developer</span> <span class="o">=</span> <span class="bp">false</span> <span class="p">};</span>
<span class="p">]</span>

<span class="n">print_endline</span> <span class="p">(</span><span class="n">show_list</span> <span class="n">show_person</span> <span class="n">people</span><span class="p">);</span>
<span class="c">(* Output:
[({ name = "Bozhidar Batsov"; age = 42; is_developer = true });
  ({ name = "Jane Doe"; age = 34; is_developer = false })]
*)</span>
</pre></td></tr></tbody></table></code></pre></div></div>

<p><strong>Trade-offs:</strong></p>

<ul>
  <li><strong>Pros:</strong> Drastically reduces boilerplate. It’s the standard, idiomatic way to make types printable for debugging. It’s less error-prone because the printer is always in sync with the type definition.</li>
  <li><strong>Cons:</strong> Adds a dependency on a PPX. It can feel a bit like “magic” if you’re not familiar with how PPXs work. The default output format is generic and may not be what you want for user-facing text.</li>
</ul>

<h3>Conclusion</h3>

<p>So, which approach should you use?</p>

<ul>
  <li>For <strong>debugging and development</strong>, <code class="language-plaintext highlighter-rouge">ppx_deriving_show</code> is the undisputed winner. The time and effort it saves are immense, and it has become a standard part of any modern OCaml workflow.</li>
  <li>For <strong>final, user-facing output</strong>, a manual printer is often the better choice. It gives you the precise control needed to format the output exactly as you want it, without the syntactic noise of the derived printers.</li>
</ul>

<p>OCaml’s explicitness is a feature, not a bug. By forcing you to define how to print your types, it maintains type safety and clarity. And with tools like PPX, you get the best of both worlds: safety and convenience.</p>
