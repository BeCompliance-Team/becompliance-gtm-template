# Be.Aliant CMP - Google Tag Manager Template

The official Google Tag Manager (GTM) custom template for **Be.Aliant** (by BeCompliance). 

This template provides a seamless, codeless integration between the Be.Aliant Consent Management Platform (CMP) and **Google Consent Mode v2**.

## 🚀 Features

* **Out-of-the-box Consent Mode v2:** Automatically sets the native GTM `default` consent state before any Google tags fire.
* **Privacy by Default:** Sets `ad_storage`, `analytics_storage`, `ad_user_data`, and `ad_personalization` to `denied` initially.
* **Secure Injection:** Safely injects the Be.Aliant CMP script using GTM's sandboxed JavaScript APIs.
* **Native Integration:** Fully compatible with GTM's "Consent Initialization" trigger to prevent data leakage.
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
3. Fill in the **BeCompliance Banner ID (Client ID)** field with your unique alphanumeric code (available in your Be.Aliant dashboard).

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

## 6. Verification and Support
After publishing, use GTM's **Preview** mode. The Be.Aliant template will output logs in the console (browser Console tab or within the Tag Assistant panel itself) confirming the script loading and the injection of consent states.

## ⚙️ Configuration Fields

* **Client ID (Required):** Your unique alphanumeric identifier. Example: `ae3e2166-c2c3-4726-848a-1d28fb5ab1a7`.

## 📚 Documentation & Support

If you need help setting up Basic or Advanced Consent Mode, or generating your Client ID, please refer to our official resources:

* **Setup Guide & Documentation:** [Be.Aliant Google Consent Mode Documentation](https://testegoogle.becompliance.com/lgpd/cookies/public-info/google-consent-mode)
* **Official Website:** [becompliance.com](https://becompliance.com)


---
*Developed and maintained by the Be.Aliant/BeCompliance tech team.*
