---
title: "Unlock the Secrets of a Custom Sign-In Page with Tailwind and JavaScript"
url: "https://developer.okta.com/blog/2025/11/24/okta-custom-sign-in-page"
date: "Mon, 24 Nov 2025 00:00:00 -0500"
author: ""
feed_url: "https://developer.okta.com/feed.xml"
---
<p>We recommend redirecting users to authenticate via the Okta-hosted sign-in page powered by the Okta Identity Engine (OIE) for your custom-built applications. It’s the most secure method for authenticating. You don’t have to manage credentials in your code and can take advantage of the strongest authentication factors without requiring any code changes.</p>

<p>The Okta Sign-In Widget (SIW) built into the sign-in page does the heavy lifting of supporting the authentication factors required by your organization. Did I mention policy changes won’t need any code changes?</p>

<p>But you may think the sign-in page and the SIW are a little bland. And maybe too Okta for your needs? What if you can have a page like this?</p>

<p><img alt="A customized Okta-hosted Sign-In Widget with custom elements, colors, and styles" class="center-image" src="/assets-jekyll/blog/okta-custom-sign-in-page/final-siw-desktop-211b475e04926250d77e2a72779c27ea2c22a65ad47f43322c22ba81006f5df2.jpg" width="800" /></p>

<p>With a bright and colorful responsive design change befitting a modern lifestyle.</p>

<p><img alt="A customized Okta-hosted Sign-In Widget with custom elements, colors, and styles for smaller form factors" class="center-image" src="/assets-jekyll/blog/okta-custom-sign-in-page/final-siw-responsive-c258e3e1daf1d1fdfe36dfbaa4eb61afd13e38e5234b3781af4ac08bbd6baa13.jpg" width="800" /></p>

<p>Let’s add some color, life, and customization to the sign-in page.</p>

<p>In this tutorial, we will customize the sign-in page for a fictional to-do app. We’ll make the following changes:</p>
<ul>
  <li>Use <a href="https://tailwindcss.com/">Tailwind</a> CSS framework to create a responsive sign-in page layout</li>
  <li>Add a footer for custom brand links</li>
  <li>Display a terms and conditions modal using <a href="https://alpinejs.dev">Alpine.js</a> that the user must accept before authenticating</li>
</ul>

<p>Take a moment to read this post on customizing the Sign-In Widget if you aren’t familiar with the process, as we will be expanding from customizing the widget to enhancing the entire sign-in page experience.</p>

<article class="link-container" style="border: 1px solid silver; border-radius: 3px; padding: 12px 15px;">
              <a href="/blog/2025/11/12/custom-signin" style="font-size: 1.375em; margin-bottom: 20px;">
                <span>Stretch Your Imagination and Build a Delightful Sign-In Experience</span>
              </a>
              <p>Customize your Gen3 Okta Sign-In Widget to match your brand. Learn to use design tokens, CSS, and JavaScript for a seamless user experience.</p>
              <div></div>
          </article>

<p>In the post, we covered how to style the Gen3 SIW using design tokens and customize the widget elements using the <code class="language-plaintext highlighter-rouge">afterTransform()</code> method. You’ll want to combine elements of both posts for the most customized experience.</p>

<p><strong class="hide">Table of Contents</strong></p>
<ul id="markdown-toc">
  <li><a href="#customize-your-okta-hosted-sign-in-page" id="markdown-toc-customize-your-okta-hosted-sign-in-page">Customize your Okta-hosted sign-in page</a></li>
  <li><a href="#use-tailwind-css-to-build-a-responsive-layout" id="markdown-toc-use-tailwind-css-to-build-a-responsive-layout">Use Tailwind CSS to build a responsive layout</a></li>
  <li><a href="#use-tailwind-for-custom-html-elements-on-your-okta-hosted-sign-in-page" id="markdown-toc-use-tailwind-for-custom-html-elements-on-your-okta-hosted-sign-in-page">Use Tailwind for custom HTML elements on your Okta-hosted sign-in page</a></li>
  <li><a href="#add-custom-interactivity-on-the-okta-hosted-sign-in-page-using-an-external-library" id="markdown-toc-add-custom-interactivity-on-the-okta-hosted-sign-in-page-using-an-external-library">Add custom interactivity on the Okta-hosted sign-in page using an external library</a></li>
  <li><a href="#customize-okta-hosted-sign-in-page-behavior-using-web-apis" id="markdown-toc-customize-okta-hosted-sign-in-page-behavior-using-web-apis">Customize Okta-hosted sign-in page behavior using Web APIs</a></li>
  <li><a href="#add-tailwind-web-apis-and-javascript-libraries-to-customize-your-okta-hosted-sign-in-page" id="markdown-toc-add-tailwind-web-apis-and-javascript-libraries-to-customize-your-okta-hosted-sign-in-page">Add Tailwind, Web APIs, and JavaScript libraries to customize your Okta-hosted sign-in page</a></li>
</ul>

<p><strong>Prerequisites</strong></p>

<p>To follow this tutorial, you need:</p>
<ul>
  <li>An Okta account with the Identity Engine, such as the <a href="https://developer.okta.com/signup/">Integrator Free account</a>.</li>
  <li>Your own domain name</li>
  <li>A basic understanding of HTML, CSS, and JavaScript</li>
  <li>A brand design in mind. Feel free to tap into your creativity!</li>
  <li>An understanding of customizing the sign-in page by following the previous blog post</li>
</ul>

<p>Let’s get started!</p>

<p>Before we begin, you must configure your Okta org to use your custom domain. Custom domains enable code customizations, allowing us to style more than just the default logo, background, favicon, and two colors. Sign in as an admin and open the Okta Admin Console, navigate to <strong>Customizations</strong> &gt; <strong>Brands</strong> and select <strong>Create Brand +</strong>.</p>

<p>Follow the <a href="https://developer.okta.com/docs/guides/custom-url-domain/main/">Customize domain and email</a> developer docs to set up your custom domain on the new brand.</p>

<h2 id="customize-your-okta-hosted-sign-in-page">Customize your Okta-hosted sign-in page</h2>

<p>We’ll first apply the base configuration using the built-in configuration options in the UI. Add your favorite primary and secondary colors, then upload your favorite logo, favicon, and background image for the page. Select <strong>Save</strong> when done. Everyone has a favorite favicon, right?</p>

<p>I’ll use <code class="language-plaintext highlighter-rouge">#ea3eda</code> and <code class="language-plaintext highlighter-rouge">#ffa738</code> as the primary and secondary colors, respectively.</p>

<p>On to the code. In the <strong>Theme</strong> tab:</p>
<ol>
  <li>Select <strong>Sign-in Page</strong> in the dropdown menu</li>
  <li>Select the <strong>Customize</strong> button</li>
  <li>On the <strong>Page Design</strong> tab, select the <strong>Code editor</strong>  toggle to see a HTML page</li>
</ol>

<blockquote>
  <p><strong>Note</strong></p>

  <p>You can only enable the code editor if you configure a <a href="https://developer.okta.com/docs/guides/custom-url-domain/">custom domain</a>.</p>
</blockquote>

<p>You’ll see the lightweight IDE already has code scaffolded. Press <strong>Edit</strong> and replace the existing code with the following.</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="cp">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd"&gt;</span>
<span class="nt">&lt;html&gt;</span>

<span class="nt">&lt;head&gt;</span>
  <span class="nt">&lt;meta</span> <span class="na">http-equiv=</span><span class="s">"Content-Type"</span> <span class="na">content=</span><span class="s">"text/html; charset=UTF-8"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;meta</span> <span class="na">name=</span><span class="s">"viewport"</span> <span class="na">content=</span><span class="s">"width=device-width, initial-scale=1.0"</span> <span class="nt">/&gt;</span>
  <span class="nt">&lt;meta</span> <span class="na">name=</span><span class="s">"robots"</span> <span class="na">content=</span><span class="s">"noindex,nofollow"</span> <span class="nt">/&gt;</span>
  <span class="c">&lt;!-- Styles generated from theme --&gt;</span>
  <span class="nt">&lt;link</span> <span class="na">href=</span><span class="s">"{{themedStylesUrl}}"</span> <span class="na">rel=</span><span class="s">"stylesheet"</span> <span class="na">type=</span><span class="s">"text/css"</span><span class="nt">&gt;</span>
  <span class="c">&lt;!-- Favicon from theme --&gt;</span>
  <span class="nt">&lt;link</span> <span class="na">rel=</span><span class="s">"shortcut icon"</span> <span class="na">href=</span><span class="s">"{{faviconUrl}}"</span> <span class="na">type=</span><span class="s">"image/x-icon"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;link</span> <span class="na">rel=</span><span class="s">"preconnect"</span> <span class="na">href=</span><span class="s">"https://fonts.googleapis.com"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;link</span> <span class="na">rel=</span><span class="s">"preconnect"</span> <span class="na">href=</span><span class="s">"https://fonts.gstatic.com"</span> <span class="na">crossorigin</span><span class="nt">&gt;</span>
  <span class="nt">&lt;link</span>
      <span class="na">href=</span><span class="s">"https://fonts.googleapis.com/css2?family=Inter+Tight:ital,wght@0,100..900;1,100..900&amp;family=Manrope:wght@200..800&amp;display=swap"</span>
      <span class="na">rel=</span><span class="s">"stylesheet"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;title&gt;</span>{{pageTitle}}<span class="nt">&lt;/title&gt;</span>
  {{{SignInWidgetResources}}}

  <span class="nt">&lt;style </span><span class="na">nonce=</span><span class="s">"{{nonceValue}}"</span><span class="nt">&gt;</span>
    <span class="nd">:root</span> <span class="p">{</span>
      <span class="py">--font-header</span><span class="p">:</span> <span class="s2">'Inter Tight'</span><span class="p">,</span> <span class="nb">sans-serif</span><span class="p">;</span>
      <span class="py">--font-body</span><span class="p">:</span> <span class="s2">'Manrope'</span><span class="p">,</span> <span class="nb">sans-serif</span><span class="p">;</span>
      <span class="py">--color-gray</span><span class="p">:</span> <span class="m">#4f4f4f</span><span class="p">;</span>
      <span class="py">--color-fuchsia</span><span class="p">:</span> <span class="m">#ea3eda</span><span class="p">;</span>
      <span class="py">--color-orange</span><span class="p">:</span> <span class="m">#ffa738</span><span class="p">;</span>
      <span class="py">--color-azul</span><span class="p">:</span> <span class="m">#016fb9</span><span class="p">;</span>
      <span class="py">--color-cherry</span><span class="p">:</span> <span class="m">#ea3e84</span><span class="p">;</span>
      <span class="py">--color-purple</span><span class="p">:</span> <span class="m">#b13fff</span><span class="p">;</span>
      <span class="py">--color-black</span><span class="p">:</span> <span class="m">#191919</span><span class="p">;</span>
      <span class="py">--color-white</span><span class="p">:</span> <span class="m">#fefefe</span><span class="p">;</span>
      <span class="py">--color-bright-white</span><span class="p">:</span> <span class="m">#fff</span><span class="p">;</span>
      <span class="py">--border-radius</span><span class="p">:</span> <span class="m">4px</span><span class="p">;</span>
      <span class="py">--color-gradient</span><span class="p">:</span> <span class="n">linear-gradient</span><span class="p">(</span><span class="m">12deg</span><span class="p">,</span> <span class="n">var</span><span class="p">(</span><span class="n">--color-fuchsia</span><span class="p">)</span> <span class="m">0%</span><span class="p">,</span> <span class="n">var</span><span class="p">(</span><span class="n">--color-orange</span><span class="p">)</span> <span class="m">100%</span><span class="p">);</span>
    <span class="p">}</span>

    <span class="p">{</span><span class="err">{#useSiwGen3</span><span class="p">}</span><span class="err">}</span>
      <span class="nt">html</span> <span class="p">{</span>
        <span class="nl">font-size</span><span class="p">:</span> <span class="m">87.5%</span><span class="p">;</span>
      <span class="p">}</span>
    <span class="p">{</span><span class="err">{/useSiwGen3</span><span class="p">}</span><span class="err">}</span>

    <span class="nf">#okta-auth-container</span> <span class="p">{</span>
      <span class="nl">display</span><span class="p">:</span> <span class="n">flex</span><span class="p">;</span>
      <span class="nl">background-image</span><span class="p">:</span> <span class="err">{{</span><span class="n">bgImageUrl</span><span class="p">}</span><span class="err">}</span><span class="o">;</span>
    <span class="err">}</span>

    <span class="nf">#okta-login-container</span> <span class="p">{</span>
      <span class="nl">display</span><span class="p">:</span> <span class="n">flex</span><span class="p">;</span>
      <span class="nl">justify-content</span><span class="p">:</span> <span class="nb">center</span><span class="p">;</span>
      <span class="nl">align-items</span><span class="p">:</span> <span class="nb">center</span><span class="p">;</span>
      <span class="nl">height</span><span class="p">:</span> <span class="m">100vh</span><span class="p">;</span>
      <span class="nl">width</span><span class="p">:</span> <span class="m">50vw</span><span class="p">;</span>
      <span class="nl">background</span><span class="p">:</span> <span class="n">var</span><span class="p">(</span><span class="n">--color-white</span><span class="p">);</span>
    <span class="p">}</span>
  <span class="nt">&lt;/style&gt;</span>
<span class="nt">&lt;/head&gt;</span>

<span class="nt">&lt;body&gt;</span>  
  <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-auth-container"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-login-container"</span><span class="nt">&gt;&lt;/div&gt;</span>      
  <span class="nt">&lt;/div&gt;</span>
    
  <span class="c">&lt;!--
   "OktaUtil" defines a global OktaUtil object
   that contains methods used to complete the Okta login flow.
  --&gt;</span>
  {{{OktaUtil}}}

  <span class="nt">&lt;script </span><span class="na">type=</span><span class="s">"text/javascript"</span> <span class="na">nonce=</span><span class="s">"{{nonceValue}}"</span><span class="nt">&gt;</span>
    <span class="c1">// "config" object contains default widget configuration</span>
    <span class="c1">// with any custom overrides defined in your admin settings.</span>

    <span class="kd">const</span> <span class="nx">config</span> <span class="o">=</span> <span class="nx">OktaUtil</span><span class="p">.</span><span class="nx">getSignInWidgetConfig</span><span class="p">();</span>
    <span class="nx">config</span><span class="p">.</span><span class="nx">theme</span> <span class="o">=</span> <span class="p">{</span>
      <span class="na">tokens</span><span class="p">:</span> <span class="p">{</span>
        <span class="na">BorderColorDisplay</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-bright-white)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">PalettePrimaryMain</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-fuchsia)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">PalettePrimaryDark</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-purple)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">PalettePrimaryDarker</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-purple)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">BorderRadiusTight</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--border-radius)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">BorderRadiusMain</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--border-radius)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">PalettePrimaryDark</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-orange)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">FocusOutlineColorPrimary</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-azul)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">TypographyFamilyBody</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--font-body)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">TypographyFamilyHeading</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--font-header)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">TypographyFamilyButton</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--font-header)</span><span class="dl">'</span><span class="p">,</span>
        <span class="na">BorderColorDangerControl</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-cherry)</span><span class="dl">'</span>
      <span class="p">}</span>
    <span class="p">}</span>

    <span class="nx">config</span><span class="p">.</span><span class="nx">i18n</span> <span class="o">=</span> <span class="p">{</span>
      <span class="dl">'</span><span class="s1">en</span><span class="dl">'</span><span class="p">:</span> <span class="p">{</span>
        <span class="dl">'</span><span class="s1">primaryauth.title</span><span class="dl">'</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Log in to create tasks</span><span class="dl">'</span><span class="p">,</span>
      <span class="p">}</span>
    <span class="p">}</span>

    <span class="c1">// Render the Okta Sign-In Widget</span>
    <span class="kd">const</span> <span class="nx">oktaSignIn</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">OktaSignIn</span><span class="p">(</span><span class="nx">config</span><span class="p">);</span>
    <span class="nx">oktaSignIn</span><span class="p">.</span><span class="nx">renderEl</span><span class="p">({</span> <span class="na">el</span><span class="p">:</span> <span class="dl">'</span><span class="s1">#okta-login-container</span><span class="dl">'</span> <span class="p">},</span>
      <span class="nx">OktaUtil</span><span class="p">.</span><span class="nx">completeLogin</span><span class="p">,</span>
      <span class="kd">function</span> <span class="p">(</span><span class="nx">error</span><span class="p">)</span> <span class="p">{</span>
        <span class="c1">// Logs errors that occur when configuring the widget.</span>
        <span class="c1">// Remove or replace this with your own custom error handler.</span>
        <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="nx">error</span><span class="p">.</span><span class="nx">message</span><span class="p">,</span> <span class="nx">error</span><span class="p">);</span>
      <span class="p">}</span>
    <span class="p">);</span>
  <span class="nt">&lt;/script&gt;</span>
<span class="nt">&lt;/body&gt;</span>
<span class="nt">&lt;/html&gt;</span>
</code></pre></div></div>

<p>This code adds style configuration to the SIW elements and configures the text for the title when signing in. Press <strong>Save to draft</strong>.</p>

<p>We must allow Okta to load font resources from an external source, Google, by adding the domains to the allowlist in the Content Security Policy (CSP).</p>

<p>Navigate to the <strong>Settings</strong> tab for your brand’s <strong>Sign-in page</strong>. Find the <strong>Content Security Policy</strong> and press <strong>Edit</strong>. Add the domains for external resources. In our example, we only load resources from Google Fonts, so we added the following two domains:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>*.googleapis.com
*.gstatic.com
</code></pre></div></div>

<p>Select <strong>Save to draft</strong>, then <strong>Publish</strong> to view your changes.</p>

<p>The sign-in page looks more stylized than before. If you try resizing the browser window, we see it’s not handling different form factors well. Let’s use Tailwind CSS to add a responsive layout.</p>

<h2 id="use-tailwind-css-to-build-a-responsive-layout">Use Tailwind CSS to build a responsive layout</h2>

<p>Tailwind makes delivering cool-looking websites much faster than writing our CSS manually. We’ll load Tailwind via CDN for our demonstration purposes.</p>

<p>Add the CDN to your CSP allowlist:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>https://cdn.jsdelivr.net
</code></pre></div></div>

<p>Navigate to <strong>Page Design</strong>, then <strong>Edit</strong> the page. Add the script to load the Tailwind resources in the <code class="language-plaintext highlighter-rouge">&lt;head&gt;</code>. I added it after the <code class="language-plaintext highlighter-rouge">&lt;style&gt;&lt;/style&gt;</code> definitions before the <code class="language-plaintext highlighter-rouge">&lt;/head&gt;</code>.</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;script </span><span class="na">src=</span><span class="s">"https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"</span> <span class="na">nonce=</span><span class="s">"{{nonceValue}}"</span><span class="nt">&gt;&lt;/script&gt;</span>
</code></pre></div></div>

<p>Loading external resources, like styles and scripts, requires a CSP nonce to mitigate cross-site scripting (XSS). You can read more about the CSP nonce on the <a href="https://content-security-policy.com/nonce/">CSP Quick Reference Guide</a>.</p>

<blockquote>
  <p><strong>Note</strong></p>

  <p>Don’t use Tailwind from NPM CDN for production use cases. The Tailwind documentation notes this is for experimentation and prototyping only, as the CDN has rate limits. If your brand uses Tailwind for other production sites, you’ve most likely defined custom mixins and themes in Tailwind. Therefore, reference your production Tailwind resources in place of the CDN we’re using in this post.</p>
</blockquote>

<p>Remove the styles for <code class="language-plaintext highlighter-rouge">#okta-auth-container</code> and <code class="language-plaintext highlighter-rouge">#okta-login-container</code> from the <code class="language-plaintext highlighter-rouge">&lt;style&gt;&lt;/style&gt;</code> section. We can use Tailwind to handle it. The <code class="language-plaintext highlighter-rouge">&lt;style&gt;&lt;/style&gt;</code> section should only contain the CSS custom properties defined in <code class="language-plaintext highlighter-rouge">:root</code> and the directive to use SIW Gen3.</p>

<p>Add the styles for Tailwind. We’ll add the classes to show the login container without the hero image in smaller form factors, then display the hero image with different widths depending on the breakpoints.</p>

<p>The two <code class="language-plaintext highlighter-rouge">div</code> containers look like this:</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-auth-container"</span> <span class="na">class=</span><span class="s">"h-screen flex bg-(--color-gray) bg-[{{bgImageUrl}}]"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-login-container"</span> <span class="na">class=</span><span class="s">"w-full min-w-sm lg:w-2/3 xl:w-1/2 bg-(image:--color-gradient) lg:bg-none bg-(--color-white) flex justify-center items-center"</span><span class="nt">&gt;&lt;/div&gt;</span>
<span class="nt">&lt;/div&gt;</span>
</code></pre></div></div>

<p>Save the file and publish the changes. Feel free to test it out!</p>

<h2 id="use-tailwind-for-custom-html-elements-on-your-okta-hosted-sign-in-page">Use Tailwind for custom HTML elements on your Okta-hosted sign-in page</h2>

<p>Tailwind excels at adding styled HTML elements to websites. We can also take advantage of this. Let’s say you want to maintain continuity of the webpage from your site through the sign-in page by adding a footer with links to your brand’s sites. Adding this new section involves changing the HTML node structure and styling the elements.</p>

<p>We want a footer pinned to the bottom of the view, so we’ll need a new parent container with vertical stacking and ensure the height of the footer stays consistent. Replace the HTML node structure to look like this:</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">"flex flex-col min-h-screen"</span><span class="nt">&gt;</span>        
  <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-auth-container"</span> <span class="na">class=</span><span class="s">"flex grow bg-(--color-gray) bg-[{{bgImageUrl}}]"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">"w-full min-w-sm lg:w-2/3 xl:w-1/2 bg-(image:--color-gradient) lg:bg-none bg-(--color-white) flex justify-center items-center"</span><span class="nt">&gt;</span>
        <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-login-container"</span><span class="nt">&gt;&lt;/div&gt;</span>
    <span class="nt">&lt;/div&gt;</span>
  <span class="nt">&lt;/div&gt;</span>
  <span class="nt">&lt;footer</span> <span class="na">class=</span><span class="s">"font-(family-name:--font-body)"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;ul</span> <span class="na">class=</span><span class="s">"h-12 flex justify-evenly items-center text-(--color-azul)"</span><span class="nt">&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://developer.okta.com"</span><span class="nt">&gt;</span>Terms<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://developer.okta.com"</span><span class="nt">&gt;</span>Docs<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://developer.okta.com/blog"</span><span class="nt">&gt;</span>Blog<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://devforum.okta.com"</span><span class="nt">&gt;</span>Community<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
    <span class="nt">&lt;/ul&gt;</span>
  <span class="nt">&lt;/footer&gt;</span>
<span class="nt">&lt;/div&gt;</span>
</code></pre></div></div>

<p>Everything redirects to the Okta Developer sites. 😊 I also maintained the style of font, text colors, and text decoration styles to match the SIW elements. CSS custom properties make consistency manageable.</p>

<p>Feel free to save and publish to check it out!</p>

<h2 id="add-custom-interactivity-on-the-okta-hosted-sign-in-page-using-an-external-library">Add custom interactivity on the Okta-hosted sign-in page using an external library</h2>

<p>Tailwind is great at styling HTML elements, but it’s not a JavaScript library. If we want interactive elements on the sign-in page, we must rely on Web APIs or libraries to assist us. Let’s say we want to ensure that users who sign in to the to-do app agree to the terms and conditions. We want a modal that blocks interaction with the SIW until the user agrees.</p>

<p>We’ll use Alpine for the heavy lifting because it’s a lightweight JavaScript library that suits this need. We add the library via the NPM CDN, as we have already allowed the domain in our CSP. Add the following to the <code class="language-plaintext highlighter-rouge">&lt;head&gt;&lt;/head&gt;</code> section of the HTML. I added mine directly after the Tailwind script.</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;script </span><span class="na">defer</span> <span class="na">src=</span><span class="s">"https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"</span> <span class="na">nonce=</span><span class="s">"{{nonceValue}}"</span><span class="nt">&gt;&lt;/script&gt;</span>
</code></pre></div></div>

<blockquote>
  <p><strong>Note</strong></p>

  <p>We’re including Alpine from the NPM CDN for demonstration and experimentation. For production applications, use a CDN that supports production scale. The NPM CDN applies rate limiting to prevent production-grade use.</p>
</blockquote>

<p>Next, we add the HTML tags to support the modal. Replace the HTML node structure to look like this:</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">"flex flex-col min-h-screen"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"modal"</span>
    <span class="na">x-data</span>
    <span class="na">x-cloak</span>
    <span class="na">x-show=</span><span class="s">"$store.modal.open"</span> 
    <span class="na">x-transition:enter=</span><span class="s">"transition ease-out duration-300"</span>
    <span class="na">x-transition:enter-start=</span><span class="s">"opacity-0"</span>
    <span class="na">x-transition:enter-end=</span><span class="s">"opacity-100"</span>
    <span class="na">x-transition:leave=</span><span class="s">"transition ease-in duration-200"</span>
    <span class="na">x-transition:leave-start=</span><span class="s">"opacity-100"</span>
    <span class="na">x-transition:leave-end=</span><span class="s">"opacity-0 hidden"</span>
    <span class="na">class=</span><span class="s">"fixed inset-0 z-50 flex items-center justify-center bg-(--color-black)/80 bg-opacity-50"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;div</span> <span class="na">x-transition:enter=</span><span class="s">"transition ease-out duration-300"</span>
         <span class="na">x-transition:enter-start=</span><span class="s">"opacity-0 scale-90"</span>
         <span class="na">x-transition:enter-end=</span><span class="s">"opacity-100 scale-100"</span>
         <span class="na">x-transition:leave=</span><span class="s">"transition ease-in duration-200"</span>
         <span class="na">x-transition:leave-start=</span><span class="s">"opacity-100 scale-100"</span>
         <span class="na">x-transition:leave-end=</span><span class="s">"opacity-0 scale-90"</span>
         <span class="na">class=</span><span class="s">"bg-(--color-white) rounded-(--border-radius) shadow-lg p-8 max-w-md w-full mx-4"</span><span class="nt">&gt;</span>
      <span class="nt">&lt;h2</span> <span class="na">class=</span><span class="s">"text-2xl font-(family-name:--font-header) text-(--color-black) mb-4 text-center"</span><span class="nt">&gt;</span>Welcome to to-do app<span class="nt">&lt;/h2&gt;</span>
      <span class="nt">&lt;p</span> <span class="na">class=</span><span class="s">"text-(--color-black) mb-6"</span><span class="nt">&gt;</span>This app is in beta. Thank you for agreeing to our terms and conditions.<span class="nt">&lt;/p&gt;</span>
      <span class="nt">&lt;button</span> <span class="err">@</span><span class="na">click=</span><span class="s">"$store.modal.hide()"</span> 
              <span class="na">class=</span><span class="s">"w-full bg-(--color-fuchsia) hover:bg-(--color-orange) text-(--color-bright-white) font-medium py-2 px-4 rounded-(--border-radius) transition duration-200"</span><span class="nt">&gt;</span>
          Agree
      <span class="nt">&lt;/button&gt;</span>
    <span class="nt">&lt;/div&gt;</span>
  <span class="nt">&lt;/div&gt;</span>        
  <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-auth-container"</span> <span class="na">class=</span><span class="s">"flex grow bg-(--color-gray) bg-[{{bgImageUrl}}]"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">"w-full min-w-sm lg:w-2/3 xl:w-1/2 bg-(image:--color-gradient) lg:bg-none bg-(--color-white) flex justify-center items-center"</span><span class="nt">&gt;</span>
      <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-login-container"</span><span class="nt">&gt;&lt;/div&gt;</span>
    <span class="nt">&lt;/div&gt;</span>
  <span class="nt">&lt;/div&gt;</span>
  <span class="nt">&lt;footer</span> <span class="na">class=</span><span class="s">"font-(family-name:--font-body)"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;ul</span> <span class="na">class=</span><span class="s">"h-12 flex justify-evenly items-center text-(--color-azul)"</span><span class="nt">&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://developer.okta.com"</span><span class="nt">&gt;</span>Terms<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://developer.okta.com"</span><span class="nt">&gt;</span>Docs<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://developer.okta.com/blog"</span><span class="nt">&gt;</span>Blog<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
      <span class="nt">&lt;li&gt;&lt;a</span> <span class="na">class=</span><span class="s">"hover:text-(--color-orange) hover:underline"</span> <span class="na">href=</span><span class="s">"https://devforum.okta.com"</span><span class="nt">&gt;</span>Community<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
    <span class="nt">&lt;/ul&gt;</span>
  <span class="nt">&lt;/footer&gt;</span>
<span class="nt">&lt;/div&gt;</span>
</code></pre></div></div>

<p>It’s a lot to add, but I want the smooth transition animations. 😅 The built-in enter and leave states make adding the transition animation so much easier than doing it manually.</p>

<p>Notice we’re using a state value to determine whether to show the modal. We’re using global state management, and setting it up is the next step. We’ll add initializing the state when Alpine initializes. Find the comment <code class="language-plaintext highlighter-rouge">// Render the Okta Sign-In Widget</code> within the <code class="language-plaintext highlighter-rouge">&lt;script&gt;&lt;/script&gt;</code> section, and add the following code that runs after Alpine initializes:</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nb">document</span><span class="p">.</span><span class="nx">addEventListener</span><span class="p">(</span><span class="dl">'</span><span class="s1">alpine:init</span><span class="dl">'</span><span class="p">,</span> <span class="p">()</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="nx">Alpine</span><span class="p">.</span><span class="nx">store</span><span class="p">(</span><span class="dl">'</span><span class="s1">modal</span><span class="dl">'</span><span class="p">,</span> <span class="p">{</span>
    <span class="na">open</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
    <span class="nx">show</span><span class="p">()</span> <span class="p">{</span>
      <span class="k">this</span><span class="p">.</span><span class="nx">open</span> <span class="o">=</span> <span class="kc">true</span><span class="p">;</span>
    <span class="p">},</span>
    <span class="nx">hide</span><span class="p">()</span> <span class="p">{</span>
      <span class="k">this</span><span class="p">.</span><span class="nx">open</span> <span class="o">=</span> <span class="kc">false</span><span class="p">;</span>
    <span class="p">}</span>
  <span class="p">});</span>
<span class="p">});</span>
</code></pre></div></div>

<p>The event listener watches for the <code class="language-plaintext highlighter-rouge">alpine:init</code> event and runs a function that defines an element in Alpine’s store, <code class="language-plaintext highlighter-rouge">modal</code>. The <code class="language-plaintext highlighter-rouge">modal</code> store contains a property to track whether it’s open and some helper methods for showing and hiding.</p>

<p>When you save and publish, you’ll see the modal upon site reload!</p>

<p><img alt="A modal which displays on top of the sign-in page where the user must accept terms before continuing" class="center-image" src="/assets-jekyll/blog/okta-custom-sign-in-page/modal-siw-2379ac6fcdef9e9e55634f4f98d033535efcbf959ab46ad793c1e3be94a69c84.jpg" width="800" /></p>

<p>We made the modal fixed even if the user presses <kbd>Esc</kbd> or selects the scrim. Users must agree to the terms to continue.</p>

<h2 id="customize-okta-hosted-sign-in-page-behavior-using-web-apis">Customize Okta-hosted sign-in page behavior using Web APIs</h2>

<p>We display the modal as soon as the webpage loads. It works, but we can also display the modal after the Sign-In Widget renders. Doing so allows us to use the nice enter and leave CSS transitions Alpine supports. We want to watch for changes to the DOM within the <code class="language-plaintext highlighter-rouge">&lt;div id="okta-login-container"&gt;&lt;/div&gt;</code>. This is the parent container that renders the SIW. We can use the <a href="https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver"><code class="language-plaintext highlighter-rouge">MutationObserver</code> Web API</a> and watch for DOM mutations within the <code class="language-plaintext highlighter-rouge">div</code>.</p>

<p>In the <code class="language-plaintext highlighter-rouge">&lt;script&gt;&lt;/script&gt;</code> section, after the event listener for <code class="language-plaintext highlighter-rouge">alpine:init</code>, add the following code:</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="nx">loginContainer</span> <span class="o">=</span> <span class="nb">document</span><span class="p">.</span><span class="nx">querySelector</span><span class="p">(</span><span class="dl">"</span><span class="s2">#okta-login-container</span><span class="dl">"</span><span class="p">);</span>

<span class="c1">// Use MutationObserver to watch for auth container element</span>
<span class="kd">const</span> <span class="nx">mutationObserver</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">MutationObserver</span><span class="p">(()</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">element</span> <span class="o">=</span> <span class="nx">loginContainer</span><span class="p">.</span><span class="nx">querySelector</span><span class="p">(</span><span class="dl">'</span><span class="s1">[data-se*="auth-container"]</span><span class="dl">'</span><span class="p">);</span>
  <span class="k">if</span> <span class="p">(</span><span class="nx">element</span><span class="p">)</span> <span class="p">{</span>
    <span class="nb">document</span><span class="p">.</span><span class="nx">getElementById</span><span class="p">(</span><span class="dl">'</span><span class="s1">modal</span><span class="dl">'</span><span class="p">).</span><span class="nx">classList</span><span class="p">.</span><span class="nx">remove</span><span class="p">(</span><span class="dl">'</span><span class="s1">hidden</span><span class="dl">'</span><span class="p">);</span>
    <span class="c1">// Open modal using Alpine store</span>
    <span class="nx">Alpine</span><span class="p">.</span><span class="nx">store</span><span class="p">(</span><span class="dl">'</span><span class="s1">modal</span><span class="dl">'</span><span class="p">).</span><span class="nx">show</span><span class="p">();</span>
    <span class="c1">// Clean up the observer</span>
    <span class="nx">mutationObserver</span><span class="p">.</span><span class="nx">disconnect</span><span class="p">();</span>
  <span class="p">}</span>
<span class="p">});</span>

<span class="nx">mutationObserver</span><span class="p">.</span><span class="nx">observe</span><span class="p">(</span><span class="nx">loginContainer</span><span class="p">,</span> <span class="p">{</span>
  <span class="na">childList</span><span class="p">:</span> <span class="kc">true</span><span class="p">,</span>
  <span class="na">subtree</span><span class="p">:</span> <span class="kc">true</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Let’s walk through what the code does. First, we’re creating a variable to reference the parent container for the SIW, as we’ll use it as the root element to target our work. Mutation observers can negatively impact performance, so it’s essential to limit the scope of the observer as much as possible.</p>

<p><strong>Create the observer</strong></p>

<p>We create the observer and define the behavior for observation. The observer first looks for the element with the data attribute named <code class="language-plaintext highlighter-rouge">se</code>, which includes the value <code class="language-plaintext highlighter-rouge">auth-container</code>. Okta adds a node with the data attribute for internal operations. We’ll do the same for our internal operations. 😎</p>

<p><strong>Define the behavior upon observation</strong></p>

<p>Once we have an element matching the <code class="language-plaintext highlighter-rouge">auth-container</code> data attribute, we show the modal, which triggers the enter transition animation. Then we clean up the observer.</p>

<p><strong>Identify what to observe</strong></p>

<p>We begin by observing the DOM and pass in the element to use as the root, along with a configuration specifying what to watch for. We want to look for changes in child elements and the subtree from the root to find the SIW elements.</p>

<p>Lastly, let’s enable the modal to trigger based on the observer. I intentionally provided you with code snippets that force the modal to display before the SIW renders, so you could take sneak peeks at your work as we went along.</p>

<p>In the HTML node structure, find the <code class="language-plaintext highlighter-rouge">&lt;div id="modal"&gt;</code>. It’s missing a class that hides the modal initially. Add the class <code class="language-plaintext highlighter-rouge">hidden</code> to the class list. The class list for the <code class="language-plaintext highlighter-rouge">&lt;div&gt;</code> should look like</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"modal"</span>
    <span class="na">x-data</span>
    <span class="na">x-cloak</span>
    <span class="na">x-show=</span><span class="s">"$store.modal.open"</span> 
    <span class="na">x-transition:enter=</span><span class="s">"transition ease-out duration-300"</span>
    <span class="na">x-transition:enter-start=</span><span class="s">"opacity-0"</span>
    <span class="na">x-transition:enter-end=</span><span class="s">"opacity-100"</span>
    <span class="na">x-transition:leave=</span><span class="s">"transition ease-in duration-200"</span>
    <span class="na">x-transition:leave-start=</span><span class="s">"opacity-100"</span>
    <span class="na">x-transition:leave-end=</span><span class="s">"opacity-0 hidden"</span>
    <span class="na">class=</span><span class="s">"hidden fixed inset-0 z-50 flex items-center justify-center bg-(--color-black)/80 bg-opacity-50"</span><span class="nt">&gt;</span>

<span class="c">&lt;!-- Remaining modal structure here. Compare your work to the class list above --&gt;</span>

<span class="nt">&lt;/div&gt;</span>
</code></pre></div></div>

<p>Then, in the <code class="language-plaintext highlighter-rouge">alpine:init</code> event listener, change the modal’s <code class="language-plaintext highlighter-rouge">open</code> property to default to <code class="language-plaintext highlighter-rouge">false</code>:</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nb">document</span><span class="p">.</span><span class="nx">addEventListener</span><span class="p">(</span><span class="dl">'</span><span class="s1">alpine:init</span><span class="dl">'</span><span class="p">,</span> <span class="p">()</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="nx">Alpine</span><span class="p">.</span><span class="nx">store</span><span class="p">(</span><span class="dl">'</span><span class="s1">modal</span><span class="dl">'</span><span class="p">,</span> <span class="p">{</span>
    <span class="na">open</span><span class="p">:</span> <span class="kc">false</span><span class="p">,</span>
    <span class="nx">show</span><span class="p">()</span> <span class="p">{</span>
      <span class="k">this</span><span class="p">.</span><span class="nx">open</span> <span class="o">=</span> <span class="kc">true</span><span class="p">;</span>
    <span class="p">},</span>
    <span class="nx">hide</span><span class="p">()</span> <span class="p">{</span>
      <span class="k">this</span><span class="p">.</span><span class="nx">open</span> <span class="o">=</span> <span class="kc">false</span><span class="p">;</span>
    <span class="p">}</span>
  <span class="p">});</span>
<span class="p">});</span>
</code></pre></div></div>

<p>Save and publish your changes. You’ll now notice a slight delay before the modal eases into view. So smooth!</p>

<p><img alt="A customized Okta-hosted Sign-In Widget with custom elements, colors, and styles" class="center-image" src="/assets-jekyll/blog/okta-custom-sign-in-page/final-siw-desktop-211b475e04926250d77e2a72779c27ea2c22a65ad47f43322c22ba81006f5df2.jpg" width="800" /></p>

<p>It’s worth noting that our solution isn’t foolproof; a savvy user can hide the modal and continue interacting with the sign-in widget by manipulating elements in the browser’s debugger. You’ll need to add extra checks and more robust code for foolproof methods. Still, this example provides a general idea of capabilities and how one might approach adding interactive components to the sign-in experience.</p>

<p>Don’t forget to test any implementation changes to the sign-in page for accessibility. The default site and the sign-in widget are accessible. Any changes or customizations we make may alter the accessibility of the site.</p>

<p>You can connect your brand to one of our sample apps to see it work end-to-end. Follow the instructions in the README of our <a href="https://github.com/okta-samples/okta-react-sample">Okta React Sample</a> to run the app locally. You’ll need to update your Okta OpenID Connect (OIDC) application to work with the domain. In the Okta Admin Console, navigate to <strong>Applications</strong> &gt; <strong>Applications</strong> and find the Okta application for your custom app. Navigate to the <strong>Sign On</strong> tab. You’ll see a section for <strong>OpenID Connect ID Token</strong>. Select <strong>Edit</strong> and select <strong>Custom URL</strong> for your brand’s sign-in URL as the <strong>Issuer</strong> value.</p>

<p>You’ll use the issuer value, which matches your brand’s custom URL, and the Okta application’s client ID in your custom app’s OIDC configuration.</p>

<h2 id="add-tailwind-web-apis-and-javascript-libraries-to-customize-your-okta-hosted-sign-in-page">Add Tailwind, Web APIs, and JavaScript libraries to customize your Okta-hosted sign-in page</h2>

<p>I hope you found this post interesting and unlocked the potential of how much you can customize the Okta-hosted Sign-In Widget experience.</p>

<p>You can find the final code for this project in the <a href="https://github.com/oktadev/okta-js-siw-customization-example/tree/main/custom-signin-blog-post">GitHub repo</a>.</p>

<p>If you liked this post, check out these resources.</p>

<ul>
  <li><a href="/blog/2025-11-12-custom-signin">Stretch Your Imagination and Build a Delightful Sign-In Experience</a></li>
  <li><a href="https://developer.okta.com/docs/concepts/sign-in-widget/">The Okta Sign-In Widget</a></li>
</ul>

<p>Remember to follow us on <a href="https://www.linkedin.com/company/oktadev">LinkedIn</a> and subscribe to our <a href="https://www.youtube.com/c/oktadev">YouTube</a> for more exciting content. Let us know how you customized the Okta-hosted sign-in page. We’d love to see what you came up with.</p>

<p>We also want to hear from you about topics you want to see and questions you may have. Leave us a comment below!</p>
