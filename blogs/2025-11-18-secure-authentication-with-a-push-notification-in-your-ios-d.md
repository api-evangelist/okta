---
title: "Secure Authentication with a Push Notification in Your iOS Device"
url: "https://developer.okta.com/blog/2025/11/18/okta-ios-directauth"
date: "Tue, 18 Nov 2025 00:00:00 -0500"
author: ""
feed_url: "https://developer.okta.com/feed.xml"
---
<p>Building secure and seamless sign-in experiences is a core challenge for today’s iOS developers. Users expect authentication that feels instant, yet protects them with strong safeguards like multi-factor authentication (MFA). With Okta’s DirectAuth and push notification support, you can achieve both – delivering native, phishing-resistant MFA flows without ever leaving your app.</p>

<p>In this post, we’ll walk you through how to:</p>
<ol>
  <li>Set up your Okta developer account</li>
  <li>Configure your Okta org for DirectAuth and push notification factor</li>
  <li>Enable your iOS app to drive DirectAuth flows natively</li>
  <li>Create an AuthService with the support of DirectAuth</li>
  <li>Build a fully working SwiftUI demo leveraging the AuthService</li>
</ol>

<p>Note: This guide assumes you’re comfortable developing in Xcode using Swift and have basic familiarity with Okta’s identity flows.</p>

<p>If you want to skip the tutorial and run the project, you can <a href="https://github.com/oktadev/okta-ios-swift-directauth-example">follow the instructions in the project’s README</a>.</p>

<p><strong class="hide">Table of Contents</strong></p>
<ul id="markdown-toc">
  <li><a href="#use-okta-directauth-with-push-notification-factor" id="markdown-toc-use-okta-directauth-with-push-notification-factor">Use Okta DirectAuth with push notification factor</a></li>
  <li><a href="#prefer-phishing-resistant-authentication-factors" id="markdown-toc-prefer-phishing-resistant-authentication-factors">Prefer phishing-resistant authentication factors</a></li>
  <li><a href="#set-up-your-ios-project-with-oktas-mobile-sdks" id="markdown-toc-set-up-your-ios-project-with-oktas-mobile-sdks">Set up your iOS project with Okta’s mobile SDKs</a></li>
  <li><a href="#authenticate-your-ios-app-using-okta-directauth" id="markdown-toc-authenticate-your-ios-app-using-okta-directauth">Authenticate your iOS app using Okta DirectAuth</a></li>
  <li><a href="#add-the-oidc-configuration-to-your-ios-app" id="markdown-toc-add-the-oidc-configuration-to-your-ios-app">Add the OIDC configuration to your iOS app</a></li>
  <li><a href="#add-authentication-in-your-ios-app-without-a-browser-redirect-using-okta-directauth" id="markdown-toc-add-authentication-in-your-ios-app-without-a-browser-redirect-using-okta-directauth">Add authentication in your iOS app without a browser redirect using Okta DirectAuth</a>    <ul>
      <li><a href="#secure-native-sign-in-in-ios" id="markdown-toc-secure-native-sign-in-in-ios">Secure, native sign-in in iOS</a></li>
      <li><a href="#sign-out-users-when-using-directauth" id="markdown-toc-sign-out-users-when-using-directauth">Sign-out users when using DirectAuth</a></li>
      <li><a href="#refresh-access-tokens-securely" id="markdown-toc-refresh-access-tokens-securely">Refresh access tokens securely</a></li>
    </ul>
  </li>
  <li><a href="#display-the-authenticated-users-information" id="markdown-toc-display-the-authenticated-users-information">Display the authenticated user’s information</a></li>
  <li><a href="#build-the-swiftui-views-to-display-authenticated-state" id="markdown-toc-build-the-swiftui-views-to-display-authenticated-state">Build the SwiftUI views to display authenticated state</a>    <ul>
      <li><a href="#read-id-token-info" id="markdown-toc-read-id-token-info">Read ID token info</a></li>
    </ul>
  </li>
  <li><a href="#view-the-authenticated-users-profile-info" id="markdown-toc-view-the-authenticated-users-profile-info">View the authenticated user’s profile info</a>    <ul>
      <li><a href="#keeping-tokens-refreshed-and-maintaining-user-sessions" id="markdown-toc-keeping-tokens-refreshed-and-maintaining-user-sessions">Keeping tokens refreshed and maintaining user sessions</a></li>
    </ul>
  </li>
  <li><a href="#build-your-own-secure-native-sign-in-ios-app" id="markdown-toc-build-your-own-secure-native-sign-in-ios-app">Build your own secure native sign-in iOS app</a></li>
</ul>

<h2 id="use-okta-directauth-with-push-notification-factor">Use Okta DirectAuth with push notification factor</h2>

<p>The first step in implementing Direct Authentication with push-based MFA is setting up your Okta org and enabling the Push Notification factor. DirectAuth allows your app to handle authentication entirely within its own native UI – no browser redirection required – while still leveraging Okta’s secure OAuth 2.0 and OpenID Connect (OIDC) standards under the hood.</p>

<p>This means your app can seamlessly verify credentials, obtain tokens, and trigger a push notification challenge without switching contexts or relying on the <code class="language-plaintext highlighter-rouge">SafariViewController</code>.</p>

<p>Before you begin, you’ll need an Okta Integrator Free Plan account. To get one, sign up for an <a href="https://developer.okta.com/login">Integrator account</a>. Once you have an account, sign in to your <a href="https://developer.okta.com/login">Integrator account</a>. Next, in the Admin Console:</p>

<ol>
  <li>Go to <strong>Applications</strong> &gt; <strong>Applications</strong></li>
  <li>Select <strong>Create App Integration</strong></li>
  <li>Select <strong>OIDC - OpenID Connect</strong> as the sign-in method</li>
  <li>Select <strong>Native Application</strong> as the application type, then select <strong>Next</strong></li>
  <li>Enter an app integration name</li>
  <li>Configure the redirect URIs:
    <ul>
      <li><strong>Redirect URI</strong>: <code class="language-plaintext highlighter-rouge">com.okta.{yourOktaDomain}:/callback</code></li>
      <li><strong>Post Logout Redirect URI</strong>: <code class="language-plaintext highlighter-rouge">com.okta.{yourOktaDomain}:/</code> (where <code class="language-plaintext highlighter-rouge">{yourOktaDomain}.okta.com</code> is your Okta domain name). Your domain name is reversed to provide a unique scheme to open your app on a device.</li>
    </ul>
  </li>
  <li>Select <strong>Advanced v</strong>.
    <ul>
      <li>Select the <strong>OOB</strong> and <strong>MFA OOB</strong> grant types.</li>
    </ul>
  </li>
  <li>In the <strong>Controlled access</strong> section, select the appropriate access level</li>
  <li>Select <strong>Save</strong></li>
</ol>

<p>NOTE: When using a custom authorization server, you need to set up authorization policies. Complete these additional steps:</p>

<ol>
  <li>In the Admin Console, go to <strong>Security</strong> &gt; <strong>API</strong> &gt; <strong>Authorization Servers</strong></li>
  <li>Select your custom authorization server (<code class="language-plaintext highlighter-rouge">default</code>)</li>
  <li>On the Access Policies tab, ensure you have at least one policy:
    <ul>
      <li>If no policies exist, select <strong>Add New Access Policy</strong></li>
      <li>Give it a name like “Default Policy”</li>
      <li>Set <strong>Assign</strong> to “All clients”</li>
      <li>Click <strong>Create Policy</strong></li>
    </ul>
  </li>
  <li>For your policy, ensure you have at least one rule:
    <ul>
      <li>Select <strong>Add Rule</strong> if no rules exist</li>
      <li>Give it a name like “Default Rule”</li>
      <li>Set <strong>Grant type</strong> is to “Authorization Code”</li>
      <li>Select <strong>Advanced</strong> and enable “MFA OOB”</li>
      <li>Set <strong>User is</strong> to “Any user assigned the app”</li>
      <li>Set <strong>Scopes requested</strong> to “Any scopes”</li>
      <li>Select <strong>Create Rule</strong></li>
    </ul>
  </li>
</ol>

<p>For more details, see the <a href="https://developer.okta.com/docs/concepts/auth-servers/#custom-authorization-server">Custom Authorization Server</a> documentation.</p>

<details>
   Where are my new app's credentials?
  <div>
    <p>Creating an OIDC Native App manually in the Admin Console configures your Okta Org with the application settings.</p>

    <p>After creating the app, you can find the configuration details on the app’s <strong>General</strong> tab:</p>
    <ul>
      <li><strong>Client ID</strong>: Found in the <strong>Client Credentials</strong> section</li>
      <li><strong>Issuer</strong>: Found in the <strong>Issuer URI</strong> field for the authorization server that appears by selecting <strong>Security</strong> &gt; <strong>API</strong> from the navigation pane.</li>
    </ul>

    <div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>  Issuer:    https://dev-133337.okta.com/oauth2/default
  Client ID: 0oab8eb55Kb9jdMIr5d6
</code></pre></div>    </div>

    <p><strong>NOTE</strong>: You can also use the <a href="https://github.com/okta/okta-cli-client">Okta CLI Client</a> or <a href="https://github.com/okta/okta-powershell-cli">Okta PowerShell Module</a> to automate this process. See this guide for more information about setting up your app.</p>
  </div>
</details>

<h2 id="prefer-phishing-resistant-authentication-factors">Prefer phishing-resistant authentication factors</h2>

<p>When implementing DirectAuth with push notifications, security remains your top priority. Every new Okta Integrator Free Plan account requires admins to configure multi-factor authentication (MFA) using Okta Verify by default. We’ll keep these default settings for this tutorial, as they already support Okta Verify Push, the recommended factor for a native and secure authentication experience.</p>

<p>Push notifications through Okta Verify provide strong, phishing-resistant protection by requiring the user to approve sign-in attempts directly from a trusted device. Combined with biometric verification (Face ID or Touch ID) or device PIN enforcement, Okta Verify Push ensures that only the legitimate user can complete the authentication flow – even if credentials are compromised.</p>

<p>By default, push factor isn’t enabled in the Integrator Free org. Let’s enable it now.</p>

<p>Navigate to <strong>Security</strong> &gt; <strong>Authenticators</strong>. Find <strong>Okta Verify</strong> and select <strong>Actions</strong> &gt; <strong>Edit</strong>. In the <strong>Okta Verify</strong> modal, find <strong>Verification options</strong> and select <strong>Push notification (Android and iOS only)</strong>. Select <strong>Save</strong>.</p>

<h2 id="set-up-your-ios-project-with-oktas-mobile-sdks">Set up your iOS project with Okta’s mobile SDKs</h2>

<p>Before integrating Okta DirectAuth and Push Notification MFA, make sure your development environment meets the following requirements:</p>

<ul>
  <li>Xcode 15.0 or later – This guide assumes you’re comfortable developing iOS apps in Swift using Xcode.</li>
  <li>Swift 5+ – All examples use modern Swift language features.</li>
  <li>Swift Package Manager (SPM) – Dependency manager handled through SPM, which is built into Xcode.</li>
</ul>

<p>Once your environment is ready, create a new iOS project in Xcode and prepare it for integration with Okta’s mobile libraries.</p>

<h2 id="authenticate-your-ios-app-using-okta-directauth">Authenticate your iOS app using Okta DirectAuth</h2>

<p>If you are starting from scratch, create a new iOS app:</p>
<ol>
  <li>Open Xcode</li>
  <li>Go to <strong>File</strong> &gt; <strong>New</strong> &gt; <strong>Project</strong></li>
  <li>Select <strong>iOS App</strong> and select <strong>Next</strong></li>
  <li>Enter the name of the project, such as “okta-mfa-direct-auth”</li>
  <li>Set the Interface to SwiftUI</li>
  <li>Select <strong>Next</strong> and save your project locally</li>
</ol>

<p>To integrate Okta’s Direct Authentication SDK into your iOS app, we’ll use Swift Package Manager (SPM) – the recommended and modern way to manage dependencies in Xcode.</p>

<p>Follow these steps:</p>

<ol>
  <li>Open your project in Xcode (or create a new one if needed)</li>
  <li>Go to <strong>File</strong> &gt; <strong>Add Package Dependencies</strong></li>
  <li>In the search field at the top-right, enter: <code class="language-plaintext highlighter-rouge">https://github.com/okta/okta-mobile-swift</code> and press <kbd>Return</kbd>. Xcode will automatically fetch the available packages.</li>
  <li>Select the <strong>latest version</strong> (recommended) or specify a compatible version with your setup</li>
  <li>When prompted to choose which products to add, ensure that you select your app target next to <strong>OktaDirectAuth</strong> and <strong>AuthFoundation</strong></li>
  <li>Select <strong>Add Package</strong></li>
</ol>

<p>These packages provide all the tools you need to implement native authentication flows using OAuth 2.0 and OpenID Connect (OIDC) with DirectAuth, including secure token handling and MFA challenge management – without relying on a browser session.</p>

<p>Once the integration is complete, you’ll see <strong>OktaMobileSwift</strong> and its dependencies listed under your project’s <strong>Package Dependencies</strong> section in Xcode.</p>

<h2 id="add-the-oidc-configuration-to-your-ios-app">Add the OIDC configuration to your iOS app</h2>

<p>The cleanest and most scalable way to manage configuration is to use a property list file for Okta stored in your app bundle.</p>

<p>Create the property list for your OIDC and app config by following these steps:</p>
<ol>
  <li>Right-click on the root folder of the project</li>
  <li>Select <strong>New File from Template</strong> (<strong>New File</strong> in legacy Xcode versions)</li>
  <li>Ensure you have iOS selected on the top picker</li>
  <li>Select <strong>Property List template</strong> and select <strong>Next</strong></li>
  <li>Name the template <code class="language-plaintext highlighter-rouge">Okta</code> and select Create to create an <code class="language-plaintext highlighter-rouge">Okta.plist</code> file</li>
</ol>

<p>You can edit the file in XML format by right-clicking and selecting <strong>Open As</strong> &gt; <strong>Source Code</strong>. Copy and paste the following code into the file.</p>

<div class="language-xml highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="cp">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="cp">&lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;</span>
<span class="nt">&lt;plist</span> <span class="na">version=</span><span class="s">"1.0"</span><span class="nt">&gt;</span>
<span class="nt">&lt;dict&gt;</span>
    <span class="nt">&lt;key&gt;</span>scopes<span class="nt">&lt;/key&gt;</span>
    <span class="nt">&lt;string&gt;</span>openid profile offline_access<span class="nt">&lt;/string&gt;</span>
    <span class="nt">&lt;key&gt;</span>redirectUri<span class="nt">&lt;/key&gt;</span>
    <span class="nt">&lt;string&gt;</span>com.okta.{yourOktaDomain}:/callback<span class="nt">&lt;/string&gt;</span>
    <span class="nt">&lt;key&gt;</span>clientId<span class="nt">&lt;/key&gt;</span>
    <span class="nt">&lt;string&gt;</span>{yourClientID}<span class="nt">&lt;/string&gt;</span>
    <span class="nt">&lt;key&gt;</span>issuer<span class="nt">&lt;/key&gt;</span>
    <span class="nt">&lt;string&gt;</span>{yourOktaDomain}/oauth2/default<span class="nt">&lt;/string&gt;</span>
    <span class="nt">&lt;key&gt;</span>logoutRedirectUri<span class="nt">&lt;/key&gt;</span>
    <span class="nt">&lt;string&gt;</span>com.okta.{yourOktaDomain}:/<span class="nt">&lt;/string&gt;</span>
<span class="nt">&lt;/dict&gt;</span>
<span class="nt">&lt;/plist&gt;</span>
</code></pre></div></div>

<p>Replace <code class="language-plaintext highlighter-rouge">{yourOktaDomain}</code> and <code class="language-plaintext highlighter-rouge">{yourClientID}</code> with the values from your Okta org.</p>

<p>If you use something like this in your code, you can directly access the <code class="language-plaintext highlighter-rouge">DirectAuth</code> shared instance, which is already initialized and ready to handle authentication requests.</p>

<h2 id="add-authentication-in-your-ios-app-without-a-browser-redirect-using-okta-directauth">Add authentication in your iOS app without a browser redirect using Okta DirectAuth</h2>

<p>Now that you’ve added the SDK and property list file, let’s implement the main authentication logic for your app.</p>

<p>We’ll build a dedicated service called <code class="language-plaintext highlighter-rouge">AuthService</code>, responsible for logging users in and out, refreshing tokens, and managing session state.</p>

<p>This service will rely on OktaDirectAuth for native authentication and <code class="language-plaintext highlighter-rouge">AuthFoundation</code> for secure token handling.</p>

<p>To set it up, create a new folder named <code class="language-plaintext highlighter-rouge">Auth</code> under your project’s folder structure, then add a new Swift file called <code class="language-plaintext highlighter-rouge">AuthService.swift</code>.</p>

<p>Here, you’ll define your authentication protocol and a concrete class that integrates directly with the Okta SDK – making it easy to use across your SwiftUI or UIKit views.</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">import</span> <span class="kt">AuthFoundation</span>
<span class="kd">import</span> <span class="kt">OktaDirectAuth</span>
<span class="kd">import</span> <span class="kt">Observation</span>
<span class="kd">import</span> <span class="kt">Foundation</span>

<span class="kd">protocol</span> <span class="kt">AuthServicing</span> <span class="p">{</span>
  <span class="c1">// The accessToken of the logged in user</span>
  <span class="k">var</span> <span class="nv">accessToken</span><span class="p">:</span> <span class="kt">String</span><span class="p">?</span> <span class="p">{</span> <span class="k">get</span> <span class="p">}</span>

  <span class="c1">// State for driving SwiftUI</span>
  <span class="k">var</span> <span class="nv">state</span><span class="p">:</span> <span class="kt">AuthService</span><span class="o">.</span><span class="kt">State</span> <span class="p">{</span> <span class="k">get</span> <span class="p">}</span>

  <span class="c1">// Sign in (Password + Okta Verify Push)</span>
  <span class="kd">func</span> <span class="nf">signIn</span><span class="p">(</span><span class="nv">username</span><span class="p">:</span> <span class="kt">String</span><span class="p">,</span> <span class="nv">password</span><span class="p">:</span> <span class="kt">String</span><span class="p">)</span> <span class="k">async</span> <span class="k">throws</span>

  <span class="c1">// Sign out &amp; revoke tokens</span>
  <span class="kd">func</span> <span class="nf">signOut</span><span class="p">()</span> <span class="k">async</span>

  <span class="c1">// Refresh access token if possible (returns updated token if refreshed)</span>
  <span class="kd">func</span> <span class="nf">refreshTokenIfNeeded</span><span class="p">()</span> <span class="k">async</span> <span class="k">throws</span>

  <span class="c1">// Getting the userInfo out of the Credential</span>
  <span class="kd">func</span> <span class="nf">userInfo</span><span class="p">()</span> <span class="k">async</span> <span class="k">throws</span> <span class="o">-&gt;</span> <span class="kt">UserInfo</span><span class="p">?</span>
<span class="p">}</span>
</code></pre></div></div>

<p>With this added, you will get an error that <code class="language-plaintext highlighter-rouge">AuthService</code> can’t be found. That’s because we haven’t created the class yet. Below this code, add the following declarations of the <code class="language-plaintext highlighter-rouge">AuthService</code> class:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">@Observable</span>
<span class="kd">final</span> <span class="kd">class</span> <span class="kt">AuthService</span><span class="p">:</span> <span class="kt">AuthServicing</span> <span class="p">{</span>

<span class="p">}</span>
</code></pre></div></div>

<p>After doing so, we next need to confirm the <code class="language-plaintext highlighter-rouge">AuthService</code> class to the <code class="language-plaintext highlighter-rouge">AuthServicing</code> protocol and also create the <code class="language-plaintext highlighter-rouge">State</code> enum, which will hold all the states of our Authentication process.</p>

<p>To do that, first let’s create the <code class="language-plaintext highlighter-rouge">State</code> enum inside the <code class="language-plaintext highlighter-rouge">AuthService</code> class like this:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">@Observable</span>
<span class="kd">final</span> <span class="kd">class</span> <span class="kt">AuthService</span><span class="p">:</span> <span class="kt">AuthServicing</span> <span class="p">{</span>
  <span class="kd">enum</span> <span class="kt">State</span><span class="p">:</span> <span class="kt">Equatable</span> <span class="p">{</span>
    <span class="k">case</span> <span class="n">idle</span>
    <span class="k">case</span> <span class="n">authenticating</span>
    <span class="k">case</span> <span class="n">waitingForPush</span>
    <span class="k">case</span> <span class="nf">authorized</span><span class="p">(</span><span class="kt">Token</span><span class="p">)</span>
    <span class="k">case</span> <span class="nf">failed</span><span class="p">(</span><span class="nv">errorMessage</span><span class="p">:</span> <span class="kt">String</span><span class="p">)</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>The new code resolved the two errors about the <code class="language-plaintext highlighter-rouge">AuthService</code> and the <code class="language-plaintext highlighter-rouge">State</code> enum. We only have one error to fix, which is confirming the class to the protocol.</p>

<p>We will start implementing the functions top to bottom. Let’s first add the two variables from the protocol, accessToken and state. After the definition of the enum, we will add the properties:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">@Observable</span>
<span class="kd">final</span> <span class="kd">class</span> <span class="kt">AuthService</span><span class="p">:</span> <span class="kt">AuthServicing</span> <span class="p">{</span>
  <span class="kd">enum</span> <span class="kt">State</span><span class="p">:</span> <span class="kt">Equatable</span> <span class="p">{</span>
    <span class="k">case</span> <span class="n">idle</span>
    <span class="k">case</span> <span class="n">authenticating</span>
    <span class="k">case</span> <span class="n">waitingForPush</span>
    <span class="k">case</span> <span class="nf">authorized</span><span class="p">(</span><span class="kt">Token</span><span class="p">)</span>
    <span class="k">case</span> <span class="nf">failed</span><span class="p">(</span><span class="nv">errorMessage</span><span class="p">:</span> <span class="kt">String</span><span class="p">)</span>
  <span class="p">}</span>

  <span class="kd">private(set)</span> <span class="k">var</span> <span class="nv">state</span><span class="p">:</span> <span class="kt">State</span> <span class="o">=</span> <span class="o">.</span><span class="n">idle</span>

  <span class="k">var</span> <span class="nv">accessToken</span><span class="p">:</span> <span class="kt">String</span><span class="p">?</span> <span class="p">{</span>
    <span class="k">return</span> <span class="kc">nil</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>For now, we will leave the <code class="language-plaintext highlighter-rouge">accessToken</code> getter with a return value of <code class="language-plaintext highlighter-rouge">nil</code>, as we are not using the token yet. We’ll add the implementation later.</p>

<p>Next, we’ll add a private property to hold a reference to the <code class="language-plaintext highlighter-rouge">DirectAuthenticationFlow</code> instance.</p>

<p>This object manages the entire DirectAuth process, including credential verification, MFA challenges, and token issuance. The object must persist across authentication steps.</p>

<p>Insert the following variable between the existing <code class="language-plaintext highlighter-rouge">state</code> and <code class="language-plaintext highlighter-rouge">accessToken</code> properties:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">private(set)</span> <span class="k">var</span> <span class="nv">state</span><span class="p">:</span> <span class="kt">State</span> <span class="o">=</span> <span class="o">.</span><span class="n">idle</span>
<span class="kd">@ObservationIgnored</span> <span class="kd">private</span> <span class="k">let</span> <span class="nv">flow</span><span class="p">:</span> <span class="kt">DirectAuthenticationFlow</span><span class="p">?</span>

<span class="k">var</span> <span class="nv">accessToken</span><span class="p">:</span> <span class="kt">String</span><span class="p">?</span> <span class="p">{</span>
  <span class="k">return</span> <span class="kc">nil</span>
<span class="p">}</span>
</code></pre></div></div>

<p>To allocate the flow variable, we will need to implement an initializer for the <code class="language-plaintext highlighter-rouge">AuthService</code> class. Inside, we’ll allocate the flow using the <code class="language-plaintext highlighter-rouge">PropertyListConfiguration</code> that we introduced earlier. Just after the <code class="language-plaintext highlighter-rouge">accessToken</code> getter, add the following function:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// MARK: Init</span>

<span class="nf">init</span><span class="p">()</span> <span class="p">{</span>
  <span class="c1">// Prefer PropertyListConfiguration if Okta.plist exists; otherwise fall back</span>
  <span class="k">if</span> <span class="k">let</span> <span class="nv">configuration</span> <span class="o">=</span> <span class="k">try</span><span class="p">?</span> <span class="kt">OAuth2Client</span><span class="o">.</span><span class="kt">PropertyListConfiguration</span><span class="p">()</span> <span class="p">{</span>
      <span class="k">self</span><span class="o">.</span><span class="n">flow</span> <span class="o">=</span> <span class="k">try</span><span class="p">?</span> <span class="kt">DirectAuthenticationFlow</span><span class="p">(</span><span class="nv">client</span><span class="p">:</span> <span class="kt">OAuth2Client</span><span class="p">(</span><span class="n">configuration</span><span class="p">))</span>
  <span class="p">}</span> <span class="k">else</span> <span class="p">{</span>
      <span class="k">self</span><span class="o">.</span><span class="n">flow</span> <span class="o">=</span> <span class="k">try</span><span class="p">?</span> <span class="kt">DirectAuthenticationFlow</span><span class="p">()</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>This will try to fetch the Okta.plist file from the project’s folder, and if not found, will fall back to the default initializer of <code class="language-plaintext highlighter-rouge">the DirectAuthenticationFlow</code>. We have now successfully allocated the <code class="language-plaintext highlighter-rouge">DirectAuthenticationFlow</code>, and we can proceed with implementing the next functions of the protocol.</p>

<p>Moving down to the first function in the protocol, which is the <code class="language-plaintext highlighter-rouge">signIn(username: String, password: String)</code>.</p>

<p>The <code class="language-plaintext highlighter-rouge">signIn</code> method below performs the full authentication flow using Okta DirectAuth and Auth Foundation.
It authenticates a user with their username and password, handles MFA challenges (in this case, Okta Verify Push), and securely stores the resulting token for future API calls. Add the following code just under the Init that we just added.</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// MARK: AuthServicing</span>
<span class="kd">func</span> <span class="nf">signIn</span><span class="p">(</span><span class="nv">username</span><span class="p">:</span> <span class="kt">String</span><span class="p">,</span> <span class="nv">password</span><span class="p">:</span> <span class="kt">String</span><span class="p">)</span> <span class="p">{</span>
  <span class="kt">Task</span> <span class="p">{</span> <span class="kd">@MainActor</span> <span class="k">in</span>
    <span class="c1">// 1️⃣ Start the Sign-In Process</span>
    <span class="c1">// Update UI state and begin the DirectAuth flow with username/password.</span>
    <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="n">authenticating</span>
    <span class="k">do</span> <span class="p">{</span>
      <span class="k">let</span> <span class="nv">result</span> <span class="o">=</span> <span class="k">try</span> <span class="k">await</span> <span class="n">flow</span><span class="p">?</span><span class="o">.</span><span class="nf">start</span><span class="p">(</span><span class="n">username</span><span class="p">,</span> <span class="nv">with</span><span class="p">:</span> <span class="o">.</span><span class="nf">password</span><span class="p">(</span><span class="n">password</span><span class="p">))</span>

      <span class="k">switch</span> <span class="n">result</span> <span class="p">{</span>
        <span class="c1">// 2️⃣ Handle Successful Authentication</span>
        <span class="c1">// Okta validated credentials, return access/refresh/ID tokens.</span>
      <span class="k">case</span> <span class="o">.</span><span class="nf">success</span><span class="p">(</span><span class="k">let</span> <span class="nv">token</span><span class="p">):</span>
        <span class="k">let</span> <span class="nv">newCred</span> <span class="o">=</span> <span class="k">try</span> <span class="kt">Credential</span><span class="o">.</span><span class="nf">store</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
        <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="o">=</span> <span class="n">newCred</span>
        <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="nf">authorized</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>

        <span class="c1">// 3️⃣ Handle MFA with Push Notification</span>
        <span class="c1">// Okta requires MFA, wait for push approval via Okta Verify.</span>
      <span class="k">case</span> <span class="o">.</span><span class="nv">mfaRequired</span><span class="p">:</span>
        <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="n">waitingForPush</span>
        <span class="k">let</span> <span class="nv">status</span> <span class="o">=</span> <span class="k">try</span> <span class="k">await</span> <span class="n">flow</span><span class="p">?</span><span class="o">.</span><span class="nf">resume</span><span class="p">(</span><span class="nv">with</span><span class="p">:</span> <span class="o">.</span><span class="nf">oob</span><span class="p">(</span><span class="nv">channel</span><span class="p">:</span> <span class="o">.</span><span class="n">push</span><span class="p">))</span>
        <span class="k">if</span> <span class="k">case</span> <span class="kd">let</span> <span class="o">.</span><span class="nf">success</span><span class="p">(</span><span class="n">token</span><span class="p">)</span> <span class="o">=</span> <span class="n">status</span> <span class="p">{</span>
          <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="o">=</span> <span class="k">try</span> <span class="kt">Credential</span><span class="o">.</span><span class="nf">store</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
          <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="nf">authorized</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
        <span class="p">}</span>
      <span class="k">default</span><span class="p">:</span>
        <span class="k">break</span>
      <span class="p">}</span>
    <span class="p">}</span> <span class="k">catch</span> <span class="p">{</span>
      <span class="c1">// 4️⃣ Handle Errors Gracefully</span>
      <span class="c1">// Update state with a descriptive error message for the UI.</span>
      <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="nf">failed</span><span class="p">(</span><span class="nv">errorMessage</span><span class="p">:</span> <span class="n">error</span><span class="o">.</span><span class="n">localizedDescription</span><span class="p">)</span>
    <span class="p">}</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Let’s break down what’s happening step by step:</p>

<p><strong>1. Start the sign-in process</strong></p>

<p>When the function is called, it launches a new asynchronous Task and sets the UI state to .authenticating.
It then initiates the DirectAuth flow using the provided username and password:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">let</span> <span class="nv">result</span> <span class="o">=</span> <span class="k">try</span> <span class="k">await</span> <span class="n">flow</span><span class="p">?</span><span class="o">.</span><span class="nf">start</span><span class="p">(</span><span class="n">username</span><span class="p">,</span> <span class="nv">with</span><span class="p">:</span> <span class="o">.</span><span class="nf">password</span><span class="p">(</span><span class="n">password</span><span class="p">))</span>
</code></pre></div></div>

<p>This sends the user’s credentials to Okta’s Direct Authentication API and waits for a response.</p>

<p><strong>2. Handle successful authentication</strong></p>

<p>If Okta validates the credentials and no additional verification is needed, the result will be <code class="language-plaintext highlighter-rouge">.success(token)</code>.</p>

<p>The returned Token object contains access, refresh, and ID tokens.</p>

<p>We securely persist the credentials using AuthFoundation:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">let</span> <span class="nv">newCred</span> <span class="o">=</span> <span class="k">try</span> <span class="kt">Credential</span><span class="o">.</span><span class="nf">store</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
<span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="o">=</span> <span class="n">newCred</span>
<span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="nf">authorized</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
</code></pre></div></div>

<p>This marks the user as authenticated and updates the app state, allowing your UI to transition to the signed-in experience.</p>

<p><strong>3. Handle MFA with push notification</strong></p>

<p>If Okta determines that an MFA challenge is required, the result will be .mfaRequired.
The app updates its state to .waitingForPush, prompting the user to approve the login on their Okta Verify app:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="n">waitingForPush</span>
<span class="k">let</span> <span class="nv">status</span> <span class="o">=</span> <span class="k">try</span> <span class="k">await</span> <span class="n">flow</span><span class="p">?</span><span class="o">.</span><span class="nf">resume</span><span class="p">(</span><span class="nv">with</span><span class="p">:</span> <span class="o">.</span><span class="nf">oob</span><span class="p">(</span><span class="nv">channel</span><span class="p">:</span> <span class="o">.</span><span class="n">push</span><span class="p">))</span>
</code></pre></div></div>

<p>The <code class="language-plaintext highlighter-rouge">.oob(channel: .push)</code> parameter resumes the authentication flow by waiting for the push approval event from Okta Verify.</p>

<p>Once the user approves, Okta returns a new token:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">if</span> <span class="k">case</span> <span class="kd">let</span> <span class="o">.</span><span class="nf">success</span><span class="p">(</span><span class="n">token</span><span class="p">)</span> <span class="o">=</span> <span class="n">status</span> <span class="p">{</span>
    <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="o">=</span> <span class="k">try</span> <span class="kt">Credential</span><span class="o">.</span><span class="nf">store</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
    <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="nf">authorized</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
<span class="p">}</span>
</code></pre></div></div>

<p><strong>4. Handle errors</strong></p>

<p>If any step fails (e.g., invalid credentials, network issues, or push timeout), the catch block updates the UI to show an error message:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="nf">failed</span><span class="p">(</span><span class="nv">errorMessage</span><span class="p">:</span> <span class="n">error</span><span class="o">.</span><span class="n">localizedDescription</span><span class="p">)</span>
</code></pre></div></div>

<p>The error function allows your app to display user-friendly error states while preserving robust error handling for debugging.</p>

<h3 id="secure-native-sign-in-in-ios">Secure, native sign-in in iOS</h3>

<p>This function demonstrates a complete native sign-in experience with Okta DirectAuth, no web views, no redirects.</p>

<p>It authenticates the user, manages token storage securely, and handles push-based MFA all within your app’s Swift layer – making the authentication flow fast, secure, and frictionless.</p>

<p>The following diagram illustrates how the authentication flow works under the hood when using Okta DirectAuth with push notification authentication factor:</p>

<p><img alt="Flowchart showing the sequence of steps for authentication flow" src="/assets-jekyll/blog/okta-ios-directauth/diagram-25e524254ab609c95e5c606597336aec6c1d0f8cdb02af6cd085b813d2ab9356.svg" width="800" /></p>

<h3 id="sign-out-users-when-using-directauth">Sign-out users when using DirectAuth</h3>

<p>Next from the protocol functions is the sign-out method. This method provides a clean and secure way to log the user out of the app.</p>

<p>It revokes the user’s active tokens from Okta and resets the local authentication state, ensuring that no stale credentials remain on the device. Add the following code right below the <code class="language-plaintext highlighter-rouge">signIn</code> method:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">func</span> <span class="nf">signOut</span><span class="p">()</span> <span class="k">async</span> <span class="p">{</span>
  <span class="k">if</span> <span class="k">let</span> <span class="nv">credential</span> <span class="o">=</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="p">{</span>
    <span class="k">try</span><span class="p">?</span> <span class="k">await</span> <span class="n">credential</span><span class="o">.</span><span class="nf">revoke</span><span class="p">()</span>
  <span class="p">}</span>
  <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="o">=</span> <span class="kc">nil</span>
  <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="n">idle</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Let’s look at what each step does:
<strong>1. Check for an existing credential</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">if</span> <span class="k">let</span> <span class="nv">credential</span> <span class="o">=</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="p">{</span>
</code></pre></div></div>

<p>The method first checks if a stored credential (token) exists in memory.
<code class="language-plaintext highlighter-rouge">Credential.default</code> represents the current authenticated session created earlier during sign-in.</p>

<p><strong>2. Revoke the tokens from Okta</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">try</span><span class="p">?</span> <span class="k">await</span> <span class="n">credential</span><span class="o">.</span><span class="nf">revoke</span><span class="p">()</span>
</code></pre></div></div>

<p>This line tells Okta to invalidate the access and refresh tokens associated with that credential.
Calling <code class="language-plaintext highlighter-rouge">revoke()</code> ensures that the user’s session terminates locally and in the authorization server, preventing further API access with those tokens.</p>

<p>The <code class="language-plaintext highlighter-rouge">try?</code> operator is used to safely ignore any errors (e.g., network failure during logout), since token revocation is a best-effort operation.</p>

<p><strong>3. Clear local credential data</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="o">=</span> <span class="kc">nil</span>
</code></pre></div></div>

<p>After revoking the tokens, the app clears the local credential object.</p>

<p>This removes any sensitive authentication data from memory, ensuring that no valid tokens remain on the device.</p>

<p><strong>4. Reset the authentication state</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="n">idle</span>
</code></pre></div></div>

<p>Finally, the app updates its internal state back to <code class="language-plaintext highlighter-rouge">.idle</code>, which tells the UI that the user is now logged out and ready to start a new session.</p>

<p>You can use this state to trigger a transition back to the login screen or turn off authenticated features.</p>

<p>The protocol confirmation is almost complete, and we only have two functions remaining to implement.</p>

<h3 id="refresh-access-tokens-securely">Refresh access tokens securely</h3>

<p>Access tokens issued by Okta have a limited lifetime to reduce the risk of misuse if compromised. OAuth clients that can’t maintain secrets, like mobile apps, require short access token lifetimes for security.</p>

<p>To maintain a seamless user experience, your app should refresh tokens automatically before they expire.
The <code class="language-plaintext highlighter-rouge">refreshTokenIfNeeded()</code> method handles this process securely using <code class="language-plaintext highlighter-rouge">AuthFoundation</code>’s built-in token management APIs.</p>

<p>Let’s walk through what it does. Add the following code right after the <code class="language-plaintext highlighter-rouge">signOut</code> method:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">func</span> <span class="nf">refreshTokenIfNeeded</span><span class="p">()</span> <span class="k">async</span> <span class="k">throws</span> <span class="p">{</span>
  <span class="k">guard</span> <span class="k">let</span> <span class="nv">credential</span> <span class="o">=</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="k">else</span> <span class="p">{</span> <span class="k">return</span> <span class="p">}</span>
  <span class="k">try</span> <span class="k">await</span> <span class="n">credential</span><span class="o">.</span><span class="nf">refresh</span><span class="p">()</span>
<span class="p">}</span>
</code></pre></div></div>

<p><strong>1. Check for an existing credential</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">guard</span> <span class="k">let</span> <span class="nv">credential</span> <span class="o">=</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="k">else</span> <span class="p">{</span> <span class="k">return</span> <span class="p">}</span>
</code></pre></div></div>

<p>Before attempting a token refresh, the method checks whether a valid credential exists.
If no credential is stored (e.g., the user hasn’t signed in yet or has logged out), the method exits early.</p>

<p><strong>2. Refresh the token</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">try</span> <span class="k">await</span> <span class="n">credential</span><span class="o">.</span><span class="nf">refresh</span><span class="p">()</span>
</code></pre></div></div>

<p>This line tells Okta to exchange the refresh token for a new access token and ID token.</p>

<p>The <code class="language-plaintext highlighter-rouge">refresh()</code> method automatically updates the <code class="language-plaintext highlighter-rouge">Credential</code> object with the new tokens and securely persists them using <code class="language-plaintext highlighter-rouge">AuthFoundation</code>.</p>

<p>If the refresh token has expired or is invalid, this call throws an error – allowing your app to detect the issue and prompt the user to sign in again.</p>

<h2 id="display-the-authenticated-users-information">Display the authenticated user’s information</h2>

<p>Lastly, let’s look at the <code class="language-plaintext highlighter-rouge">userInfo()</code> function. After authenticating, your app can access the user’s profile information – such as their name, email, or user ID – from Okta using a standard OIDC endpoint.</p>

<p>The <code class="language-plaintext highlighter-rouge">userInfo()</code> method retrieves this data from the ID token or by calling the authorization server’s <code class="language-plaintext highlighter-rouge">/userinfo</code> endpoint. The ID token doesn’t necessarily include all of the profile information though, as the ID token is intentionally lightweight.</p>

<p>Here’s how it works. Add the following code after the end of <code class="language-plaintext highlighter-rouge">refreshTokenIfNeeded()</code>:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">func</span> <span class="nf">userInfo</span><span class="p">()</span> <span class="k">async</span> <span class="k">throws</span> <span class="o">-&gt;</span> <span class="kt">UserInfo</span><span class="p">?</span> <span class="p">{</span>
  <span class="k">if</span> <span class="k">let</span> <span class="nv">userInfo</span> <span class="o">=</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="n">userInfo</span> <span class="p">{</span>
    <span class="k">return</span> <span class="n">userInfo</span>
  <span class="p">}</span> <span class="k">else</span> <span class="p">{</span>
    <span class="k">do</span> <span class="p">{</span>
      <span class="k">guard</span> <span class="k">let</span> <span class="nv">userInfo</span> <span class="o">=</span> <span class="k">try</span> <span class="k">await</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="nf">userInfo</span><span class="p">()</span> <span class="k">else</span> <span class="p">{</span>
        <span class="k">return</span> <span class="kc">nil</span>
      <span class="p">}</span>
      <span class="k">return</span> <span class="n">userInfo</span>
    <span class="p">}</span> <span class="k">catch</span> <span class="p">{</span>
      <span class="k">return</span> <span class="kc">nil</span>
    <span class="p">}</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p><strong>1. Return the cached user info</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">if</span> <span class="k">let</span> <span class="nv">userInfo</span> <span class="o">=</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="n">userInfo</span> <span class="p">{</span>
  <span class="k">return</span> <span class="n">userInfo</span>
<span class="p">}</span>
</code></pre></div></div>

<p>If the user’s profile information has already been fetched and stored in memory, the method returns it immediately.</p>

<p>This avoids unnecessary network calls, providing a fast and responsive experience.</p>

<p><strong>2. Fetch user info</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">guard</span> <span class="k">let</span> <span class="nv">userInfo</span> <span class="o">=</span> <span class="k">try</span> <span class="k">await</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="nf">userInfo</span><span class="p">()</span> <span class="k">else</span> <span class="p">{</span>
  <span class="k">return</span> <span class="kc">nil</span>
<span class="p">}</span>
</code></pre></div></div>

<p>If the cached data isn’t available, the method fetches it directly from Okta using the <code class="language-plaintext highlighter-rouge">UserInfo</code> endpoint.</p>

<p>This endpoint returns standard OpenID Connect claims such as:</p>

<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>sub (the user's unique ID)
name
email
preferred_username
etc...
</code></pre></div></div>

<p>The <code class="language-plaintext highlighter-rouge">AuthFoundation</code> SDK handles the request and parsing for you, returning a <code class="language-plaintext highlighter-rouge">UserInfo</code> object.</p>

<p><strong>3. Handle errors gracefully</strong></p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">catch</span> <span class="p">{</span>
  <span class="k">return</span> <span class="kc">nil</span>
<span class="p">}</span>
</code></pre></div></div>

<p>If the request fails (for example, due to a network issue or expired token), the function returns <code class="language-plaintext highlighter-rouge">nil</code>.
This prevents your app from crashing and allows you to handle the error by displaying a default user state or prompting re-authentication.</p>

<p>With this implemented, you’ve resolved all the errors and should be able to build the app. 🎉</p>

<h2 id="build-the-swiftui-views-to-display-authenticated-state">Build the SwiftUI views to display authenticated state</h2>

<p>Now that we’ve built the <code class="language-plaintext highlighter-rouge">AuthService</code> to handle sign-in, sign-out, token management, and user info retrieval, let’s see how to integrate it into your app’s UI.</p>

<p>To maintain consistency in your architecture, rename the default <code class="language-plaintext highlighter-rouge">ContentView</code> to <code class="language-plaintext highlighter-rouge">AuthView</code> and update all references accordingly.</p>

<p>This clarifies the purpose of the view – it will serve as the primary authentication interface.
Then, create a <code class="language-plaintext highlighter-rouge">Views</code> folder under your project’s folder, drag and drop the <code class="language-plaintext highlighter-rouge">AuthView</code> into the newly created folder, and create a new file named <code class="language-plaintext highlighter-rouge">AuthViewModel.swift</code> in the same folder.</p>

<p>The <code class="language-plaintext highlighter-rouge">AuthViewModel</code> will encapsulate all authentication-related state and actions, acting as the communication layer between your view and the underlying <code class="language-plaintext highlighter-rouge">AuthService</code>.</p>

<p>Add the following code in <code class="language-plaintext highlighter-rouge">AuthViewModel.swift</code>:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">import</span> <span class="kt">Foundation</span>
<span class="kd">import</span> <span class="kt">Observation</span>
<span class="kd">import</span> <span class="kt">AuthFoundation</span>

<span class="c1">/// The `AuthViewModel` acts as the bridge between your app's UI and the authentication layer (`AuthService`).</span>
<span class="c1">/// It coordinates user actions such as signing in, signing out, refreshing tokens, and fetching user profile data.</span>
<span class="c1">/// This class uses Swift's `@Observable` macro so that your SwiftUI views can automatically react to state changes.</span>
<span class="kd">@Observable</span>
<span class="kd">final</span> <span class="kd">class</span> <span class="kt">AuthViewModel</span> <span class="p">{</span>
  <span class="c1">// MARK: - Dependencies</span>

  <span class="c1">/// The authentication service responsible for handling DirectAuth sign-in,</span>
  <span class="c1">/// push-based MFA, token management, and user info retrieval.</span>
  <span class="kd">private</span> <span class="k">let</span> <span class="nv">authService</span><span class="p">:</span> <span class="kt">AuthServicing</span>

  <span class="c1">// MARK: - UI State Properties</span>

  <span class="c1">/// Stores the user's token, which can be used for secure communication</span>
  <span class="c1">/// with backend services that validate the user's identity.</span>
  <span class="k">var</span> <span class="nv">accessToken</span><span class="p">:</span> <span class="kt">String</span><span class="p">?</span>

  <span class="c1">/// Represents a loading statex. Set to `true` when background operations are running</span>
  <span class="c1">/// (such as sign-in, sign-out, or token refresh) to display a progress indicator.</span>
  <span class="k">var</span> <span class="nv">isLoading</span><span class="p">:</span> <span class="kt">Bool</span> <span class="o">=</span> <span class="kc">false</span>

  <span class="c1">/// Holds any human-readable error messages that should be displayed in the UI</span>
  <span class="c1">/// (for example, invalid credentials or network errors).</span>
  <span class="k">var</span> <span class="nv">errorMessage</span><span class="p">:</span> <span class="kt">String</span><span class="p">?</span>

  <span class="c1">/// The username and password properties are bound to text fields in the UI.</span>
  <span class="c1">/// As the user types, these values update automatically thanks to SwiftUI's reactive data binding.</span>
  <span class="c1">/// The view model then uses them to perform DirectAuth sign-in when the user submits the form.</span>
  <span class="k">var</span> <span class="nv">username</span><span class="p">:</span> <span class="kt">String</span> <span class="o">=</span> <span class="s">""</span>
  <span class="k">var</span> <span class="nv">password</span><span class="p">:</span> <span class="kt">String</span> <span class="o">=</span> <span class="s">""</span>

  <span class="c1">/// Exposes the current authentication state (idle, authenticating, waitingForPush, authorized, failed)</span>
  <span class="c1">/// as defined by the `AuthService.State` enum. The view can use this to display the correct UI.</span>
  <span class="k">var</span> <span class="nv">state</span><span class="p">:</span> <span class="kt">AuthService</span><span class="o">.</span><span class="kt">State</span> <span class="p">{</span>
    <span class="n">authService</span><span class="o">.</span><span class="n">state</span>
  <span class="p">}</span>

  <span class="c1">// MARK: - Initialization</span>

  <span class="c1">/// Initializes the view model with a default instance of `AuthService`.</span>
  <span class="c1">/// You can inject a mock `AuthServicing` implementation for testing.</span>
  <span class="nf">init</span><span class="p">(</span><span class="nv">authService</span><span class="p">:</span> <span class="kt">AuthServicing</span> <span class="o">=</span> <span class="kt">AuthService</span><span class="p">())</span> <span class="p">{</span>
    <span class="k">self</span><span class="o">.</span><span class="n">authService</span> <span class="o">=</span> <span class="n">authService</span>
  <span class="p">}</span>

  <span class="c1">// MARK: - Authentication Actions</span>

  <span class="c1">/// Attempts to authenticate the user with the provided credentials.</span>
  <span class="c1">/// This triggers the full DirectAuth flow -- including password verification,</span>
  <span class="c1">/// push notification MFA (if required), and secure token storage via AuthFoundation.</span>
  <span class="kd">@MainActor</span>
  <span class="kd">func</span> <span class="nf">signIn</span><span class="p">()</span> <span class="k">async</span> <span class="p">{</span>
    <span class="nf">setLoading</span><span class="p">(</span><span class="kc">true</span><span class="p">)</span>
    <span class="k">defer</span> <span class="p">{</span> <span class="nf">setLoading</span><span class="p">(</span><span class="kc">false</span><span class="p">)</span> <span class="p">}</span>

    <span class="k">do</span> <span class="p">{</span>
      <span class="k">try</span> <span class="k">await</span> <span class="n">authService</span><span class="o">.</span><span class="nf">signIn</span><span class="p">(</span><span class="nv">username</span><span class="p">:</span> <span class="n">username</span><span class="p">,</span> <span class="nv">password</span><span class="p">:</span> <span class="n">password</span><span class="p">)</span>
      <span class="n">accessToken</span> <span class="o">=</span> <span class="n">authService</span><span class="o">.</span><span class="n">accessToken</span>
    <span class="p">}</span> <span class="k">catch</span> <span class="p">{</span>
      <span class="n">errorMessage</span> <span class="o">=</span> <span class="n">error</span><span class="o">.</span><span class="n">localizedDescription</span>
    <span class="p">}</span>
  <span class="p">}</span>

  <span class="c1">/// Signs the user out by revoking active tokens, clearing local credentials,</span>
  <span class="c1">/// and resetting the app's authentication state.</span>
  <span class="kd">@MainActor</span>
  <span class="kd">func</span> <span class="nf">signOut</span><span class="p">()</span> <span class="k">async</span> <span class="p">{</span>
    <span class="nf">setLoading</span><span class="p">(</span><span class="kc">true</span><span class="p">)</span>
    <span class="k">defer</span> <span class="p">{</span> <span class="nf">setLoading</span><span class="p">(</span><span class="kc">false</span><span class="p">)</span> <span class="p">}</span>

    <span class="k">await</span> <span class="n">authService</span><span class="o">.</span><span class="nf">signOut</span><span class="p">()</span>
  <span class="p">}</span>

  <span class="c1">// MARK: - Token Handling</span>

  <span class="c1">/// Refreshes the user's access token using their refresh token.</span>
  <span class="c1">/// This allows the app to maintain a valid session without requiring</span>
  <span class="c1">/// the user to log in again after the access token expires.</span>
  <span class="kd">@MainActor</span>
  <span class="kd">func</span> <span class="nf">refreshToken</span><span class="p">()</span> <span class="k">async</span> <span class="p">{</span>
    <span class="nf">setLoading</span><span class="p">(</span><span class="kc">true</span><span class="p">)</span>
    <span class="k">defer</span> <span class="p">{</span> <span class="nf">setLoading</span><span class="p">(</span><span class="kc">false</span><span class="p">)</span> <span class="p">}</span>

    <span class="k">do</span> <span class="p">{</span>
      <span class="k">try</span> <span class="k">await</span> <span class="n">authService</span><span class="o">.</span><span class="nf">refreshTokenIfNeeded</span><span class="p">()</span>
      <span class="n">accessToken</span> <span class="o">=</span> <span class="n">authService</span><span class="o">.</span><span class="n">accessToken</span>
    <span class="p">}</span> <span class="k">catch</span> <span class="p">{</span>
      <span class="n">errorMessage</span> <span class="o">=</span> <span class="n">error</span><span class="o">.</span><span class="n">localizedDescription</span>
    <span class="p">}</span>
  <span class="p">}</span>

  <span class="c1">// MARK: - User Info Retrieval</span>

  <span class="c1">/// Fetches the authenticated user's profile information from Okta.</span>
  <span class="c1">/// Returns a `UserInfo` object containing standard OIDC claims (such as `name`, `email`, and `sub`).</span>
  <span class="c1">/// If fetching fails (e.g., due to expired tokens or network issues), it returns `nil`.</span>
  <span class="kd">@MainActor</span>
  <span class="kd">func</span> <span class="nf">fetchUserInfo</span><span class="p">()</span> <span class="k">async</span> <span class="o">-&gt;</span> <span class="kt">UserInfo</span><span class="p">?</span> <span class="p">{</span>
    <span class="k">do</span> <span class="p">{</span>
      <span class="k">let</span> <span class="nv">userInfo</span> <span class="o">=</span> <span class="k">try</span> <span class="k">await</span> <span class="n">authService</span><span class="o">.</span><span class="nf">userInfo</span><span class="p">()</span>
      <span class="k">return</span> <span class="n">userInfo</span>
    <span class="p">}</span> <span class="k">catch</span> <span class="p">{</span>
      <span class="n">errorMessage</span> <span class="o">=</span> <span class="n">error</span><span class="o">.</span><span class="n">localizedDescription</span>
      <span class="k">return</span> <span class="kc">nil</span>
    <span class="p">}</span>
  <span class="p">}</span>

  <span class="c1">// MARK: - UI Helpers</span>

  <span class="c1">/// Updates the `isLoading` property. This is used to show or hide</span>
  <span class="c1">/// a loading spinner in your SwiftUI view while background work is in progress.</span>
  <span class="kd">private</span> <span class="kd">func</span> <span class="nf">setLoading</span><span class="p">(</span><span class="n">_</span> <span class="nv">value</span><span class="p">:</span> <span class="kt">Bool</span><span class="p">)</span> <span class="p">{</span>
    <span class="n">isLoading</span> <span class="o">=</span> <span class="n">value</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>With the view model in place, the next step is to bind it to your SwiftUI view.
The <code class="language-plaintext highlighter-rouge">AuthView</code> will observe the <code class="language-plaintext highlighter-rouge">AuthViewModel</code>, updating automatically as the authentication state changes.</p>

<p>It will show the user’s ID token when authenticated and provide controls for signing in, signing out, and refreshing the token.</p>

<p>Open <code class="language-plaintext highlighter-rouge">AuthView.swift</code>, remove the existing template code, and insert the following implementation:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">import</span> <span class="kt">SwiftUI</span>
<span class="kd">import</span> <span class="kt">AuthFoundation</span>

<span class="c1">/// A simple wrapper for `UserInfo` used to present user profile data in a full-screen modal.</span>
<span class="c1">/// Conforms to `Identifiable` so it can be used with `.fullScreenCover(item:)`.</span>
<span class="kd">struct</span> <span class="kt">UserInfoModel</span><span class="p">:</span> <span class="kt">Identifiable</span> <span class="p">{</span>
  <span class="k">let</span> <span class="nv">id</span> <span class="o">=</span> <span class="kt">UUID</span><span class="p">()</span>
  <span class="k">let</span> <span class="nv">user</span><span class="p">:</span> <span class="kt">UserInfo</span>
<span class="p">}</span>

<span class="c1">/// The main SwiftUI view for managing the authentication experience.</span>
<span class="c1">/// This view observes the `AuthViewModel`, displays different UI states</span>
<span class="c1">/// based on the current authentication flow, and provides controls for</span>
<span class="c1">/// signing in, signing out, refreshing tokens, and viewing user or token information.</span>
<span class="kd">struct</span> <span class="kt">AuthView</span><span class="p">:</span> <span class="kt">View</span> <span class="p">{</span>

  <span class="c1">// MARK: - View Model</span>

  <span class="c1">/// The view model that manages all authentication logic and state transitions.</span>
  <span class="c1">/// It uses `@Observable` from Swift's Observation framework, so changes here</span>
  <span class="c1">/// automatically trigger UI updates.</span>
  <span class="kd">@State</span> <span class="kd">private</span> <span class="k">var</span> <span class="nv">viewModel</span> <span class="o">=</span> <span class="kt">AuthViewModel</span><span class="p">()</span>

  <span class="c1">// MARK: - State and Presentation</span>

  <span class="c1">/// Holds the currently fetched user information (if available).</span>
  <span class="c1">/// When this value is set, the `UserInfoView` is displayed as a full-screen sheet.</span>
  <span class="kd">@State</span> <span class="kd">private</span> <span class="k">var</span> <span class="nv">userInfo</span><span class="p">:</span> <span class="kt">UserInfoModel</span><span class="p">?</span>

  <span class="c1">/// Controls whether the Token Info screen is presented as a full-screen modal.</span>
  <span class="kd">@State</span> <span class="kd">private</span> <span class="k">var</span> <span class="nv">showTokenInfo</span> <span class="o">=</span> <span class="kc">false</span>

  <span class="c1">// MARK: - View Body</span>

  <span class="k">var</span> <span class="nv">body</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
    <span class="kt">VStack</span> <span class="p">{</span>
      <span class="c1">// Render the UI based on the current authentication state.</span>
      <span class="c1">// Each case corresponds to a different phase of the DirectAuth flow.</span>
      <span class="k">switch</span> <span class="n">viewModel</span><span class="o">.</span><span class="n">state</span> <span class="p">{</span>
      <span class="k">case</span> <span class="o">.</span><span class="n">idle</span><span class="p">,</span> <span class="o">.</span><span class="nv">failed</span><span class="p">:</span>
        <span class="n">loginForm</span>
      <span class="k">case</span> <span class="o">.</span><span class="nv">authenticating</span><span class="p">:</span>
        <span class="kt">ProgressView</span><span class="p">(</span><span class="s">"Signing in..."</span><span class="p">)</span>
      <span class="k">case</span> <span class="o">.</span><span class="nv">waitingForPush</span><span class="p">:</span>
        <span class="c1">// Waiting for Okta Verify push approval</span>
        <span class="kt">WaitingForPushView</span> <span class="p">{</span>
          <span class="kt">Task</span> <span class="p">{</span> <span class="k">await</span> <span class="n">viewModel</span><span class="o">.</span><span class="nf">signOut</span><span class="p">()</span> <span class="p">}</span>
        <span class="p">}</span>
      <span class="k">case</span> <span class="o">.</span><span class="nv">authorized</span><span class="p">:</span>
        <span class="n">successView</span>
      <span class="p">}</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
  <span class="p">}</span>
<span class="p">}</span>

<span class="c1">// MARK: - Login Form View</span>
<span class="kd">private</span> <span class="kd">extension</span> <span class="kt">AuthView</span> <span class="p">{</span>
  <span class="c1">/// The initial sign-in form displayed when the user is not authenticated.</span>
  <span class="c1">/// Captures username and password input and triggers the DirectAuth sign-in flow.</span>
  <span class="kd">private</span> <span class="k">var</span> <span class="nv">loginForm</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
    <span class="kt">VStack</span><span class="p">(</span><span class="nv">spacing</span><span class="p">:</span> <span class="mi">16</span><span class="p">)</span> <span class="p">{</span>
      <span class="kt">Text</span><span class="p">(</span><span class="s">"Okta DirectAuth (Password + Okta Verify Push)"</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">headline</span><span class="p">)</span>

      <span class="c1">// Email input field (bound to view model's username property)</span>
      <span class="kt">TextField</span><span class="p">(</span><span class="s">"Email"</span><span class="p">,</span> <span class="nv">text</span><span class="p">:</span> <span class="err">$</span><span class="n">viewModel</span><span class="o">.</span><span class="n">username</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">keyboardType</span><span class="p">(</span><span class="o">.</span><span class="n">emailAddress</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">textContentType</span><span class="p">(</span><span class="o">.</span><span class="n">username</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">textInputAutocapitalization</span><span class="p">(</span><span class="o">.</span><span class="n">never</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">autocorrectionDisabled</span><span class="p">()</span>

      <span class="c1">// Secure password input field</span>
      <span class="kt">SecureField</span><span class="p">(</span><span class="s">"Password"</span><span class="p">,</span> <span class="nv">text</span><span class="p">:</span> <span class="err">$</span><span class="n">viewModel</span><span class="o">.</span><span class="n">password</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">textContentType</span><span class="p">(</span><span class="o">.</span><span class="n">password</span><span class="p">)</span>

      <span class="c1">// Triggers authentication via DirectAuth and Push MFA</span>
      <span class="kt">Button</span><span class="p">(</span><span class="s">"Sign In"</span><span class="p">)</span> <span class="p">{</span>
        <span class="kt">Task</span> <span class="p">{</span> <span class="k">await</span> <span class="n">viewModel</span><span class="o">.</span><span class="nf">signIn</span><span class="p">()</span> <span class="p">}</span>
      <span class="p">}</span>
      <span class="o">.</span><span class="nf">buttonStyle</span><span class="p">(</span><span class="o">.</span><span class="n">borderedProminent</span><span class="p">)</span>
      <span class="o">.</span><span class="nf">disabled</span><span class="p">(</span><span class="n">viewModel</span><span class="o">.</span><span class="n">username</span><span class="o">.</span><span class="n">isEmpty</span> <span class="o">||</span> <span class="n">viewModel</span><span class="o">.</span><span class="n">password</span><span class="o">.</span><span class="n">isEmpty</span><span class="p">)</span>

      <span class="c1">// Display error message if sign-in fails</span>
      <span class="k">if</span> <span class="k">case</span> <span class="o">.</span><span class="nf">failed</span><span class="p">(</span><span class="k">let</span> <span class="nv">message</span><span class="p">)</span> <span class="o">=</span> <span class="n">viewModel</span><span class="o">.</span><span class="n">state</span> <span class="p">{</span>
        <span class="kt">Text</span><span class="p">(</span><span class="n">message</span><span class="p">)</span>
          <span class="o">.</span><span class="nf">foregroundColor</span><span class="p">(</span><span class="o">.</span><span class="n">red</span><span class="p">)</span>
          <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">footnote</span><span class="p">)</span>
      <span class="p">}</span>
    <span class="p">}</span>
  <span class="p">}</span>
<span class="p">}</span>

<span class="c1">// MARK: - Authorized State View</span>
<span class="kd">private</span> <span class="kd">extension</span> <span class="kt">AuthView</span> <span class="p">{</span>
  <span class="c1">/// Displayed once the user has successfully signed in and completed MFA.</span>
  <span class="c1">/// Shows the user's ID token and provides actions for token refresh, user info,</span>
  <span class="c1">/// token details, and sign-out.</span>
  <span class="kd">private</span> <span class="k">var</span> <span class="nv">successView</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
    <span class="kt">VStack</span><span class="p">(</span><span class="nv">spacing</span><span class="p">:</span> <span class="mi">16</span><span class="p">)</span> <span class="p">{</span>
      <span class="kt">Text</span><span class="p">(</span><span class="s">"Signed in 🎉"</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">title2</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">bold</span><span class="p">()</span>

      <span class="c1">// Scrollable ID token display (for demo purposes)</span>
      <span class="kt">ScrollView</span> <span class="p">{</span>
        <span class="kt">Text</span><span class="p">(</span><span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="n">token</span><span class="o">.</span><span class="n">idToken</span><span class="p">?</span><span class="o">.</span><span class="n">rawValue</span> <span class="p">??</span> <span class="s">"(no id token)"</span><span class="p">)</span>
          <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">footnote</span><span class="p">)</span>
          <span class="o">.</span><span class="nf">textSelection</span><span class="p">(</span><span class="o">.</span><span class="n">enabled</span><span class="p">)</span>
          <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
          <span class="o">.</span><span class="nf">background</span><span class="p">(</span><span class="o">.</span><span class="n">thinMaterial</span><span class="p">)</span>
          <span class="o">.</span><span class="nf">cornerRadius</span><span class="p">(</span><span class="mi">8</span><span class="p">)</span>
      <span class="p">}</span>
      <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">maxHeight</span><span class="p">:</span> <span class="mi">220</span><span class="p">)</span>

      <span class="c1">// Authenticated user actions</span>
      <span class="n">signoutButton</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
  <span class="p">}</span>
<span class="p">}</span>

<span class="c1">// MARK: - Action Buttons</span>
<span class="kd">private</span> <span class="kd">extension</span> <span class="kt">AuthView</span> <span class="p">{</span>
  <span class="c1">/// Signs the user out, revoking tokens and returning to the login form.</span>
  <span class="k">var</span> <span class="nv">signoutButton</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
    <span class="kt">Button</span><span class="p">(</span><span class="s">"Sign Out"</span><span class="p">)</span> <span class="p">{</span>
      <span class="kt">Task</span> <span class="p">{</span> <span class="k">await</span> <span class="n">viewModel</span><span class="o">.</span><span class="nf">signOut</span><span class="p">()</span> <span class="p">}</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="nf">system</span><span class="p">(</span><span class="nv">size</span><span class="p">:</span> <span class="mi">14</span><span class="p">))</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>With this added, you will receive an error stating that <code class="language-plaintext highlighter-rouge">WaitingForPushView</code> can’t be found in scope. To fix this, we need to add that view next. Add a new empty Swift file in the <code class="language-plaintext highlighter-rouge">Views</code> folder and name it <code class="language-plaintext highlighter-rouge">WaitingForPushView</code>. When complete, add the following implementation inside:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">import</span> <span class="kt">SwiftUI</span>

<span class="kd">struct</span> <span class="kt">WaitingForPushView</span><span class="p">:</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="k">let</span> <span class="nv">onCancel</span><span class="p">:</span> <span class="p">()</span> <span class="o">-&gt;</span> <span class="kt">Void</span>

  <span class="k">var</span> <span class="nv">body</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
    <span class="kt">VStack</span><span class="p">(</span><span class="nv">spacing</span><span class="p">:</span> <span class="mi">16</span><span class="p">)</span> <span class="p">{</span>
      <span class="kt">ProgressView</span><span class="p">()</span>
      <span class="kt">Text</span><span class="p">(</span><span class="s">"Approve the Okta Verify push on your device."</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">multilineTextAlignment</span><span class="p">(</span><span class="o">.</span><span class="n">center</span><span class="p">)</span>

      <span class="kt">Button</span><span class="p">(</span><span class="s">"Cancel"</span><span class="p">,</span> <span class="nv">action</span><span class="p">:</span> <span class="n">onCancel</span><span class="p">)</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Now you can run the application on a simulator, and it should present you with the option to log in first with a username and password. After selecting <strong>SignIn</strong>, it will redirect to the “Waiting for push notification” screen and remain active until you acknowledge the request from the Okta Verify App. If you’re logged in, you’ll see the access token and a sign-out button.</p>

<h3 id="read-id-token-info">Read ID token info</h3>

<p>Once your app authenticates a user with Okta DirectAuth, the resulting credentials are securely stored in the device’s keychain through <code class="language-plaintext highlighter-rouge">AuthFoundation</code>.</p>

<p>These credentials include access, ID, and (optionally) refresh tokens – all essential for securely calling APIs or verifying user identity.</p>

<p>In this section, we’ll create a skeleton <code class="language-plaintext highlighter-rouge">TokenInfoView</code> that reads the current tokens from <code class="language-plaintext highlighter-rouge">Credential.default</code> and displays them in a developer-friendly format.</p>

<p>This view helps visualize the credential in the store and to inspect the scope. And it helps verify that the authentication flow works.</p>

<p>Create a new Swift file in the <code class="language-plaintext highlighter-rouge">Views</code> folder and name it <code class="language-plaintext highlighter-rouge">TokenInfoView</code>. Add the following code:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">import</span> <span class="kt">SwiftUI</span>
<span class="kd">import</span> <span class="kt">AuthFoundation</span>

<span class="c1">/// Displays detailed information about the tokens stored in the current</span>
<span class="c1">/// `Credential.default` instance. This view is helpful for debugging and</span>
<span class="c1">/// validating your DirectAuth flow -- confirming that tokens are correctly</span>
<span class="c1">/// issued, stored, and refreshed.</span>
<span class="c1">///</span>
<span class="c1">/// ⚠️ **Important:** Avoid showing full token strings in production apps.</span>
<span class="c1">/// Tokens should be treated as sensitive secrets.</span>
<span class="kd">struct</span> <span class="kt">TokenInfoView</span><span class="p">:</span> <span class="kt">View</span> <span class="p">{</span>

  <span class="c1">/// Retrieves the current credential object managed by `AuthFoundation`.</span>
  <span class="c1">/// If the user is signed in, this will contain their access, ID, and refresh tokens.</span>
  <span class="kd">private</span> <span class="k">var</span> <span class="nv">credential</span><span class="p">:</span> <span class="kt">Credential</span><span class="p">?</span> <span class="p">{</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span> <span class="p">}</span>

  <span class="c1">/// Used to dismiss the current view when the close button is tapped.</span>
  <span class="kd">@Environment</span><span class="p">(\</span><span class="o">.</span><span class="n">dismiss</span><span class="p">)</span> <span class="k">var</span> <span class="nv">dismiss</span>

  <span class="k">var</span> <span class="nv">body</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
    <span class="kt">ScrollView</span> <span class="p">{</span>
        <span class="kt">VStack</span><span class="p">(</span><span class="nv">alignment</span><span class="p">:</span> <span class="o">.</span><span class="n">leading</span><span class="p">,</span> <span class="nv">spacing</span><span class="p">:</span> <span class="mi">20</span><span class="p">)</span> <span class="p">{</span>

          <span class="c1">// MARK: - Close Button</span>
          <span class="c1">// Dismisses the token info view when tapped.</span>
          <span class="kt">Button</span> <span class="p">{</span>
            <span class="nf">dismiss</span><span class="p">()</span>
          <span class="p">}</span> <span class="nv">label</span><span class="p">:</span> <span class="p">{</span>
            <span class="kt">Image</span><span class="p">(</span><span class="nv">systemName</span><span class="p">:</span> <span class="s">"xmark.circle.fill"</span><span class="p">)</span>
              <span class="o">.</span><span class="nf">resizable</span><span class="p">()</span>
              <span class="o">.</span><span class="nf">foregroundStyle</span><span class="p">(</span><span class="o">.</span><span class="n">black</span><span class="p">)</span>
              <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">width</span><span class="p">:</span> <span class="mi">40</span><span class="p">,</span> <span class="nv">height</span><span class="p">:</span> <span class="mi">40</span><span class="p">)</span>
              <span class="o">.</span><span class="nf">padding</span><span class="p">(</span><span class="o">.</span><span class="n">leading</span><span class="p">,</span> <span class="mi">10</span><span class="p">)</span>
          <span class="p">}</span>

          <span class="c1">// MARK: - Token Display</span>
          <span class="c1">// Displays the token information as formatted monospaced text.</span>
          <span class="c1">// If no credential is available, a "No token found" message is shown.</span>
          <span class="kt">Text</span><span class="p">(</span><span class="n">credential</span><span class="p">?</span><span class="o">.</span><span class="nf">toString</span><span class="p">()</span> <span class="p">??</span> <span class="s">"No token found"</span><span class="p">)</span>
            <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="nf">system</span><span class="p">(</span><span class="o">.</span><span class="n">body</span><span class="p">,</span> <span class="nv">design</span><span class="p">:</span> <span class="o">.</span><span class="n">monospaced</span><span class="p">))</span>
            <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
            <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">maxWidth</span><span class="p">:</span> <span class="o">.</span><span class="n">infinity</span><span class="p">,</span> <span class="nv">alignment</span><span class="p">:</span> <span class="o">.</span><span class="n">leading</span><span class="p">)</span>
        <span class="p">}</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">background</span><span class="p">(</span><span class="kt">Color</span><span class="p">(</span><span class="o">.</span><span class="n">systemGroupedBackground</span><span class="p">))</span>
    <span class="o">.</span><span class="nf">navigationTitle</span><span class="p">(</span><span class="s">"Token Info"</span><span class="p">)</span>
    <span class="o">.</span><span class="nf">navigationBarTitleDisplayMode</span><span class="p">(</span><span class="o">.</span><span class="n">inline</span><span class="p">)</span>
  <span class="p">}</span>
<span class="p">}</span>

<span class="c1">// MARK: - Credential Display Helper</span>

<span class="kd">extension</span> <span class="kt">Credential</span> <span class="p">{</span>
  <span class="c1">/// Returns a formatted string representation of the stored token values.</span>
  <span class="c1">/// Includes access, ID, and refresh tokens as well as their associated scopes.</span>
  <span class="c1">///</span>
  <span class="c1">/// - Returns: A multi-line string suitable for debugging and display in `TokenInfoView`.</span>
  <span class="kd">func</span> <span class="nf">toString</span><span class="p">()</span> <span class="o">-&gt;</span> <span class="kt">String</span> <span class="p">{</span>
    <span class="k">var</span> <span class="nv">result</span> <span class="o">=</span> <span class="s">""</span>

    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"Token type: </span><span class="se">\(</span><span class="n">token</span><span class="o">.</span><span class="n">tokenType</span><span class="se">)</span><span class="s">"</span><span class="p">)</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>

    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"Access Token: </span><span class="se">\(</span><span class="n">token</span><span class="o">.</span><span class="n">accessToken</span><span class="se">)</span><span class="s">"</span><span class="p">)</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>

    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"Scopes: </span><span class="se">\(</span><span class="n">token</span><span class="o">.</span><span class="n">scope</span><span class="p">?</span><span class="o">.</span><span class="nf">joined</span><span class="p">(</span><span class="nv">separator</span><span class="p">:</span> <span class="s">","</span><span class="p">)</span> <span class="p">??</span> <span class="s">"No scopes found"</span><span class="se">)</span><span class="s">"</span><span class="p">)</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>

    <span class="k">if</span> <span class="k">let</span> <span class="nv">idToken</span> <span class="o">=</span> <span class="n">token</span><span class="o">.</span><span class="n">idToken</span> <span class="p">{</span>
      <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"ID Token: </span><span class="se">\(</span><span class="n">idToken</span><span class="o">.</span><span class="n">rawValue</span><span class="se">)</span><span class="s">"</span><span class="p">)</span>
      <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>
    <span class="p">}</span>

    <span class="k">if</span> <span class="k">let</span> <span class="nv">refreshToken</span> <span class="o">=</span> <span class="n">token</span><span class="o">.</span><span class="n">refreshToken</span> <span class="p">{</span>
      <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"Refresh Token: </span><span class="se">\(</span><span class="n">refreshToken</span><span class="se">)</span><span class="s">"</span><span class="p">)</span>
      <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>
    <span class="p">}</span>

    <span class="k">return</span> <span class="n">result</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>To view this on screen, we need to instruct SwiftUI to present it. We added the <code class="language-plaintext highlighter-rouge">State</code> variable in the <code class="language-plaintext highlighter-rouge">AuthView</code> for this purpose - it’s named <code class="language-plaintext highlighter-rouge">showTokenInfo</code>. Next, we need to add a button to present the <code class="language-plaintext highlighter-rouge">TokenInfoView</code>. Go to the <code class="language-plaintext highlighter-rouge">AuthView.swift</code> and scroll down to the last private extension where it says “Action Buttons” and add the following button:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">/// Opens the full-screen view showing token info.</span>
<span class="k">var</span> <span class="nv">tokenInfoButton</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="kt">Button</span><span class="p">(</span><span class="s">"Token Info"</span><span class="p">)</span> <span class="p">{</span>
    <span class="n">showTokenInfo</span> <span class="o">=</span> <span class="kc">true</span>
  <span class="p">}</span>
  <span class="o">.</span><span class="nf">disabled</span><span class="p">(</span><span class="n">viewModel</span><span class="o">.</span><span class="n">isLoading</span><span class="p">)</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Now that this is in place, we need to tell SwiftUI that we want to present <code class="language-plaintext highlighter-rouge">TokenInfoView</code> whenever the <code class="language-plaintext highlighter-rouge">showTokenInfo</code> boolean is true. In the <code class="language-plaintext highlighter-rouge">AuthView</code>, find the body and add this code at the end below the <code class="language-plaintext highlighter-rouge">.padding()</code>:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// Show Token Info full screen</span>
<span class="o">.</span><span class="nf">fullScreenCover</span><span class="p">(</span><span class="nv">isPresented</span><span class="p">:</span> <span class="err">$</span><span class="n">showTokenInfo</span><span class="p">)</span> <span class="p">{</span>
  <span class="kt">TokenInfoView</span><span class="p">()</span>
<span class="p">}</span>
</code></pre></div></div>

<p>If you build and run the app, you’ll no longer see the <strong>Token Info</strong> button when logged in. To keep the button visible, we also need to reference the <code class="language-plaintext highlighter-rouge">tokenInfoButton</code> in the <code class="language-plaintext highlighter-rouge">successView</code>. In the <code class="language-plaintext highlighter-rouge">AuthView</code> file, scroll down to “Authorized State View” (<code class="language-plaintext highlighter-rouge">successView</code>) and reference the button just above the <code class="language-plaintext highlighter-rouge">signoutButton</code> like this:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">private</span> <span class="k">var</span> <span class="nv">successView</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="kt">VStack</span><span class="p">(</span><span class="nv">spacing</span><span class="p">:</span> <span class="mi">16</span><span class="p">)</span> <span class="p">{</span>
    <span class="kt">Text</span><span class="p">(</span><span class="s">"Signed in 🎉"</span><span class="p">)</span>
      <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">title2</span><span class="p">)</span>
      <span class="o">.</span><span class="nf">bold</span><span class="p">()</span>

    <span class="c1">// Scrollable ID token display (for demo purposes)</span>
    <span class="kt">ScrollView</span> <span class="p">{</span>
      <span class="kt">Text</span><span class="p">(</span><span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="n">token</span><span class="o">.</span><span class="n">idToken</span><span class="p">?</span><span class="o">.</span><span class="n">rawValue</span> <span class="p">??</span> <span class="s">"(no id token)"</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">footnote</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">textSelection</span><span class="p">(</span><span class="o">.</span><span class="n">enabled</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
        <span class="o">.</span><span class="nf">background</span><span class="p">(</span><span class="o">.</span><span class="n">thinMaterial</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">cornerRadius</span><span class="p">(</span><span class="mi">8</span><span class="p">)</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">maxHeight</span><span class="p">:</span> <span class="mi">220</span><span class="p">)</span>

    <span class="c1">// Authenticated user actions</span>
    <span class="n">tokenInfoButton</span> <span class="c1">// this is added</span>
    <span class="n">signoutButton</span>
  <span class="p">}</span>
  <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Try building and running the app. You should now see the <strong>Token Info</strong> button after logging in. Tapping the button should open the Token Info View.</p>

<h2 id="view-the-authenticated-users-profile-info">View the authenticated user’s profile info</h2>

<p>Once your app authenticates a user with Okta DirectAuth, it can use the stored credentials to request profile information from the <code class="language-plaintext highlighter-rouge">UserInfo</code> endpoint securely.</p>

<p>This endpoint returns standard OpenID Connect (OIDC) claims, including the user’s name, email address, and unique identifier (<code class="language-plaintext highlighter-rouge">sub</code>).</p>

<p>In this section, you’ll add a <strong>User Info</strong> button to your authenticated view and implement a corresponding <code class="language-plaintext highlighter-rouge">UserInfoView</code> that displays these profile details.</p>

<p>This is a quick and powerful way to confirm the validity of the access token and that your app can retrieve user data after sign-in.</p>

<p>Create a new empty Swift file in the <code class="language-plaintext highlighter-rouge">Views</code> folder and name it <code class="language-plaintext highlighter-rouge">UserInfoView</code>. Then add the following code:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">import</span> <span class="kt">SwiftUI</span>
<span class="kd">import</span> <span class="kt">AuthFoundation</span>

<span class="c1">/// A view that displays the authenticated user's profile information</span>
<span class="c1">/// retrieved from Okta's **UserInfo** endpoint.</span>
<span class="c1">///</span>
<span class="c1">/// The `UserInfo` object is provided by **AuthFoundation** and contains</span>
<span class="c1">/// standard OpenID Connect (OIDC) claims such as `name`, `preferred_username`,</span>
<span class="c1">/// and `sub` (subject identifier). This view is shown after the user has</span>
<span class="c1">/// successfully authenticated, allowing you to confirm that your access token</span>
<span class="c1">/// can retrieve user data.</span>
<span class="kd">struct</span> <span class="kt">UserInfoView</span><span class="p">:</span> <span class="kt">View</span> <span class="p">{</span>

  <span class="c1">/// The user information returned by the Okta UserInfo endpoint.</span>
  <span class="k">let</span> <span class="nv">userInfo</span><span class="p">:</span> <span class="kt">UserInfo</span>

  <span class="c1">/// Used to dismiss the view when the close button is tapped.</span>
  <span class="kd">@Environment</span><span class="p">(\</span><span class="o">.</span><span class="n">dismiss</span><span class="p">)</span> <span class="k">var</span> <span class="nv">dismiss</span>

  <span class="k">var</span> <span class="nv">body</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
    <span class="kt">ScrollView</span> <span class="p">{</span>
      <span class="kt">VStack</span><span class="p">(</span><span class="nv">alignment</span><span class="p">:</span> <span class="o">.</span><span class="n">leading</span><span class="p">,</span> <span class="nv">spacing</span><span class="p">:</span> <span class="mi">20</span><span class="p">)</span> <span class="p">{</span>

          <span class="c1">// MARK: - Close Button</span>
          <span class="c1">// Dismisses the full-screen user info view.</span>
          <span class="kt">Button</span> <span class="p">{</span>
            <span class="nf">dismiss</span><span class="p">()</span>
          <span class="p">}</span> <span class="nv">label</span><span class="p">:</span> <span class="p">{</span>
            <span class="kt">Image</span><span class="p">(</span><span class="nv">systemName</span><span class="p">:</span> <span class="s">"xmark.circle.fill"</span><span class="p">)</span>
              <span class="o">.</span><span class="nf">resizable</span><span class="p">()</span>
              <span class="o">.</span><span class="nf">foregroundStyle</span><span class="p">(</span><span class="o">.</span><span class="n">black</span><span class="p">)</span>
              <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">width</span><span class="p">:</span> <span class="mi">40</span><span class="p">,</span> <span class="nv">height</span><span class="p">:</span> <span class="mi">40</span><span class="p">)</span>
              <span class="o">.</span><span class="nf">padding</span><span class="p">(</span><span class="o">.</span><span class="n">leading</span><span class="p">,</span> <span class="mi">10</span><span class="p">)</span>
          <span class="p">}</span>

          <span class="c1">// MARK: - User Information Text</span>
          <span class="c1">// Displays formatted user claims (name, username, subject, etc.)</span>
          <span class="kt">Text</span><span class="p">(</span><span class="n">formattedData</span><span class="p">)</span>
            <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="nf">system</span><span class="p">(</span><span class="nv">size</span><span class="p">:</span> <span class="mi">14</span><span class="p">))</span>
            <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">maxWidth</span><span class="p">:</span> <span class="o">.</span><span class="n">infinity</span><span class="p">,</span> <span class="nv">alignment</span><span class="p">:</span> <span class="o">.</span><span class="n">leading</span><span class="p">)</span>
            <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
        <span class="p">}</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">background</span><span class="p">(</span><span class="kt">Color</span><span class="p">(</span><span class="o">.</span><span class="n">systemBackground</span><span class="p">))</span>
    <span class="o">.</span><span class="nf">navigationTitle</span><span class="p">(</span><span class="s">"User Info"</span><span class="p">)</span>
    <span class="o">.</span><span class="nf">navigationBarTitleDisplayMode</span><span class="p">(</span><span class="o">.</span><span class="n">inline</span><span class="p">)</span>
  <span class="p">}</span>

  <span class="c1">// MARK: - Data Formatting</span>

  <span class="c1">/// Builds a simple multi-line string of readable user information.</span>
  <span class="c1">/// Extracts common OIDC claims and formats them for display.</span>
  <span class="kd">private</span> <span class="k">var</span> <span class="nv">formattedData</span><span class="p">:</span> <span class="kt">String</span> <span class="p">{</span>
    <span class="k">var</span> <span class="nv">result</span> <span class="o">=</span> <span class="s">""</span>

    <span class="c1">// User's full name</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"Name: "</span> <span class="o">+</span> <span class="p">(</span><span class="n">userInfo</span><span class="o">.</span><span class="n">name</span> <span class="p">??</span> <span class="s">"No name set"</span><span class="p">))</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>

    <span class="c1">// Preferred username (email or login identifier)</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"Username: "</span> <span class="o">+</span> <span class="p">(</span><span class="n">userInfo</span><span class="o">.</span><span class="n">preferredUsername</span> <span class="p">??</span> <span class="s">"No username set"</span><span class="p">))</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>

    <span class="c1">// Subject identifier (unique Okta user ID)</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"User ID: "</span> <span class="o">+</span> <span class="p">(</span><span class="n">userInfo</span><span class="o">.</span><span class="n">subject</span> <span class="p">??</span> <span class="s">"No ID found"</span><span class="p">))</span>
    <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"</span><span class="se">\n\n</span><span class="s">"</span><span class="p">)</span>

    <span class="c1">// Last updated timestamp (if available)</span>
    <span class="k">if</span> <span class="k">let</span> <span class="nv">updatedAt</span> <span class="o">=</span> <span class="n">userInfo</span><span class="o">.</span><span class="n">updatedAt</span> <span class="p">{</span>
      <span class="k">let</span> <span class="nv">dateFormatter</span> <span class="o">=</span> <span class="kt">DateFormatter</span><span class="p">()</span>
      <span class="n">dateFormatter</span><span class="o">.</span><span class="n">dateStyle</span> <span class="o">=</span> <span class="o">.</span><span class="n">medium</span>
      <span class="n">dateFormatter</span><span class="o">.</span><span class="n">timeStyle</span> <span class="o">=</span> <span class="o">.</span><span class="n">short</span>
      <span class="k">let</span> <span class="nv">formattedDate</span> <span class="o">=</span> <span class="n">dateFormatter</span><span class="o">.</span><span class="nf">string</span><span class="p">(</span><span class="nv">for</span><span class="p">:</span> <span class="n">updatedAt</span><span class="p">)</span>
      <span class="n">result</span><span class="o">.</span><span class="nf">append</span><span class="p">(</span><span class="s">"Updated at: "</span> <span class="o">+</span> <span class="p">(</span><span class="n">formattedDate</span> <span class="p">??</span> <span class="s">""</span><span class="p">))</span>
    <span class="p">}</span>

    <span class="k">return</span> <span class="n">result</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Once again, to display this in our app, we need to add a new button to show the new view. To do that, open the <code class="language-plaintext highlighter-rouge">AuthView.swift</code>, scroll down to the last private extension where it says “Action Buttons”, and add the following button just below the <code class="language-plaintext highlighter-rouge">tokenInfoButton</code>:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">/// Loads user info and presents it full screen.</span>
<span class="kd">@MainActor</span>
<span class="k">var</span> <span class="nv">userInfoButton</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="kt">Button</span><span class="p">(</span><span class="s">"User Info"</span><span class="p">)</span> <span class="p">{</span>
    <span class="kt">Task</span> <span class="p">{</span>
      <span class="k">if</span> <span class="k">let</span> <span class="nv">user</span> <span class="o">=</span> <span class="k">await</span> <span class="n">viewModel</span><span class="o">.</span><span class="nf">fetchUserInfo</span><span class="p">()</span> <span class="p">{</span>
        <span class="n">userInfo</span> <span class="o">=</span> <span class="kt">UserInfoModel</span><span class="p">(</span><span class="nv">user</span><span class="p">:</span> <span class="n">user</span><span class="p">)</span>
      <span class="p">}</span>
    <span class="p">}</span>
  <span class="p">}</span>
  <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="nf">system</span><span class="p">(</span><span class="nv">size</span><span class="p">:</span> <span class="mi">14</span><span class="p">))</span>
  <span class="o">.</span><span class="nf">disabled</span><span class="p">(</span><span class="n">viewModel</span><span class="o">.</span><span class="n">isLoading</span><span class="p">)</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Next, we need to add the button to the <code class="language-plaintext highlighter-rouge">successView</code> like we did with the <code class="language-plaintext highlighter-rouge">tokenInfoButton</code>. Then, we will use the <code class="language-plaintext highlighter-rouge">userInfo</code> property in the <code class="language-plaintext highlighter-rouge">AuthView</code>, which we added at the start. Navigate to the <code class="language-plaintext highlighter-rouge">AuthView.swift</code> file and find the <code class="language-plaintext highlighter-rouge">successView</code> in the “Authorized State View” mark and reference the <code class="language-plaintext highlighter-rouge">userInfoButton</code> after the <code class="language-plaintext highlighter-rouge">tokenInfoButton</code> like this:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">private</span> <span class="k">var</span> <span class="nv">successView</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="kt">VStack</span><span class="p">(</span><span class="nv">spacing</span><span class="p">:</span> <span class="mi">16</span><span class="p">)</span> <span class="p">{</span>
    <span class="kt">Text</span><span class="p">(</span><span class="s">"Signed in 🎉"</span><span class="p">)</span>
      <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">title2</span><span class="p">)</span>
      <span class="o">.</span><span class="nf">bold</span><span class="p">()</span>

    <span class="c1">// Scrollable ID token display (for demo purposes)</span>
    <span class="kt">ScrollView</span> <span class="p">{</span>
      <span class="kt">Text</span><span class="p">(</span><span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="n">token</span><span class="o">.</span><span class="n">idToken</span><span class="p">?</span><span class="o">.</span><span class="n">rawValue</span> <span class="p">??</span> <span class="s">"(no id token)"</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">footnote</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">textSelection</span><span class="p">(</span><span class="o">.</span><span class="n">enabled</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
        <span class="o">.</span><span class="nf">background</span><span class="p">(</span><span class="o">.</span><span class="n">thinMaterial</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">cornerRadius</span><span class="p">(</span><span class="mi">8</span><span class="p">)</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">maxHeight</span><span class="p">:</span> <span class="mi">220</span><span class="p">)</span>

    <span class="c1">// Authenticated user actions</span>
    <span class="n">tokenInfoButton</span>
    <span class="n">userInfoButton</span> <span class="c1">// this is added</span>
    <span class="n">signoutButton</span>
  <span class="p">}</span>
  <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
<span class="p">}</span>
</code></pre></div></div>

<p>We need to tell SwiftUI that we want to open a new <code class="language-plaintext highlighter-rouge">UserInfoView</code> whenever the value on the <code class="language-plaintext highlighter-rouge">userInfo</code> property changes. To do so, open the <code class="language-plaintext highlighter-rouge">AuthView</code> and find the body variable, add the following code after the last closing bracket:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">// Show User Info full screen</span>
<span class="o">.</span><span class="nf">fullScreenCover</span><span class="p">(</span><span class="nv">item</span><span class="p">:</span> <span class="err">$</span><span class="n">userInfo</span><span class="p">)</span> <span class="p">{</span> <span class="n">info</span> <span class="k">in</span>
  <span class="kt">UserInfoView</span><span class="p">(</span><span class="nv">userInfo</span><span class="p">:</span> <span class="n">info</span><span class="o">.</span><span class="n">user</span><span class="p">)</span>
<span class="p">}</span>
</code></pre></div></div>

<p>The body of your <code class="language-plaintext highlighter-rouge">AuthView</code> should look like this now:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">var</span> <span class="nv">body</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="kt">VStack</span> <span class="p">{</span>
    <span class="c1">// Render the UI based on the current authentication state.</span>
    <span class="c1">// Each case corresponds to a different phase of the DirectAuth flow.</span>
    <span class="k">switch</span> <span class="n">viewModel</span><span class="o">.</span><span class="n">state</span> <span class="p">{</span>
    <span class="k">case</span> <span class="o">.</span><span class="n">idle</span><span class="p">,</span> <span class="o">.</span><span class="nv">failed</span><span class="p">:</span>
      <span class="n">loginForm</span>
    <span class="k">case</span> <span class="o">.</span><span class="nv">authenticating</span><span class="p">:</span>
      <span class="kt">ProgressView</span><span class="p">(</span><span class="s">"Signing in..."</span><span class="p">)</span>
    <span class="k">case</span> <span class="o">.</span><span class="nv">waitingForPush</span><span class="p">:</span>
      <span class="c1">// Waiting for Okta Verify push approval</span>
      <span class="kt">WaitingForPushView</span> <span class="p">{</span>
        <span class="kt">Task</span> <span class="p">{</span> <span class="k">await</span> <span class="n">viewModel</span><span class="o">.</span><span class="nf">signOut</span><span class="p">()</span> <span class="p">}</span>
      <span class="p">}</span>
    <span class="k">case</span> <span class="o">.</span><span class="nv">authorized</span><span class="p">:</span>
      <span class="n">successView</span>
    <span class="p">}</span>

    <span class="k">if</span> <span class="n">viewModel</span><span class="o">.</span><span class="n">isLoading</span> <span class="p">{</span>
      <span class="kt">ProgressView</span><span class="p">()</span>
    <span class="p">}</span>
  <span class="p">}</span>
  <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
  <span class="c1">// Show Token Info full screen</span>
  <span class="o">.</span><span class="nf">fullScreenCover</span><span class="p">(</span><span class="nv">isPresented</span><span class="p">:</span> <span class="err">$</span><span class="n">showTokenInfo</span><span class="p">)</span> <span class="p">{</span>
    <span class="kt">TokenInfoView</span><span class="p">()</span>
  <span class="p">}</span>
  <span class="c1">// Show User Info full screen</span>
  <span class="o">.</span><span class="nf">fullScreenCover</span><span class="p">(</span><span class="nv">item</span><span class="p">:</span> <span class="err">$</span><span class="n">userInfo</span><span class="p">)</span> <span class="p">{</span> <span class="n">info</span> <span class="k">in</span>
    <span class="kt">UserInfoView</span><span class="p">(</span><span class="nv">userInfo</span><span class="p">:</span> <span class="n">info</span><span class="o">.</span><span class="n">user</span><span class="p">)</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<h3 id="keeping-tokens-refreshed-and-maintaining-user-sessions">Keeping tokens refreshed and maintaining user sessions</h3>

<p>Access tokens have a limited lifetime to ensure your app’s security. When a token expires, the user shouldn’t have to sign-in again – instead, your app can request a new access token using the refresh token stored in the credential.</p>

<p>In this section, you’ll add support for token refresh, allowing users to stay authenticated without repeating the entire sign-in and MFA flow.</p>

<p>You’ll add an action in the UI that calls the <code class="language-plaintext highlighter-rouge">refreshTokenIfNeeded()</code> method from your <code class="language-plaintext highlighter-rouge">AuthService</code>, which silently exchanges the refresh token for a new set of valid tokens. We’re making this call manually, but you can watch for upcoming expiry and refresh the token before it happens preemptively. While we don’t show it here, you should use <strong>Refresh Token Rotation</strong> to ensure refresh tokens are also short-lived as a security measure.</p>

<p>First, we need to add the <code class="language-plaintext highlighter-rouge">refreshTokenButton</code>, which we’ll add to the <code class="language-plaintext highlighter-rouge">AuthView</code>. Open the <code class="language-plaintext highlighter-rouge">AuthView</code>, scroll down to the last private extension in the “Action Buttons” mark, and add the following button at the end of the extension:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">/// Refresh Token if needed</span>
<span class="k">var</span> <span class="nv">refreshTokenButton</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="kt">Button</span><span class="p">(</span><span class="s">"Refresh Token"</span><span class="p">)</span> <span class="p">{</span>
    <span class="kt">Task</span> <span class="p">{</span> <span class="k">await</span> <span class="n">viewModel</span><span class="o">.</span><span class="nf">refreshToken</span><span class="p">()</span> <span class="p">}</span>
  <span class="p">}</span>
  <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="nf">system</span><span class="p">(</span><span class="nv">size</span><span class="p">:</span> <span class="mi">14</span><span class="p">))</span>
  <span class="o">.</span><span class="nf">disabled</span><span class="p">(</span><span class="n">viewModel</span><span class="o">.</span><span class="n">isLoading</span><span class="p">)</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Next, we need to reference the button somewhere in our view. We will do that inside the <code class="language-plaintext highlighter-rouge">successView</code>, like we did with the other buttons. Find the <code class="language-plaintext highlighter-rouge">successView</code> and add the button. Your <code class="language-plaintext highlighter-rouge">successView</code> should look like this:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">private</span> <span class="k">var</span> <span class="nv">successView</span><span class="p">:</span> <span class="kd">some</span> <span class="kt">View</span> <span class="p">{</span>
  <span class="kt">VStack</span><span class="p">(</span><span class="nv">spacing</span><span class="p">:</span> <span class="mi">16</span><span class="p">)</span> <span class="p">{</span>
    <span class="kt">Text</span><span class="p">(</span><span class="s">"Signed in 🎉"</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">title2</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">bold</span><span class="p">()</span>

    <span class="c1">// Scrollable ID token display (for demo purposes)</span>
    <span class="kt">ScrollView</span> <span class="p">{</span>
      <span class="kt">Text</span><span class="p">(</span><span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="n">token</span><span class="o">.</span><span class="n">idToken</span><span class="p">?</span><span class="o">.</span><span class="n">rawValue</span> <span class="p">??</span> <span class="s">"(no id token)"</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">font</span><span class="p">(</span><span class="o">.</span><span class="n">footnote</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">textSelection</span><span class="p">(</span><span class="o">.</span><span class="n">enabled</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
        <span class="o">.</span><span class="nf">background</span><span class="p">(</span><span class="o">.</span><span class="n">thinMaterial</span><span class="p">)</span>
        <span class="o">.</span><span class="nf">cornerRadius</span><span class="p">(</span><span class="mi">8</span><span class="p">)</span>
    <span class="p">}</span>
    <span class="o">.</span><span class="nf">frame</span><span class="p">(</span><span class="nv">maxHeight</span><span class="p">:</span> <span class="mi">220</span><span class="p">)</span>

    <span class="c1">// Authenticated user actions</span>
    <span class="n">tokenInfoButton</span>
    <span class="n">userInfoButton</span>
    <span class="n">refreshTokenButton</span> <span class="c1">// this is added</span>
    <span class="n">signoutButton</span>
  <span class="p">}</span>
  <span class="o">.</span><span class="nf">padding</span><span class="p">()</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Now, if you run the app and tap the <code class="language-plaintext highlighter-rouge">refreshTokenButton</code>, you should see your token change in the token preview label.</p>

<p>One thing that we didn’t implement and left with a default implementation to return <code class="language-plaintext highlighter-rouge">nil</code> is the <code class="language-plaintext highlighter-rouge">accessToken</code> property on the <code class="language-plaintext highlighter-rouge">AuthService</code>. Navigate to the <code class="language-plaintext highlighter-rouge">AuthService</code>, find the <code class="language-plaintext highlighter-rouge">accessToken</code> property, and replace the code so it looks like this:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">var</span> <span class="nv">accessToken</span><span class="p">:</span> <span class="kt">String</span><span class="p">?</span> <span class="p">{</span>
  <span class="k">switch</span> <span class="n">state</span> <span class="p">{</span>
  <span class="k">case</span> <span class="o">.</span><span class="nf">authorized</span><span class="p">(</span><span class="k">let</span> <span class="nv">token</span><span class="p">):</span>
    <span class="k">return</span> <span class="n">token</span><span class="o">.</span><span class="n">accessToken</span>
  <span class="k">default</span><span class="p">:</span>
    <span class="k">return</span> <span class="kc">nil</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>Currently, if you restart the app, you’ll get a prompt to log in each time. This is not a good user experience, and the user should remain logged in. We can add this feature by adding code in the <code class="language-plaintext highlighter-rouge">AuthService</code> initializer. Open your <code class="language-plaintext highlighter-rouge">AuthService</code> class and replace the <code class="language-plaintext highlighter-rouge">init</code> function with the following:</p>

<div class="language-swift highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nf">init</span><span class="p">()</span> <span class="p">{</span>
  <span class="c1">// Prefer PropertyListConfiguration if Okta.plist exists; otherwise fall back</span>
  <span class="k">if</span> <span class="k">let</span> <span class="nv">configuration</span> <span class="o">=</span> <span class="k">try</span><span class="p">?</span> <span class="kt">OAuth2Client</span><span class="o">.</span><span class="kt">PropertyListConfiguration</span><span class="p">()</span> <span class="p">{</span>
    <span class="k">self</span><span class="o">.</span><span class="n">flow</span> <span class="o">=</span> <span class="k">try</span><span class="p">?</span> <span class="kt">DirectAuthenticationFlow</span><span class="p">(</span><span class="nv">client</span><span class="p">:</span> <span class="kt">OAuth2Client</span><span class="p">(</span><span class="n">configuration</span><span class="p">))</span>
  <span class="p">}</span> <span class="k">else</span> <span class="p">{</span>
    <span class="k">self</span><span class="o">.</span><span class="n">flow</span> <span class="o">=</span> <span class="k">try</span><span class="p">?</span> <span class="kt">DirectAuthenticationFlow</span><span class="p">()</span>
  <span class="p">}</span>

  <span class="c1">// Added</span>
  <span class="k">if</span> <span class="k">let</span> <span class="nv">token</span> <span class="o">=</span> <span class="kt">Credential</span><span class="o">.</span><span class="k">default</span><span class="p">?</span><span class="o">.</span><span class="n">token</span> <span class="p">{</span>
    <span class="n">state</span> <span class="o">=</span> <span class="o">.</span><span class="nf">authorized</span><span class="p">(</span><span class="n">token</span><span class="p">)</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>
<h2 id="build-your-own-secure-native-sign-in-ios-app">Build your own secure native sign-in iOS app</h2>

<p>You’ve now built a fully native authentication flow on iOS using Okta DirectAuth with push notification MFA – no browser redirects required. You can check your work against <a href="https://github.com/oktadev/okta-ios-swift-directauth-example">the GitHub repo</a> for this project.</p>

<p>Your app securely signs users in, handles multi-factor verification through Okta Verify, retrieves user profile details, displays token information, and refreshes tokens to maintain an active session.
By combining <code class="language-plaintext highlighter-rouge">AuthFoundation</code> and <code class="language-plaintext highlighter-rouge">OktaDirectAuth</code>, you’ve implemented a modern, phishing-resistant authentication system that balances strong security with a seamless user experience – all directly within your SwiftUI app.</p>

<p>If you found this post interesting, you may want to check out these resources:</p>
<ul>
  <li><a href="/blog/2025/08/20/ios-mfa">How to Build a Secure iOS App with MFA</a></li>
  <li><a href="/blog/2022/08/30/introducing-the-new-okta-mobile-sdks">Introducing the New Okta Mobile SDKs</a></li>
  <li><a href="/blog/2022/01/13/mobile-sso">A History of the Mobile SSO (Single Sign-On) Experience in iOS</a></li>
</ul>

<p>Follow OktaDev on <a href="https://twitter.com/oktadev">Twitter</a> and subscribe to our <a href="https://www.youtube.com/c/OktaDev/">YouTube channel</a> to learn about secure authentication and other exciting content. We also want to hear from you about topics you want to see and questions you may have. Leave us a comment below!</p>
