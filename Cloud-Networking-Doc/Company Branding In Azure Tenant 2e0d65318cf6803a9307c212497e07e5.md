# Company Branding In Azure Tenant

### **Introduction** & Objective

With the technical identity (`virusinferno.xyz`) established, my next goal was to establish the *visual* identity of the organization. By default, the Microsoft sign-in screen is generic.

In this project, I configured the **Company Branding** settings within Microsoft Entra ID. The objective was to ensure that whenever a user logs in to my tenant, they are greeted with the **VirusInferno** logo, color scheme, and custom background, rather than the standard Microsoft look. This builds trust and confirms to users that they are accessing the correct corporate resources.

## Asset Preparation (Prerequisites)

I learned that Microsoft is extremely strict regarding image dimensions and file sizes for branding assets. Before opening the Azure Portal, I spent time preparing my graphics to meet these specific requirements:

- **Favicon:** I designed a 32x32 pixel icon (PNG) and compressed it to be **under 5KB**.
- **Background Image:** I selected a high-quality 1920x1080 pixel image representing my brand and ensured it was compressed to **under 300KB**.
- **Header Logo:** I prepared a banner-style logo (approx. 245x36 pixels) and kept it **under 10KB**.

## Implementation Steps

### Step 1: Accessing the Branding Portal

I logged into the **Microsoft Entra Admin Center** and navigated to the User Experience settings.

- **Path:** **Identity** > **User experiences** > **Company branding**.
- **Action:** I clicked on **"Edit"** (or "Customize") on the Default Sign-in experience to begin the configuration.

> 
> 
> 
> ![image.png](image%2081.png)
> 

### Step 2: Configuring Basic Visuals

On the "Basics" tab, I uploaded my primary assets.

1. **Favicon:** I uploaded the 32x32 icon. This will appear in the web browser tab.
2. **Background Image:** I uploaded the 1920x1080 image.
3. **Background Color:** I set a fallback hex color (e.g., `#000000` or `#FFFFFF`) to match my brand in case the image fails to load on slow connections.

> 
> 
> 
> ![image.png](image%2082.png)
> 

### Step 3: Configuring Layout & Header

I moved to the "Layout" and "Header" tabs to refine the login box.

- **Header Logo:** I uploaded the banner logo. This appears at the top of the white sign-in box.
- **Sign-in Page Text:** I decided to keep the layout standard to ensure the focus remained on the login fields.

### Step 4: Footer Configuration

To ensure compliance and professionalism, I added legal links in the "Footer" section.

- **Privacy & Terms:** I filled in the text fields for "Privacy Policy" and "Terms of Use" (using placeholder URLs for now).
- **Copyright:** I added the text: *"© 2026 VirusInferno Tech Ltd."*

## Testing the Experience

After saving the configuration, I needed to verify that the branding was active.

- **Method:** I opened an **Incognito/Private** browser window.
- **Action:** I navigated to `portal.azure.com` and entered a user email associated with my tenant (e.g., `oluwasheyi@virusinferno.xyz`).
- **Result:** As soon as I tabbed out of the email field, the generic Microsoft background instantly switched to my custom **VirusInferno** background and logo.

> 
> 
> 
> [WhatsApp Video 2026-01-07 at 04.50.35.mp4](WhatsApp_Video_2026-01-07_at_04.50.35.mp4)
> 

## Summary

I have successfully transformed the user experience. My Azure Tenant now looks like a proprietary enterprise environment. Any employee logging in will immediately recognize the **VirusInferno** brand, adding a layer of professionalism and security to the platform.

**NEXT PAGE HERE👇👇👇**

[Azure Entra ID IAM - Single User Management](Azure%20Entra%20ID%20IAM%20-%20Single%20User%20Management%202e0d65318cf680958905c9d9f39de20f.md)