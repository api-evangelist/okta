---
title: "Develop a XAA-Enabled Resource Application and Test with Okta"
url: "https://developer.okta.com/blog/2026/02/17/xaa-resource-app"
date: "Tue, 17 Feb 2026 00:00:00 -0500"
author: ""
feed_url: "https://developer.okta.com/feed.xml"
---
<p>From an enterprise resource app owner’s perspective, Cross App Access (XAA) is a game-changer because it allows their resources to be “AI-ready” without compromising on security. In the XAA model, resource apps rely on the enterprise’s Identity Provider (IdP) to manage access. Instead of building out interactive OAuth flows, they defer to the IdP to check enterprise policies and user groups, assign AI agent permissions, and log and audit AI agent requests as they occur. In return, the app’s OAuth server needs only to perform a few checks:</p>

<ul>
  <li>When the app’s OAuth server receives a POST request to its token endpoint from an AI agent, the app fetches the IdP’s public keys (via the JWKS endpoint) to ensure the ID-JAG token attached to the request was actually minted by the trusted company IdP.</li>
  <li>It confirms the token was intended for this app specifically. If the <code class="language-plaintext highlighter-rouge">aud</code> claim doesn’t match the app’s own identifier, it rejects the request.</li>
  <li>Finally, it checks the end user ID in the token’s <code class="language-plaintext highlighter-rouge">sub</code> claim to know whose data to look up in your database. It must map to the same IdP identity. It will reject the request if the user isn’t recognized.</li>
</ul>

<p>You can read in depth about XAA to better understand how this works and examine the token exchange flow.</p>

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

<p>Or watch the video about Cross App Access:</p>

<div class="jekyll-youtube-plugin" style="text-align: center;">
            
        </div>

<p>In this tutorial, we’ll demonstrate how to test that an XAA-enabled resource app you have created (<strong>TaskFlow</strong>) is correctly using Okta as an <strong>enterprise Identity Provider (IdP)</strong> to sign users in, and we’ll demonstrate how a sample AI app (<strong>Agent0</strong>) uses XAA to get access to TaskFlow. To do this, you’ll:</p>

<ul>
  <li>Enable Cross App Access in your Okta org</li>
  <li>Register and configure the resource app (TaskFlow) in your org</li>
  <li>Register the requesting app (Agent0) in your org as a known XAA app and connect it to TaskFlow.</li>
  <li>Test that the XAA flow is working correctly when Agent0 requests access to TaskFlow.</li>
</ul>

<blockquote>
  <p>Note that the apps (TaskFlow or Agent0) do not use Okta as their authorization server.</p>
</blockquote>

<h1 id="enable-cross-app-access-in-your-okta-org">Enable Cross App Access in your Okta org</h1>

<p>To register your resource app with Okta, and set up secure agent-to-app connections, you’ll need an Okta Developer org enabled with XAA:</p>

<ul>
  <li>If you don’t already have an account, sign up for a new one here: <a href="https://developer.okta.com/signup">Okta Integrator Free Plan</a></li>
  <li>Once created, sign in to your new Integrator Free Plan org</li>
  <li>In the Okta Admin Console, select <strong>Settings &gt; Features</strong></li>
  <li>Navigate to <strong>Early access</strong></li>
  <li>Find <strong>Cross App Access</strong> and select <strong>Turn on</strong> (enable the toggle)</li>
  <li>Refresh the Admin Console</li>
</ul>

<blockquote>
  <p>Note: Cross App Access is currently a self-service Early Access (EA) feature. You must enable it through the Admin Console before the apps appear in the catalog. If you don’t see the option right away, refresh and confirm you have the necessary admin permissions. Learn more in the <a href="https://help.okta.com/oie/en-us/content/topics/security/manage-ea-and-beta-features.htm">Okta documentation on managing EA and beta features</a>.</p>
</blockquote>

<p><img alt=" " class="center-image" src="/assets-jekyll/blog/xaa-resource-app/image3-c9a95bb9918d5d631678622b2343d7776e314875e72ecd70fea836d264d8164c.jpg" width="800" /></p>

<h1 id="register-your-requesting-app-agent0">Register your requesting app (Agent0)</h1>

<p>To test whether your resource app is working correctly, Okta provides a placeholder entry in the Okta Integration Network catalog. It is called <strong><em>Agent0 - Cross App Access (XAA) Sample Requesting App</em></strong>. Add this to your org’s integrations.</p>

<ul>
  <li>Still in Admin Console, go to <strong>Applications &gt; Applications</strong></li>
  <li>Select <strong>Browse App Catalog</strong></li>
  <li>Search for “Agent0 - Cross App Access (XAA) Sample Requesting App”, and select it</li>
  <li>Select <strong>Add Integration</strong></li>
</ul>

<p>Now to configure it correctly. First, assign user access to Agent0.</p>

<ul>
  <li>Change the <strong>Application</strong> label if required, and select <strong>Done</strong>,</li>
  <li>Select the Assignments tab
    <ul>
      <li>To assign it to a single user, select <strong>Assign &gt; Assign to People</strong> and choose your user</li>
      <li>To assign it to a user group, select <strong>Assign &gt; Assign to Groups</strong> and choose your user group</li>
    </ul>
  </li>
  <li>Click Done</li>
</ul>

<p>Finally, configure Agent0 with the redirect URI you will use to test Agent0</p>

<ul>
  <li>Select the <strong>Sign On tab</strong></li>
  <li>Select <strong>Edit</strong>, and locate the Advanced Sign-on Settings section.</li>
  <li>Set the <strong>Redirect URI</strong> to the URL that your app will use. For example, <a href="http://localhost:8080/redirect">http://localhost:8080/redirect</a></li>
  <li>Click Save.</li>
  <li>Locate and copy the Client ID and Client secret in the Sign-On methods section. Your app must use these when signing users in through Okta.</li>
</ul>

<blockquote>
  <p>Note: Only the org authorization server can be used to exchange ID-JAG tokens. Ensure you are using the org authorization server and not an Okta “custom authorization server”.</p>
</blockquote>

<p><img alt=" " class="center-image" src="/assets-jekyll/blog/xaa-resource-app/image2-ad5eed8a0e6b495caa26809fb24390efdd64204ac8c638c84d249a028882537b.jpg" width="800" /></p>

<h2 id="get-a-xaa-client-id-for-agent0-from-the-resource-apps-auth-server">Get a (XAA) Client ID for Agent0 from the Resource app’s Auth Server</h2>

<p>To allow the exchange of an ID-JAG token between Agent0 and your resource app, Agent0 must be registered as an OAuth client in your resource app’s OAuth server.</p>

<ul>
  <li>Register your requesting app (<strong>Agent0</strong>) as an OAuth client in your resource app’s OAuth server.</li>
  <li>Make a note of the Client ID for your requesting app (<strong>Agent0</strong>). You’ll need this as you set up your resource app.</li>
</ul>

<blockquote>
  <p>Note: The process for registering a client ID from your resource app’s OAuth server will vary depending on the product.</p>
</blockquote>

<h1 id="set-up-your-resource-app-taskflow">Set up your resource app (TaskFlow)</h1>

<p>To set up your resource app in your org, you can use the placeholder integration in the OIN catalog called <strong><em>Todo0 - Cross App Access (XAA) Sample Resource App</em></strong> and configure it as your resource app.</p>

<ul>
  <li>Still in Admin Console, navigate to <strong>Applications &gt; Applications</strong></li>
  <li>Select <strong>Browse App Catalog</strong></li>
  <li>Search for <strong>Todo0 - Cross App Access (XAA) Sample Resource App</strong>, and select it</li>
  <li>Select <strong>Add Integration</strong></li>
</ul>

<p>Now give it a helpful name and assign user access to TaskFlow.</p>

<ul>
  <li>Set the Application label to <strong><em>TaskFlow</em></strong>, and click Done.</li>
  <li>Select the <strong>Assignments</strong> tab
    <ul>
      <li>To assign it to a single user, select <strong>Assign &gt; Assign to People</strong> and choose your user</li>
      <li>To assign it to a user group, select <strong>Assign &gt; Assign to Groups</strong> and choose your user group</li>
    </ul>
  </li>
  <li>Click <strong>Done</strong></li>
</ul>

<h2 id="update-the-audience-value-of-your-resource-apps-auth-server">Update the audience value of your Resource app’s auth server</h2>

<p>By default, Okta will issue an ID-JAG token for Agent0 with the audience (<code class="language-plaintext highlighter-rouge">aud</code>) value set to that of the sample resource app (Todo0): <code class="language-plaintext highlighter-rouge">http://localhost:5001/</code>. You must change this so the ID-JAG token includes an audience value that identifies your actual resource app’s authorization server.</p>

<p>To do this, contact the Okta XAA team to replace your app’s audience value in Okta by sending an email to xaa@okta.com. Provide the following information to the Okta XAA team:</p>

<p><strong><em>Okta Integrator Org URL:</em></strong> ‘<span class="okta-preview-domain">https://{yourOktaDomain}</span>’&lt;br /&gt;
&lt;strong&gt;&lt;em&gt;Audience:&lt;/em&gt;&lt;/strong&gt; ‘http://yourresourceapps.authserver.org’
&lt;strong&gt;&lt;em&gt;Client ID from your own OAuth server:&lt;/em&gt;&lt;/strong&gt; [Agent0’s XAA client ID you created earlier]&lt;/p&gt;

&lt;p&gt;Please note that the Client ID you provide must be the client ID from your own OAuth server that was created earlier.&lt;/p&gt;

&lt;h1 id="establish-connections-between-agent0-and-your-resource-app"&gt;Establish Connections between Agent0 and your resource app&lt;/h1&gt;

&lt;p&gt;Now that you have set up both requesting and resource apps, you need to establish that Agent0 can be trusted to make requests to your resource app.&lt;/p&gt;

&lt;ul&gt;
  &lt;li&gt;Still in Admin Console, navigate to &lt;strong&gt;Applications &amp;gt; Applications &amp;gt; Agent0&lt;/strong&gt;&lt;/li&gt;
  &lt;li&gt;Go to the &lt;strong&gt;Manage Connections&lt;/strong&gt; tab&lt;/li&gt;
  &lt;li&gt;Under &lt;strong&gt;Apps providing consent&lt;/strong&gt;, select &lt;strong&gt;Add resource apps&lt;/strong&gt;, select &lt;strong&gt;TaskFlow&lt;/strong&gt;, then &lt;strong&gt;Save&lt;/strong&gt;&lt;/li&gt;
  &lt;li&gt;Confirm that your resource app appears under &lt;strong&gt;Apps providing consent&lt;/strong&gt;&lt;/li&gt;
&lt;/ul&gt;

&lt;p&gt;Now Agent0 and TaskFlow are connected.&lt;/p&gt;

&lt;p&gt;&lt;img src="/assets-jekyll/blog/xaa-resource-app/image1-723faeb6e83b3230d953dadecfc1e00b0483aa4db06c25d6c1ca30a265f504ae.jpg" alt=" " width="800" class="center-image" /&gt;&lt;/p&gt;

&lt;h1 id="validate-that-your-resource-app-and-auth-server-work-as-intended"&gt;Validate that your Resource App and Auth Server work as intended&lt;/h1&gt;

&lt;p&gt;Once the Okta XAA team confirms that your app’s audience value has been updated in Okta, Agent0 can make a Token Exchange request to Okta and will receive an ID-JAG with the correct audience.&lt;/p&gt;

&lt;p&gt;To test the end-to-end XAA flow with Agent0 to your authorization server, create a testing client that completes the following steps:&lt;/p&gt;

&lt;ol&gt;
  &lt;li&gt;Agent0 signs the user in with OIDC.&lt;/li&gt;
  &lt;li&gt;Agent0 exchanges the ID token for an ID-JAG at Okta&lt;/li&gt;
  &lt;li&gt;Agent0 makes a token request with the ID-JAG at your authorization server&lt;/li&gt;
&lt;/ol&gt;

&lt;p&gt;If you need support with taking the steps above, contact xaa@okta.com.&lt;/p&gt;

&lt;p&gt;With testing complete, consider publicizing your resource app on the Okta Integration Network (OIN) catalog. Adding it to the catalog makes it easy for Okta’s roughly 18000 enterprise customers to learn about and add it to the suite of tools on their Okta dashboards.&lt;/p&gt;

&lt;h1 id="learn-more-about-cross-app-access-oauth-20-and-securing-your-applications"&gt;Learn more about Cross App Access, OAuth 2.0, and securing your applications&lt;/h1&gt;

&lt;p&gt;If this walkthrough helped you understand more about how Cross App Access works in practice, consider learning more about&lt;/p&gt;

&lt;p&gt;📘 &lt;a href="https://xaa.dev/"&gt;xaa.dev&lt;/a&gt; - a free, open sandbox that lets you explore Cross App Access end-to-end. No local setup. No infrastructure to provision. Just a working environment where you can see the protocol in action.&lt;br /&gt;
📘 &lt;a href="https://help.okta.com/oie/en-us/content/topics/apps/apps-cross-app-access.htm"&gt;Okta’s Cross App Access Documentation&lt;/a&gt; – official guides and admin docs to configure and manage Cross App Access in production&lt;br /&gt;
🎙️ &lt;a href="https://www.youtube.com/watch?v=qKs4k5Y1x_s"&gt;Okta Developer Podcast on MCP and Cross App Access&lt;/a&gt; – hear the backstory, use cases, and why this matters for developers&lt;br /&gt;
📄 &lt;a href="https://datatracker.ietf.org/doc/draft-ietf-oauth-identity-assertion-authz-grant/"&gt;OAuth Identity Assertion Authorization Grant (IETF Draft)&lt;/a&gt; – the emerging standard that powers this flow&lt;/p&gt;
