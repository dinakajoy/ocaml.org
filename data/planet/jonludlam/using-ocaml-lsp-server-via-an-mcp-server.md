---
title: Using ocaml-lsp-server via an MCP server
description:
url: https://jon.recoil.org/blog/2025/08/ocaml-lsp-mcp.html
date: 2025-08-27T01:00:00-00:00
preview_image:
authors:
- Jon Ludlam
source:
ignore:
---

<section><h1><a href="https://jon.recoil.org/atom.xml#using-ocaml-lsp-server-via-an-mcp-server" class="anchor"></a>Using ocaml-lsp-server via an MCP server</h1><ul class="at-tags"><li class="published"><span class="at-tag">published</span> <p>2025-08-27</p></li></ul><ul class="at-tags"><li class="notanotebook"><span class="at-tag">notanotebook</span> </li></ul><p>Here's a quick post on how to get the OCaml Language Server (ocaml-lsp-server) working with an MCP server.</p><p>We're going to use <a href="https://github.com/isaacphi">issacphi</a>'s adapter for LSP servers, which is written in go. So install go, and then:</p><div><pre class="language-bash"><code># go install github.com/isaacphi/mcp-language-server@latest</code></pre><div class="odoc-src-output"></div></div><p>Once that's done, make sure you've got `ocaml-lsp-server` installed in your switch:</p><div><pre class="language-bash"><code># opam install ocaml-lsp-server</code></pre><div class="odoc-src-output"></div></div><p>Then add the MCP config for claude where you want to run it:</p><div><pre class="language-bash"><code># claude mcp add ocamllsp -s local -t stdio -- /Users/jon/go/bin/mcp-language-server -workspace . -lsp ocamllsp</code></pre><div class="odoc-src-output"></div></div><p>It'd be nice to get this working `globally` - that is, with `-s user` - but I haven't been able to get that to work yet.</p></section>
