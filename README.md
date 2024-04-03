# Onboarding On-Premises Servers to Azure with Azure Arc

This project guides you through the process of onboarding an on-premises server to Azure using Azure Arc, with the aim of leveraging the benefits of Microsoft Defender for Servers. 

For demonstration purposes, a Windows Server 2022 with Desktop experience is used. Please note that the onboarding process for a Windows Server Core would differ from the steps outlined in this project.

![Diagram](https://github.com/acibojbp/Azure-Arc/assets/164168280/d936cf17-57b6-4a14-8665-8f3aeea5c815) 

## Contents
[Add a Machine to Azure Arc](#add-a-machine-to-azure-arc)

[Integrating with Microsoft Defender for Servers](#integrating-with-microsoft-defender-for-servers)

[Configuring Data Collection in Microsoft Sentinel](#configuring-data-collection-in-microsoft-sentinel)

[Verification and Testing](#verification-and-testing)


## Add a Machine to Azure Arc

Begin by accessing the Azure Portal and entering "Azure Arc" into the search bar. Once the "Azure Arc" option appears, click on it to proceed to the next page. 

![Screenshot 2024-04-02 192756](https://github.com/acibojbp/Azure-Arc/assets/164168280/f4dde14c-a67b-413a-b72f-2443cac62ee9)

On the Azure Arc overview page, you will see various options. Choose "Machines" from the available categories. After selecting "Machines," click on "Add/Create" and then select "Add a Machine".

![Screenshot 2024-04-02 192831](https://github.com/acibojbp/Azure-Arc/assets/164168280/d4fd4ea3-8928-4c52-b89a-4843ee5c5f42)

After selecting "Add a Machine," you will be presented with additional options. Since we are only onboarding a single server for this lab, navigate to the "Add a single server" section and click "Generate script."

![Screenshot 2024-04-02 192840](https://github.com/acibojbp/Azure-Arc/assets/164168280/b1d7c683-9546-4954-8701-537e3209b9b0)

First up, we need to pick a region. Go ahead and select the one that's closest to your location.

After selecting the region, it's important to set up a resource group. Keeping a separate group for your servers is a good practice for organization. For this lab, let's create a new resource group and name it "Windows-Servers."

![Screenshot 2024-04-02 193244](https://github.com/acibojbp/Azure-Arc/assets/164168280/1ca37d38-e97a-4863-83e8-68278b78821a)

With the region and resource group in place, click on the "Download and run script" button to move on to the next page. Here, we'll find the generated PowerShell script. Click on the copy icon to copy it to our clipboard. This script will be pasted into our Windows Server in the next step to initiate the onboarding process.

![Screenshot 2024-04-02 201357](https://github.com/acibojbp/Azure-Arc/assets/164168280/97b88916-ede9-400b-8810-6deed408071b)

### Important Note for Best Practices
- Adhere to best security practices by avoiding the use of an Azure account with Owner access. Instead, use an account with Azure Connected Machine Onboarding or Azure Connected Machine Resource Administrator role assignments. [Learn more](https://aka.ms/ArcServerOnboardingSecurityPractices)

Once we've configured the region and resource group, we'll move on to the next page. Here, we'll find the generated PowerShell script. Click on the copy icon next to the script to copy it to our clipboard. This script will be pasted into our Windows Server in the next step to initiate the onboarding process.

Now, let's shift our focus to our Windows Server. To run the script, open PowerShell ISE. Make sure to run it as an administrator.

![VirtualBox_Windows Server_02_04_2024_20_08_17](https://github.com/acibojbp/Azure-Arc/assets/164168280/c599c09c-e5f5-49e5-b859-b5c75f68b015)

Create a new script then paste the copied script into PowerShell ISE. 

![VirtualBox_Windows Server_02_04_2024_20_08_44](https://github.com/acibojbp/Azure-Arc/assets/164168280/1f3e2976-918c-48b0-91a7-addad6c2620b)

Then, either click the play button or press F5 to run the script. After executing the script, wait for it to complete. You'll be prompted to log in to our Azure account.

![VirtualBox_Windows Server_02_04_2024_20_10_04](https://github.com/acibojbp/Azure-Arc/assets/164168280/32daf2b6-fe20-4b45-baa2-db9ad79e2e89)

Once the login process is completed, return to the Azure Portal. Navigate to Azure Arc and select "Servers." We should now see our machine listed there.

![Screenshot 2024-04-02 201444](https://github.com/acibojbp/Azure-Arc/assets/164168280/2a8a60ff-88a3-4ded-a3c7-beada28e7233)

## Integrating with Microsoft Defender for Servers

After successfully onboarding our server to Azure Arc, the next crucial step is to integrate it with Microsoft Defender for Servers.

Start by navigating to the Azure Portal and searching for "Microsoft Defender for Cloud." Once you've selected it from the results, move to "Environment Settings" in the Defender console and choose the subscription where we onboarded our Windows machine.

After selecting the appropriate subscription, click on the three dots (...) and choose "Edit settings." 

![Screenshot 2024-04-02 201621](https://github.com/acibojbp/Azure-Arc/assets/164168280/2e937dd5-71b6-411d-ac8e-2097750975cd)

This action will take you to the Defender plans section. Here, enable "Servers" under "Cloud Workload Protection." 

![Screenshot 2024-04-02 201717](https://github.com/acibojbp/Azure-Arc/assets/164168280/f7a07568-8ec4-4081-bab5-c7320925062b)

Upon enabling this feature, a new option will appear under "Monitoring Coverage." Click on "Settings."

![Screenshot 2024-04-02 201725](https://github.com/acibojbp/Azure-Arc/assets/164168280/86c53d7b-09f7-4fd9-80e1-90fd062c253d)

Within the settings, you'll find various functionalities of Microsoft Defender for Servers. The main change we need to make here is related to the log analytics agents. Click on "Edit configuration."

>Please note, Microsoft has announced plans to retire the Log Analytics Agent by August 2024. More information can be found [here](https://techcommunity.microsoft.com/t5/microsoft-defender-for-cloud/microsoft-defender-for-cloud-strategy-and-plan-towards-log/ba-p/3883341).

Next, change the workspace from the default setting to a Custom Workspace where Microsoft Sentinel is located. In my case, I have my Sentinel in a workspace is named "Security-Monitoring." After making these changes, click "Apply" and then "Save."

![Screenshot 2024-04-02 202017](https://github.com/acibojbp/Azure-Arc/assets/164168280/c6561f5d-e334-4ed4-9705-f482b77db17d)

![Screenshot 2024-04-02 202649](https://github.com/acibojbp/Azure-Arc/assets/164168280/8f8894ca-a670-444c-b54e-ac3050508f63)


## Configuring Data Collection in Microsoft Sentinel

Now that we've integrated our server with Microsoft Defender for Servers, the next step is to specify which data to collect. We'll achieve this by creating a data collection rule inside Microsoft Sentinel.

Begin by searching for "Microsoft Sentinel" in the Azure Portal and navigating to "Data connectors." Look for "Windows Security Events via AMA."

![Screenshot 2024-04-02 202859](https://github.com/acibojbp/Azure-Arc/assets/164168280/d2c7753f-298b-4e39-ac5e-c55258225c39)

**Note:** If you don't see this connector, you'll need to install it from the Content Hub. To do this, go to Content Hub, search for "Windows Security Events," and click "Install." Once the installation is complete, return to "Data Connectors."

![Screenshot 2024-04-02 203029](https://github.com/acibojbp/Azure-Arc/assets/164168280/c2d803af-026b-4fcb-8158-e23d6fadf952)

In the Data Connectors section, you'll now see two options. Choose "Windows Security Events via AMA (Azure Monitor Agent)." Ensure that you're not selecting the legacy agent, which is the Log Analytics Agent. So, make sure to opt for AMA.

![Screenshot 2024-04-02 203320](https://github.com/acibojbp/Azure-Arc/assets/164168280/7b11aa94-41b5-4119-bfec-21fe4afbb0bd)

Next, click on "Open connector page," followed by "Create data collection rule."  

![Screenshot 2024-04-02 203348](https://github.com/acibojbp/Azure-Arc/assets/164168280/540182f0-b77f-454f-a5fa-59ac63635e33)

Provide a descriptive name for the rule; for instance, "Windows-Server."

![Screenshot 2024-04-02 203431](https://github.com/acibojbp/Azure-Arc/assets/164168280/412aaab4-8652-4d30-a82c-485ba16265e8)

Proceed to the "Resources" section and select the resource group associated with our Windows machine.

![Screenshot 2024-04-02 203715](https://github.com/acibojbp/Azure-Arc/assets/164168280/c6d88060-ff7e-4ed9-b1f2-c602bc2c2f9b)

Click "Next: Collect." Here, you can specify which events to collect. For the purpose of this demonstration, we'll choose "All Security Events."

![Screenshot 2024-04-02 203651](https://github.com/acibojbp/Azure-Arc/assets/164168280/08baf50e-ccb6-4c88-8069-e09c4fb4157f)

**Note:** If you wish to collect specific custom events, you can find the event IDs [here](https://learn.microsoft.com/en-us/azure/sentinel/windows-security-event-id-reference).

Finally, click "Review + Create" to finalize the data collection rule.

## Verification and Testing

To ensure that everything is set up correctly, let's perform some verification steps.

Navigate to Microsoft Defender for Cloud and click on "Inventory." You should be able to see our machine listed here, with Defender for Cloud now enabled.

![Screenshot 2024-04-02 205136](https://github.com/acibojbp/Azure-Arc/assets/164168280/42ff1968-de49-448d-a1ed-2fe93cda651b)

Query "SecurityEvent" in Microsoft Sentinel. You should see all the security events from our machine listed.

![Screenshot 2024-04-02 205225](https://github.com/acibojbp/Azure-Arc/assets/164168280/f2025677-97b3-4798-81e1-6de5ebe03ca2)

>**Note:** With Defender for Server Plan 2, we are allocated 500 MB per VM per day.

To generate some telemetry, try signing out of the server and then attempt to sign back in with an incorrect password. This action should generate a log in Sentinel indicating a "failed login attempt."

![Screenshot 2024-04-02 205600](https://github.com/acibojbp/Azure-Arc/assets/164168280/aca048e4-c74e-4f2a-9bd4-c2aa39b5d23e)

![Screenshot 2024-04-02 205516](https://github.com/acibojbp/Azure-Arc/assets/164168280/0de62cf0-ac2f-4c80-bb8a-722cd5641db5)

By following these verification and testing steps, we can confirm that we have successfully onboarded our machine to Microsoft Defender for Servers using Azure Arc.
