---
title: "Make Secure App-to-App Connections Using Cross App Access"
url: "https://developer.okta.com/blog/2026/02/10/xaa-client"
date: "Tue, 10 Feb 2026 00:00:00 -0500"
author: ""
feed_url: "https://developer.okta.com/feed.xml"
---
<p>Imagine you built a note-taking app. It’s so successful that LargeCorp, an aptly named large enterprise corporation, signed on as a customer. To make it a power tool for your enterprise customers, you need to allow your app to integrate with other productivity tools, such as turning a note into a task in a to-do app.</p>

<p>While common integration patterns work well for individual users, these patterns create security and compliance hurdles for large organizations.</p>

<h2 id="limitations-of-api-keys-and-oauth-in-enterprise-app-to-app-connectivity">Limitations of API keys and OAuth in enterprise app-to-app connectivity</h2>

<p>Connecting independent apps usually involves one of two common strategies. Both have significant drawbacks when used in a corporate environment:</p>
<ul>
  <li><strong>API keys and service accounts</strong>
  These lack user context. They often lead to over-privileged access and create challenging rotation requirements.</li>
  <li><strong>Standard OAuth 2.0</strong>
  A much better, industry-standard best practice over API keys and service accounts, but this relies on individual user consent. IT admins cannot see or control which apps employees connect to, creating shadow IT risks and compliance and security concerns.</li>
</ul>

<h2 id="cross-app-access-xaa-extends-oauth-flows-to-manage-application-access">Cross App Access (XAA) extends OAuth flows to manage application access</h2>

<p>Cross App Access is an OAuth extension based on the <a href="https://drafts.oauth.net/oauth-identity-assertion-authz-grant/draft-ietf-oauth-identity-assertion-authz-grant.html">Identity Assertion Authorization Grant</a>. It addresses these challenges by using the Enterprise Identity Provider (IdP) as a central broker and was proposed by a collaborative group of organizations and interested individuals.</p>

<p>With XAA, the Identity Provider (IdP) facilitates a secure token exchange. This provides three main benefits.</p>
<ul>
  <li>IT Governance - Admins centrally manage and approve app-to-app connections</li>
  <li>Reduced friction - Users avoid repeated and confusing consent prompts</li>
  <li>Granular security - Access is limited to specific users and specific tasks.</li>
</ul>

<p>You can read in depth about XAA in <a href="/blog/2025/06/23/enterprise-ai">Integrate Your Enterprise AI Tools with Cross App Access</a> to better understand how this works and to look at the token exchange flow</p>

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

<p>In this tutorial, we’ll add XAA to connect a note-taking app to a to-do app using <a href="https://xaa.dev">xaa.dev</a> as our testing ground.</p>

<p><strong class="hide">Table of Contents</strong></p>
<ul id="markdown-toc">
  <li><a href="#limitations-of-api-keys-and-oauth-in-enterprise-app-to-app-connectivity" id="markdown-toc-limitations-of-api-keys-and-oauth-in-enterprise-app-to-app-connectivity">Limitations of API keys and OAuth in enterprise app-to-app connectivity</a></li>
  <li><a href="#cross-app-access-xaa-extends-oauth-flows-to-manage-application-access" id="markdown-toc-cross-app-access-xaa-extends-oauth-flows-to-manage-application-access">Cross App Access (XAA) extends OAuth flows to manage application access</a></li>
  <li><a href="#make-app-to-app-requests-using-cross-app-access" id="markdown-toc-make-app-to-app-requests-using-cross-app-access">Make app-to-app requests using Cross App Access</a></li>
  <li><a href="#bring-your-own-requestor-app-to-the-xaadev-testing-site" id="markdown-toc-bring-your-own-requestor-app-to-the-xaadev-testing-site">Bring your own requestor app to the xaa.dev testing site</a></li>
  <li><a href="#get-the-nestjs-project-with-oauth-and-openid-connect-oidc-started" id="markdown-toc-get-the-nestjs-project-with-oauth-and-openid-connect-oidc-started">Get the NestJS project with OAuth and OpenID Connect (OIDC) started</a></li>
  <li><a href="#exchanging-an-id-token-for-an-access-token-for-another-app" id="markdown-toc-exchanging-an-id-token-for-an-access-token-for-another-app">Exchanging an ID token for an access token for another app</a>    <ul>
      <li><a href="#exchange-the-id-token-for-an-intermediary-id-jag-token-type" id="markdown-toc-exchange-the-id-token-for-an-intermediary-id-jag-token-type">Exchange the ID token for an intermediary ID-JAG token type</a></li>
      <li><a href="#use-the-id-jag-token-to-request-an-access-token-for-a-separate-app" id="markdown-toc-use-the-id-jag-token-to-request-an-access-token-for-a-separate-app">Use the ID-JAG token to request an access token for a separate app</a></li>
    </ul>
  </li>
  <li><a href="#inspecting-the-xaa-token-exchange" id="markdown-toc-inspecting-the-xaa-token-exchange">Inspecting the XAA token exchange</a></li>
  <li><a href="#learn-more-about-xaa-and-elevating-identity-security-using-oauth" id="markdown-toc-learn-more-about-xaa-and-elevating-identity-security-using-oauth">Learn more about XAA and elevating identity security using OAuth</a></li>
</ul>

<h2 id="make-app-to-app-requests-using-cross-app-access">Make app-to-app requests using Cross App Access</h2>

<p>We’re using <a href="https://nestjs.com/">NestJS</a> in this project. The tech stack relies on TypeScript, and we’ll use an OpenID Connect (OIDC) client library to communicate with the IdP and the to-do app’s OAuth Authorization server. Using a well-maintained OIDC client library is a best practice when creating apps that use OAuth flows, as it helps ensure you don’t make subtle errors in OAuth handshakes that compromise security.</p>

<p>For this workshop, you need the following required tooling:</p>

<p><strong>Required tools</strong></p>
<ul>
  <li><a href="https://nodejs.org/en">Node.js</a> LTS version (v22 or higher at the time of this post)</li>
  <li>Command-line terminal application</li>
  <li>A code editor/Integrated development environment (IDE), such as <a href="https://code.visualstudio.com/">Visual Studio Code</a> (VS Code)</li>
  <li><a href="https://git-scm.com/">Git</a></li>
</ul>

<blockquote>
  <p><strong>Note</strong></p>

  <p>This code project is best for developers with web development and TypeScript experience and familiarity with OAuth and OpenID Connect (OIDC) flows at a high level.</p>
</blockquote>

<p>If you want to skip directly to the working project, you can find it <a href="https://github.com/oktadev/okta-js-xaa-requestor-example">in the GitHub repo</a>.</p>

<h2 id="bring-your-own-requestor-app-to-the-xaadev-testing-site">Bring your own requestor app to the xaa.dev testing site</h2>

<p>The <a href="https://xaa.dev">xaa.dev</a> testing site supports testing local client apps. It’s IdP-agnostic, meaning it’s focused on the spec and education, not on a specific company’s product line. In this scenario, we can verify whether our client app, the note-taking app, handles the token exchange with an IdP and the resource app’s authorization server. The best part about this testing site is that it’s self-contained and works out of the box. So you don’t need to create an account with an IdP, nor do you have a resource app with a conformant OAuth authorization server! We just have to bring our client code for testing! Yay for simplicity!</p>

<p>You can read more about the site here:</p>

<article class="link-container" style="border: 1px solid silver; border-radius: 3px; padding: 12px 15px;">
              <a href="/blog/2026/01/20/xaa-dev-playground" style="font-size: 1.375em; margin-bottom: 20px;">
                <span>Introducing xaa.dev: A Playground for Cross App Access</span>
              </a>
              <p>Explore Cross App Access end-to-end with xaa.dev, a free, open playground that lets you test the XAA protocol without any local setup or infrastructure.</p>
              <div><div class="BlogPost-attribution">
            <a href="/blog/authors/sohail-pathan/">
              <img alt="avatar-avatar-sohail-pathan.jpeg" class="BlogPost-avatar" src="/assets-jekyll/avatar-sohail-pathan-fa148e78133752dcc86034268bffe3367e2708874b1ea957b09712e8937b8cc7.jpg" />
            </a>
            <span class="BlogPost-author">
                <a href="/blog/authors/sohail-pathan/">Sohail Pathan</a>
            </span>
          </div></div>
          </article>

<p>Let’s register our note-taking app now.</p>

<p>In your browser, navigate to <a href="https://xaa.dev">xaa.dev</a>. The main site provides information about the players in this flow, and you can test the XAA flow step by step there. Please take a moment to step through the flow to get a better sense of the code we’ll build.</p>

<p>When you’re ready, navigate to <strong>Developer</strong> &gt; <strong>Register Client</strong>. Add a totally made-up email for more fun when registering.</p>

<p>Select <strong>+ Register New Client</strong> and fill out the required information:</p>
<ul>
  <li><strong>Application Name</strong> - I used “Notes App”</li>
  <li><strong>Redirect URIs</strong> - Enter <code class="language-plaintext highlighter-rouge">http://localhost:3000/auth/callback</code></li>
  <li><strong>Post-Logout Redirect URIs</strong> - Enter <code class="language-plaintext highlighter-rouge">http://localhost:3000</code></li>
  <li><strong>Resource Connections &gt; Add Resource</strong> - Choose “Todo0 Resource App” and mark “todos.read” as your allowed scopes before clicking the Add Connection button.</li>
</ul>

<p>Once all necessary fields have been filled select <strong>Register App</strong>.</p>

<p>You’ll see a modal with the Client ID and Client Secret. The xaa.dev testing site also provides credentials for the resource app’s authorization server - the Resource Client ID and Resource Client Secret. Copy all four values. We need to add these to our project.</p>

<h2 id="get-the-nestjs-project-with-oauth-and-openid-connect-oidc-started">Get the NestJS project with OAuth and OpenID Connect (OIDC) started</h2>

<p>You’ll use a starter note-taking app project written in NestJS. Before you get too excited, remember this is a demo app. While the note-taking features are minimal, it does include built-in authentication.</p>

<p>Open a terminal window and run the following commands to get a local copy of the project in a directory named <code class="language-plaintext highlighter-rouge">okta-xaa-project</code> and install dependencies. Feel free to fork the repo to track your changes.</p>

<div class="language-shell highlighter-rouge"><div class="highlight"><pre class="highlight"><code>git clone <span class="nt">-b</span> starter https://github.com/oktadev/okta-js-xaa-requestor-example.git okta-xaa-project
<span class="nb">cd </span>okta-xaa-project
npm ci
</code></pre></div></div>

<p>Open the project in your IDE. Let’s go over the main components and framework choices so you don’t have to discover everything on your own:</p>
<ol>
  <li>The NestJS project depends on <a href="https://expressjs.com/">Express</a> as the base engine and uses <a href="https://www.typescriptlang.org/">TypeScript</a>.</li>
  <li>Views for the landing page and the notes interface use <a href="https://mozilla.github.io/nunjucks/">Nunjucks</a> as the templating engine.</li>
  <li>Relies on the <a href="https://github.com/panva/openid-client/tree/main">openid-client</a> to handle all OAuth handshakes. It’s an OIDC client library for JavaScript runtimes.</li>
  <li>There’s a basic interceptor implementation that logs HTTP requests and responses to the console. This way, we can see the token exchange flow.</li>
</ol>

<p>The app requires a client ID, client secret, resource client ID, and resource client secret to run. Let’s add those to the project.</p>

<p>Rename the <code class="language-plaintext highlighter-rouge">.env.example</code> file to <code class="language-plaintext highlighter-rouge">.env</code>. It already has variables defined and values added to match the URI of the XAA testing site components. Replace the <code class="language-plaintext highlighter-rouge">CLIENT_ID</code>, <code class="language-plaintext highlighter-rouge">CLIENT_SECRET</code>, <code class="language-plaintext highlighter-rouge">RESOURCE_CLIENT_ID</code>, and <code class="language-plaintext highlighter-rouge">RESOURCE_CLIENT_SECRET</code> values with the values from the XAA testing site.</p>

<p>The app should now run, but it still won’t make a successful cross-app access request. Serve the app using the command shown:</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>npm start
</code></pre></div></div>

<p>Navigate to <a href="http://localhost:3000">http://localhost:3000</a>. You should see a landing page that looks like this:</p>

<p><img alt="The notes app landing page with a log in button in the top header" class="center-image" src="/assets-jekyll/blog/xaa-client/notes-app-fab860469237f856ce30c43a5701eb5b3d3d2d578cbfa873bcc4c1879315153c.jpg" width="800" /></p>

<p>Feel free to sign in. You’re redirected to the XAA testing site’s IdP for the user challenge. Enter the email address and any combination of numbers for the one-time password. You’ll redirect to the notes view and see something like this:</p>

<p><img alt="The notes app after signing in. The left nav has notes, the middle section displays the selected note, and the right side shows an empty todo pane" class="center-image" src="/assets-jekyll/blog/xaa-client/notes-start-ed0d8d62e286046a35c7dc90aac2f6eb1a9b2dea0416bc99185471b0c0dc80c7.jpg" width="800" /></p>

<p>There are no todos yet, and in the IDE’s console we see logging and errors. Each request and response to the XAA testing site’s components has a corresponding log entry. We see the IdP’s redirect with the authorization code, the <code class="language-plaintext highlighter-rouge">POST</code> to get tokens along with the request params, and a request to the todo API, which returns a <code class="language-plaintext highlighter-rouge">401 Unauthorized</code> HTTP status code. We need to add the code for the XAA token exchange. Stop serving the app by entering <kbd>Ctrl</kbd>+<kbd>C</kbd> in the terminal.</p>

<h2 id="exchanging-an-id-token-for-an-access-token-for-another-app">Exchanging an ID token for an access token for another app</h2>

<p>When you sign in to the note-taking app, the IdP issues an ID token. From here, the XAA token flow is a two-step process:</p>
<ol>
  <li>The note-taking app requests the IDP’s OAuth authorization server to exchange the ID token for a trustworthy intermediary token type, an Identity Assertion JSON Web Token (JWT) also known as ID-JAG, that the todo app recognizes and supports.</li>
  <li>The todo app’s OAuth authorization server exchanges the intermediary token and issues an access token.</li>
</ol>

<p>With the access token in hand, the note-taking app can make resource requests to the todo app’s resource server.</p>

<p>First, we request the trustworthy intermediary token type, the ID-JAG token.</p>

<h3 id="exchange-the-id-token-for-an-intermediary-id-jag-token-type">Exchange the ID token for an intermediary ID-JAG token type</h3>

<p>In the IDE, open the <code class="language-plaintext highlighter-rouge">src/auth/auth.service.ts</code> file. This file contains code for authentication and the OAuth exchange, along with some utility functions. You already have the code to sign in and have the ID token. We’ll continue using the <code class="language-plaintext highlighter-rouge">openid-client</code> library for the XAA token exchanges. Find the private helper method <code class="language-plaintext highlighter-rouge">exchangeIdTokenForIdJag()</code>. The body of the method has a comment:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// add logic to return an ID-JAG token given the user's ID token</span>
</code></pre></div></div>

<p>We need to replace the inner workings of this method to return the ID-JAG token instead of an empty promise. No empty promises for us! Our promises are as good as tokens. 👻</p>

<p>Replace the code within the method as shown, then I’ll walk through each code block.</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="cm">/**
 * Exchange ID token for ID-JAG token (step 1 of ID-JAG flow)
 */</span>
<span class="k">private</span> <span class="k">async</span> <span class="nx">exchangeIdTokenForIdJag</span><span class="p">(</span>
  <span class="nx">config</span><span class="p">:</span> <span class="nx">openidClient</span><span class="p">.</span><span class="nx">Configuration</span><span class="p">,</span>
  <span class="nx">idToken</span><span class="p">:</span> <span class="kr">string</span><span class="p">,</span>
  <span class="nx">authServerUrl</span><span class="p">:</span> <span class="kr">string</span><span class="p">,</span>
  <span class="nx">resourceUrl</span><span class="p">:</span> <span class="kr">string</span><span class="p">,</span>
  <span class="nx">scope</span><span class="p">:</span> <span class="kr">string</span><span class="p">[],</span>
<span class="p">):</span> <span class="nb">Promise</span><span class="o">&lt;</span><span class="kr">string</span><span class="o">&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">tokenExchangeParams</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">requested_token_type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">urn:ietf:params:oauth:token-type:id-jag</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">audience</span><span class="p">:</span> <span class="nx">authServerUrl</span><span class="p">,</span>
    <span class="na">resource</span><span class="p">:</span> <span class="nx">resourceUrl</span><span class="p">,</span>
    <span class="na">subject_token</span><span class="p">:</span> <span class="nx">idToken</span><span class="p">,</span>
    <span class="na">subject_token_type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">urn:ietf:params:oauth:token-type:id_token</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">scope</span><span class="p">:</span> <span class="nx">scope</span><span class="p">.</span><span class="nx">join</span><span class="p">(</span><span class="dl">'</span><span class="s1"> </span><span class="dl">'</span><span class="p">),</span>
  <span class="p">};</span>

  <span class="kd">const</span> <span class="nx">tokenExchangeResponse</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">openidClient</span><span class="p">.</span><span class="nx">genericGrantRequest</span><span class="p">(</span>
    <span class="nx">config</span><span class="p">,</span>
    <span class="dl">'</span><span class="s1">urn:ietf:params:oauth:grant-type:token-exchange</span><span class="dl">'</span><span class="p">,</span>
    <span class="nx">tokenExchangeParams</span><span class="p">,</span>
  <span class="p">);</span>

  <span class="k">return</span> <span class="nx">tokenExchangeResponse</span><span class="p">.</span><span class="nx">access_token</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>In this first exchange, we call the IdP. The IdP acts as the broker between the two apps as it’s the trusted source.</p>

<p>Let’s step through the key parts of the first code block where we set the token exchange parameters:</p>
<ul>
  <li><strong><code class="language-plaintext highlighter-rouge">requested_token_type</code></strong> - we’re asking the IDP for the ID-JAG token</li>
  <li><strong><code class="language-plaintext highlighter-rouge">audience</code></strong> and <strong><code class="language-plaintext highlighter-rouge">resource</code></strong> - the authorization server and the todo API we’re requesting resources from</li>
  <li><strong><code class="language-plaintext highlighter-rouge">subject_token</code></strong> - the token we’re using for this exchange</li>
  <li><strong><code class="language-plaintext highlighter-rouge">subject_token_type</code></strong> - the type of the token we’re using for the exchange</li>
  <li><strong><code class="language-plaintext highlighter-rouge">scopes</code></strong> - the requested scopes, such as reading todos</li>
</ul>

<p>Once we have all these parameters set, we can call the IdP. The <code class="language-plaintext highlighter-rouge">openid-client</code> library has a function for making generic grant requests. We can use it to request the token exchange grant type. While the return value is not an access token, the grant request relies on existing OAuth models that defined the <code class="language-plaintext highlighter-rouge">access_token</code> response parameter.</p>

<p>Let’s call the method so we can test it out. Find the comment:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>// Step 1: Exchange ID token for ID-JAG token
</code></pre></div></div>
<p>in the <code class="language-plaintext highlighter-rouge">exchangeIdTokenForAccessToken()</code> method.</p>

<p>Add the call to the method like this:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// Step 1: Exchange ID token for ID-JAG token</span>
<span class="kd">const</span> <span class="nx">idJagToken</span> <span class="o">=</span> <span class="k">await</span> <span class="k">this</span><span class="p">.</span><span class="nx">exchangeIdTokenForIdJag</span><span class="p">(</span>
  <span class="nx">idpConfig</span><span class="p">,</span>
  <span class="nx">idToken</span><span class="p">,</span>
  <span class="nx">authServerUrl</span><span class="p">,</span>
  <span class="nx">resourceUrl</span><span class="p">,</span>
  <span class="nx">scope</span><span class="p">,</span>
<span class="p">);</span>
</code></pre></div></div>

<p>We’re adding configuration information, including the IdP, client ID, and client secret. And we have some other required configuration values pulled from the <code class="language-plaintext highlighter-rouge">.env</code> file, such as the servers for the todo app and the scopes.</p>

<p>We’ll get the signed Identity Assertion JWT Authorization grant when the call succeeds. This is a signed token from the IdP, so whenever we exchange it in the next step, the recipient knows it’s trustworthy. Step one complete. ✅</p>

<p>Feel free to start the app and check the console log for your first exchange request. You should see the call to <code class="language-plaintext highlighter-rouge">LOG [OAuth HTTP] → POST idp.xaa.dev/token</code> in the console. Below that, you’ll see the token exchange parameters that look something like this:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>DEBUG [OAuth HTTP]   body:
    requested_token_type=urn:ietf:params:oauth:token-type:id-jag
    audience=https://auth.resource.xaa.dev
    resource=https://api.resource.xaa.dev
    subject_token=eyJhbGc...IdoRppJyZmV9Q
    subject_token_type=urn:ietf:params:oauth:token-type:id_token
    scope=todos.read
    grant_type=urn:ietf:params:oauth:grant-type:token-exchange
</code></pre></div></div>

<p>The call to get todos will still fail, but you can see the first exchange request in action! 🚀</p>

<h3 id="use-the-id-jag-token-to-request-an-access-token-for-a-separate-app">Use the ID-JAG token to request an access token for a separate app</h3>

<p>With the ID-JAG token in hand, we can now move on to the second exchange, exchanging the ID-JAG intermediary token for an access token to the todo app. We make this exchange with the todo app’s OAuth authorization server. The IdP oversees both the note-taking app and the todo app, and trust domains between the two apps facilitate this flow. Remember, in our first exchange, we had to specify the audience for the ID-JAG token in our request - the todo app.</p>

<p>Back in <code class="language-plaintext highlighter-rouge">src/auth/auth.service.ts</code>, find the comment:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>// add logic to return an access token given the ID-JAG token
</code></pre></div></div>

<p>This comment is in the placeholder code for the <code class="language-plaintext highlighter-rouge">exchangeIdJagForAccessToken()</code> method.</p>

<p>Replace the placeholder code to make the exchange. Your code will look like this:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="cm">/**
  * Exchange ID-JAG token for access token (step 2 of ID-JAG flow)
  */</span>
<span class="k">private</span> <span class="k">async</span> <span class="nx">exchangeIdJagForAccessToken</span><span class="p">(</span>
  <span class="nx">config</span><span class="p">:</span> <span class="nx">openidClient</span><span class="p">.</span><span class="nx">Configuration</span><span class="p">,</span>
  <span class="nx">idJagToken</span><span class="p">:</span> <span class="kr">string</span><span class="p">,</span>
  <span class="nx">scope</span><span class="p">:</span> <span class="kr">string</span><span class="p">[],</span>
<span class="p">):</span> <span class="nb">Promise</span><span class="o">&lt;</span><span class="kr">string</span><span class="o">&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">jwtBearerParams</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">assertion</span><span class="p">:</span> <span class="nx">idJagToken</span><span class="p">,</span>
    <span class="na">scope</span><span class="p">:</span> <span class="nx">scope</span><span class="p">.</span><span class="nx">join</span><span class="p">(</span><span class="dl">'</span><span class="s1"> </span><span class="dl">'</span><span class="p">),</span>
  <span class="p">};</span>

  <span class="kd">const</span> <span class="nx">resourceTokenResponse</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">openidClient</span><span class="p">.</span><span class="nx">genericGrantRequest</span><span class="p">(</span>
    <span class="nx">config</span><span class="p">,</span>
    <span class="dl">'</span><span class="s1">urn:ietf:params:oauth:grant-type:jwt-bearer</span><span class="dl">'</span><span class="p">,</span>
    <span class="nx">jwtBearerParams</span><span class="p">,</span>
  <span class="p">);</span>

  <span class="k">return</span> <span class="nx">resourceTokenResponse</span><span class="p">.</span><span class="nx">access_token</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>We’re following a similar pattern to the first exchange, with a difference in the grant request. This time, the parameters include an assertion, the ID-JAG token. And we make the grant request to the todo app’s OAuth authorization server with the <code class="language-plaintext highlighter-rouge">urn:ietf:params:oauth:grant-type:jwt-bearer</code> grant type. This exchange relies upon a pre-existing spec where one can use a bearer JWT for as a grant type to request an access token. That’s what we’re doing in this step.</p>

<p>Next, we’ll call this method in <code class="language-plaintext highlighter-rouge">exchangeIdTokenForAccessToken()</code>.</p>

<p>Find the comment:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>// Step 2: Exchange ID-JAG token for access token
</code></pre></div></div>

<p>Because we’re calling a new authorization server, the todo app’s OAuth authorization server, we first need to read the well-known discovery docs. The discovery docs include information about the authorization server, such as the server’s capabilities and endpoints, including the token endpoint. Since we’re authenticating with the todo app’s authorization server, not the IdP, we use the resource app’s credentials here. The todo app’s authorization server recognizes RESOURCE_CLIENT_ID and RESOURCE_CLIENT_SECRET, not your notes app’s credentials. We’ve been using a custom <code class="language-plaintext highlighter-rouge">fetch</code> implementation to capture the logging you see, so we must include that implementation in <code class="language-plaintext highlighter-rouge">openid-client</code> too. Then make the call to the <code class="language-plaintext highlighter-rouge">exchangeIdJagForAccessToken()</code> helper method. Your code will look like this:</p>

<div class="language-ts highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// Step 2: Exchange ID-JAG token for access token</span>
<span class="kd">const</span> <span class="nx">resourceAuthConfig</span> <span class="o">=</span> <span class="k">await</span> <span class="nx">openidClient</span><span class="p">.</span><span class="nx">discovery</span><span class="p">(</span>
  <span class="k">new</span> <span class="nx">URL</span><span class="p">(</span><span class="nx">authServerUrl</span><span class="p">),</span>
  <span class="nx">resourceClientId</span><span class="p">,</span>
  <span class="nx">resourceClientSecret</span><span class="p">,</span>
  <span class="nx">openidClient</span><span class="p">.</span><span class="nx">ClientSecretPost</span><span class="p">(</span><span class="nx">resourceClientSecret</span> <span class="o">??</span> <span class="dl">''</span><span class="p">),</span>
<span class="p">);</span>
<span class="nx">resourceAuthConfig</span><span class="p">[</span><span class="nx">openidClient</span><span class="p">.</span><span class="nx">customFetch</span><span class="p">]</span> <span class="o">=</span> <span class="nx">loggedFetch</span><span class="p">;</span>

<span class="k">return</span> <span class="k">this</span><span class="p">.</span><span class="nx">exchangeIdJagForAccessToken</span><span class="p">(</span>
  <span class="nx">resourceAuthConfig</span><span class="p">,</span>
  <span class="nx">idJagToken</span><span class="p">,</span>
  <span class="nx">scope</span><span class="p">,</span>
<span class="p">);</span>
</code></pre></div></div>

<p>Make sure to remove any placeholder implementation. Step two complete. ✅</p>

<p>The code to make a request to the todo API using the bearer token already exists in the project. Let’s try running the app now using <code class="language-plaintext highlighter-rouge">npm start</code>.</p>

<h2 id="inspecting-the-xaa-token-exchange">Inspecting the XAA token exchange</h2>

<p>After you authenticate, you’ll see the notes and the todos! 🎉</p>

<p><img alt="The notes app with todos listed on the side" class="center-image" src="/assets-jekyll/blog/xaa-client/notes-todos-631e2643399d8e41702e2d3460371f7d6e112fc1c0f03a7b097aec8e5217e649.jpg" width="800" /></p>

<p>In the terminal console, you’ll see each step of the handshake and requests:</p>

<ol>
  <li>Authentication in the notes app with the IdP returning the ID token</li>
  <li>Exchanging the ID token for an ID-JAG token with the IDP’s OAuth authorization server</li>
  <li>Exchanging the ID-JAG token for an access token with the todo app’s OAuth authorization server</li>
  <li>Call the todo app’s resource server (the API)</li>
</ol>

<p>Feel free to inspect each step of this flow, the request parameters, and the responses.</p>

<p>These steps allow an app to make requests to a third-party app within enterprise systems securely. You can find the completed project <a href="https://github.com/oktadev/okta-js-xaa-requestor-example">in the GitHub repo</a> with instructions to also test on <a href="https://github.com/features/codespaces">GitHub Codespaces</a>.</p>

<h2 id="learn-more-about-xaa-and-elevating-identity-security-using-oauth">Learn more about XAA and elevating identity security using OAuth</h2>

<p>I hope you enjoyed this post on making secure cross-app requests for enterprise use cases. If you found this post interesting, I encourage you to check out these links:</p>

<ul>
  <li><a href="/blog/2025/09/03/cross-app-access">Build Secure Agent-to-App Connections with Cross App Access (XAA)</a></li>
  <li><a href="https://drafts.oauth.net/oauth-identity-assertion-authz-grant/draft-ietf-oauth-identity-assertion-authz-grant.html">Identity Assertion JWT Authorization Grant</a></li>
  <li><a href="/blog/2024/04/30/express-universal-logout">How to Instantly Sign a User Out across All Your Apps</a></li>
  <li><a href="/blog/2024/02/29/net-scim">How to Manage User Lifecycle with .NET and SCIM</a></li>
  <li><a href="/blog/2023/09/25/oauth-api-tokens">Why You Should Migrate to OAuth 2.0 From Static API Tokens</a></li>
</ul>

<p>Remember to follow us on <a href="https://twitter.com/oktadev">Twitter</a> and subscribe to our <a href="https://www.youtube.com/c/OktaDev/">YouTube channel</a> for more exciting content. We also want to hear from you about the topics you’d like to see and any questions you may have. Leave us a comment below!</p>
