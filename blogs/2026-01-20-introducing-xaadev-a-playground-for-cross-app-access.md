---
title: "Introducing xaa.dev: A Playground for Cross App Access"
url: "https://developer.okta.com/blog/2026/01/20/xaa-dev-playground"
date: "Tue, 20 Jan 2026 00:00:00 -0500"
author: ""
feed_url: "https://developer.okta.com/feed.xml"
---
<p>AI agents are quickly becoming part of everyday enterprise development. They summarize emails, coordinate calendars, query internal systems, and automate workflows across tools.</p>

<p>But once an AI agent needs to access an enterprise application <em>on behalf of a user</em>, things get complicated.</p>

<p>How do you securely let an AI-powered app act for a user without exposing credentials, spamming consent prompts, or losing administrative control?</p>

<p>This is the problem <strong>Cross App Access (XAA)</strong> is designed to solve.</p>

<p>Today, we’re introducing <strong><a href="https://xaa.dev">xaa.dev</a></strong>, a free, open playground that lets you explore Cross App Access end-to-end. <strong>No local setup. No infrastructure to provision.</strong> Just a working environment where you can see the protocol in action.</p>

<p><img alt="xaa.dev playground homepage showing the Cross App Access flow" class="center-image" src="/assets-jekyll/blog/xaa-dev-playground/xaa-dev-homepage-1ced782c627da4675bc483f0b21b24dbb583b30b0fc3978cf753233df80a5cda.jpg" width="800" /></p>

<blockquote>
  <p><strong>Note:</strong> xaa.dev is currently in beta. We’re actively developing new features for the next release, and your feedback helps shape what comes next.</p>
</blockquote>

<p><strong class="hide">Table of Contents</strong></p>

<ul id="markdown-toc">
  <li><a href="#what-is-cross-app-access" id="markdown-toc-what-is-cross-app-access">What is Cross App Access?</a></li>
  <li><a href="#the-problem-testing-xaa-is-hard" id="markdown-toc-the-problem-testing-xaa-is-hard">The problem: testing XAA is hard</a></li>
  <li><a href="#what-you-can-do-on-xaadev" id="markdown-toc-what-you-can-do-on-xaadev">What you can do on xaa.dev</a>    <ul>
      <li><a href="#requesting-app" id="markdown-toc-requesting-app">Requesting App</a></li>
      <li><a href="#resource-app" id="markdown-toc-resource-app">Resource App</a></li>
      <li><a href="#identity-provider" id="markdown-toc-identity-provider">Identity Provider</a></li>
      <li><a href="#resource-mcp-server" id="markdown-toc-resource-mcp-server">Resource MCP Server</a></li>
      <li><a href="#bring-your-own-requesting-app" id="markdown-toc-bring-your-own-requesting-app">Bring your own Requesting App</a></li>
    </ul>
  </li>
  <li><a href="#how-to-get-started" id="markdown-toc-how-to-get-started">How to get started</a></li>
  <li><a href="#why-we-built-a-testing-site-for-cross-app-access" id="markdown-toc-why-we-built-a-testing-site-for-cross-app-access">Why we built a testing site for cross app access</a></li>
  <li><a href="#inspect-the-xaa-flow" id="markdown-toc-inspect-the-xaa-flow">Inspect the XAA flow</a></li>
  <li><a href="#learn-more" id="markdown-toc-learn-more">Learn more</a></li>
</ul>

<h2 id="what-is-cross-app-access">What is Cross App Access?</h2>

<p>Cross App Access refers to a typical enterprise pattern: <strong>one application accesses another application’s resources on behalf of a user.</strong></p>

<p>For example:</p>

<ul>
  <li>An internal AI assistant fetching updates from a project management system</li>
  <li>A workflow engine booking meetings through a calendar API</li>
  <li>An agent querying internal data sources to complete a task</li>
</ul>

<p>Traditionally, OAuth consent flows handle this. That approach works well for consumer-based apps, but it creates friction in enterprise environments where organizations require workforce oversight:</p>

<ul>
  <li>Applications and their access levels are centrally managed</li>
  <li>IT teams need visibility into trust relationships</li>
  <li>Access must be revocable without user involvement</li>
</ul>

<p>Cross App Access shifts responsibility from end users to the enterprise identity layer.</p>

<p>Instead of prompting users for consent, the <strong>Identity Provider (IdP)</strong> issues a signed identity assertion called an <strong>ID-JAG (Identity JWT Authorization Grant)</strong>. This assertion cryptographically represents the user and the requesting application. Resource applications trust the IdP’s assertion and issue access accordingly.</p>

<p>The result:</p>

<ul>
  <li>No interactive consent screens making application access seamless for employees</li>
  <li>Clear, auditable trust boundaries</li>
  <li>Complete administrative control over app-to-app access</li>
</ul>

<p>For a deeper dive into why this matters for enterprise AI, read more about Cross App Access in this post:</p>

<article class="link-container" style="border: 1px solid silver; border-radius: 3px; padding: 12px 15px;">
              <a href="/blog/2025/06/23/enterprise-ai" style="font-size: 1.375em; margin-bottom: 20px;">
                <span>Integrate Your Enterprise AI Tools with Cross-App Access</span>
              </a>
              <p>Manage user and non-human identities, including AI in the enterprise with Cross App Access</p>
              <div><div class="BlogPost-attribution">
            <a href="/blog/authors/semona-igama/">
              <img alt="avatar-avatar-semona-igama.jpeg" class="BlogPost-avatar" src="/assets-jekyll/avatar-semona-igama-03eb4c28aca3765f862b574e032d32f6f8186d04ae9f0db75bed9c74f48a9a3f.jpg" />
            </a>
            <span class="BlogPost-author">
                <a href="/blog/authors/semona-igama/">Semona Igama</a>
            </span>
          </div></div>
          </article>

<h2 id="the-problem-testing-xaa-is-hard">The problem: testing XAA is hard</h2>

<p>XAA is built on an emerging OAuth extension called the <a href="https://datatracker.ietf.org/doc/html/draft-ietf-oauth-identity-assertion-authz-grant">Identity Assertion JWT Authorization Grant</a> – an IETF draft that Okta, along with public and industry contributors, has been actively contributing to. It’s powerful, but it’s also new, and new protocols need experimentation.</p>

<p>Here’s the challenge: to test XAA locally, you’d need to spin up:</p>

<ul>
  <li>An Identity Provider (IdP)</li>
  <li>An Authorization Server for the resource application</li>
  <li>The resource API itself</li>
  <li>A requesting application (the agent or client app)</li>
</ul>

<p>That’s hours (or days) of configuration before you can even see a single token exchange. Most developers give up before getting to the interesting part.</p>

<p><strong>xaa.dev changes that.</strong></p>

<p>We pre-configured all the components so you can focus on understanding the flow, not debugging dev environments. Go from zero to a working XAA token exchange in under 60 seconds.</p>

<p><strong><a href="https://xaa.dev">Launch the playground</a></strong>. It’s free and requires no signup.</p>

<h2 id="what-you-can-do-on-xaadev">What you can do on xaa.dev</h2>

<p>The playground gives you hands-on access to every role in the Cross App Access flow:</p>

<h3 id="requesting-app">Requesting App</h3>
<p>Step into the shoes of an AI agent or client application. Authenticate a user, request an ID-JAG from the IdP, and exchange it for an access token at the resource server.</p>

<h3 id="resource-app">Resource App</h3>
<p>See the other side of the transaction. Watch how a resource server validates the identity assertion, verifies the trust relationship, and issues scoped access tokens.</p>

<h3 id="identity-provider">Identity Provider</h3>
<p>We’ve built a simulated IdP with pre-configured test users. Log in, see how ID-JAGs are minted, and inspect the cryptographic claims that make XAA secure.</p>

<h3 id="resource-mcp-server">Resource MCP Server</h3>
<p>Connect your AI agents using the Model Context Protocol (MCP). The playground provides a ready-to-use MCP server that acts as a resource application, letting you test how AI agents can securely access protected resources through the Cross App Access flow.</p>

<h3 id="bring-your-own-requesting-app">Bring your own Requesting App</h3>
<p>The built-in Requesting App is great for learning, but the real power comes when you test with your own application, whether it’s a traditional app or an MCP client. <a href="https://xaa.dev/developer/register">Register a client</a> on the playground, grab the configuration, and integrate it into your local app. This lets you validate your XAA implementation against a working IdP and Resource App without spinning up your own infrastructure. The <a href="https://xaa.dev/docs">playground documentation</a> walks you through the setup step-by-step.</p>

<h2 id="how-to-get-started">How to get started</h2>

<p>Getting started with xaa.dev takes less than a minute:</p>

<p><strong>Step 1: Open the playground</strong></p>

<p>Visit <a href="https://xaa.dev">xaa.dev</a>. No account required.</p>

<p><strong>Step 2: Explore the components</strong></p>

<p>The playground has three components (Requesting App, Resource App, and Identity Provider), each with its own URL. Visit any component to see its configuration and understand how it participates in the XAA flow.</p>

<p><strong>Step 3: Follow the guided flow</strong></p>

<p>Walk through the four steps of the XAA flow: User Authentication (SSO), Token Exchange, Access Token Request, and Access Resource. Inspect the requests and responses at each step to see exactly how XAA works under the hood.</p>

<p>That’s it. No local tools installations, Docker containers, environment variables, or CORS headaches.</p>

<p>Watch this walkthrough video of the playground if you’d like a guided tour:</p>

<div class="jekyll-youtube-plugin" style="text-align: center;">
            
        </div>

<h2 id="why-we-built-a-testing-site-for-cross-app-access">Why we built a testing site for cross app access</h2>

<p>XAA is built on an emerging IETF specification, the Identity Assertion JWT Authorization Grant. As enterprise AI adoption accelerates, there’s a clear need: developers want to understand XAA, but the barrier to entry is too high.</p>

<p>xaa.dev lowers the barrier. It helps you:</p>

<ul>
  <li><strong>Learn faster</strong> – See the protocol in action before writing any code</li>
  <li><strong>Build confidently</strong> – Understand exactly what tokens to expect and validate</li>
  <li><strong>Experiment safely</strong> – Test edge cases without affecting production systems</li>
</ul>

<h2 id="inspect-the-xaa-flow">Inspect the XAA flow</h2>

<p>XAA is how enterprise applications will securely connect in an AI-first world. Whether you’re building agents, integrating SaaS tools, or just curious about modern OAuth patterns, <a href="https://xaa.dev">xaa.dev</a> gives you a risk-free environment to learn. Check it out and let us know how it works for you!</p>

<h2 id="learn-more">Learn more</h2>

<p>Ready to go deeper? Check out these resources:</p>

<ul>
  <li><a href="https://www.okta.com/integrations/cross-app-access/">Checkout Cross App Access Integration in Okta </a> – Securing AI-driven access together</li>
  <li><a href="/blog/2025/09/03/cross-app-access">Build Secure Agent-to-App Connections with Cross App Access</a> – Hands-on implementation guide</li>
  <li><a href="https://datatracker.ietf.org/doc/html/draft-ietf-oauth-identity-assertion-authz-grant">Identity Assertion JWT Authorization Grant (IETF Draft)</a> – The specification behind XAA</li>
</ul>

<p>Have questions or feedback? Reach out to us on <a href="https://twitter.com/oktadev">Twitter</a>, join the conversation on the <a href="https://devforum.okta.com/">Okta Developer Forums</a>, or drop a comment below. We’re actively improving xaa.dev based on developer input – your feedback shapes what we build next.</p>

<p>Follow us on <a href="https://twitter.com/oktadev">Twitter</a> and subscribe to our <a href="https://youtube.com/oktadev">YouTube channel</a> for more content on identity, security, and building with Okta.</p>
