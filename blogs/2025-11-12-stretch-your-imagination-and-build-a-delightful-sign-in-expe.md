---
title: "Stretch Your Imagination and Build a Delightful Sign-In Experience"
url: "https://developer.okta.com/blog/2025/11/12/custom-signin"
date: "Wed, 12 Nov 2025 00:00:00 -0500"
author: ""
feed_url: "https://developer.okta.com/feed.xml"
---
<p>When you choose Okta as your IAM provider, one of the features you get access to is customizing your Okta-hosted Sign-In Widget (SIW), which is our recommended method for the highest levels of identity security. It’s a customizable JavaScript component that provides a ready-made login interface you can use immediately as part of your web application.</p>

<p>The Okta Identity Engine (OIE) utilizes authentication policies to drive authentication challenges, and the SIW supports various authentication factors, ranging from basic username and password login to more advanced scenarios, such as multi-factor authentication, biometrics, passkeys, social login, account registration, account recovery, and more. Under the hood, it interacts with Okta’s APIs, so you don’t have to build or manage complex auth logic yourself. It’s all handled for you!</p>

<p>One of the perks of using the Okta SIW, especially with the 3rd Generation Standard (Gen3), is that customization is a configuration thanks to <a href="https://m3.material.io/foundations/design-tokens/overview">design tokens</a>, so you don’t have to write CSS to style the widget elements.</p>

<h2 id="style-the-okta-sign-in-widget-to-match-your-brand">Style the Okta Sign-In Widget to match your brand</h2>

<p>In this tutorial, we will customize the Sign In Widget for a fictional to-do app. We’ll make the following changes:</p>
<ul>
  <li>Replace font selections</li>
  <li>Define border, error, and focus colors</li>
  <li>Remove elements from the SIW, such as the horizontal rule and add custom elements</li>
  <li>Shift the control to the start of the site and add a background panel</li>
</ul>

<p>Without any changes, when you try to sign in to your Okta account, you see something like this:</p>

<p><img alt="Default Okta-hosted Sign-In Widget" class="center-image" src="/assets-jekyll/blog/custom-signin/default-siw-ca3d7bddb94294c86b7cf43444d188b3738323c9ae4e11ce6ecc2b5979fffb15.jpg" width="800" /></p>

<p>At the end of the tutorial, your login screen will look something like this 🎉</p>

<p><img alt="A customized Okta-hosted Sign-In Widget with custom elements, colors, and styles" class="center-image" src="/assets-jekyll/blog/custom-signin/final-siw-84d06d435ba3788183f7327b043d8dcaede89e6470bd0444c8862dc875c4b219.jpg" width="800" /></p>

<p>We’ll use the SIW gen3 along with new recommendations to customize form elements and style using design tokens.</p>

<p><strong class="hide">Table of Contents</strong></p>
<ul id="markdown-toc">
  <li><a href="#style-the-okta-sign-in-widget-to-match-your-brand" id="markdown-toc-style-the-okta-sign-in-widget-to-match-your-brand">Style the Okta Sign-In Widget to match your brand</a></li>
  <li><a href="#customize-your-okta-hosted-sign-in-page" id="markdown-toc-customize-your-okta-hosted-sign-in-page">Customize your Okta-hosted sign-in page</a></li>
  <li><a href="#understanding-the-okta-hosted-sign-in-widget-default-code" id="markdown-toc-understanding-the-okta-hosted-sign-in-widget-default-code">Understanding the Okta-hosted Sign-In Widget default code</a></li>
  <li><a href="#customize-the-ui-elements-within-the-okta-sign-in-widget" id="markdown-toc-customize-the-ui-elements-within-the-okta-sign-in-widget">Customize the UI elements within the Okta Sign-In Widget</a></li>
  <li><a href="#organize-your-sign-in-widget-customizations-with-css-custom-properties" id="markdown-toc-organize-your-sign-in-widget-customizations-with-css-custom-properties">Organize your Sign-In Widget customizations with CSS Custom properties</a></li>
  <li><a href="#extending-the-siw-theme-with-a-custom-color-palette" id="markdown-toc-extending-the-siw-theme-with-a-custom-color-palette">Extending the SIW theme with a custom color palette</a></li>
  <li><a href="#add-custom-html-elements-to-the-sign-in-widget" id="markdown-toc-add-custom-html-elements-to-the-sign-in-widget">Add custom HTML elements to the Sign-In Widget</a></li>
  <li><a href="#overriding-okta-sign-in-widget-element-styles" id="markdown-toc-overriding-okta-sign-in-widget-element-styles">Overriding Okta Sign-In Widget element styles</a></li>
  <li><a href="#change-the-layout-of-the-okta-hosted-sign-in-page" id="markdown-toc-change-the-layout-of-the-okta-hosted-sign-in-page">Change the layout of the Okta-hosted Sign-In page</a></li>
  <li><a href="#customize-your-gen3-okta-hosted-sign-in-widget" id="markdown-toc-customize-your-gen3-okta-hosted-sign-in-widget">Customize your Gen3 Okta-hosted Sign-In Widget</a></li>
</ul>

<p><strong>Prerequisites</strong>
To follow this tutorial, you need:</p>
<ul>
  <li>An Okta account with the Identity Engine, such as the <a href="https://developer.okta.com/signup/">Integrator Free account</a>. The SIW version in the org we’re using is 7.36.</li>
  <li>Your own domain name</li>
  <li>A basic understanding of HTML, CSS, and JavaScript</li>
  <li>A brand design in mind. Feel free to tap into your creativity!</li>
</ul>

<p>Let’s get started!</p>

<h2 id="customize-your-okta-hosted-sign-in-page">Customize your Okta-hosted sign-in page</h2>

<p>Before we begin, you must configure your Okta org to use your custom domain. Custom domains enable code customizations, allowing us to style more than just the default logo, background, favicon, and two colors. Sign in as an admin and open the Okta Admin Console, navigate to <strong>Customizations</strong> &gt; <strong>Brands</strong> and select <strong>Create Brand +</strong>.</p>

<p>Follow the <a href="https://developer.okta.com/docs/guides/custom-url-domain/main/">Customize domain and email</a> developer docs to set up your custom domain on the new brand.</p>

<p>You can also follow this post if you prefer.</p>

<article class="link-container" style="border: 1px solid silver; border-radius: 3px; padding: 12px 15px;">
              <a href="/blog/2023/01/12/signin-custom-domain" style="font-size: 1.375em; margin-bottom: 20px;">
                <span>A Secure and Themed Sign-in Page</span>
              </a>
              <p>Redirecting to the Okta-hosted sign-in page is the most secure way to authenticate users in your application. But the default configuration yield a very neutral sign-in page. This post walks you through customization options and setting up a custom domain so the personality of your site shines all through the user's experience.</p>
              <div><div class="BlogPost-attribution">
            <a href="/blog/authors/alisa-duncan/">
              <img alt="avatar-avatar-alisa_duncan.jpeg" class="BlogPost-avatar" src="/assets-jekyll/avatar-alisa_duncan-b29fa4df50f5c99f536307c6bc0e5cb3434a922bdada7fe4f4b3cf8488299465.jpg" />
            </a>
            <span class="BlogPost-author">
                <a href="/blog/authors/alisa-duncan/">Alisa Duncan</a>
            </span>
          </div></div>
          </article>

<p>Once you have a working brand with a custom domain, select your brand to configure it.
First, navigate to <strong>Settings</strong> and select <strong>Use third generation</strong> to enable the SIW Gen3. <strong>Save</strong> your selection.</p>

<blockquote>
  <p>⚠️ <strong>Note</strong></p>

  <p>The code in this post relies on using SIW Gen3. It will not work on SIW Gen2.</p>
</blockquote>

<p>Navigate to <strong>Theme</strong>. You’ll see a default brand page that looks something like this:</p>

<p><img alt="Default styles for the Okta-hosted SIW" class="center-image" src="/assets-jekyll/blog/custom-signin/default-siw-styles-049249e12debf07e4d791d667958a3c417e1b438a8a5b6057f3f715d8895ce07.jpg" width="800" /></p>

<p>Let’s start making it more aligned with the theme we have in mind. Change the primary and secondary colors, then the logo and favicon images with your preferred options</p>

<p>To change either color, click on the text field and enter the hex code for each. We’re going for a bold and colorful approach, so we’ll use <code class="language-plaintext highlighter-rouge">#ea3eda</code> as the primary color and <code class="language-plaintext highlighter-rouge">#ffa738</code> as the secondary color, and upload the logo and favicon images for the brand. Select <strong>Save</strong>.</p>

<p>Take a look at your sign-in page now by navigating to the sign-in URL for the brand. With your configuration, the sign-in widget looks more interesting than the default view, but we can make things even more exciting.</p>

<p>Let’s dive into the main task, customizing the signup page. On the <strong>Theme</strong> tab:</p>
<ol>
  <li>Select <strong>Sign-in Page</strong> in the dropdown menu</li>
  <li>Select the <strong>Customize</strong> button</li>
  <li>On the <strong>Page Design</strong> tab, select the <strong>Code editor</strong>  toggle to see a HTML page</li>
</ol>

<blockquote>
  <p>Note: You can only enable the code editor if you configure a <a href="https://developer.okta.com/docs/guides/custom-url-domain/">custom domain</a>.</p>
</blockquote>

<h2 id="understanding-the-okta-hosted-sign-in-widget-default-code">Understanding the Okta-hosted Sign-In Widget default code</h2>

<p>If you’re familiar with basic HTML, CSS, and JavaScript, the sign-in code appears standard, although it’s somewhat unusual in certain areas. There are two major blocks of code we should examine: the top of the <code class="language-plaintext highlighter-rouge">body</code> tag on the page and the sign-in configuration in the <code class="language-plaintext highlighter-rouge">script</code> tag.</p>

<p>The first one looks something like this:</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-login-container"</span><span class="nt">&gt;&lt;/div&gt;</span>
</code></pre></div></div>

<p>The second looks like this:</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">var</span> <span class="nx">config</span> <span class="o">=</span> <span class="nx">OktaUtil</span><span class="p">.</span><span class="nx">getSignInWidgetConfig</span><span class="p">();</span>

<span class="c1">// Render the Okta Sign-In Widget</span>
<span class="kd">var</span> <span class="nx">oktaSignIn</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">OktaSignIn</span><span class="p">(</span><span class="nx">config</span><span class="p">);</span>
<span class="nx">oktaSignIn</span><span class="p">.</span><span class="nx">renderEl</span><span class="p">({</span> <span class="na">el</span><span class="p">:</span> <span class="dl">'</span><span class="s1">#okta-login-container</span><span class="dl">'</span> <span class="p">},</span>
  <span class="nx">OktaUtil</span><span class="p">.</span><span class="nx">completeLogin</span><span class="p">,</span>
  <span class="kd">function</span><span class="p">(</span><span class="nx">error</span><span class="p">)</span> <span class="p">{</span>
    <span class="c1">// Logs errors that occur when configuring the widget.</span>
    <span class="c1">// Remove or replace this with your own custom error handler.</span>
    <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="nx">error</span><span class="p">.</span><span class="nx">message</span><span class="p">,</span> <span class="nx">error</span><span class="p">);</span>
  <span class="p">}</span>
<span class="p">);</span>
</code></pre></div></div>

<p>Let’s take a closer look at how this code works. In the HTML, there’s a designated parent element that the <code class="language-plaintext highlighter-rouge">OktaSignIn</code> instance uses to render the SIW as a child node. This means that when the page loads, you’ll see the <code class="language-plaintext highlighter-rouge">&lt;div id="okta-login-container"&gt;&lt;/div&gt;</code> in the DOM with the HTML elements for SIW functionality as its child within the <code class="language-plaintext highlighter-rouge">div</code>. The SIW handles all authentication and user registration processes as defined by policies, allowing us to focus entirely on customization.</p>

<p>To create the SIW, we need to pass in the configuration. The configuration includes properties like the theme elements and messages for labels. The method <code class="language-plaintext highlighter-rouge">renderEl()</code> identifies the HTML element to use for rendering the SIW. We’re passing in <code class="language-plaintext highlighter-rouge">#okta-login-container</code> as the identifier.</p>

<p>The <code class="language-plaintext highlighter-rouge">#okta-login-container</code> is a <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_selectors">CSS selector</a>. While any correct CSS selector works, we recommend you use the ID of the element. Element IDs must be unique within the HTML document, so this is the safest and easiest method.</p>

<h2 id="customize-the-ui-elements-within-the-okta-sign-in-widget">Customize the UI elements within the Okta Sign-In Widget</h2>

<p>Now that we have a basic understanding of how the Okta Sign-In Widget works, let’s start customizing the code. We’ll start by customizing the elements within the SIW. To manipulate the Okta SIW DOM elements in Gen3, we use the <code class="language-plaintext highlighter-rouge">afterTransform</code> method. The <code class="language-plaintext highlighter-rouge">afterTransform</code> method allows us to remove or update elements for individual or all forms.</p>

<p>Find the button <strong>Edit</strong> on the <strong>Code editor</strong> view, which makes the code editor editable and behaves like a lightweight IDE.</p>

<p>Below the <code class="language-plaintext highlighter-rouge">oktaSignIn.renderEl()</code> method within the <code class="language-plaintext highlighter-rouge">&lt;script&gt;</code> tag, add</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">oktaSignIn</span><span class="p">.</span><span class="nx">afterTransform</span><span class="p">(</span><span class="dl">'</span><span class="s1">identify</span><span class="dl">'</span><span class="p">,</span> <span class="p">({</span> <span class="nx">formBag</span> <span class="p">})</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">title</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Title</span><span class="dl">'</span><span class="p">);</span>
  <span class="k">if</span> <span class="p">(</span><span class="nx">title</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">title</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">content</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">Log in and create a task</span><span class="dl">"</span><span class="p">;</span>
  <span class="p">}</span>

  <span class="kd">const</span> <span class="nx">help</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span> <span class="o">&amp;&amp;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">dataSe</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">help</span><span class="dl">'</span><span class="p">);</span>
  <span class="kd">const</span> <span class="nx">unlock</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span> <span class="o">&amp;&amp;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">dataSe</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">unlock</span><span class="dl">'</span><span class="p">);</span>
  <span class="kd">const</span> <span class="nx">divider</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Divider</span><span class="dl">'</span><span class="p">);</span>
  <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">filter</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="p">[</span><span class="nx">help</span><span class="p">,</span> <span class="nx">unlock</span><span class="p">,</span> <span class="nx">divider</span><span class="p">].</span><span class="nx">includes</span><span class="p">(</span><span class="nx">ele</span><span class="p">));</span>
<span class="p">});</span>
</code></pre></div></div>

<p>This <code class="language-plaintext highlighter-rouge">afterTransform</code> hook only runs before the ‘identify’ form. We can find and target UI elements using the <code class="language-plaintext highlighter-rouge">FormBag</code>. The <code class="language-plaintext highlighter-rouge">afterTransform</code> hook is a more streamlined way to manipulate DOM elements within the SIW before rendering the widget. For example, we can search elements by type to filter them out of the view before rendering, which is more performant than manipulating DOM elements after SIW renders. We filtered out elements such as the <code class="language-plaintext highlighter-rouge">unlock</code> account element and dividers in this snippet.</p>

<p>Let’s take a look at what this looks like. Press <strong>Save to draft</strong> and <strong>Publish</strong>.</p>

<p>Navigate to your sign-in URL for your brand to view the changes you made. When we compare to the default state, we no longer see the horizontal rule below the logo or the “Help” link. The account unlock element is no longer available.</p>

<p>We explored how we can customize the widget elements. Now, let’s add some flair.</p>

<h2 id="organize-your-sign-in-widget-customizations-with-css-custom-properties">Organize your Sign-In Widget customizations with CSS Custom properties</h2>

<p>At its core, we’re styling an HTML document. This means we operate on the SIW customization in the same way as we would any HTML page, and code organization principles still apply. We can define customization values as <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_cascading_variables/Using_CSS_custom_properties">CSS Custom properties</a> (also known as CSS variables).</p>

<p>Defining styles using CSS variables keeps our code <a href="https://en.wikipedia.org/wiki/Don%27t_repeat_yourself">DRY</a>. Setting up style values for reuse even extends beyond the Okta-hosted sign-in page. If your organization hosts stylesheets with brand color defined as CSS custom properties publicly, you can use the colors defined there and link your stylesheet.</p>

<p>Before making code edits, identify the fonts you want to use for your customization. We found a header and body font to use.</p>

<p>Open the SIW code editor for your brand and select <strong>Edit</strong> to make changes.</p>

<p>Import the fonts into the HTML. You can <code class="language-plaintext highlighter-rouge">&lt;link&gt;</code> or <code class="language-plaintext highlighter-rouge">@import</code> the fonts based on your preference. We added the <code class="language-plaintext highlighter-rouge">&lt;link&gt;</code> instructions to the <code class="language-plaintext highlighter-rouge">&lt;head&gt;</code> of the HTML.</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;link</span> <span class="na">rel=</span><span class="s">"preconnect"</span> <span class="na">href=</span><span class="s">"https://fonts.googleapis.com"</span><span class="nt">&gt;</span>
<span class="nt">&lt;link</span> <span class="na">rel=</span><span class="s">"preconnect"</span> <span class="na">href=</span><span class="s">"https://fonts.gstatic.com"</span> <span class="na">crossorigin</span><span class="nt">&gt;</span>
<span class="nt">&lt;link</span> <span class="na">href=</span><span class="s">"https://fonts.googleapis.com/css2?family=Inter+Tight:ital,wght@0,100..900;1,100..900&amp;family=Poiret+One&amp;display=swap"</span> <span class="na">rel=</span><span class="s">"stylesheet"</span><span class="nt">&gt;</span>
</code></pre></div></div>

<p>Find the  <code class="language-plaintext highlighter-rouge">&lt;style nonce="{{nonceValue}}"&gt;</code>  tag. Within the tag, define your properties using the <code class="language-plaintext highlighter-rouge">:root</code> selector:</p>

<div class="language-css highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nd">:root</span> <span class="p">{</span>
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
    <span class="py">--font-header</span><span class="p">:</span> <span class="s2">'Poiret One'</span><span class="p">,</span> <span class="nb">sans-serif</span><span class="p">;</span>
    <span class="py">--font-body</span><span class="p">:</span> <span class="s2">'Inter Tight'</span><span class="p">,</span> <span class="nb">sans-serif</span><span class="p">;</span>
 <span class="p">}</span>
</code></pre></div></div>

<p>Feel free to add new properties or replace the property value for your brand. Now is a good opportunity to add your own brand colors and customizations!</p>

<p>Let’s configure the SIW with our variables using design tokens.</p>

<p>Find <code class="language-plaintext highlighter-rouge">var config = OktaUtil.getSignInWidgetConfig();</code>. After this line of code, set the values of the design tokens using your CSS Custom properties. You’ll use the <code class="language-plaintext highlighter-rouge">var()</code> function to access your variables:</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">config</span><span class="p">.</span><span class="nx">theme</span> <span class="o">=</span> <span class="p">{</span>
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
    <span class="na">TypographyFamilyButton</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--font-body)</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">BorderColorDangerControl</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-cherry)</span><span class="dl">'</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Save your changes, publish the page, and view your brand’s sign-in URI site. Yay! You see, there’s no border outline, the border radius of the widget and HTML elements changed, a different focus color, and a different color for element outlines when there’s a form error. You can inspect the HTML elements and view the computed styles. Or if you prefer, feel free to update the CSS variables to something more visible.</p>

<p>When you inspect your brand’s sign-in URL site, you’ll notice that the fonts aren’t loading properly and that there are errors in your browser’s debugging console. This is because you need to configure Content Security Policies (CSP) to allow resources loaded from external sites. CSPs are a security measure to mitigate cross-site scripting (XSS) attacks. You can read <a href="/blog/2021/10/18/security-headers-best-practices">An Overview of Best Practices for Security Headers</a>  to learn more about CSPs.</p>

<p>Navigate to the <strong>Settings</strong> tab for your brand’s <strong>Sign-in page</strong>. Find the <strong>Content Security Policy</strong> and press <strong>Edit</strong>. Add the domains for external resources. In our example, we only load resources from Google Fonts, so we added the following two domains:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>*.googleapis.com
*.gstatic.com
</code></pre></div></div>

<p>Press <strong>Save to draft</strong> and press <strong>Publish</strong> to view your changes. The SIW now displays the fonts you selected!</p>

<h2 id="extending-the-siw-theme-with-a-custom-color-palette">Extending the SIW theme with a custom color palette</h2>

<p>In our example, we selectively added colors. The SIW design system adheres to WCAG accessibility standards and relies on <a href="https://m2.material.io/">Material Design</a> color palettes.</p>

<p>Okta generates colors based on your primary color that conform to accessibility standards and contrast requirements. Check out <a href="https://help.okta.com/oie/en-us/content/topics/settings/branding-siw-color-contrast.htm">Understand Sign-In Widget color customization</a> to learn more about color contrast and how Okta color generation works. You must supply accessible colors to the configuration.</p>

<p>Material Design supports themes by customizing color palettes. The <a href="https://developer.okta.com/docs/guides/custom-widget-gen3/main/#use-design-tokens">list of all configurable design tokens</a> displays all available options, including <code class="language-plaintext highlighter-rouge">Hue*</code> properties for precise color control. Consider exploring color palette customization options tailored to your brand’s specific needs. You can use Material palette generators such as <a href="https://m2.material.io/inline-tools/color/">this color picker</a> from the Google team or an open source <a href="https://materialpalettes.com/">Material Design Palette Generator</a> that allows you to enter a HEX color value.</p>

<p>Don’t forget to keep accessibility in mind. You can run an accessibility audit using <a href="https://developer.chrome.com/docs/lighthouse/overview">Lighthouse</a> in the Chrome browser and the <a href="https://webaim.org/resources/contrastchecker/">WebAIM Contrast Checker</a>. Our selected primary color doesn’t quite meet contrast requirements. 😅</p>

<h2 id="add-custom-html-elements-to-the-sign-in-widget">Add custom HTML elements to the Sign-In Widget</h2>

<p>Previously, we filtered HTML elements out of the SIW. We can also add new custom HTML elements to SIW. We’ll experiment by adding a link to the Okta Developer blog. Find the <code class="language-plaintext highlighter-rouge">afterTransform()</code> method. Update the <code class="language-plaintext highlighter-rouge">afterTransform()</code> method to look like this:</p>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">oktaSignIn</span><span class="p">.</span><span class="nx">afterTransform</span><span class="p">(</span><span class="dl">'</span><span class="s1">identify</span><span class="dl">'</span><span class="p">,</span> <span class="p">({</span><span class="nx">formBag</span><span class="p">})</span> <span class="o">=&gt;</span> <span class="p">{</span>
  <span class="kd">const</span> <span class="nx">title</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Title</span><span class="dl">'</span><span class="p">);</span>
  <span class="k">if</span> <span class="p">(</span><span class="nx">title</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">title</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">content</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">Log in and create a task</span><span class="dl">"</span><span class="p">;</span>
  <span class="p">}</span>

  <span class="kd">const</span> <span class="nx">help</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span> <span class="o">&amp;&amp;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">dataSe</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">help</span><span class="dl">'</span><span class="p">);</span>
  <span class="kd">const</span> <span class="nx">unlock</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span> <span class="o">&amp;&amp;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">dataSe</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">unlock</span><span class="dl">'</span><span class="p">);</span>
  <span class="kd">const</span> <span class="nx">divider</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Divider</span><span class="dl">'</span><span class="p">);</span>
  <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">filter</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="p">[</span><span class="nx">help</span><span class="p">,</span> <span class="nx">unlock</span><span class="p">,</span> <span class="nx">divider</span><span class="p">].</span><span class="nx">includes</span><span class="p">(</span><span class="nx">ele</span><span class="p">));</span>

  <span class="kd">const</span> <span class="nx">blogLink</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span><span class="p">,</span>
    <span class="na">contentType</span><span class="p">:</span> <span class="dl">'</span><span class="s1">footer</span><span class="dl">'</span><span class="p">,</span> 
    <span class="na">options</span><span class="p">:</span> <span class="p">{</span>
      <span class="na">href</span><span class="p">:</span> <span class="dl">'</span><span class="s1">https://developer.okta.com/blog</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">label</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Read our blog</span><span class="dl">'</span><span class="p">,</span>
      <span class="na">dataSe</span><span class="p">:</span> <span class="dl">'</span><span class="s1">blogCustomLink</span><span class="dl">'</span>
    <span class="p">}</span>
  <span class="p">};</span>
  <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">push</span><span class="p">(</span><span class="nx">blogLink</span><span class="p">);</span>
<span class="p">});</span>
</code></pre></div></div>

<p>We created a new element named <code class="language-plaintext highlighter-rouge">blogLink</code> and set properties such as the type, where the content resides, and options related to the <code class="language-plaintext highlighter-rouge">type</code>. We also added a <code class="language-plaintext highlighter-rouge">dataSe</code> property that adds the value <code class="language-plaintext highlighter-rouge">blogCustomLink</code> to an <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/How_to/Use_data_attributes">HTML data attribute</a>. Doing so makes it easier for us to select the element for customization or for testing purposes.</p>

<p>When you continue past the ‘identify’ form in the sign-in flow, you’ll no longer see the link to the blog.</p>

<h2 id="overriding-okta-sign-in-widget-element-styles">Overriding Okta Sign-In Widget element styles</h2>

<p>We should use design tokens for customizations wherever possible. In cases where a design token isn’t available for your styling needs, you can fall back to defining style manually.</p>

<p>Let’s start with the element we added, the blog link. Let’s say we want to display the text in capital casing. It’s not good practice to define the label value using capital casing for accessibility. We should use CSS to transform the text.</p>

<p>In the styles definition, find the <code class="language-plaintext highlighter-rouge">#login-bg-image-id</code>. After the styles for the background image, add the style to target the <code class="language-plaintext highlighter-rouge">blogCustomLink</code> data attribute and define the text transform like this:</p>

<div class="language-css highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">a</span><span class="o">[</span><span class="nt">data-se</span><span class="o">=</span><span class="s1">"blogCustomLink"</span><span class="o">]</span> <span class="p">{</span>
    <span class="nl">text-transform</span><span class="p">:</span> <span class="nb">uppercase</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Save and publish the page to check out your changes.</p>

<p>Now, let’s say you want to style an Okta-provided HTML element. Use design tokens wherever possible, and make style changes cautiously as doing so adds brittleness and security concerns.</p>

<p>Here’s a terrible example of styling an Okta-provided HTML element that you shouldn’t emulate, as it makes the text illegible. Let’s say you want to change the background of the <strong>Next</strong> button to be a gradient. 🌈</p>

<p>Inspect the SIW element you want to style. We want to style the <code class="language-plaintext highlighter-rouge">button</code> with the data attribute <code class="language-plaintext highlighter-rouge">okta-sign-in-header</code>.</p>

<p>After the <code class="language-plaintext highlighter-rouge">blogCustomLink</code> style, add the following:</p>

<div class="language-css highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">button</span><span class="o">[</span><span class="nt">data-se</span><span class="o">=</span><span class="s1">"save"</span><span class="o">]</span> <span class="p">{</span>
    <span class="nl">background</span><span class="p">:</span> <span class="n">linear-gradient</span><span class="p">(</span><span class="m">12deg</span><span class="p">,</span> <span class="n">var</span><span class="p">(</span><span class="n">--color-fuchsia</span><span class="p">)</span> <span class="m">0%</span><span class="p">,</span> <span class="n">var</span><span class="p">(</span><span class="n">--color-orange</span><span class="p">)</span> <span class="m">100%</span><span class="p">);</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Save and publish the site. The button background is now a gradient.</p>

<p>However, style the Okta-provided SIW elements with caution. The dangers with this approach are two-fold:</p>
<ol>
  <li>The Okta Sign-in widget undergoes accessibility audits, and changing styles and behavior manually may decrease accessibility thresholds</li>
  <li>The Okta Sign-in widget is internationalized, and changing styles around text layout manually may break localization needs</li>
  <li>Okta can’t guarantee that the data attributes or DOM elements remain unchanged, leading to customization breaks</li>
</ol>

<p>In the rare case where you style an Okta-provided SIW element you may need to pin the SIW version so your customizations don’t break from under you. Navigate to the <strong>Settings</strong> tab and find the <strong>Sign-In Widget version</strong> section. Select <strong>Edit</strong> and select the most recent version of the widget, as this one should be compatible with your code. We are using widget version 7.36 in this post.</p>

<blockquote>
  <p>⚠️ <strong>Note</strong></p>

  <p>When you pin the widget, you won’t get the latest and greatest updates from the SIW without manually updating the version. Pinning the version prevents any forward progress in the evolution and extensibility of the end-user experiences. For the most secure option, allow SIW to update automatically and avoid overly customizing the SIW with CSS. Use the design tokens wherever possible.</p>
</blockquote>

<h2 id="change-the-layout-of-the-okta-hosted-sign-in-page">Change the layout of the Okta-hosted Sign-In page</h2>

<p>We left the HTML nodes defined in the SIW customization unedited so far. You can change the layout of the default <code class="language-plaintext highlighter-rouge">&lt;div&gt;</code> containers to make a significant impact. Change the <code class="language-plaintext highlighter-rouge">display</code> CSS property to make an impactful change, such as using <a href="https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/CSS_layout/Flexbox">Flexbox</a> or <a href="https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/CSS_layout/Grids">CSS Grid</a>. I’ll use Flexbox in this example.</p>

<p>Find the <code class="language-plaintext highlighter-rouge">div</code> for the background image container and the <code class="language-plaintext highlighter-rouge">okta-login-container</code>. Replace those <code class="language-plaintext highlighter-rouge">div</code> elements with this HTML snippet:</p>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"login-bg-image-id"</span> <span class="na">class=</span><span class="s">"login-bg-image tb--background"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">"login-container-panel"</span><span class="nt">&gt;</span>
        <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-login-container"</span><span class="nt">&gt;&lt;/div&gt;</span>
    <span class="nt">&lt;/div&gt;</span>
<span class="nt">&lt;/div&gt;</span>
</code></pre></div></div>

<p>We moved the <code class="language-plaintext highlighter-rouge">okta-login-container</code> div inside another parent container and made it a child of the background image container.</p>

<p>Find <code class="language-plaintext highlighter-rouge">#login-bg-image</code> style. Add the <code class="language-plaintext highlighter-rouge">display: flex;</code> property. The styles should look like this:</p>

<div class="language-css highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="nf">#login-bg-image-id</span> <span class="p">{</span>
     <span class="nl">background-image</span><span class="p">:</span> <span class="err">{{</span><span class="n">bgImageUrl</span><span class="p">}</span><span class="err">}</span><span class="o">;</span>
     <span class="nt">display</span><span class="o">:</span> <span class="nt">flex</span><span class="o">;</span>
<span class="err">}</span>
</code></pre></div></div>

<p>We want to style the <code class="language-plaintext highlighter-rouge">okta-login-container</code>’s parent <code class="language-plaintext highlighter-rouge">&lt;div&gt;</code> to set the background color and to center the SIW on the panel. Add new styles for the <code class="language-plaintext highlighter-rouge">login-container-panel</code> class:</p>

<div class="language-css highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nc">.login-container-panel</span> <span class="p">{</span>
    <span class="nl">background</span><span class="p">:</span> <span class="n">var</span><span class="p">(</span><span class="n">--color-white</span><span class="p">);</span>
    <span class="nl">display</span><span class="p">:</span> <span class="n">flex</span><span class="p">;</span>
    <span class="nl">justify-content</span><span class="p">:</span> <span class="nb">center</span><span class="p">;</span>
    <span class="nl">align-items</span><span class="p">:</span> <span class="nb">center</span><span class="p">;</span>
    <span class="nl">width</span><span class="p">:</span> <span class="m">40%</span><span class="p">;</span>
    <span class="nl">min-width</span><span class="p">:</span> <span class="m">400px</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Save your changes and view the sign-in page. What do you think of the new layout? 🎊</p>

<blockquote>
  <p>⚠️ <strong>Note</strong></p>

  <p>Flexbox and CSS Grid are responsive, but you may still need to add properties handling responsiveness or media queries to fit your needs.</p>
</blockquote>

<p>Your final code might look something like this:</p>

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
    <span class="nt">&lt;link</span> <span class="na">href=</span><span class="s">"https://fonts.googleapis.com/css2?family=Inter+Tight:ital,wght@0,100..900;1,100..900&amp;family=Poiret+One&amp;display=swap"</span> <span class="na">rel=</span><span class="s">"stylesheet"</span><span class="nt">&gt;</span>    

    <span class="nt">&lt;title&gt;</span>{{pageTitle}}<span class="nt">&lt;/title&gt;</span>
    {{{SignInWidgetResources}}}

    <span class="nt">&lt;style </span><span class="na">nonce=</span><span class="s">"{{nonceValue}}"</span><span class="nt">&gt;</span>
        <span class="nd">:root</span> <span class="p">{</span>
            <span class="py">--font-header</span><span class="p">:</span> <span class="s2">'Poiret One'</span><span class="p">,</span> <span class="nb">sans-serif</span><span class="p">;</span>
            <span class="py">--font-body</span><span class="p">:</span> <span class="s2">'Inter Tight'</span><span class="p">,</span> <span class="nb">sans-serif</span><span class="p">;</span>
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
        <span class="p">}</span>

        <span class="p">{</span><span class="err">{</span> <span class="err">#useSiwGen3</span> <span class="p">}</span><span class="err">}</span>

        <span class="nt">html</span> <span class="p">{</span>
            <span class="nl">font-size</span><span class="p">:</span> <span class="m">87.5%</span><span class="p">;</span>
        <span class="p">}</span>

        <span class="p">{</span><span class="err">{</span> <span class="err">/useSiwGen3</span> <span class="p">}</span><span class="err">}</span>

        <span class="nf">#login-bg-image-id</span> <span class="p">{</span>
            <span class="nl">background-image</span><span class="p">:</span> <span class="err">{{</span><span class="n">bgImageUrl</span><span class="p">}</span><span class="err">}</span><span class="o">;</span>
            <span class="nt">display</span><span class="o">:</span> <span class="nt">flex</span><span class="o">;</span>
        <span class="err">}</span>
   
       <span class="nc">.login-container-panel</span> <span class="p">{</span>
            <span class="nl">background</span><span class="p">:</span> <span class="n">var</span><span class="p">(</span><span class="n">--color-white</span><span class="p">);</span>
            <span class="nl">display</span><span class="p">:</span> <span class="n">flex</span><span class="p">;</span>
            <span class="nl">justify-content</span><span class="p">:</span> <span class="nb">center</span><span class="p">;</span>
            <span class="nl">align-items</span><span class="p">:</span> <span class="nb">center</span><span class="p">;</span>
            <span class="nl">width</span><span class="p">:</span> <span class="m">40%</span><span class="p">;</span>
            <span class="nl">min-width</span><span class="p">:</span> <span class="m">400px</span><span class="p">;</span>
        <span class="p">}</span>

        <span class="nt">a</span><span class="o">[</span><span class="nt">data-se</span><span class="o">=</span><span class="s1">"blogCustomLink"</span><span class="o">]</span> <span class="p">{</span>
            <span class="nl">text-transform</span><span class="p">:</span> <span class="nb">uppercase</span><span class="p">;</span>
        <span class="p">}</span>
    <span class="nt">&lt;/style&gt;</span>
<span class="nt">&lt;/head&gt;</span>

<span class="nt">&lt;body&gt;</span>
   <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"login-bg-image-id"</span> <span class="na">class=</span><span class="s">"login-bg-image tb--background"</span><span class="nt">&gt;</span>
        <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">"login-container-panel"</span><span class="nt">&gt;</span>
            <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"okta-login-container"</span><span class="nt">&gt;&lt;/div&gt;</span>
        <span class="nt">&lt;/div&gt;</span>
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
                <span class="na">TypographyFamilyButton</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--font-body)</span><span class="dl">'</span><span class="p">,</span>
                <span class="na">BorderColorDangerControl</span><span class="p">:</span> <span class="dl">'</span><span class="s1">var(--color-cherry)</span><span class="dl">'</span>
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

        <span class="nx">oktaSignIn</span><span class="p">.</span><span class="nx">afterTransform</span><span class="p">(</span><span class="dl">'</span><span class="s1">identify</span><span class="dl">'</span><span class="p">,</span> <span class="p">({</span> <span class="nx">formBag</span> <span class="p">})</span> <span class="o">=&gt;</span> <span class="p">{</span>
            <span class="kd">const</span> <span class="nx">title</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Title</span><span class="dl">'</span><span class="p">);</span>
            <span class="k">if</span> <span class="p">(</span><span class="nx">title</span><span class="p">)</span> <span class="p">{</span>
                <span class="nx">title</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">content</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">Log in and create a task</span><span class="dl">"</span><span class="p">;</span>
            <span class="p">}</span>

            <span class="kd">const</span> <span class="nx">help</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span> <span class="o">&amp;&amp;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">dataSe</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">help</span><span class="dl">'</span><span class="p">);</span>
            <span class="kd">const</span> <span class="nx">unlock</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span> <span class="o">&amp;&amp;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">options</span><span class="p">.</span><span class="nx">dataSe</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">unlock</span><span class="dl">'</span><span class="p">);</span>
            <span class="kd">const</span> <span class="nx">divider</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="nx">ele</span><span class="p">.</span><span class="nx">type</span> <span class="o">===</span> <span class="dl">'</span><span class="s1">Divider</span><span class="dl">'</span><span class="p">);</span>
            <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span> <span class="o">=</span> <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">filter</span><span class="p">(</span><span class="nx">ele</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="p">[</span><span class="nx">help</span><span class="p">,</span> <span class="nx">unlock</span><span class="p">,</span> <span class="nx">divider</span><span class="p">].</span><span class="nx">includes</span><span class="p">(</span><span class="nx">ele</span><span class="p">));</span>

            <span class="kd">const</span> <span class="nx">blogLink</span> <span class="o">=</span> <span class="p">{</span>
                <span class="na">type</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Link</span><span class="dl">'</span><span class="p">,</span>
                <span class="na">contentType</span><span class="p">:</span> <span class="dl">'</span><span class="s1">footer</span><span class="dl">'</span><span class="p">,</span>
                <span class="na">options</span><span class="p">:</span> <span class="p">{</span>
                    <span class="na">href</span><span class="p">:</span> <span class="dl">'</span><span class="s1">https://developer.okta.com/blog</span><span class="dl">'</span><span class="p">,</span>
                    <span class="na">label</span><span class="p">:</span> <span class="dl">'</span><span class="s1">Read our blog</span><span class="dl">'</span><span class="p">,</span>
                    <span class="na">dataSe</span><span class="p">:</span> <span class="dl">'</span><span class="s1">blogCustomLink</span><span class="dl">'</span>
                <span class="p">}</span>
            <span class="p">};</span>
            <span class="nx">formBag</span><span class="p">.</span><span class="nx">uischema</span><span class="p">.</span><span class="nx">elements</span><span class="p">.</span><span class="nx">push</span><span class="p">(</span><span class="nx">blogLink</span><span class="p">);</span>
        <span class="p">});</span>

    <span class="nt">&lt;/script&gt;</span>


<span class="nt">&lt;/body&gt;</span>

<span class="nt">&lt;/html&gt;</span>
</code></pre></div></div>

<p>You can also find the code in the <a href="https://github.com/oktadev/okta-js-siw-customization-example/tree/main/custom-signin-blog-post">GitHub repository for this blog post</a>. With these code changes, you can connect this with an app to see how it works end-to-end. You’ll need to update your Okta OpenID Connect (OIDC) application to work with the domain. In the Okta Admin Console, navigate to <strong>Applications</strong> &gt; <strong>Applications</strong> and find the Okta application for your custom app. Navigate to the <strong>Sign On</strong> tab. You’ll see a section for <strong>OpenID Connect ID Token</strong>. Select <strong>Edit</strong> and select <strong>Custom URL</strong> for your brand’s sign-in URL as the <strong>Issuer</strong> value.</p>

<p>You’ll use the issuer value, which matches your brand’s custom URL, and the Okta application’s client ID in your custom app’s OIDC configuration. If you want to try this and don’t have a pre-built app, you can use one of our samples, such as the <a href="https://github.com/okta-samples/okta-react-sample">Okta React sample</a>.</p>

<h2 id="customize-your-gen3-okta-hosted-sign-in-widget">Customize your Gen3 Okta-hosted Sign-In Widget</h2>

<p>I hope you enjoyed customizing the sign-in experience for your brand. Using the Okta-hosted Sign-In widget is the best, most secure way to add identity security to your sites. With all the configuration options available, you can have a highly customized sign-in experience with a custom domain without anyone knowing you’re using Okta.</p>

<p>If you like this post, there’s a good chance you’ll find these links helpful:</p>
<ul>
  <li><a href="/blog/2025/07/22/react-pwa">Create a React PWA with Social Login Authentication</a></li>
  <li><a href="https://developer.okta.com/docs/journeys/OCI-secure-your-first-web-app/main/">Secure your first web app</a></li>
  <li><a href="/blog/2025/08/20/ios-mfa">How to Build a Secure iOS App with MFA</a></li>
</ul>

<p>Remember to follow us on <a href="https://twitter.com/oktadev">Twitter</a> and subscribe to our <a href="https://www.youtube.com/c/OktaDev/">YouTube</a> channel for fun and educational content. We also want to hear from you about topics you want to see and questions you may have. Leave us a comment below! Until next time!</p>
