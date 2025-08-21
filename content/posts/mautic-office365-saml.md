---
Title: "Setting Up Mautic with Office365 SSO"
Image: "/images/mautic-logo.webp"
Description: "A guide to configuring Mautic with Office365 Single Sign-On (SSO)"
Date: 2023-09-04T08:01:00-00:00
Draft: false
Tags: ["Technology", "Integration", "SSO", "Office365"]
Series: "Technology Insights"
---
## Background
Recently, I deployed [Mautic](https://www.mautic.org/) to run email campaigns. I find it quite impressive, although there is room for improvement in the cron-based scheduling. Regardless, it gets the job done, making it a cost-effective solution for email campaigns. The cost-effectiveness is possible because [Kayaking St. Augustine](https://kayakingstaugustine.com) uses Office365 for email hosting. I set up a Postfix container and connected it to Office365, with Mautic utilizing local SMTP.

![Mautic Dashboard](/images/mautic-dashboard.webp)

The next step was to connect Mautic to Office365.

## Setting up SSO
### Microsoft Entra

Log in to Azure AD, or rather, *Microsoft Entra*.

Navigate to:

**Identity -> Applications -> Enterprise Applications -> New Application**

![Microsoft Entra 1](/images/mautic-entra-1.webp)

Click **Create your own application** and give it a name like "Mautic." Select **Integrate any other application you can't find in the gallery (Non-gallery)**.

![Microsoft Entra 2](/images/mautic-entra-2.webp)

Once that's done, assign some users and groups to the application. **Manage -> Users and groups -> Add user/group**.

![Microsoft Entra 3](/images/mautic-entra-3.webp)

Now, go to **Single sign-on** and select SAML Authentication.

Click "Edit" under **Basic SAML Configuration** and input the following:

* **Identifier (Entity ID)**: `https://mautic.yourdomain.com` (your main Mautic URL).
* **Reply URL (Assertion Consumer Service URL)**: `https://mautic.yourdomain.com/s/saml/login_check` (your Mautic domain + `/s/saml/login_check`).
* **Logout URL**: `https://mautic.yourdomain.com/s/saml/login_check` (your Mautic domain + `/s/logout`).

![Microsoft Entra 4](/images/mautic-entra-4.webp)

![Microsoft Entra 5](/images/mautic-entra-5.webp)

Next, download the **SAML Certificate (Base64)** and **Federation Metadata XML**.

With these files downloaded, it's time to configure Mautic.

### Mautic

Before setting up Authentication, create a group to map Office365 users to, with the desired permissions. **Gear icon in the top right -> Configuration -> Roles**.

From the dashboard, go to **Gear icon in the top right -> Configuration -> User/Authentication Settings**.

Upload the Federation Metadata XML under **Identity provider metadata xml**.

Select the group you created under **Default Role for created users**.

In the Attribute fields, fill out the following settings:
* **Email**: `email`
* **First Name**: `givenname`
* **Last Name**: `surname`
* **Username**: `nameidentifier`

Finally, upload the SAML Certificate under **X.509 Certificate**.

![Mautic Control Panel](/images/mautic-controlpanel.webp)

Open a new browser instance and try to go to your Mautic URL. You will now be redirected to an Office 365 login page.