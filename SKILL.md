---
name: ai-seo-site-builder
description: Use when a user wants an AI agent such as Hermes Agent or Codex to build, optimize, verify, and deploy an SEO-first Astro static website through guided questions, fixed page skills, SEO checks, content workflow, and Cloudflare Pages deployment.
version: 1.0.0
author: XingFeng SEO Workflow
license: MIT
metadata:
  hermes:
    tags: [astro, seo, static-site, cloudflare-pages, agent-workflow, independent-site]
    related_skills: [static-site-architecture, ocr-and-documents]
---

# AI SEO Site Builder

## Overview

This skill turns the agent into an SEO-first independent-site builder for Chinese export business owners, SEO beginners, and independent-site operators.

The user should not manually create every page or know all technical details. The agent must guide the user with short questions, collect missing information step by step, generate an Astro static site, enforce SEO rules, create content workflows, verify the build, and prepare Cloudflare Pages deployment.

The website is static-first. Source code can be modular, but final public URLs should stay flat whenever possible: `/about-us/`, `/contact-us/`, `/products/`, `/blog/`, `/faq/`.

## When to Use

Use this skill when the user asks to:

- Build an SEO-first independent website with AI assistance.
- Create an Astro static website for export, B2B, ecommerce, service, or lead generation.
- Convert business information into a site structure, pages, SEO metadata, and content plan.
- Generate blog/product/category pages with internal links and anti-orphan rules.
- Deploy a static site to Cloudflare Pages with Wrangler.
- Package a reusable AI website-building workflow for Hermes Agent, Codex, or similar tools.

Do not use this skill for:

- Dynamic SaaS dashboards that require login, databases, billing, or user roles.
- WordPress theme development.
- Sites where the user explicitly wants every page hand-coded without agent guidance.

## Core Principle

The agent is the operator. The user only provides business facts and decisions when asked.

Never dump a long form and ask the user to fill everything at once. Ask only the next missing information needed for the current step. If obvious defaults exist, use them and tell the user.

## Six Module Workflow

### 1. User Site Information Collection

Collect only what is needed for the next step.

Required business inputs:

- Company name
- Brand name
- Logo or instruction to create placeholder text logo
- Primary color or reference website
- Target country
- Target language
- Industry
- Product/service type
- Contact email
- Form recipient email if different
- Contact methods: WhatsApp, Telegram, phone, social links
- Domain status
- Cloudflare account status

Feature inputs:

- Home page is always required and must be selected by default.
- Blog pages are optional.
- If blog pages are selected, blog category pages are mandatory.
- Product pages are optional.
- If product pages are selected, product category pages are mandatory.
- Case pages are optional.
- FAQ pages are optional.
- Resource/download pages are optional and mean static PDF/catalog/resource downloads.

### 2. Astro Static Site Generation

Generate an Astro project with modular source code and flat final URLs.

Recommended source structure:

```text
src/
  components/
  layouts/
  pages/
  content/
public/
  images/
  downloads/
  robots.txt
  sitemap.xml
```

Final URL rules:

- Use lowercase English letters, numbers, and hyphens.
- Avoid Chinese characters, spaces, underscores, uppercase letters, query-style URLs, or unnecessary nested paths.
- Prefer `/about-us/`, `/contact-us/`, `/products/`, `/blog/`, `/faq/`.
- Avoid deep paths like `/blog/category/topic/` unless the user explicitly needs them.

### 3. SEO Rules and Page Validation

Every generated page must pass these checks:

- SEO title exists, is unique, and has reasonable length.
- Meta description exists, is unique, and has reasonable length.
- Exactly one H1 per page.
- H1 matches the page topic and is unique across the site.
- canonical points to the current normalized URL.
- Images have alt/title suggestions.
- Page belongs to required category if it is a blog or product detail page.
- Page has at least one internal entry point from navigation, category pages, related pages, sitemap, or contextual links.
- `robots.txt`, `sitemap.xml`, and localized `404` page exist.

If checks fail, fix the site before telling the user the work is complete.

### 4. Keyword Import and Content Production

Accept keyword data from pasted CSV, Markdown table, Excel export, or user messages.

Minimum keyword fields:

- Keyword
- Language
- Country
- What the user wants this article to express

Optional fields:

- Target page type
- Product/service relation
- Target customer
- Competitor URLs
- Internal link targets
- Image needs
- Video needs

Before writing each article, output:

1. User search demand analysis.
2. SERP competitor content analysis and summary.
3. Article outline.
4. Information gain points.

After writing, check:

- Whether it duplicates existing or same-batch content.
- Whether it includes internal links.
- Whether it includes image suggestions.

Never generate mass-produced, same-structure, low-value SEO spam.

### 5. Cloudflare Pages Deployment

The first deployment target is Cloudflare Pages.

For local manual deployment, the user does not need to create a Token first:

```bash
npx wrangler login
```

Wrangler opens the Cloudflare authorization page. After login and approval, Wrangler stores authorization locally.

Deployment flow:

```bash
npm run build
npx wrangler pages deploy dist --project-name PROJECT_NAME
```

If this becomes a SaaS that deploys for other users, use Cloudflare API Token or OAuth. Never ask for or store the user's Cloudflare password.

### 7. AI-Friendly Access and WebMCP

Generated sites should be friendly to AI agents, browser automation, and accessibility tooling.

The AI Accessibility Skill must check and fix:

- Accessibility tree quality: semantic landmarks such as `header`, `nav`, `main`, `section`, `article`, `footer`; meaningful headings; labels for all inputs; descriptive button/link text; image alt text.
- Cumulative Layout Shift: avoid layout jumps by reserving dimensions for images, embeds, banners, and dynamic sections; avoid injecting late content above existing content.
- WebMCP form coverage: important forms should include machine-readable annotations so AI agents can identify form purpose, fields, submit action, and expected result.
- WebMCP tools registration: if the page exposes AI tools/actions, list them in a discoverable JSON script or endpoint.
- WebMCP schema validity: tool schemas must use valid JSON, clear names, descriptions, required fields, and predictable output.
- `llms.txt`: publish `/llms.txt` as Markdown with at least one H1 heading, site purpose, allowed AI usage, important URLs, contact, and sitemap.

Recommended public files:

```text
public/llms.txt
public/webmcp.json
```

Recommended form annotations:

```html
<form data-webmcp-form="lead-capture" aria-label="Contact sales form">
  <input name="email" autocomplete="email" aria-label="Email address" required />
</form>
```

Recommended WebMCP schema file shape:

```json
{
  "name": "site-lead-tools",
  "version": "1.0.0",
  "tools": [
    {
      "name": "submit_contact_request",
      "description": "Submit a contact or quote request through the website form.",
      "inputSchema": {
        "type": "object",
        "required": ["name", "email", "message"],
        "properties": {
          "name": { "type": "string" },
          "email": { "type": "string", "format": "email" },
          "message": { "type": "string" }
        }
      }
    }
  ]
}
```

### 8. Post-Launch Checks and Operations Advice

After deployment, remind the user to:

- Check TDK with Screaming Frog.
- Check 301 and 404 errors.
- Compare crawled URL count with generated local URL count.
- Check orphan pages.
- Confirm sitemap URL.
- Bind Google Search Console.
- Submit sitemap XML.
- Continue publishing SEO content based on keyword research.

## Fixed Skill Types

The agent must choose a skill type before generating a page.

| Skill | Use For | Required Fields | Validation | Output |
|---|---|---|---|---|
| Blog Skill | SEO articles, guides, tutorials | keyword, language, country, category, intent, internal links | title, description, one H1, category, internal links | blog detail page/content file |
| Product Skill | Product/service detail pages | product name, slug, category, specs, selling points | unique content, category, related products | product page/content file |
| Product Category Skill | Product listing/category pages | category name, slug, intro, related products | links to products, SEO metadata | category page |
| Blog Category Skill | Blog listing/category pages | category name, slug, intro, related posts | links to posts, SEO metadata | category page |
| Home Section Skill | Hero, USP, services, FAQ, CTA | section type, copy, links | visible CTA, internal links | component or homepage block |
| SEO Check Skill | All pages | page inventory | full SEO validation | report and fixes |
| AI Accessibility Skill | AI-agent-friendly access, accessibility tree, WebMCP, llms.txt | forms, tools schema, accessibility targets | structured accessibility tree, low CLS, WebMCP annotations/schema, valid llms.txt | AI access report and fixes |
| Deployment Skill | Cloudflare Pages | project name, build output | build succeeds, deploy command clear | deployed static site |

## Agent Question Flow

Start with this message:

> 我会按 AI 建站流程帮你生成网站，不会让你一次性填一大堆表。先问几个必要信息，缺的地方我会给默认建议。第一步：你的公司名、品牌名、目标国家、目标语言、行业、主要产品/服务分别是什么？

Then ask follow-up questions only when needed:

1. Brand and market information.
2. Contact information.
3. Page features.
4. Visual direction/reference site.
5. Domain and Cloudflare status.
6. Products/categories or blog categories.
7. Keywords/content plans.
8. Deployment permission.

If the user wants speed, create sensible defaults and proceed.

## Build Requirements

When implementing the generated site, the agent should:

1. Create or update an Astro project.
2. Generate pages/components/content data.
3. Generate `robots.txt`, `sitemap.xml`, and `404`.
4. Run build.
5. Run SEO validation.
6. Fix failures.
7. Provide deployment command.

Do not stop after writing files. The deliverable must be backed by real build or validation output.

## Common Pitfalls

1. **Building only a web form.** This skill is for an AI agent workflow. A web UI can be a demo, but the core package must be readable by Hermes/Codex.
2. **Asking for every field at once.** Ask only the next necessary question.
3. **Forgetting dependency rules.** Product pages require product category pages. Blog pages require blog category pages. Home page is always required.
4. **Disabled checkbox data missing in forms.** If building a UI, explicitly add mandatory/dependent features to the generated manifest.
5. **Deep URL paths.** Keep final URLs flat unless the user requests otherwise.
6. **Claiming deployment without real output.** Build and verify first; deploy only when credentials/session allow it.

## Verification Checklist

- [ ] User information was collected through guided questions or defaults.
- [ ] Home page is included.
- [ ] Product category exists when product pages exist.
- [ ] Blog category exists when blog pages exist.
- [ ] URL slugs are valid and flat.
- [ ] Every page has title, description, canonical, and exactly one H1.
- [ ] No orphan pages are generated.
- [ ] `robots.txt`, `sitemap.xml`, `llms.txt`, `webmcp.json`, and `404` exist.
- [ ] Important forms have labels, accessible names, autocomplete where useful, and WebMCP annotations.
- [ ] Accessibility tree uses semantic landmarks and meaningful headings.
- [ ] Layout avoids avoidable cumulative layout shift.
- [ ] Build command was run.
- [ ] SEO validation was run.
- [ ] Cloudflare Pages deployment path is clear.
