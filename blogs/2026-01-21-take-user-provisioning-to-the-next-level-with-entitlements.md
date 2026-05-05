---
title: "Take User Provisioning to the Next Level with Entitlements"
url: "https://developer.okta.com/blog/2026/01/21/user-entitlements-workshop"
date: "Wed, 21 Jan 2026 00:00:00 -0500"
author: ""
feed_url: "https://developer.okta.com/feed.xml"
---
<p>When you work on B2B SaaS apps used by large customer organizations, synchronizing those customers’ users within your software system is tricky! You must synchronize user profile information and the user attributes required for access control management. Customers with large workforces may have thousands of users to manage. They demand a speedy onboarding process, including automated user provisioning from their identity provider!</p>

<p>Managing users across domains is critical to making B2B apps enterprise-scalable. In the <a href="/blog/tags/enterprise-ready-workshops/">Enterprise-Ready and Enterprise-Maturity on-demand workshop series</a>, we tackle the dilemmas faced by developers of SaaS products wanting to scale their apps to enterprise customers. We iterate on a fictitious B2B Todo app more secure and capable for enterprise customers using industry-recognized standards such as OpenID Connect (OIDC) authentication and System for Cross-Domain Identity Management (SCIM) for user provisioning. In this workshop, you build upon a previous workshop introducing automated user provisioning to add support for users’ access management and permissions attributes—their entitlements.</p>

<table>
<tr>
    <td style="font-size: 3rem;">️ℹ️</td>
    <td>
      <strong>Note</strong> <br />
    This post requires Okta Identity Governance (OIG) features in your Okta org. <a href="https://developer.okta.com/signup/">Sign up for a new Integrator Free plan</a> to continue.
    </td>
</tr>
</table>

<table>
  <thead>
    <tr>
      <th>Posts in the on-demand workshop series</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1. <a href="/blog/2023/07/27/enterprise-ready-getting-started">How to Get Going with the On-Demand SaaS Apps Workshops</a></td>
    </tr>
    <tr>
      <td>2. <a href="/blog/2023/07/28/oidc_workshop">Enterprise-Ready Workshop: Authenticate with OpenID Connect</a></td>
    </tr>
    <tr>
      <td>3. <a href="/blog/2023/07/28/scim-workshop">Enterprise-Ready Workshop: Manage Users with SCIM</a></td>
    </tr>
    <tr>
      <td>4. <a href="/blog/2023/07/28/terraform-workshop">Enterprise Maturity Workshop: Terraform</a></td>
    </tr>
    <tr>
      <td>5. <a href="/blog/2023/09/15/workflows-workshop">Enterprise Maturity Workshop: Automate with no-code Okta Workflows</a></td>
    </tr>
    <tr>
      <td>6. <a href="/blog/2024/04/30/express-universal-logout">How to Instantly Sign a User Out across All Your Apps</a></td>
    </tr>
    <tr>
      <td>7. <strong>Take User Provisioning to the Next Level with Entitlements</strong></td>
    </tr>
  </tbody>
</table>

<p>This workshop walks you through adding the code to support entitlements in a sample application with three broad sections:</p>
<ol>
  <li>Introduction to the base application, tools, and the development process</li>
  <li>See your application’s user and entitlements information in Okta</li>
  <li>Use Okta to manage user roles and custom entitlements</li>
</ol>

<p>If you want to skip to the completed code project for this workshop, you can find it in the <a href="https://github.com/oktadev/okta-enterprise-ready-workshops/tree/entitlements-workshop-complete"><code class="language-plaintext highlighter-rouge">entitlements-completed</code> branch on the GitHub repo</a>.</p>

<p><strong class="hide">Table of Contents</strong></p>
<ul id="markdown-toc">
  <li><a href="#manage-users-at-scale-using-system-for-cross-domain-identity-management-scim" id="markdown-toc-manage-users-at-scale-using-system-for-cross-domain-identity-management-scim">Manage users at scale using System for Cross-domain Identity Management (SCIM)</a>    <ul>
      <li><a href="#prepare-the-expressjs-api-project" id="markdown-toc-prepare-the-expressjs-api-project">Prepare the Express.js API project</a></li>
      <li><a href="#serve-the-expressjs-api-and-test-the-roles-scim-endpoint" id="markdown-toc-serve-the-expressjs-api-and-test-the-roles-scim-endpoint">Serve the Express.js API and test the <code class="language-plaintext highlighter-rouge">/Roles</code> SCIM endpoint</a></li>
    </ul>
  </li>
  <li><a href="#support-user-roles-in-the-database" id="markdown-toc-support-user-roles-in-the-database">Support user roles in the database</a></li>
  <li><a href="#connect-okta-to-the-scim-server" id="markdown-toc-connect-okta-to-the-scim-server">Connect Okta to the SCIM server</a></li>
  <li><a href="#create-an-okta-scim-application-for-entitlements-governance" id="markdown-toc-create-an-okta-scim-application-for-entitlements-governance">Create an Okta SCIM application for entitlements governance</a></li>
  <li><a href="#scim-schemas-and-resources" id="markdown-toc-scim-schemas-and-resources">SCIM schemas and resources</a>    <ul>
      <li><a href="#harness-typescript-to-conform-to-scim-schemas" id="markdown-toc-harness-typescript-to-conform-to-scim-schemas">Harness TypeScript to conform to SCIM schemas</a></li>
      <li><a href="#scim-list-response" id="markdown-toc-scim-list-response">SCIM list response</a></li>
      <li><a href="#return-database-defined-roles-in-the-scim-roles-endpoint" id="markdown-toc-return-database-defined-roles-in-the-scim-roles-endpoint">Return database-defined roles in the SCIM <code class="language-plaintext highlighter-rouge">/Roles</code> endpoint</a></li>
    </ul>
  </li>
  <li><a href="#scim-resource-types" id="markdown-toc-scim-resource-types">SCIM resource types</a></li>
  <li><a href="#add-roles-to-the-scim-users-endpoints" id="markdown-toc-add-roles-to-the-scim-users-endpoints">Add roles to the SCIM Users endpoints</a>    <ul>
      <li><a href="#update-the-scim-add-users-call-to-include-roles" id="markdown-toc-update-the-scim-add-users-call-to-include-roles">Update the SCIM add users call to include roles</a></li>
      <li><a href="#add-roles-when-getting-a-list-of-users-in-scim" id="markdown-toc-add-roles-when-getting-a-list-of-users-in-scim">Add roles when getting a list of users in SCIM</a></li>
      <li><a href="#update-the-users-call-so-scim-clients-can-set-their-roles" id="markdown-toc-update-the-users-call-so-scim-clients-can-set-their-roles">Update the <code class="language-plaintext highlighter-rouge">/Users</code> call so SCIM clients can set their roles</a></li>
    </ul>
  </li>
  <li><a href="#entitlements-discovery-in-okta" id="markdown-toc-entitlements-discovery-in-okta">Entitlements discovery in Okta</a>    <ul>
      <li><a href="#syncing-user-entitlements" id="markdown-toc-syncing-user-entitlements">Syncing user entitlements</a></li>
      <li><a href="#schema-discovery-for-custom-entitlements" id="markdown-toc-schema-discovery-for-custom-entitlements">Schema discovery for custom entitlements</a></li>
    </ul>
  </li>
  <li><a href="#multi-tenant-use-cases-for-entitlements" id="markdown-toc-multi-tenant-use-cases-for-entitlements">Multi-tenant use cases for entitlements</a></li>
  <li><a href="#use-scim-to-manage-user-provisioning-and-entitlements" id="markdown-toc-use-scim-to-manage-user-provisioning-and-entitlements">Use SCIM to manage user provisioning and entitlements</a></li>
</ul>

<h2 id="manage-users-at-scale-using-system-for-cross-domain-identity-management-scim">Manage users at scale using System for Cross-domain Identity Management (SCIM)</h2>

<p>The Todo app tech stack uses a React frontend and an Express API backend. For this workshop, you need the following required tooling:</p>

<p><strong>Required tools</strong></p>
<ul>
  <li><a href="https://nodejs.org/en">Node.js</a> v18 or higher</li>
  <li>Command-line terminal application</li>
  <li>A code editor/Integrated development environment (IDE), such as <a href="https://code.visualstudio.com/">Visual Studio Code</a> (VS Code)</li>
  <li>An HTTP client testing tool, such as <a href="https://www.postman.com/">Postman</a> or the <a href="https://marketplace.visualstudio.com/items?itemName=mkloubert.vscode-http-client">HTTP Client</a> VS Code extension</li>
</ul>

<blockquote>
  <p>VS Code has integrated terminals and HTTP client extensions that allow you to work out of this one application for almost everything required in this workshop. The IDE also supports TypeScript, so you’ll get quicker responses on type errors and help with importing modules.</p>
</blockquote>

<p>Follow the instructions in the getting started guide for installing the required tools and serving the Todo application.</p>

<article class="link-container" style="border: 1px solid silver; border-radius: 3px; padding: 12px 15px;">
              <a href="/blog/2023/07/27/enterprise-ready-getting-started" style="font-size: 1.375em; margin-bottom: 20px;">
                <span>How to Get Going with the On-Demand SaaS Apps Workshops</span>
              </a>
              <p>Start your journey to identity maturity for your SaaS applications in the enterprise-ready workshops! This post covers installing and running the base application in preparation for the upcoming workshops.</p>
              <div><div class="BlogPost-attribution">
            <a href="/blog/authors/alisa-duncan/">
              <img alt="avatar-avatar-alisa_duncan.jpeg" class="BlogPost-avatar" src="/assets-jekyll/avatar-alisa_duncan-b29fa4df50f5c99f536307c6bc0e5cb3434a922bdada7fe4f4b3cf8488299465.jpg" />
            </a>
            <span class="BlogPost-author">
                <a href="/blog/authors/alisa-duncan/">Alisa Duncan</a>
            </span>
          </div></div>
          </article>

<p>You’ll build upon a prior workshop introducing syncing users across systems using the <a href="https://scim.cloud/">System for Cross-domain Identity Management</a> (SCIM) protocol.</p>

<p>In this workshop, you’ll dive deeper into automated user provisioning by adding the user attributes required for access management, such as user roles, licensing, permissions, or something else you use to denote what actions a user has access to. The access management attributes of users are known by the generic term, user entitlements. Then, we will continue diving deeper into supporting customized user entitlements using the SCIM protocol.</p>

<p>Before we get going with user entitlements, you’ll first step through the interactive and fun <a href="/blog/2023/07/28/scim-workshop">Enterprise-Ready Workshop: Manage Users with SCIM</a> workshop to get the SCIM overview, set up the code and your Okta account, and see how the protocol works. I’ll settle down with a cup of tea and a good book and wait while you learn about SCIM and are ready to continue! 🫖🍵📚</p>

<article class="link-container" style="border: 1px solid silver; border-radius: 3px; padding: 12px 15px;">
              <a href="/blog/2023/07/28/scim-workshop" style="font-size: 1.375em; margin-bottom: 20px;">
                <span>Enterprise-Ready Workshop: Manage users with SCIM</span>
              </a>
              <p>In this workshop, you will add SCIM support to a sample application, so that user changes made in your app can sync to your customer's Identity Provider!</p>
              <div><div class="BlogPost-attribution">
            <a href="/blog/authors/semona-igama/">
              <img alt="avatar-avatar-semona-igama.jpeg" class="BlogPost-avatar" src="/assets-jekyll/avatar-semona-igama-03eb4c28aca3765f862b574e032d32f6f8186d04ae9f0db75bed9c74f48a9a3f.jpg" />
            </a>
            <span class="BlogPost-author">
                <a href="/blog/authors/semona-igama/">Semona Igama</a>
            </span>
          </div></div>
          </article>

<h3 id="prepare-the-expressjs-api-project">Prepare the Express.js API project</h3>

<p>Start from a clean code project by using the SCIM workshop’s completed project code from the <a href="https://github.com/oktadev/okta-enterprise-ready-workshops/tree/scim-workshop-complete">scim-workshop-complete</a> branch. I’ll post the instructions using <a href="https://git-scm.com/">Git</a>, but you can download the code as <a href="https://github.com/oktadev/okta-enterprise-ready-workshops/archive/refs/heads/scim-workshop-complete.zip">a zip file</a> if you prefer and skip the Git command.</p>

<p>Get a local copy of the completed SCIM workshop code and install dependencies by running the following commands in your terminal:</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>git clone <span class="nt">-b</span> scim-workshop-complete https://github.com/oktadev/okta-enterprise-ready-workshops.git
<span class="nb">cd </span>okta-enterprise-ready-workshops
npm ci
</code></pre></div></div>

<p>Open the code project in your IDE. We’ll work exclusively within the Express.js API for this project, and the code files for the API are in the <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src</code> directory.</p>

<p>Create a file named <code class="language-plaintext highlighter-rouge">entitlements.ts</code>. We’ll define the API routes for user entitlements in the <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/entitlements.ts</code> file.</p>

<p>Let’s start by hard-coding an API endpoint for <code class="language-plaintext highlighter-rouge">/Roles</code> that returns a list of roles. In the <code class="language-plaintext highlighter-rouge">entitlements.ts</code> file, add the following code:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">Router</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">express</span><span class="dl">'</span><span class="p">;</span>

<span class="k">export</span> <span class="kd">const</span> <span class="nx">rolesRoute</span> <span class="o">=</span> <span class="nx">Router</span><span class="p">();</span>

<span class="nx">rolesRoute</span><span class="p">.</span><span class="nx">route</span><span class="p">(</span><span class="dl">'</span><span class="s1">/</span><span class="dl">'</span><span class="p">)</span>
<span class="p">.</span><span class="kd">get</span><span class="p">(</span><span class="k">async</span> <span class="p">(</span><span class="nx">req</span><span class="p">,</span> <span class="nx">res</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">roles</span> <span class="o">=</span> <span class="p">[</span>
    <span class="dl">'</span><span class="s1">Todo-er</span><span class="dl">'</span><span class="p">,</span>
    <span class="dl">'</span><span class="s1">Admin</span><span class="dl">'</span>
  <span class="p">];</span>

  <span class="k">return</span> <span class="nx">res</span><span class="p">.</span><span class="nx">json</span><span class="p">(</span><span class="nx">roles</span><span class="p">);</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/scim.ts</code>. We need to register the endpoint in the Express app by including it as part of the SCIM routes.</p>

<p>At the top of the file, import <code class="language-plaintext highlighter-rouge">rolesRoutes</code></p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">rolesRoute</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./entitlements</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>At the bottom of the file below the existing code, add</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">scimRoute</span><span class="p">.</span><span class="nx">use</span><span class="p">(</span><span class="dl">'</span><span class="s1">/Roles</span><span class="dl">'</span><span class="p">,</span> <span class="nx">rolesRoute</span><span class="p">);</span>
</code></pre></div></div>

<p>to register the endpoint. Let’s make sure everything works!</p>

<h3 id="serve-the-expressjs-api-and-test-the-roles-scim-endpoint">Serve the Express.js API and test the <code class="language-plaintext highlighter-rouge">/Roles</code> SCIM endpoint</h3>

<p>In the terminal, start the API by running</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>npm run serve-api
</code></pre></div></div>

<p>This command serves the API on port 3333. Launch your HTTP client and call the <code class="language-plaintext highlighter-rouge">/Roles</code> endpoint:</p>

<div class="language-http highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nf">GET</span> <span class="nn">http://localhost:3333/scim/v2/Roles</span> <span class="k">HTTP</span><span class="o">/</span><span class="m">1.1</span>
</code></pre></div></div>

<p>Do you see a successful response with a list of roles?</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>HTTP/1.1 200 OK

[
  "Todo-er",
  "Admin"
]
</code></pre></div></div>

<p>Take a look at the terminal output. You’ll see output recording the <code class="language-plaintext highlighter-rouge">GET</code> request!</p>

<p><img alt="Terminal output showing the GET request to the /Roles route and a 200OK HTTP response" class="center-image" src="/assets-jekyll/blog/user-entitlements-workshop/morgan-968d824c03d48283182cce88f47f8163b3a9af02a9b98ed786a98e1a24589296.jpg" width="800" /></p>

<p>The project uses <a href="https://github.com/expressjs/morgan">Morgan</a>, a library that automatically adds HTTP logging to the Express API. The terminal output includes <code class="language-plaintext highlighter-rouge">POST</code> and <code class="language-plaintext highlighter-rouge">PUT</code> request payloads, so it’s an excellent way to track the SCIM calls as you work through the workshop.</p>

<p>The <code class="language-plaintext highlighter-rouge">npm run serve-api</code> process watches for changes and automatically updates the API, so we don’t need to stop and restart it constantly. But we’re about to make some significant changes. Stop serving the API by entering <kbd>Ctrl</kbd>+<kbd>c</kbd> in the terminal so we can prepare the database.</p>

<h2 id="support-user-roles-in-the-database">Support user roles in the database</h2>

<p>The Todo app database needs to support roles; we’ve hardcoded roles so far. It’s time to bring the database to the party. A fancier SaaS app might allow each customer to define their roles. We’ll skip that level of customizability for now and focus on the simplest case. For this workshop, we’ll define supported roles for all Todo app customers instead of allowing role configurations per organization. Taking the position of application roles instead of organization roles makes our database modeling easier. I’ll discuss ways to add per-organization configurability later in the post.</p>

<p>Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/prisma/schema.prisma</code>. Add the role model at the end of the file.</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>model Role {
  id Int @id @default(autoincrement())
  name String
  users User[]
}
</code></pre></div></div>

<p>A user may have zero or more roles. Update the user model to add roles so that the user model looks like this:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>model User {
  id         Int    @id @default(autoincrement())
  email      String
  password   String?
  name       String
  Todo       Todo[]
  org        Org?    @relation(fields: [orgId], references: [id])
  orgId      Int?
  externalId String?
  active     Boolean?
  roles      Role[]
  @@unique([orgId, externalId])
}
</code></pre></div></div>

<p>With the roles model defined, it’s time to update the database to match the model. We’ll start with a fresh, clean database for this project. In the terminal run</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>npx prisma migrate reset <span class="nt">-f</span>
</code></pre></div></div>

<p>It helps to have some seed data so we can get going. Here, we’ll define roles available within the Todo app. A user can be a “Todo-er,” “Todo Auditor,” and “Manager.” Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/prisma/seed_script.ts</code> and replace the entire file with the code below:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">PrismaClient</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">@prisma/client</span><span class="dl">'</span><span class="p">;</span>

<span class="kd">const</span> <span class="nx">prisma</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">PrismaClient</span><span class="p">();</span>

<span class="k">async</span> <span class="kd">function</span> <span class="nx">main</span><span class="p">()</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">org</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">org</span><span class="p">.</span><span class="nx">create</span><span class="p">({</span>
    <span class="na">data</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">domain</span><span class="p">:</span> <span class="dl">'</span><span class="s1">gridco.example</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">apikey</span><span class="p">:</span> <span class="dl">'</span><span class="s1">123123</span><span class="dl">'</span>
    <span class="p">}</span>
  <span class="p">});</span>
  <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">Created org Portal</span><span class="dl">'</span><span class="p">,</span> <span class="nx">org</span><span class="p">);</span>

  <span class="c1">// Roles defined by the Todo app</span>
  <span class="kd">const</span> <span class="nx">roles</span> <span class="o">=</span> <span class="p">[</span>
    <span class="p">{</span> <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Todo-er</span><span class="dl">'</span> <span class="p">},</span>
    <span class="p">{</span> <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Todo Auditor</span><span class="dl">'</span> <span class="p">},</span>
    <span class="p">{</span> <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Manager</span><span class="dl">'</span><span class="p">}</span>
  <span class="p">];</span>

  <span class="kd">const</span> <span class="nx">createdRoles</span> <span class="o">=</span> <span class="k">await</span> <span class="nb">Promise</span><span class="p">.</span><span class="nx">all</span><span class="p">(</span>
    <span class="nx">roles</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">data</span> <span class="o">=&gt;</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">role</span><span class="p">.</span><span class="nx">create</span><span class="p">({</span><span class="nx">data</span><span class="p">}))</span>
  <span class="p">);</span>

  <span class="k">for</span> <span class="p">(</span><span class="kd">const</span> <span class="nx">role</span> <span class="k">of</span> <span class="nx">createdRoles</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">Created role </span><span class="dl">'</span><span class="p">,</span> <span class="nx">role</span><span class="p">);</span>
  <span class="p">}</span>

  <span class="kd">const</span> <span class="nx">somnusUser</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">create</span><span class="p">({</span>
    <span class="na">data</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Somnus Henderson</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">email</span><span class="p">:</span> <span class="dl">'</span><span class="s1">somnus.henderson@gridco.example</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">password</span><span class="p">:</span> <span class="dl">'</span><span class="s1">correct horse battery staple</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">orgId</span><span class="p">:</span> <span class="nx">org</span><span class="p">.</span><span class="nx">id</span><span class="p">,</span>
      <span class="na">externalId</span><span class="p">:</span> <span class="dl">'</span><span class="s1">31</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">active</span><span class="p">:</span> <span class="kc">true</span>
    <span class="p">}</span>
  <span class="p">});</span>
  <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">Created user Somnus</span><span class="dl">'</span><span class="p">,</span> <span class="nx">somnusUser</span><span class="p">)</span>

 <span class="kd">const</span> <span class="nx">trinityUser</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">create</span><span class="p">({</span>
    <span class="na">data</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Trinity JustTrinity</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">email</span><span class="p">:</span> <span class="dl">'</span><span class="s1">trinity@gridco.example</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">password</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Zion</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">orgId</span><span class="p">:</span> <span class="nx">org</span><span class="p">.</span><span class="nx">id</span><span class="p">,</span>
      <span class="na">externalId</span><span class="p">:</span> <span class="dl">'</span><span class="s1">32</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">active</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
      <span class="na">roles</span><span class="p">:</span> <span class="p">{</span>
        <span class="na">connect</span><span class="p">:</span> <span class="p">{</span>
          <span class="na">id</span><span class="p">:</span> <span class="nx">createdRoles</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">r</span> <span class="o">=&gt;</span> <span class="nx">r</span><span class="p">.</span><span class="nx">name</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Todo-er</span><span class="dl">'</span><span class="p">)?.</span><span class="nx">id</span>
        <span class="p">}</span>
      <span class="p">}</span>
    <span class="p">},</span>
  <span class="p">})</span>
  <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">Created user Trinity</span><span class="dl">'</span><span class="p">,</span> <span class="nx">trinityUser</span><span class="p">)</span>
<span class="p">}</span>

<span class="nx">main</span><span class="p">()</span>
  <span class="p">.</span><span class="nx">then</span><span class="p">(</span><span class="k">async</span> <span class="p">()</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">$disconnect</span><span class="p">()</span>
  <span class="p">})</span>
  <span class="p">.</span><span class="k">catch</span><span class="p">(</span><span class="k">async</span> <span class="p">(</span><span class="nx">e</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="nx">console</span><span class="p">.</span><span class="nx">error</span><span class="p">(</span><span class="nx">e</span><span class="p">)</span>
    <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">$disconnect</span><span class="p">()</span>
    <span class="nx">process</span><span class="p">.</span><span class="nx">exit</span><span class="p">(</span><span class="mi">1</span><span class="p">)</span>
  <span class="p">})</span>
</code></pre></div></div>

<p>Save the file and run the npm script in the terminal to seed the database.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>npm run init-db
</code></pre></div></div>

<p>You’ll see console output for each newly created database record. 🎉</p>

<p><strong>Inspect the database records</strong></p>

<p>You can inspect the database records using <a href="https://www.prisma.io/studio">Prisma Studio</a>. In a separate terminal, run</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>npx prisma studio
</code></pre></div></div>

<p>which launches a web interface to view the database. The site URL is usually <code class="language-plaintext highlighter-rouge">http://localhost:5555</code>, shown in the terminal output. Open the site in your browser to view the database tables, records, and relationships.</p>

<h2 id="connect-okta-to-the-scim-server">Connect Okta to the SCIM server</h2>

<p>The SCIM Client (the identity provider, Okta) makes requests upon objects held by the SCIM Server (the Todo app).</p>

<p><img alt="SCIM workflow showing the Identity Provider requests the SCIM server with GET, POST, PUT, and DEL user calls and the SCIM server responds with a standard SCIM interface" class="center-image" src="/assets-jekyll/blog/scim-workshop/scim-diagram-4cfb2cb57d2031d826a9221b3fdf59284e2df35657a79c53118f3bb776be0440.jpg" width="800" /></p>

<p>First, we need to serve the API so Okta can access it. You’ll use a temporary tunnel for local development that makes <code class="language-plaintext highlighter-rouge">localhost:3333</code> publicly accessible so that Okta, the SCIM client, can call your API, the SCIM server. I’ll include the instructions using an NPM library that we don’t have to install or sign up for, but feel free to use your favorite tunneling system if you have one.</p>

<p>You need two terminal sessions.</p>

<p>In one terminal, serve the API using the command:</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>npm run serve-api
</code></pre></div></div>

<p>In the second terminal, you’ll run the local tunnel. Run the command:</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>npx localtunnel <span class="nt">--port</span> 3333
</code></pre></div></div>

<p>This creates a tunnel for the application serving on port 3333. The console output displays the tunnel URL in the format <code class="language-plaintext highlighter-rouge">https://{yourTunnelSubdomain}.loca.lt</code>, such as:</p>

<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="go">your URL is: https://awesome-devs-club.loca.lt
</span></code></pre></div></div>

<p>You’ll need this tunnel URL to configure the Okta application.</p>

<h2 id="create-an-okta-scim-application-for-entitlements-governance">Create an Okta SCIM application for entitlements governance</h2>

<p>In the prerequisite SCIM workshop, you added a SCIM application in Okta to connect to the Todo app. We must do something similar to connect SCIM with entitlements support.</p>

<p>Sign into your <a href="https://developer.okta.com/login/">Okta Integrator Free account</a>. In the Admin Console, navigate to <strong>Applications</strong> &gt; <strong>Applications</strong>. Press the <strong>Browse App Catalog</strong> button to create a new Okta SCIM application.</p>

<p>In the search bar, search for “(Header Auth) Governance with SCIM 2.0” and select the app. Press <strong>Add Integration</strong>.</p>

<p>You’ll see a configuration view with two tabs. Press <strong>Next</strong> on the <strong>General settings</strong> tab. Leave default settings on the <strong>Sign-On Options</strong> tab and press <strong>Done</strong>.</p>

<p>You’ll navigate to your newly created Okta application to add specific configurations about the Todo app.</p>

<p>First, you need to enable Identity Governance. Navigate to the <strong>General</strong> tab and find the <strong>Identity Governance</strong> section. Press <strong>Edit</strong> to select <strong>Enabled</strong> for <strong>Governance Engine</strong>. Remember to <strong>Save</strong> your change.</p>

<p>Navigate to the <strong>Provisioning</strong> tab and press the <strong>Configure API Integration</strong> button. Check the <strong>Enable API integration</strong> checkbox—two more form fields display.</p>
<ul>
  <li>
    <p>In <strong>Base URL</strong> field, enter <code class="language-plaintext highlighter-rouge">https://{yourTunnelSubdomain}.loca.lt/scim/v2</code>.</p>

    <p>It will look like <code class="language-plaintext highlighter-rouge">https://awesome-devs-club.loca.lt/scim/v2</code></p>
  </li>
  <li>
    <p>In the <strong>API Token</strong> field, enter <code class="language-plaintext highlighter-rouge">Bearer 123123</code></p>
  </li>
</ul>

<p>press <strong>Save</strong>.</p>

<p>The <strong>Provisioning</strong> tab has more options to configure within the <strong>Settings</strong> side nav.</p>

<p>Navigate to the <strong>To App</strong> option and press <strong>Edit</strong>.</p>
<ul>
  <li>Enable <strong>Create Users</strong></li>
  <li>Enable <strong>Update User Attributes</strong></li>
  <li>Enable <strong>Deactivate Users</strong></li>
</ul>

<p>Press <strong>Save</strong>.</p>

<p>Import users from the todo app into Okta. Navigate to the <strong>Import</strong> tab and press the <strong>Import Now</strong> button. Okta discovers users in your app and tries to match them with users already defined in Okta. A dialog shows Okta discovered the two users you added using the DB script. Select both users and press the <strong>Confirm Assignments</strong> to confirm the assignments.</p>

<p>You’ll see the imported users in the <strong>Assignments</strong> tab. But what about entitlements? They’re coming right up!</p>

<p>Stop the tunnel and the API using the <kbd>Ctrl</kbd>+<kbd>c</kbd> command in the terminal windows. We’ll make some changes to the API that won’t automatically reflect in the local tunnel, so we’ll get all our entitlements changes made and resynchronize with Okta.</p>

<h2 id="scim-schemas-and-resources">SCIM schemas and resources</h2>

<p>In the first SCIM workshop, you learned about SCIM’s <code class="language-plaintext highlighter-rouge">User</code> resource and built out operations around the user. You updated only a handful of user properties in the workshop, but SCIM is way more powerful thanks to its superpower – <em>extensibility</em>. ✨ User is not the only resource type defined in SCIM.</p>

<p>A <code class="language-plaintext highlighter-rouge">Resource</code> represents an object SCIM operates on, such as a user or group. SCIM identified core properties each <code class="language-plaintext highlighter-rouge">Resource</code> must define, such as <code class="language-plaintext highlighter-rouge">id</code> and a link to the resource’s schema definition. From there, a user extends from the core properties and adds attributes specific to the object, such as adding <code class="language-plaintext highlighter-rouge">userName</code> and their emails. A standard published schema exists for all those user-specific attributes within the SCIM spec. You can continue extending resources as needed to represent new resources, such as another SCIM standard-defined schema for Enterprise User.</p>

<p><img alt="Class diagram representing core Resource properties, User class extending from core Resource adds username and emails properties. The Enterprise User class extends from User adds department and costCenter properties. Group class extends from core Resource and adds displayName and members properties. Other class extends from core Resource demonstrating new resource representations." class="center-image" src="/assets-jekyll/blog/user-entitlements-workshop/resource-class-diagram-696cc8f04feb6e62c81cc68743f76254c52319cfd1c26411d6933db9274a183d.svg" width="800" /></p>

<p>What’s an example resource other than a user or group? If you said “role” or an “entitlement,” you’re correct! Those resource types must have an <code class="language-plaintext highlighter-rouge">id</code> and <code class="language-plaintext highlighter-rouge">schemas</code>. Here, Okta used SCIM’s extensibility to define a new resource type.</p>

<p><img alt="Class diagram representing core Resource properties. The User, Group, OktaRole, and Other class extends from core Resource." class="center-image" src="/assets-jekyll/blog/user-entitlements-workshop/resource-role-class-diagram-db8aa8eb1908e9bbbcfe2bae7f02a814ec732e4dc925ae761a629c159839dfd2.svg" width="800" /></p>

<p>Okta defines a schema for the <code class="language-plaintext highlighter-rouge">Role</code> representation. We can use the schema to ensure we conform to the definition.</p>

<h3 id="harness-typescript-to-conform-to-scim-schemas">Harness TypeScript to conform to SCIM schemas</h3>

<p>We can define an interface to model the <code class="language-plaintext highlighter-rouge">Role</code> representation. Add a new file to the project named <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/scim-types.ts</code> and open it up in the IDE. This file will contain the SCIM schema definitions, such as the SCIM core <code class="language-plaintext highlighter-rouge">Resource</code>. Each interface defines required and optional properties and the property’s type.</p>

<p>Copy and paste the first interface for the SCIM resource into the <code class="language-plaintext highlighter-rouge">scim-types.ts</code> file.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IScimResource</span> <span class="p">{</span>
  <span class="nl">id</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">schemas</span><span class="p">:</span> <span class="kr">string</span><span class="p">[];</span>
  <span class="nl">meta</span><span class="p">?:</span> <span class="nx">IMetadata</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>A SCIM resource has an optional meta property containing the resource’s metadata. Your IDE shows errors, so we can fix this by adding the <code class="language-plaintext highlighter-rouge">IMetadata</code> definition to the file below the <code class="language-plaintext highlighter-rouge">IScimResource</code>:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IMetadata</span> <span class="p">{</span>
  <span class="nl">resourceType</span><span class="p">:</span> <span class="nx">RESOURCE_TYPES</span><span class="p">;</span>
  <span class="nl">location</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>You’ll have a new error for <code class="language-plaintext highlighter-rouge">RESOURCE_TYPES</code>. We’ll fix it soon.</p>

<p>Now, on to the Okta <code class="language-plaintext highlighter-rouge">Role</code> representation. The role representation extends from the core SCIM resource and adds extra properties. Okta’s schema overlaps with the SCIM standard User <code class="language-plaintext highlighter-rouge">roles</code> field, which includes a property for <code class="language-plaintext highlighter-rouge">display</code> text. Define the interface and add it to <code class="language-plaintext highlighter-rouge">IMetadata</code> below.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IOktaRole</span> <span class="kd">extends</span> <span class="nx">IScimResource</span><span class="p">{</span>
  <span class="nl">displayName</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>The <code class="language-plaintext highlighter-rouge">IOktaRole</code> extends from the core <code class="language-plaintext highlighter-rouge">IScimResource</code> interface and adds a new required property, <code class="language-plaintext highlighter-rouge">displayName</code>. Each resource requires a schema, a Uniform Resource Namespace (URN) string. Instead of repeatedly typing the string for each role resource, define it below the <code class="language-plaintext highlighter-rouge">IOktaRole</code> interface for reusability</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kd">const</span> <span class="nx">SCHEMA_OKTA_ROLE</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">urn:okta:scim:schemas:core:1.0:Role</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>Let’s fix the <code class="language-plaintext highlighter-rouge">RESOURCE_TYPES</code> error. Below the <code class="language-plaintext highlighter-rouge">SCHEMA_OKTA_ROLE</code> constant, add the following:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kd">type</span> <span class="nx">RESOURCE_TYPES</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">Role</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>You can use the <code class="language-plaintext highlighter-rouge">IOktaRole</code> interface in the <code class="language-plaintext highlighter-rouge">/Roles</code> endpoint to ensure the response matches the expected structure. Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/entitlements.ts</code>, and update the code to use the interface.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">Router</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">express</span><span class="dl">'</span><span class="p">;</span>
<span class="k">import</span> <span class="p">{</span> <span class="nx">IOktaRole</span><span class="p">,</span> <span class="nx">SCHEMA_OKTA_ROLE</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./scim-types</span><span class="dl">'</span><span class="p">;</span>

<span class="k">export</span> <span class="kd">const</span> <span class="nx">rolesRoute</span> <span class="o">=</span> <span class="nx">Router</span><span class="p">();</span>

<span class="nx">rolesRoute</span><span class="p">.</span><span class="nx">route</span><span class="p">(</span><span class="dl">'</span><span class="s1">/</span><span class="dl">'</span><span class="p">)</span>
<span class="p">.</span><span class="kd">get</span><span class="p">(</span><span class="k">async</span> <span class="p">(</span><span class="nx">req</span><span class="p">,</span> <span class="nx">res</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="na">roles</span><span class="p">:</span> <span class="nx">IOktaRole</span><span class="p">[]</span> <span class="o">=</span> <span class="p">[{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">],</span>
    <span class="na">id</span><span class="p">:</span> <span class="dl">'</span><span class="s1">one</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">displayName</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Todo-er</span><span class="dl">'</span>
  <span class="p">}];</span>

  <span class="k">return</span> <span class="nx">res</span><span class="p">.</span><span class="nx">json</span><span class="p">(</span><span class="nx">roles</span><span class="p">);</span>
<span class="p">});</span>
</code></pre></div></div>

<blockquote>
  <p><strong>Why use TypeScript and interfaces?</strong></p>

  <p>TypeScript, a superset of JavaScript, supports type safety. Type safety means we’ll catch errors within the IDE or at build time instead of getting caught by surprise with a runtime error. Here, we state the <code class="language-plaintext highlighter-rouge">roles</code> array is of type <code class="language-plaintext highlighter-rouge">IOktaRole[]</code>. Try commenting out the required <code class="language-plaintext highlighter-rouge">schemas</code> property. You’ll see an error in an IDE that supports TypeScript or when you try to serve the API as console output. We can use type safety to ensure we meet the expectations of required SCIM properties in our calls.</p>

  <p><img alt="IDE and terminal showing the type error when `schemas` is commented out" class="center-image" src="/assets-jekyll/blog/user-entitlements-workshop/type-error-e1d841636e4957ba250a1e85269ef5807987fd47bd070653fd8289d6737d20d6.jpg" width="600" /></p>
</blockquote>

<p>Every code change deserves a quick check. Serve the API and double check everything still works for you when you make the HTTP call to</p>

<div class="language-http highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nf">GET</span> <span class="nn">http://localhost:3333/scim/v2/Roles</span> <span class="k">HTTP</span><span class="o">/</span><span class="m">1.1</span>
</code></pre></div></div>

<p>Do you see the one ‘Todo-er’ role in the response? ✅</p>

<h3 id="scim-list-response">SCIM list response</h3>

<p>We return the array of Okta roles directly in the API response, but this format doesn’t match SCIM list responses. SCIM has a structured response format for lists and a defined schema. This way, SCIM structures all communication between the client and the server so each side knows how to format and parse data.</p>

<p>Let’s define the <code class="language-plaintext highlighter-rouge">ListResponse</code> interface. Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/scim-types.ts</code>. The list response contains standard information supporting pagination, the schema for the list response, and the list of objects. Add the interface to the file. I like to organize my definitions, so I added the code between the <code class="language-plaintext highlighter-rouge">IOktaRole</code> interface and <code class="language-plaintext highlighter-rouge">SCHEMA_OKTA_ROLE</code> string constant.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IListResponse</span> <span class="p">{</span>
  <span class="nl">schemas</span><span class="p">:</span> <span class="kr">string</span><span class="p">[];</span>
  <span class="nl">totalResults</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">startIndex</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">itemsPerPage</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">Resources</span><span class="p">:</span> <span class="nx">IOktaRole</span><span class="p">[];</span>
<span class="p">}</span>
</code></pre></div></div>

<p>The list response also has a schema URN. Create a constant for this string as you did for the Okta role and add it after the role schema string.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kd">const</span> <span class="nx">SCHEMA_LIST_RESPONSE</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">urn:ietf:params:scim:api:messages:2.0:ListResponse</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>The API response must match the list format. Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/entitlements.ts</code> and add <code class="language-plaintext highlighter-rouge">IListResponse</code> and <code class="language-plaintext highlighter-rouge">SCHEMA_LIST_RESPONSE</code> to the imports from the <code class="language-plaintext highlighter-rouge">scim-types</code> file:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">IListResponse</span><span class="p">,</span> <span class="nx">IOktaRole</span><span class="p">,</span> <span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">,</span> <span class="nx">SCHEMA_OKTA_ROLE</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./scim-types</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>Change <code class="language-plaintext highlighter-rouge">rolesRoute</code> response to use the list response:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">rolesRoute</span><span class="p">.</span><span class="nx">route</span><span class="p">(</span><span class="dl">'</span><span class="s1">/</span><span class="dl">'</span><span class="p">)</span>
<span class="p">.</span><span class="kd">get</span><span class="p">(</span><span class="k">async</span> <span class="p">(</span><span class="nx">req</span><span class="p">,</span> <span class="nx">res</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="na">roles</span><span class="p">:</span> <span class="nx">IOktaRole</span><span class="p">[]</span> <span class="o">=</span> <span class="p">[{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">],</span>
    <span class="na">id</span><span class="p">:</span> <span class="dl">'</span><span class="s1">one</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">displayName</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Todo-er</span><span class="dl">'</span>
  <span class="p">}];</span>

  <span class="kd">const</span> <span class="na">listResponse</span><span class="p">:</span> <span class="nx">IListResponse</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">],</span>
    <span class="na">totalResults</span><span class="p">:</span> <span class="nx">roles</span><span class="p">.</span><span class="nx">length</span><span class="p">,</span>
    <span class="na">itemsPerPage</span><span class="p">:</span> <span class="nx">roles</span><span class="p">.</span><span class="nx">length</span><span class="p">,</span>
    <span class="na">startIndex</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
    <span class="na">Resources</span><span class="p">:</span> <span class="nx">roles</span>
  <span class="p">};</span>

  <span class="k">return</span> <span class="nx">res</span><span class="p">.</span><span class="nx">json</span><span class="p">(</span><span class="nx">listResponse</span><span class="p">);</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Double-check everything still works. Send the HTTP request to your API. ✅</p>

<div class="language-http highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nf">GET</span> <span class="nn">http://localhost:3333/scim/v2/Roles</span> <span class="k">HTTP</span><span class="o">/</span><span class="m">1.1</span>
</code></pre></div></div>

<h3 id="return-database-defined-roles-in-the-scim-roles-endpoint">Return database-defined roles in the SCIM <code class="language-plaintext highlighter-rouge">/Roles</code> endpoint</h3>

<p>Each role has an ID and a name. We can retrieve the roles from the database and populate the <code class="language-plaintext highlighter-rouge">/Roles</code> response.</p>

<p>Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/entitlements.ts</code> and make the changes to retrieve the roles from the database and map the database results to the <code class="language-plaintext highlighter-rouge">IOktaRole</code> properties. You’ll need to import some dependencies, so ensure the import statements match. The SCIM <code class="language-plaintext highlighter-rouge">ListResponse</code> supports pagination, so we’ll add the required code to consider the query parameters.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">Router</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">express</span><span class="dl">'</span><span class="p">;</span>
<span class="k">import</span> <span class="p">{</span> <span class="nx">PrismaClient</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">@prisma/client</span><span class="dl">'</span><span class="p">;</span>
<span class="k">import</span> <span class="p">{</span> <span class="nx">IListResponse</span><span class="p">,</span> <span class="nx">IOktaRole</span><span class="p">,</span> <span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">,</span> <span class="nx">SCHEMA_OKTA_ROLE</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./scim-types</span><span class="dl">'</span><span class="p">;</span>

<span class="kd">const</span> <span class="nx">prisma</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">PrismaClient</span><span class="p">();</span>

<span class="k">export</span> <span class="kd">const</span> <span class="nx">rolesRoute</span> <span class="o">=</span> <span class="nx">Router</span><span class="p">();</span>

<span class="nx">rolesRoute</span><span class="p">.</span><span class="nx">route</span><span class="p">(</span><span class="dl">'</span><span class="s1">/</span><span class="dl">'</span><span class="p">)</span>
<span class="p">.</span><span class="kd">get</span><span class="p">(</span><span class="k">async</span> <span class="p">(</span><span class="nx">req</span><span class="p">,</span> <span class="nx">res</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">startIndex</span> <span class="o">=</span> <span class="nb">parseInt</span><span class="p">(</span><span class="nx">req</span><span class="p">.</span><span class="nx">query</span><span class="p">.</span><span class="nx">startIndex</span> <span class="k">as</span> <span class="kr">string</span> <span class="o">??</span> <span class="dl">'</span><span class="s1">1</span><span class="dl">'</span><span class="p">);</span>
  <span class="kd">const</span> <span class="nx">recordLimit</span> <span class="o">=</span> <span class="nb">parseInt</span><span class="p">(</span><span class="nx">req</span><span class="p">.</span><span class="nx">query</span><span class="p">.</span><span class="nx">recordLimit</span> <span class="k">as</span> <span class="kr">string</span> <span class="o">??</span> <span class="dl">'</span><span class="s1">100</span><span class="dl">'</span><span class="p">);</span>

  <span class="kd">const</span> <span class="nx">roles</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">role</span><span class="p">.</span><span class="nx">findMany</span><span class="p">({</span>
    <span class="na">take</span><span class="p">:</span> <span class="nx">recordLimit</span><span class="p">,</span>
    <span class="na">skip</span><span class="p">:</span> <span class="nx">startIndex</span> <span class="o">-</span> <span class="mi">1</span>
  <span class="p">});</span>

<span class="kd">const</span> <span class="na">listResponse</span><span class="p">:</span> <span class="nx">IListResponse</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">],</span>
    <span class="na">totalResults</span><span class="p">:</span> <span class="nx">roles</span><span class="p">.</span><span class="nx">length</span><span class="p">,</span>
    <span class="nx">startIndex</span><span class="p">,</span>
    <span class="na">itemsPerPage</span><span class="p">:</span> <span class="nx">recordLimit</span><span class="p">,</span>
    <span class="na">Resources</span><span class="p">:</span> <span class="nx">roles</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span>
      <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">],</span>
      <span class="na">id</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">(),</span>
      <span class="na">displayName</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">name</span>
    <span class="p">}))</span>
  <span class="p">};</span>

  <span class="k">return</span> <span class="nx">res</span><span class="p">.</span><span class="nx">json</span><span class="p">(</span><span class="nx">listResponse</span><span class="p">);</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Run a quick check to ensure everything still works. Serve the API and call the <code class="language-plaintext highlighter-rouge">/Roles</code> endpoint using your HTTP client. ✅</p>

<p>You should see three roles matching the roles in the database. 🎉</p>

<h2 id="scim-resource-types">SCIM resource types</h2>

<p>We implemented the <code class="language-plaintext highlighter-rouge">/Roles</code> endpoint and discussed how SCIM defines a resource. But how would the SCIM client know about this Okta Role type? Enter discovery—learning about a SCIM server’s capabilities and supported objects such as resources!</p>

<p>SCIM clients and servers communicate about the types of resources through a standard endpoint, the<code class="language-plaintext highlighter-rouge">/ResourceType</code> endpoint. SCIM clients call the endpoint to discover what resources they can expect. The endpoint returns a SCIM list response outlining resources. You can add every resource type used, including the standard <code class="language-plaintext highlighter-rouge">User</code> and <code class="language-plaintext highlighter-rouge">EnterpriseUser</code> resources, but Okta expects resource definitions only for custom types.</p>

<p>First, we’ll create the interface for the <code class="language-plaintext highlighter-rouge">ResourceType</code> and define some strings. Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/scim-types.ts</code>. Add the interface for <code class="language-plaintext highlighter-rouge">IResourceType</code> above the <code class="language-plaintext highlighter-rouge">IListResponse</code> interface.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IResourceType</span> <span class="p">{</span>
  <span class="nl">id</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">schemas</span><span class="p">:</span> <span class="kr">string</span><span class="p">[];</span>
  <span class="nl">name</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span> 
  <span class="nl">description</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">endpoint</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">schema</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span> 
  <span class="nl">meta</span><span class="p">:</span> <span class="nx">IMetadata</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Notice the <code class="language-plaintext highlighter-rouge">IResourceType</code> doesn’t extend from the <code class="language-plaintext highlighter-rouge">IScimResource</code> interface. For example, the SCIM standard doesn’t require <code class="language-plaintext highlighter-rouge">id</code> for a resource type. Since the SCIM standard treats <code class="language-plaintext highlighter-rouge">ResourceType</code> as an exception case of <code class="language-plaintext highlighter-rouge">Resource</code>, we defined it separately without the relation instead of extending from <code class="language-plaintext highlighter-rouge">IScimResource</code>.</p>

<p>When following the SCIM protocol, responses that list values, such as the list of roles or resource types, use the SCIM list response format.</p>

<p>The <code class="language-plaintext highlighter-rouge">IListResource</code> interface must support <code class="language-plaintext highlighter-rouge">IOktaRole</code> and <code class="language-plaintext highlighter-rouge">IResourceType</code>. Using <a href="https://www.typescriptlang.org/docs/handbook/2/generics.html">generics</a> and <a href="https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types">union types</a>, we can support different list response objects . Update the <code class="language-plaintext highlighter-rouge">IListResource</code> to match the code below.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IListResponse</span><span class="o">&lt;</span><span class="nx">T</span> <span class="kd">extends</span> <span class="nx">IScimResource</span> <span class="o">|</span> <span class="nx">IResourceType</span><span class="o">&gt;</span> <span class="p">{</span>
  <span class="na">schemas</span><span class="p">:</span> <span class="kr">string</span><span class="p">[];</span>
  <span class="nl">totalResults</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">startIndex</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">itemsPerPage</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">Resources</span><span class="p">:</span> <span class="nx">T</span><span class="p">[];</span>
<span class="p">}</span>
</code></pre></div></div>

<p>You’ll see errors in the IDE and, if you’re running the API, within the console output. No worries; we’ll fix those errors soon!</p>

<p>Resource types have a schema URN and use “ResourceType” as the <code class="language-plaintext highlighter-rouge">resourceType</code> string in the metadata. Add <code class="language-plaintext highlighter-rouge">SCHEMA_RESOURCE_TYPE</code> and edit <code class="language-plaintext highlighter-rouge">RESOURCE_TYPES</code> so your string constants section looks like the code below.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kd">const</span> <span class="nx">SCHEMA_OKTA_ROLE</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">urn:okta:scim:schemas:core:1.0:Role</span><span class="dl">'</span><span class="p">;</span>
<span class="k">export</span> <span class="kd">const</span> <span class="nx">SCHEMA_LIST_RESPONSE</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">urn:ietf:params:scim:api:messages:2.0:ListResponse</span><span class="dl">'</span><span class="p">;</span>
<span class="k">export</span> <span class="kd">const</span> <span class="nx">SCHEMA_RESOURCE_TYPE</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">urn:ietf:params:scim:schemas:core:2.0:ResourceType</span><span class="dl">'</span><span class="p">;</span>
<span class="k">export</span> <span class="kd">type</span> <span class="nx">RESOURCE_TYPES</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">Role</span><span class="dl">'</span> <span class="o">|</span> <span class="dl">'</span><span class="s1">ResourceType</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/entitlements.ts</code>. Let’s fix the <code class="language-plaintext highlighter-rouge">IListResponse</code> error for the <code class="language-plaintext highlighter-rouge">/Roles</code> endpoint and specify the object type in the list, the <code class="language-plaintext highlighter-rouge">IOktaRole</code> type. The code building out the list changes to</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code>  <span class="kd">const</span> <span class="nx">listResponse</span><span class="p">:</span> <span class="nx">IListResponse</span><span class="o">&lt;</span><span class="nx">IOktaRole</span><span class="o">&gt;</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">],</span>
    <span class="na">totalResults</span><span class="p">:</span> <span class="nx">roles</span><span class="p">.</span><span class="nx">length</span><span class="p">,</span>
    <span class="nx">startIndex</span><span class="p">,</span>
    <span class="na">itemsPerPage</span><span class="p">:</span> <span class="nx">recordLimit</span><span class="p">,</span>
    <span class="na">Resources</span><span class="p">:</span> <span class="nx">roles</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span>
      <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">],</span>
      <span class="na">id</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">(),</span>
      <span class="na">displayName</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">name</span>
    <span class="p">}))</span>
  <span class="p">};</span>
</code></pre></div></div>

<p>You shouldn’t see errors anymore! 🎉</p>

<p>We have a new endpoint to add. Update the imports from the <code class="language-plaintext highlighter-rouge">./scim-types</code> file and declare a new route for resource types.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">Router</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">express</span><span class="dl">'</span><span class="p">;</span>
<span class="k">import</span> <span class="p">{</span> <span class="nx">PrismaClient</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">@prisma/client</span><span class="dl">'</span><span class="p">;</span>
<span class="k">import</span> <span class="p">{</span>
  <span class="nx">IListResponse</span><span class="p">,</span> <span class="nx">IOktaRole</span><span class="p">,</span> <span class="nx">IResourceType</span><span class="p">,</span> <span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">,</span> <span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">,</span> <span class="nx">SCHEMA_RESOURCE_TYPE</span>
<span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./scim-types</span><span class="dl">'</span><span class="p">;</span>

<span class="kd">const</span> <span class="nx">prisma</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">PrismaClient</span><span class="p">();</span>
<span class="k">export</span> <span class="kd">const</span> <span class="nx">rolesRoute</span> <span class="o">=</span> <span class="nx">Router</span><span class="p">();</span>
<span class="k">export</span> <span class="kd">const</span> <span class="nx">resourceTypesRoute</span> <span class="o">=</span> <span class="nx">Router</span><span class="p">();</span>


<span class="c1">// existing rolesRoute code below</span>
</code></pre></div></div>

<p>Then create the <code class="language-plaintext highlighter-rouge">/ResourceTypes</code> route by adding the code below the <code class="language-plaintext highlighter-rouge">rolesRoute</code></p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">resourceTypesRoute</span><span class="p">.</span><span class="nx">route</span><span class="p">(</span><span class="dl">'</span><span class="s1">/</span><span class="dl">'</span><span class="p">)</span>
<span class="p">.</span><span class="kd">get</span><span class="p">((</span><span class="nx">req</span><span class="p">,</span> <span class="nx">res</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="na">resourceTypes</span><span class="p">:</span> <span class="nx">IResourceType</span><span class="p">[]</span> <span class="o">=</span> <span class="p">[{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_RESOURCE_TYPE</span><span class="p">],</span>
    <span class="na">id</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Role</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Role</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">endpoint</span><span class="p">:</span> <span class="dl">'</span><span class="s1">/Roles</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">description</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Roles you can set on users of Todo App</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">schema</span><span class="p">:</span> <span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">,</span>
    <span class="na">meta</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">resourceType</span><span class="p">:</span> <span class="dl">'</span><span class="s1">ResourceType</span><span class="dl">'</span>
    <span class="p">}</span>
  <span class="p">}];</span>

  <span class="kd">const</span> <span class="na">resourceTypesListResponse</span><span class="p">:</span> <span class="nx">IListResponse</span><span class="o">&lt;</span><span class="nx">IResourceType</span><span class="o">&gt;</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">],</span>
    <span class="na">totalResults</span><span class="p">:</span> <span class="nx">resourceTypes</span><span class="p">.</span><span class="nx">length</span><span class="p">,</span>
    <span class="na">startIndex</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
    <span class="na">itemsPerPage</span><span class="p">:</span> <span class="nx">resourceTypes</span><span class="p">.</span><span class="nx">length</span><span class="p">,</span>
    <span class="na">Resources</span><span class="p">:</span> <span class="nx">resourceTypes</span>
  <span class="p">};</span>

  <span class="k">return</span> <span class="nx">res</span><span class="p">.</span><span class="nx">json</span><span class="p">(</span><span class="nx">resourceTypesListResponse</span><span class="p">);</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Next, you must register the <code class="language-plaintext highlighter-rouge">/ResourceTypes</code> route in the API. Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/scim.ts</code>.</p>

<p>Update the import to include <code class="language-plaintext highlighter-rouge">resourceTypesRoute</code></p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">resourceTypesRoute</span><span class="p">,</span> <span class="nx">rolesRoute</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./entitlements</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>Add the <code class="language-plaintext highlighter-rouge">/ResourceTypes</code> endpoint to the end of the file. You should have two routes defined.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">scimRoute</span><span class="p">.</span><span class="nx">use</span><span class="p">(</span><span class="dl">'</span><span class="s1">/Roles</span><span class="dl">'</span><span class="p">,</span> <span class="nx">rolesRoute</span> <span class="p">);</span>
<span class="nx">scimRoute</span><span class="p">.</span><span class="nx">use</span><span class="p">(</span><span class="dl">'</span><span class="s1">/ResourceTypes</span><span class="dl">'</span><span class="p">,</span> <span class="nx">resourceTypesRoute</span><span class="p">);</span>
</code></pre></div></div>

<p>Double-check your new route by starting the API if it’s not running. Use your HTTP client to make the call</p>

<div class="language-http highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nf">GET</span> <span class="nn">http://localhost:3333/scim/v2/ResourceTypes</span> <span class="k">HTTP</span><span class="o">/</span><span class="m">1.1</span>
</code></pre></div></div>

<p>If you see a response with the Okta role resource type, the API call works as expected! ✅</p>

<h2 id="add-roles-to-the-scim-users-endpoints">Add roles to the SCIM Users endpoints</h2>

<p>Let’s add roles to the existing user calls. We want to reflect a user’s roles in Okta within the Todo app, so the GET and POST <code class="language-plaintext highlighter-rouge">/Users</code> calls must support roles. Near the top of the <code class="language-plaintext highlighter-rouge">scim.ts</code> file, find <code class="language-plaintext highlighter-rouge">IUserSchema</code> interface.</p>

<p>Update the interface to add the <code class="language-plaintext highlighter-rouge">roles</code> property:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kr">interface</span> <span class="nx">IUserSchema</span> <span class="p">{</span>
  <span class="nl">schemas</span><span class="p">:</span> <span class="kr">string</span><span class="p">[];</span>
  <span class="nl">userName</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">id</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">name</span><span class="p">?:</span> <span class="p">{</span>
    <span class="na">givenName</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
    <span class="nl">familyName</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="p">};</span>
  <span class="nl">emails</span><span class="p">?:</span> <span class="p">{</span><span class="na">primary</span><span class="p">:</span> <span class="nx">boolean</span><span class="p">,</span> <span class="na">value</span><span class="p">:</span> <span class="kr">string</span><span class="p">,</span> <span class="na">type</span><span class="p">:</span> <span class="kr">string</span><span class="p">}[];</span>
  <span class="nl">displayName</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">locale</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">meta</span><span class="p">?:</span> <span class="p">{</span>
    <span class="na">resourceType</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="p">}</span>
  <span class="nl">externalId</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">groups</span><span class="p">?:</span> <span class="p">[];</span>
  <span class="nl">password</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">active</span><span class="p">?:</span> <span class="nx">boolean</span><span class="p">;</span>
  <span class="nl">detail</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">status</span><span class="p">?:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">roles</span><span class="p">?:</span> <span class="p">{</span><span class="na">value</span><span class="p">:</span> <span class="kr">string</span><span class="p">,</span> <span class="na">display</span><span class="p">:</span> <span class="kr">string</span><span class="p">}[];</span>
<span class="p">}</span>
</code></pre></div></div>

<p>The User SCIM schema defines <code class="language-plaintext highlighter-rouge">roles</code> property as a list of objects that may contain properties named <code class="language-plaintext highlighter-rouge">value</code> and <code class="language-plaintext highlighter-rouge">display</code>, among others. Okta uses these properties for role data.</p>

<h3 id="update-the-scim-add-users-call-to-include-roles">Update the SCIM add users call to include roles</h3>

<p>The first route defined is the <code class="language-plaintext highlighter-rouge">POST /Users</code> route definition. You need to add roles when saving to the database. Find the comment</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// Create the User in the database</span>
</code></pre></div></div>

<p>and update the database command and the as shown.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// Create the User in the database</span>
<span class="kd">const</span> <span class="nx">user</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">create</span><span class="p">({</span>
  <span class="na">data</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">org</span> <span class="p">:</span> <span class="p">{</span> <span class="na">connect</span><span class="p">:</span> <span class="p">{</span><span class="na">id</span><span class="p">:</span> <span class="nx">ORG_ID</span><span class="p">}},</span>
    <span class="nx">name</span><span class="p">,</span>
    <span class="nx">email</span><span class="p">,</span>
    <span class="nx">password</span><span class="p">,</span>
    <span class="nx">externalId</span><span class="p">,</span>
    <span class="nx">active</span><span class="p">,</span>
    <span class="na">roles</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">connect</span><span class="p">:</span> <span class="nx">newUser</span><span class="p">.</span><span class="nx">roles</span><span class="p">?.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span><span class="na">id</span><span class="p">:</span> <span class="nb">parseInt</span><span class="p">(</span><span class="nx">role</span><span class="p">.</span><span class="nx">value</span><span class="p">)}))</span> <span class="o">||</span> <span class="p">[]</span>
    <span class="p">}</span>
  <span class="p">},</span>
  <span class="na">include</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">roles</span><span class="p">:</span> <span class="kc">true</span>
  <span class="p">}</span>
<span class="p">});</span>

<span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">Account Created ID: </span><span class="dl">'</span><span class="p">,</span> <span class="nx">user</span><span class="p">.</span><span class="nx">id</span><span class="p">);</span>
</code></pre></div></div>

<p>One more place to update in the <code class="language-plaintext highlighter-rouge">POST /Users</code> call. We need to return the roles in the response. Right below the <code class="language-plaintext highlighter-rouge">console.log()</code> update the <code class="language-plaintext highlighter-rouge">userResponse</code> to</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">userResponse</span> <span class="o">=</span> <span class="p">{</span> <span class="p">...</span><span class="nx">defaultUserSchema</span><span class="p">,</span>
  <span class="na">id</span><span class="p">:</span> <span class="s2">`</span><span class="p">${</span><span class="nx">user</span><span class="p">.</span><span class="nx">id</span><span class="p">}</span><span class="s2">`</span><span class="p">,</span>
  <span class="na">userName</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">email</span><span class="p">,</span>
  <span class="na">name</span><span class="p">:</span> <span class="p">{</span>
    <span class="nx">givenName</span><span class="p">,</span>
    <span class="nx">familyName</span>
  <span class="p">},</span>
  <span class="na">emails</span><span class="p">:</span> <span class="p">[{</span>
    <span class="na">primary</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">value</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">email</span><span class="p">,</span>
    <span class="na">type</span><span class="p">:</span> <span class="dl">"</span><span class="s2">work</span><span class="dl">"</span>
  <span class="p">}],</span>
  <span class="na">displayName</span><span class="p">:</span> <span class="nx">name</span><span class="p">,</span>
  <span class="na">externalId</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">externalId</span><span class="p">,</span>
  <span class="na">active</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">active</span><span class="p">,</span>
  <span class="na">roles</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">roles</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span><span class="na">display</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">name</span><span class="p">,</span> <span class="na">value</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">()}))</span>
<span class="p">};</span>
</code></pre></div></div>

<h3 id="add-roles-when-getting-a-list-of-users-in-scim">Add roles when getting a list of users in SCIM</h3>

<p>Continuing to the <code class="language-plaintext highlighter-rouge">GET /Users</code> call, search for the code to find users in the database</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">findMany</span><span class="p">({...});</span>
</code></pre></div></div>

<p>to add <code class="language-plaintext highlighter-rouge">roles</code> to the <code class="language-plaintext highlighter-rouge">select</code> argument.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="nx">users</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">findMany</span><span class="p">({</span>
  <span class="na">take</span><span class="p">:</span> <span class="nx">recordLimit</span><span class="p">,</span>
  <span class="na">skip</span><span class="p">:</span> <span class="nx">startIndex</span><span class="p">,</span>
  <span class="na">select</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">id</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">email</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">name</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">externalId</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">active</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">roles</span><span class="p">:</span> <span class="kc">true</span>
  <span class="p">},</span>
  <span class="nx">where</span>
<span class="p">});</span>
</code></pre></div></div>

<p>The <code class="language-plaintext highlighter-rouge">GET /Users</code> response also needs roles, so update the</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">usersResponse</span><span class="p">[</span><span class="dl">'</span><span class="s1">Resources</span><span class="dl">'</span><span class="p">]</span> <span class="o">=</span> <span class="nx">users</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">user</span> <span class="o">=&gt;</span> <span class="p">{...});</span>
</code></pre></div></div>

<p>like this.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">usersResponse</span><span class="p">[</span><span class="dl">'</span><span class="s1">Resources</span><span class="dl">'</span><span class="p">]</span> <span class="o">=</span> <span class="nx">users</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">user</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="p">[</span><span class="nx">givenName</span><span class="p">,</span> <span class="nx">familyName</span><span class="p">]</span> <span class="o">=</span> <span class="nx">user</span><span class="p">.</span><span class="nx">name</span><span class="p">.</span><span class="nx">split</span><span class="p">(</span><span class="dl">"</span><span class="s2"> </span><span class="dl">"</span><span class="p">);</span>
  <span class="k">return</span> <span class="p">{</span>
    <span class="p">...</span><span class="nx">defaultUserSchema</span><span class="p">,</span>
    <span class="na">id</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">(),</span>
    <span class="na">userName</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">email</span><span class="p">,</span>
    <span class="na">name</span><span class="p">:</span> <span class="p">{</span>
      <span class="nx">givenName</span><span class="p">,</span>
      <span class="nx">familyName</span>
    <span class="p">},</span>
    <span class="na">emails</span><span class="p">:</span> <span class="p">[{</span>
      <span class="na">primary</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
      <span class="na">value</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">email</span><span class="p">,</span>
      <span class="na">type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">work</span><span class="dl">'</span>
    <span class="p">}],</span>
    <span class="na">displayName</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">name</span><span class="p">,</span>
    <span class="na">externalId</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">externalId</span><span class="p">,</span>
    <span class="na">active</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">active</span><span class="p">,</span>
    <span class="na">roles</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">roles</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span><span class="na">display</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">name</span><span class="p">,</span> <span class="na">value</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">()}))</span>
  <span class="p">}</span>
<span class="p">});</span>
</code></pre></div></div>

<h4 id="update-the-response-for-an-individual-user">Update the response for an individual user</h4>

<p>On to the next call, <code class="language-plaintext highlighter-rouge">GET /Users/:userId</code>. We need to add <code class="language-plaintext highlighter-rouge">roles</code> to the</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="nx">user</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">findFirst</span><span class="p">({...});</span>
</code></pre></div></div>

<p>database command. Update it to match the code below.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="nx">user</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">findFirst</span><span class="p">({</span>
  <span class="na">select</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">id</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">email</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">name</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">externalId</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">active</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">roles</span><span class="p">:</span> <span class="kc">true</span>
  <span class="p">},</span>
  <span class="na">where</span><span class="p">:</span> <span class="p">{</span>
    <span class="nx">id</span><span class="p">,</span>
    <span class="na">org</span><span class="p">:</span> <span class="p">{</span><span class="na">id</span><span class="p">:</span> <span class="nx">ORG_ID</span><span class="p">},</span>
  <span class="p">}</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Then, find the comment</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// If no response from DB, return 404</span>
</code></pre></div></div>

<p>to update the <code class="language-plaintext highlighter-rouge">userResponse</code> object inside the <code class="language-plaintext highlighter-rouge">if</code> statement. Update the <code class="language-plaintext highlighter-rouge">userResponse</code> to match the code shown.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">userResponse</span> <span class="o">=</span> <span class="p">{</span>
  <span class="p">...</span><span class="nx">defaultUserSchema</span><span class="p">,</span>
  <span class="na">id</span><span class="p">:</span> <span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">(),</span>
  <span class="na">userName</span><span class="p">:</span> <span class="nx">email</span><span class="p">,</span>
  <span class="na">name</span><span class="p">:</span> <span class="p">{</span>
    <span class="nx">givenName</span><span class="p">,</span>
    <span class="nx">familyName</span>
  <span class="p">},</span>
  <span class="na">emails</span><span class="p">:</span> <span class="p">[{</span>
    <span class="na">primary</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">value</span><span class="p">:</span> <span class="nx">email</span><span class="p">,</span>
    <span class="na">type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">work</span><span class="dl">'</span>
  <span class="p">}],</span>
  <span class="na">displayName</span><span class="p">:</span> <span class="nx">name</span><span class="p">,</span>
  <span class="na">externalId</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">externalId</span><span class="p">,</span>
  <span class="na">active</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">active</span><span class="p">,</span>
  <span class="na">roles</span><span class="p">:</span> <span class="nx">user</span><span class="p">.</span><span class="nx">roles</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span><span class="na">display</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">name</span><span class="p">,</span> <span class="na">value</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">()}))</span>
<span class="p">}</span> <span class="nx">satisfies</span> <span class="nx">IUserSchema</span><span class="p">;</span>
</code></pre></div></div>

<h3 id="update-the-users-call-so-scim-clients-can-set-their-roles">Update the <code class="language-plaintext highlighter-rouge">/Users</code> call so SCIM clients can set their roles</h3>

<p>Another endpoint down, but there’s one more left, the <code class="language-plaintext highlighter-rouge">PUT /Users/:userId</code>.</p>

<p>Find the code</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="p">{</span> <span class="nx">name</span><span class="p">,</span> <span class="nx">emails</span> <span class="p">}</span> <span class="o">=</span> <span class="nx">updatedUserRequest</span><span class="p">;</span>
</code></pre></div></div>

<p>and change it to the following code so we can work with the user’s updated roles and save the changes in the database.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="p">{</span> <span class="nx">name</span><span class="p">,</span> <span class="nx">emails</span><span class="p">,</span> <span class="nx">roles</span> <span class="p">}</span> <span class="o">=</span> <span class="nx">updatedUserRequest</span><span class="p">;</span>

<span class="kd">const</span> <span class="nx">updatedUser</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">prisma</span><span class="p">.</span><span class="nx">user</span><span class="p">.</span><span class="nx">update</span><span class="p">({</span>
  <span class="na">data</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">email</span><span class="p">:</span> <span class="nx">emails</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">email</span> <span class="o">=&gt;</span> <span class="nx">email</span><span class="p">.</span><span class="nx">primary</span><span class="p">).</span><span class="nx">value</span><span class="p">,</span>
    <span class="na">name</span><span class="p">:</span> <span class="s2">`</span><span class="p">${</span><span class="nx">name</span><span class="p">.</span><span class="nx">givenName</span><span class="p">}</span><span class="s2"> </span><span class="p">${</span><span class="nx">name</span><span class="p">.</span><span class="nx">familyName</span><span class="p">}</span><span class="s2">`</span><span class="p">,</span>
    <span class="na">roles</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">set</span><span class="p">:</span> <span class="nx">roles</span><span class="p">?.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span><span class="na">id</span><span class="p">:</span> <span class="nb">parseInt</span><span class="p">(</span><span class="nx">role</span><span class="p">.</span><span class="nx">value</span><span class="p">)}))</span> <span class="o">||</span> <span class="p">[]</span>
    <span class="p">}</span>
  <span class="p">},</span>
  <span class="na">where</span> <span class="p">:</span> <span class="p">{</span>
    <span class="nx">id</span>
  <span class="p">},</span>
  <span class="na">include</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">roles</span><span class="p">:</span> <span class="kc">true</span>
  <span class="p">}</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Lastly, we need to update the response from the <code class="language-plaintext highlighter-rouge">PUT /Users/:userId</code> call. Update the <code class="language-plaintext highlighter-rouge">userResponse</code> object to look like this.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">userResponse</span> <span class="o">=</span> <span class="p">{</span>
  <span class="p">...</span><span class="nx">defaultUserSchema</span><span class="p">,</span>
  <span class="na">id</span><span class="p">:</span> <span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">(),</span>
  <span class="na">userName</span><span class="p">:</span> <span class="nx">updatedUser</span><span class="p">.</span><span class="nx">email</span><span class="p">,</span>
  <span class="na">name</span><span class="p">:</span> <span class="p">{</span>
    <span class="nx">givenName</span><span class="p">,</span>
    <span class="nx">familyName</span>
  <span class="p">},</span>
  <span class="na">emails</span><span class="p">:</span> <span class="p">[{</span>
    <span class="na">primary</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="na">value</span><span class="p">:</span> <span class="nx">updatedUser</span><span class="p">.</span><span class="nx">email</span><span class="p">,</span>
    <span class="na">type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">work</span><span class="dl">'</span>
  <span class="p">}],</span>
  <span class="na">displayName</span><span class="p">:</span> <span class="nx">updatedUser</span><span class="p">.</span><span class="nx">name</span><span class="p">,</span>
  <span class="na">externalId</span><span class="p">:</span> <span class="nx">updatedUser</span><span class="p">.</span><span class="nx">externalId</span><span class="p">,</span>
  <span class="na">active</span><span class="p">:</span> <span class="nx">updatedUser</span><span class="p">.</span><span class="nx">active</span><span class="p">,</span>
  <span class="na">roles</span><span class="p">:</span> <span class="nx">updatedUser</span><span class="p">.</span><span class="nx">roles</span><span class="p">?.</span><span class="nx">map</span><span class="p">(</span><span class="nx">role</span> <span class="o">=&gt;</span> <span class="p">({</span><span class="na">display</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">name</span><span class="p">,</span> <span class="na">value</span><span class="p">:</span> <span class="nx">role</span><span class="p">.</span><span class="nx">id</span><span class="p">.</span><span class="nx">toString</span><span class="p">()}))</span>
<span class="p">}</span> <span class="nx">satisfies</span> <span class="nx">IUserSchema</span><span class="p">;</span>
</code></pre></div></div>

<p>Serve the API if you aren’t running it using <code class="language-plaintext highlighter-rouge">npm run serve-api</code>. Let’s make an HTTP call to get all users to double-check our work.</p>

<div class="language-http highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="err">GET http://localhost:3333/scim/v2/Users
Authorization: Bearer 123123
</span></code></pre></div></div>

<p>You will see the list of users. Each user object has a <code class="language-plaintext highlighter-rouge">roles</code> and <code class="language-plaintext highlighter-rouge">entitlements</code> property. ✅</p>

<h2 id="entitlements-discovery-in-okta">Entitlements discovery in Okta</h2>

<p>What can Okta do with user entitlements? Okta can discover defined entitlements, such as the roles you define for the Todo app, and applies existing roles on users. Now that you have all the endpoints needed for a SCIM client to discover resources held by a SCIM server, you can see this in action on Okta.</p>

<p>You’ll need to serve the API and create a local tunnel. Serve the API using the <code class="language-plaintext highlighter-rouge">npm run serve-api</code> command. In a second terminal window, run <code class="language-plaintext highlighter-rouge">npx localtunnel --port 3333</code>. Take note of your tunnel URL.</p>

<p>Sign into your <a href="https://developer.okta.com/login/">Okta Developer Edition account</a>. Navigate to <strong>Applications</strong> &gt; <strong>Applications</strong> and select the “(Header Auth) Governance with SCIM 2.0” app. Navigate to the <strong>Provisioning</strong> tab and select <strong>Integration</strong>. Press <strong>Edit</strong>.</p>

<p>Update the <strong>Base URL</strong> field by replacing the tunnel URL with your new tunnel URL. Make sure you keep the <code class="language-plaintext highlighter-rouge">/scim/v2</code> path. Your base URL might look something like <code class="language-plaintext highlighter-rouge">https://beep-bop-boop.loca.lt/scim/v2</code>. Press <strong>Save</strong>.</p>

<p>Updating the API integration kicks off a discovery process. Okta automatically looks for roles as a possible entitlement type. It then matches the roles it discovers for the Todo application and matches them again with roles defined on the users. You can see Okta working by looking at the terminal window serving the API. You can see the calls Okta makes by inspecting the HTTP requests and their payloads written to the console.  🔍</p>

<p>Make sure to keep the API running! There’s more work to do here!</p>

<p>Navigate to the <strong>Governance</strong> tab. The tab you see is <strong>Entitlements</strong>. Do you see <strong>Role</strong> in the sidenav below the <strong>Search</strong> input? If not, hang tight. Because an app may have many defined entitlements, Okta starts a background job to discover roles asynchronously. It could take up to 10 minutes for the roles to populate.</p>

<p>Eventually, you’ll see <strong>Role</strong>; when you select it, you’ll see metadata about it, such as the variable name, data type, and description. We also see the values: “Manager,” “Todo Auditor,” and “Todo-er.”</p>

<p><img alt="Governance tab with roles discovered by Okta" class="center-image" src="/assets-jekyll/blog/user-entitlements-workshop/entitlements-roles-7344da8e7387c37c7b6f3c9d16abcbf326e6477fe02ebb172b2eaf2666ee2958.jpg" width="800" /></p>

<p>You can define policies for users that automatically assign their entitlements when adding them to this integration app. While that’s pretty nifty, this post focuses on building out the SCIM endpoints for entitlements, so I’ll include links to resources that explain this feature in more detail at the end of the post.</p>

<p>Press <strong>&lt; Back to application</strong> to return to the SCIM Okta app.</p>

<h3 id="syncing-user-entitlements">Syncing user entitlements</h3>

<p>When you use an identity provider, you want that system to be the source of truth for managing the users’ identities and access levels. You want to set the roles you defined for the Todo app onto users within Okta. That would be pretty sweet, right?</p>

<p>Since we last ran our user import with hardcoded roles, let’s ensure we’ve synchronized everything from the starting state of the application before we start managing with Okta.</p>

<p>Within the SCIM application tab, navigate to <strong>Import</strong> and press the <strong>Import Now</strong> button. Okta scans the users in the todo app, but since there are no new users, there’s no confirmation process. The user scan synced the existing users and the roles!</p>

<p>Navigate to <strong>Assignments</strong>. Each user has a vertical 3-dot menu icon to display a context menu allowing you to <strong>Edit user assignment</strong>, <strong>View access details **, and **Unassign</strong>. Find “Trinity” and **View access details ** on them. A panel shows you Trinity’s role pre-assigned in the Todo app. 🎉 Exit the side panel by clicking outside the side panel.</p>

<p>Let’s assign a new role to “Somnus” using Okta.  Open the context menu for “Somnus” and <strong>View access details</strong>. Press the <strong>Edit access</strong> button. You’ll see a page titled <strong>Edit access</strong>. Press the <strong>Customize entitlements</strong> button. You’ll see a warning followed by a section called <strong>Custom Entitlements</strong>.</p>

<p>You’ll see <strong>Role</strong> and a dropdown list with values. Select a role, such as “Todo-er,” and press <strong>Save</strong> to add the role to the user.</p>

<p>But how about the Todo app? Take a look at the terminal output where you’re serving the API. The HTTP call tracing shows a <code class="language-plaintext highlighter-rouge">PUT</code> request on the user adding the role. Can you see the role of the user in the database? You can check it out by opening another terminal window, running <code class="language-plaintext highlighter-rouge">npx prisma studio</code>, and navigating to the website. ✅</p>

<p>You can now use Okta to manage user roles centrally and automatically update the user’s grants!</p>

<p>Stop serving the local tunnel and API for this next section.</p>

<h3 id="schema-discovery-for-custom-entitlements">Schema discovery for custom entitlements</h3>

<p>What if we have something other than roles in the application? Can SCIM support custom entitlement strategies? SCIM is extensible, meaning it has the structure for custom schemas and extends beyond the core resources. A SCIM server can publish a custom schema if it defines custom resource types.</p>

<p>Let’s say you have user roles but want to add a custom entitlement, such as licenses, profiles, or something else. Let’s walk through the example where we want to add a custom entitlement. We will call this “Characteristic,” such as whether the user is tall. We know Trinity is tall, so it’s logical to note their tallness as part of their user attributes.</p>

<p>SCIM clients must discover resources through schemas. So, we first need to define the schema describing “Characteristics.” Note that I came up with “Characteristics” as the name of this attribute, but you will need to change it for your user entitlements model, whether it be some sort of permissions system or something else. Custom schemas can extend from an existing schema, such as Okta’s entitlement schema, which tracks data as a key-value pair, and add our own flavoring to it.</p>

<p>In the IDE, open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/scim-types.ts</code>.</p>

<p>Add new schema URNs after the <code class="language-plaintext highlighter-rouge">SCHEMA_OKTA_ROLE</code> definition towards the end of the file:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kd">const</span> <span class="nx">SCHEMA_OKTA_ENTITLEMENT</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">urn:okta:scim:schemas:core:1.0:Entitlement</span><span class="dl">'</span><span class="p">;</span>
<span class="k">export</span> <span class="kd">const</span> <span class="nx">SCHEMA_CHARACTERISTIC</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">urn:bestapps:scim:schemas:extension:todoapp:1.0:Characteristic</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>We defined a new schema URN for the characteristic SCIM resource. Following naming conventions for extension schemas, we substituted our company name (Best Apps) and added the app’s name (Todo app). The format looks like this</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>urn:&lt;Company name&gt;:scim:schemas:extension:&lt;App name&gt;:1.0:&lt;Custom entitlement&gt;
</code></pre></div></div>

<p>Right now, there’s a custom TypeScript type for <code class="language-plaintext highlighter-rouge">RESOURCE_TYPES</code>. Since we’ll have custom schemas as a resource type, update the code.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kd">type</span> <span class="nx">RESOURCE_TYPES</span> <span class="o">=</span> <span class="dl">'</span><span class="s1">Role</span><span class="dl">'</span> <span class="o">|</span> <span class="dl">'</span><span class="s1">ResourceType</span><span class="dl">'</span> <span class="o">|</span> <span class="dl">'</span><span class="s1">Schema</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>SCIM defines required and optional attributes to describe a schema resource. We’ll define the interfaces for a schema resource. Add the following interfaces to the <code class="language-plaintext highlighter-rouge">scim-types.ts</code> file. I added mine after the other interfaces and before the URNs.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">ISchema</span> <span class="p">{</span>
  <span class="nl">id</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">name</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">description</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">attributes</span><span class="p">:</span> <span class="nx">IAttribute</span><span class="p">[];</span>
  <span class="nl">meta</span><span class="p">:</span> <span class="nx">IMetadata</span><span class="p">;</span>
<span class="p">}</span>

<span class="k">export</span> <span class="kr">interface</span> <span class="nx">IAttribute</span> <span class="p">{</span>
  <span class="nl">name</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">description</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">type</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">multiValued</span><span class="p">:</span> <span class="nx">boolean</span><span class="p">;</span>
  <span class="nl">required</span><span class="p">:</span> <span class="nx">boolean</span><span class="p">;</span>
  <span class="nl">caseExact</span><span class="p">:</span> <span class="nx">boolean</span><span class="p">;</span>
  <span class="nl">mutability</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">returned</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">uniqueness</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Characteristic is a unique resource type because it’s a new, custom type extending from an existing schema. We must explicitly show this relationship for consuming SCIM clients, like Okta. Find the <code class="language-plaintext highlighter-rouge">IResourceType</code> interface. We’ll add a new optional property, <code class="language-plaintext highlighter-rouge">schemaExtensions</code> and inline the type definition.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IResourceType</span> <span class="p">{</span>
  <span class="nl">id</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">schemas</span><span class="p">:</span> <span class="kr">string</span><span class="p">[];</span>
  <span class="nl">name</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">description</span><span class="p">?:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">endpoint</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">schema</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">schemaExtensions</span><span class="p">?:</span> <span class="p">{</span><span class="na">schema</span><span class="p">:</span> <span class="kr">string</span><span class="p">,</span> <span class="na">required</span><span class="p">:</span> <span class="nx">boolean</span><span class="p">}[];</span>
  <span class="nl">meta</span><span class="p">:</span> <span class="nx">IMetadata</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>SCIM clients expect a list of schemas that you offer in the SCIM server. You might’ve guessed what that means. You must wrap all the schemas in a SCIM <code class="language-plaintext highlighter-rouge">ListResponse</code>. Find <code class="language-plaintext highlighter-rouge">IListResponse</code> and add <code class="language-plaintext highlighter-rouge">ISchema</code> as a supported type. The <code class="language-plaintext highlighter-rouge">IListResponse</code> interface changes to:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">IListResponse</span><span class="o">&lt;</span><span class="nx">T</span> <span class="kd">extends</span> <span class="nx">IScimResource</span> <span class="o">|</span> <span class="nx">IResourceType</span> <span class="o">|</span> <span class="nx">ISchema</span><span class="o">&gt;</span> <span class="p">{</span>
  <span class="na">schemas</span><span class="p">:</span> <span class="kr">string</span><span class="p">[];</span>
  <span class="nl">totalResults</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">startIndex</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">itemsPerPage</span><span class="p">:</span> <span class="kr">number</span><span class="p">;</span>
  <span class="nl">Resources</span><span class="p">:</span> <span class="nx">T</span><span class="p">[];</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Finally, we define what a characteristic attribute looks like by adding the interface shown below.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kr">interface</span> <span class="nx">ICharacteristic</span> <span class="kd">extends</span> <span class="nx">IScimResource</span> <span class="p">{</span>
  <span class="nl">type</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
  <span class="nl">displayName</span><span class="p">:</span> <span class="kr">string</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>With all the types and interfaces defined, it’s time to write the code for the route. Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/entitlements.ts</code>.</p>

<p>Update the import array from <code class="language-plaintext highlighter-rouge">./scim-types.ts</code>:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span>
  <span class="nx">ICharacteristic</span><span class="p">,</span>
  <span class="nx">IListResponse</span><span class="p">,</span>
  <span class="nx">IOktaRole</span><span class="p">,</span>
  <span class="nx">IResourceType</span><span class="p">,</span>
  <span class="nx">ISchema</span><span class="p">,</span>
  <span class="nx">SCHEMA_CHARACTERISTIC</span><span class="p">,</span>
  <span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">,</span>
  <span class="nx">SCHEMA_OKTA_ENTITLEMENT</span><span class="p">,</span>
  <span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">,</span>
  <span class="nx">SCHEMA_RESOURCE_TYPE</span>
<span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./scim-types</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>Below the other route definitions, add two new route definitions.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">export</span> <span class="kd">const</span> <span class="nx">schemasRoute</span> <span class="o">=</span> <span class="nx">Router</span><span class="p">();</span>
<span class="k">export</span> <span class="kd">const</span> <span class="nx">characteristicsRoute</span> <span class="o">=</span> <span class="nx">Router</span><span class="p">();</span>
</code></pre></div></div>

<p>Now, it’s time to define the <code class="language-plaintext highlighter-rouge">/Schemas</code> route. The <code class="language-plaintext highlighter-rouge">/Schemas</code> endpoint returns a list of schemas. You can return schemas for all the resources you use, even for <code class="language-plaintext highlighter-rouge">User</code>, but Okta allows us to skip the strict SCIM requirements and only return custom schemas. The custom schema we’ll return has metadata about a user characteristic, specifically whether the user is tall. Add the following code at the end of the file.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">schemasRoute</span><span class="p">.</span><span class="nx">route</span><span class="p">(</span><span class="dl">'</span><span class="s1">/</span><span class="dl">'</span><span class="p">)</span>
  <span class="p">.</span><span class="kd">get</span><span class="p">((</span><span class="nx">_</span><span class="p">,</span> <span class="nx">res</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="kd">const</span> <span class="na">characteristic</span><span class="p">:</span> <span class="nx">ISchema</span> <span class="o">=</span> <span class="p">{</span>
      <span class="na">id</span><span class="p">:</span> <span class="nx">SCHEMA_CHARACTERISTIC</span><span class="p">,</span>
      <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Characteristic</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">description</span><span class="p">:</span> <span class="dl">'</span><span class="s1">User characteristics for entitlements</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">attributes</span><span class="p">:</span> <span class="p">[{</span>
        <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">is_tall</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">description</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Profile entitlement extension for tallness factor</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">string</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">multiValued</span><span class="p">:</span> <span class="kc">false</span><span class="p">,</span>
        <span class="na">required</span><span class="p">:</span> <span class="kc">false</span><span class="p">,</span>
        <span class="na">mutability</span><span class="p">:</span> <span class="dl">'</span><span class="s1">readWrite</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">returned</span><span class="p">:</span> <span class="dl">'</span><span class="s1">default</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">caseExact</span><span class="p">:</span> <span class="kc">false</span><span class="p">,</span>
        <span class="na">uniqueness</span><span class="p">:</span> <span class="dl">'</span><span class="s1">none</span><span class="dl">'</span>
      <span class="p">}],</span>
      <span class="na">meta</span><span class="p">:</span> <span class="p">{</span>
        <span class="na">resourceType</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Schema</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">location</span><span class="p">:</span> <span class="s2">`/v2/Schemas/</span><span class="p">${</span><span class="nx">SCHEMA_CHARACTERISTIC</span><span class="p">}</span><span class="s2">`</span>
      <span class="p">}</span>
    <span class="p">};</span>

    <span class="kd">const</span> <span class="nx">schemas</span> <span class="o">=</span> <span class="p">{</span>
      <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_LIST_RESPONSE</span><span class="p">],</span>
      <span class="na">totalResults</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
      <span class="na">startIndex</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
      <span class="na">itemsPerPage</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
      <span class="na">Resources</span><span class="p">:</span> <span class="p">[</span>
        <span class="nx">characteristic</span>
      <span class="p">]</span>
    <span class="p">};</span>

    <span class="k">return</span> <span class="nx">res</span><span class="p">.</span><span class="nx">json</span><span class="p">(</span><span class="nx">schemas</span><span class="p">);</span>
  <span class="p">});</span>
</code></pre></div></div>

<p>And we must define a route for <code class="language-plaintext highlighter-rouge">/Characteristics</code>, in the same way one exists for <code class="language-plaintext highlighter-rouge">/Roles</code>. We won’t worry about updating the database for this as I don’t want to detract from the SCIM concepts. We’ll hardcode the characteristic for now so you can see what this looks like within Okta. Feel free to add the required code to connect it to the database as homework. 🏆 Add the following code below the schemas route:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">characteristicsRoute</span><span class="p">.</span><span class="nx">route</span><span class="p">(</span><span class="dl">'</span><span class="s1">/</span><span class="dl">'</span><span class="p">)</span>
  <span class="p">.</span><span class="kd">get</span><span class="p">((</span><span class="nx">_</span><span class="p">,</span> <span class="nx">res</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="kd">const</span> <span class="na">characteristicsListResponse</span><span class="p">:</span> <span class="nx">IListResponse</span><span class="o">&lt;</span><span class="nx">ICharacteristic</span><span class="o">&gt;</span> <span class="o">=</span> <span class="p">{</span>
      <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span>
        <span class="nx">SCHEMA_OKTA_ENTITLEMENT</span><span class="p">,</span>
        <span class="nx">SCHEMA_CHARACTERISTIC</span>
      <span class="p">],</span>
      <span class="na">totalResults</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
      <span class="na">startIndex</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
      <span class="na">itemsPerPage</span><span class="p">:</span> <span class="mi">1</span><span class="p">,</span>
      <span class="na">Resources</span><span class="p">:</span> <span class="p">[{</span>
        <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_CHARACTERISTIC</span><span class="p">],</span>
        <span class="na">type</span><span class="p">:</span> <span class="dl">"</span><span class="s2">Characteristic</span><span class="dl">"</span><span class="p">,</span>
        <span class="na">id</span><span class="p">:</span> <span class="dl">"</span><span class="s2">is_tall</span><span class="dl">"</span><span class="p">,</span>
        <span class="na">displayName</span><span class="p">:</span> <span class="dl">"</span><span class="s2">This user is so tall</span><span class="dl">"</span>
      <span class="p">}]</span>
    <span class="p">};</span>

    <span class="k">return</span> <span class="nx">res</span><span class="p">.</span><span class="nx">json</span><span class="p">(</span><span class="nx">characteristicsListResponse</span><span class="p">);</span>
  <span class="p">});</span>
</code></pre></div></div>

<p>Notice the ID is the string “is_tall”. I modeled it to look like an enum here so that it’s distinct from roles, but IDs in your system may be a UUID or an integer.</p>

<p>Lastly, we must add the new characteristic resource type to the <code class="language-plaintext highlighter-rouge">/ResourceTypes</code> response so that Okta knows the resource exists. Find the <code class="language-plaintext highlighter-rouge">resourceTypes.route('/')</code> definition and update the <code class="language-plaintext highlighter-rouge">resourceTypes</code> array to include both roles and characteristics.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code>  <span class="kd">const</span> <span class="nx">resourceTypes</span><span class="p">:</span> <span class="nx">IResourceType</span><span class="p">[]</span> <span class="o">=</span> <span class="p">[{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_RESOURCE_TYPE</span><span class="p">],</span>
    <span class="na">id</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Role</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Role</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">endpoint</span><span class="p">:</span> <span class="dl">'</span><span class="s1">/Roles</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">description</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Roles you can set on users of Todo App</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">schema</span><span class="p">:</span> <span class="nx">SCHEMA_OKTA_ROLE</span><span class="p">,</span>
    <span class="na">meta</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">resourceType</span><span class="p">:</span> <span class="dl">'</span><span class="s1">ResourceType</span><span class="dl">'</span>
    <span class="p">}</span>
  <span class="p">},</span>
  <span class="p">{</span>
    <span class="na">schemas</span><span class="p">:</span> <span class="p">[</span><span class="nx">SCHEMA_RESOURCE_TYPE</span><span class="p">],</span>
    <span class="na">id</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Characteristic</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">name</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Characteristic</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">endpoint</span><span class="p">:</span> <span class="dl">'</span><span class="s1">/Characteristics</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">description</span><span class="p">:</span> <span class="dl">'</span><span class="s1">This resource type is user characteristics</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">schema</span><span class="p">:</span> <span class="dl">'</span><span class="s1">urn:okta:scim:schemas:core:1.0:Entitlement</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">schemaExtensions</span><span class="p">:</span> <span class="p">[</span>
      <span class="p">{</span>
        <span class="na">schema</span><span class="p">:</span> <span class="nx">SCHEMA_CHARACTERISTIC</span><span class="p">,</span>
        <span class="na">required</span><span class="p">:</span> <span class="kc">true</span>
      <span class="p">}</span>
    <span class="p">],</span>
    <span class="na">meta</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">resourceType</span><span class="p">:</span> <span class="dl">'</span><span class="s1">ResourceType</span><span class="dl">'</span>
    <span class="p">}</span>
  <span class="p">}</span>
<span class="p">];</span>
</code></pre></div></div>

<p>Now, we must register the routes in the API. Open <code class="language-plaintext highlighter-rouge">okta-enterprise-ready-workshops/apps/api/src/scim.ts</code>. At the top of the file, update the imports from <code class="language-plaintext highlighter-rouge">./entitlements</code> to</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="p">{</span> <span class="nx">characteristicsRoute</span><span class="p">,</span> <span class="nx">resourceTypesRoute</span><span class="p">,</span> <span class="nx">rolesRoute</span><span class="p">,</span> <span class="nx">schemasRoute</span> <span class="p">}</span> <span class="k">from</span> <span class="dl">'</span><span class="s1">./entitlements</span><span class="dl">'</span><span class="p">;</span>
</code></pre></div></div>

<p>At the end of the file, add the code to register the <code class="language-plaintext highlighter-rouge">/Schemas</code> and <code class="language-plaintext highlighter-rouge">/Charactertistics</code> routes to the API.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">scimRoute</span><span class="p">.</span><span class="nx">use</span><span class="p">(</span><span class="dl">'</span><span class="s1">/Schemas</span><span class="dl">'</span><span class="p">,</span> <span class="nx">schemasRoute</span><span class="p">);</span>
<span class="nx">scimRoute</span><span class="p">.</span><span class="nx">use</span><span class="p">(</span><span class="dl">'</span><span class="s1">/Characteristics</span><span class="dl">'</span><span class="p">,</span> <span class="nx">characteristicsRoute</span><span class="p">);</span>
</code></pre></div></div>

<p>Serve the API by running <code class="language-plaintext highlighter-rouge">npm run serve-api</code> in a terminal window. In a second terminal window, run <code class="language-plaintext highlighter-rouge">npx localtunnel --port 3333</code> to create a local tunnel for the API. Keep track of the tunnel URL.</p>

<p>Back in the <a href="https://developer.okta.com/login/">Okta Admin console</a>, navigate to <strong>Applications</strong> &gt; <strong>Applications</strong> and open the SCIM with governance Okta app. Navigate to <strong>Provisioning</strong> &gt; <strong>Integration</strong>. Press <strong>Edit</strong> and update the <strong>Base URL</strong> using the new tunnel URL. Don’t forget to keep the <code class="language-plaintext highlighter-rouge">/scim/v2</code> at the end of the URL. The URL should look something like</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>https://{yourTunnelSubdomain}.loca.lt/scim/v2
</code></pre></div></div>

<p>Press <strong>Save</strong>.</p>

<p>Okta discovers schemas and resource types when updating the provisioning configuration. If you look at the HTTP call tracing in the terminal window serving the API, you’ll see that Okta made a GET request to both <code class="language-plaintext highlighter-rouge">/Schemas</code> and <code class="language-plaintext highlighter-rouge">/Characteristics</code>.</p>

<p>Navigate to the <strong>Governance</strong>. <strong>Characteristic</strong> may take 10-15 minutes to populate, but you’ll see the display name and value when it does. Go <strong>&lt; Back to application</strong> and navigate to <strong>Assignments</strong>. Open the user context menu for “Trinity” by pressing the three vertical dots icon menu and opening <strong>View entitlements</strong>. Press <strong>Edit</strong> and <strong>Customize entitlements</strong> to add the <code class="language-plaintext highlighter-rouge">is_tall</code> user characteristic. <strong>Save</strong> the changes and navigate back to the Okta SCIM app.</p>

<p>Check out the terminal serving the API for the HTTP call tracing. You’ll see a <code class="language-plaintext highlighter-rouge">PUT</code> request on Trinity adding the new characteristic. The field goes into the core SCIM User <code class="language-plaintext highlighter-rouge">entitlements</code> property. Check it out by inspecting the HTTP tracing in the console output. ✅</p>

<h2 id="multi-tenant-use-cases-for-entitlements">Multi-tenant use cases for entitlements</h2>

<p>In this workshop, we defined roles for the entire Todo app. But what if your SaaS app supports tenant-configurable roles? You must make structural changes to the Todo app database to support organization roles. Notice that an organization has a unique API key, and we included this API as a <code class="language-plaintext highlighter-rouge">Bearer</code> token value in the <code class="language-plaintext highlighter-rouge">Authorization</code> header. All the SCIM calls from Okta can target a specific organization in the Todo app, including the organization’s custom roles.</p>

<table>
<tr>
    <td style="font-size: 3rem;">️ℹ️</td>
    <td>
      <strong>Note</strong> <br />
      We used an API key for demonstration purposes, but we recommend using OAuth to secure the calls from Okta to your API for production applications.
    </td>
</tr>
</table>

<h2 id="use-scim-to-manage-user-provisioning-and-entitlements">Use SCIM to manage user provisioning and entitlements</h2>

<p>In this workshop, you dived deeper into SCIM and learned about resources and schemas. You also synced users and their pre-existing entitlements from the Todo app and provisioned users within Okta. I hope you enjoyed this workshop and have ideas for using it for your SaaS applications! Check out the <a href="https://help.okta.com/oie/en-us/content/topics/identity-governance/iga.htm">Identity Governance</a> help docs to learn about Okta Identity Governance.</p>

<p>You can find the completed code project in the <a href="https://github.com/oktadev/okta-enterprise-ready-workshops/tree/entitlements-workshop-complete"><code class="language-plaintext highlighter-rouge">entitlements-workshop-completed</code> branch within the GitHub repo</a>.</p>

<p>If you want to learn more about what it means to be enterprise-ready and to have enterprise maturity, check out the other workshops in this series</p>

<table>
  <thead>
    <tr>
      <th>Posts in the on-demand workshop series</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1. <a href="/blog/2023/07/27/enterprise-ready-getting-started">How to Get Going with the On-Demand SaaS Apps Workshops</a></td>
    </tr>
    <tr>
      <td>2. <a href="/blog/2023/07/28/oidc_workshop">Enterprise-Ready Workshop: Authenticate with OpenID Connect</a></td>
    </tr>
    <tr>
      <td>3. <a href="/blog/2023/07/28/scim-workshop">Enterprise-Ready Workshop: Manage Users with SCIM</a></td>
    </tr>
    <tr>
      <td>4. <a href="/blog/2023/07/28/terraform-workshop">Enterprise Maturity Workshop: Terraform</a></td>
    </tr>
    <tr>
      <td>5. <a href="/blog/2023/09/15/workflows-workshop">Enterprise Maturity Workshop: Automate with no-code Okta Workflows</a></td>
    </tr>
    <tr>
      <td>6. <a href="/blog/2024/04/30/express-universal-logout">How to Instantly Sign a User Out across All Your Apps</a></td>
    </tr>
    <tr>
      <td>7. <strong>Take User Provisioning to the Next Level with Entitlements</strong></td>
    </tr>
  </tbody>
</table>

<p>Want to learn about more exciting topics? Let us know by commenting below. To get notified about exciting new content, follow us on <a href="https://twitter.com/oktadev">Twitter</a> and subscribe to our <a href="https://www.youtube.com/c/oktadev">YouTube</a> channel.</p>
