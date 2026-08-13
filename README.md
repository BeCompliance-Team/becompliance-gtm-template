# Be.Aliant CMP - Google Tag Manager Template

The official Google Tag Manager (GTM) custom template for **Be.Aliant** (by BeCompliance). 

This template provides a seamless, codeless integration between the Be.Aliant Consent Management Platform (CMP) and **Google Consent Mode v2**.

## 🚀 Features

* **Out-of-the-box Consent Mode v2:** Automatically sets the native GTM `default` consent state before any Google tags fire.
* **Privacy by Default:** Sets `ad_storage`, `analytics_storage`, `ad_user_data`, and `ad_personalization` to `denied` initially.
* **Secure Injection:** Safely injects the Be.Aliant CMP script using GTM's sandboxed JavaScript APIs.
* **Native Integration:** Fully compatible with GTM's "Consent Initialization" trigger to prevent data leakage.
* **Transmission Controls:** emits `url_passthrough` and `ads_data_redaction` as proper `set` commands, before the default — the only form gtag actually honours.
* **Single Source of Truth:** signals the banner that the default state has already been published, so it is not emitted twice.
* **Debug Support:** Outputs clear initialization logs to the GTM Preview console for easy troubleshooting.

# Implementation Guide: Google Consent Mode v2 with Be.Aliant (via GTM)

This guide provides step-by-step instructions on configuring the Be.Aliant Cookie Banner, natively integrated with Google Consent Mode v2 through Google Tag Manager (GTM).

## 1. Adding the Be.Aliant Template to GTM
To ensure a secure integration with no data leakage, use our official template:
1. Access your Google Tag Manager workspace.
2. On the left menu, click **Templates**.
3. In the **Tag Templates** section, click **Search Gallery**.
4. Search for **Be.Aliant** and add the template to your workspace.

## 2. Creating and Configuring the Tag
1. Go to the **Tags** menu and click **New**.
2. Click **Tag Configuration** and select the **Be.Aliant** custom template you just added.
3. Fill in the **BeCompliance Banner ID (Client ID)** field. In your Be.Aliant dashboard, open the banner and use the **Copy** button on the **"ID para configuração no GTM"** field — the value already comes in the `{company}/{banner}` format required here (e.g. `1234/a1b2c3d4-0000-4000-8000-000000000000`). Pasting only the banner UUID produces an incomplete URL and the banner will not load.

## 3. Choosing the Operation Mode (Basic vs. Advanced Mode)
Google Consent Mode v2 can operate in two distinct modes. Our template allows you to choose the best approach for your business through the **"Enable Google Consent Mode v2 (Recommended)"** checkbox.

### Option A: Advanced Mode
**How to configure:** Keep the "Enable Google Consent Mode v2" checkbox **CHECKED**.
* **How it works:** The Be.Aliant template will automatically send a default block signal (`default denied`) to Google tags before the page loads. Google tags (Analytics, Ads) will fire immediately, but in a restricted mode, collecting only anonymous pings without using cookies. When the user accepts the banner, the tags will be updated to full mode.
* **Benefit:** Enables conversion and behavioral modeling in Google Analytics 4 and Google Ads for users who have not granted consent.

### Option B: Basic Mode
**How to configure:** **UNCHECK** the "Enable Google Consent Mode v2" checkbox.
* **How it works:** The Be.Aliant template will only inject the visual banner onto your page, **without** sending the default state command to Google. 
* **Mandatory Action:** In this mode, you are strictly responsible for going into the settings of each Google tag within your GTM and configuring **Additional Consent Checks** (blocking rules). Google tags must only fire *after* the user's consent event. No data (not even anonymous pings) will be sent to Google prior to explicit consent.

## 4. Regional Settings (Default Consent)
If you enabled Advanced Mode, you will need to define the default consent rules. Laws vary by country, and our template allows for flexible configurations.

In the **Regional Settings (Default Consent)** section, you can add rows to the table to dictate how tags should behave initially:
* **Global Block (Recommended):** Leave the "Region" field blank and set the parameters (`ad_storage`, `analytics_storage`, `ad_user_data`, `ad_personalization`) to **Denied**. This blocks cookies for all users worldwide until they accept the banner.
* **Country-Specific Rules:** If you wish to apply different rules by territory, click "Add Row". Type the two-letter ISO code in the Region field (e.g., **BR** for Brazil, **US-CA** for California) and adjust the parameters to **Granted** or **Denied** according to your legal guidance.

## 5. The Mandatory Trigger
To prevent data leakage, the Be.Aliant tag must be the very first to execute on your site.
1. Scroll down the tag page to the **Triggering** section.
2. Click to add a trigger and select **EXACTLY** this option: **Consent Initialization - All Pages**.
3. Save your Tag and publish the GTM container.

## 6. Google tags outside the container

The Consent Initialization trigger guarantees the order **inside** the container: no tag in it fires before this template publishes the consent state. If all your Google tags live in GTM, you are covered and nothing else is needed.

The gap is a Google tag pasted directly into the page HTML, above the GTM snippet. The GTM snippet is `async`, so it loads in parallel with the HTML parsing — a synchronous `gtag.js` in the `<head>` runs *during* parsing, before the container exists. With no consent default queued yet, gtag assumes `granted`. No amount of reordering inside the template fixes this: nothing in the container can run before the container itself.

If that is your case, add our synchronous bootstrap as the **first line** of the `<head>`, above any Google tag:

```html
<script src="https://cdn-api-cmp.becompliance.com/client-side/inject.js"></script>
```

It is a static file, identical for every domain, and it publishes the denied default on the page's first byte. It works alongside this template — the bootstrap covers tags that fire before GTM boots, while `setDefaultConsentState` feeds GTM's internal consent model, which is what makes the container's consent checks work.

The alternative, if you prefer not to touch the HTML, is to move those tags into the GTM container.

## 7. Where the default consent state can come from

Three places can define it, and mixing them without intent leads to surprises:

* **This tag's Regional Settings table** — applies to everything the container fires.
* **Global Consent Defaults**, configured on Google's side — relevant if you use Google tag gateway.
* **The banner's regional configuration** in the Be.Aliant dashboard.

Per Google's rule, a command carrying a `region` beats a global one. If you set Global Consent Defaults or Data Transmission Controls, also review the banner's regional configuration in the platform, so the state sent to Google stays coherent — in regions where you choose not to display the banner, the CMP grants consent automatically.

## 8. Verification and Support
After publishing, use GTM's **Preview** mode. The Be.Aliant template will output logs in the console (browser Console tab or within the Tag Assistant panel itself) confirming the script loading and the injection of consent states.

## ⚙️ Configuration Fields

* **Client ID (Required):** the value from the **"ID para configuração no GTM"** field in your Be.Aliant dashboard, already in the `{company}/{banner}` format. Example: `1234/a1b2c3d4-0000-4000-8000-000000000000`.
* **Enable Google Consent Mode v2 (default: on):** publishes the default consent state and tells the banner it does not need to publish it again.
* **Enable url_passthrough (default: on):** preserves campaign identifiers (`gclid`, `dclid`) in the URL while the user has not consented to cookies.
* **Enable ads_data_redaction (default: on):** redacts data sent to ad tags while `ad_storage` is denied. Both are separate `set` commands — inside the consent object gtag silently discards them.

## 📚 Documentation & Support

If you need help setting up Basic or Advanced Consent Mode, or generating your Client ID, please refer to our official resources:

* **Setup Guide & Documentation:** [Be.Aliant Google Consent Mode Documentation](https://cookies.becompliance.com/)
* **Official Website:** [becompliance.com](https://becompliance.com)


---
*Developed and maintained by the Be.Aliant/BeCompliance tech team.*
