# Onboarding On-Premises Servers to Azure with Azure Arc

This project guides you through the process of onboarding an on-premises server to Azure using Azure Arc, with the aim of leveraging the benefits of Microsoft Defender for Servers. 

For demonstration purposes, a Windows Server 2022 with Desktop experience is used. Please note that the onboarding process for a Windows Server Core would differ from the steps outlined in this project.

## Contents
[Add a Machine to Azure Arc](#add-a-machine-to-azure-arc)

[Integrating with Microsoft Defender for Servers](#integrating-with-microsoft-defender-for-servers)

[Configuring Data Collection in Microsoft Sentinel](#configuring-data-collection-in-microsoft-sentinel)

[Verification and Testing](#verification-and-testing)


## Add a Machine to Azure Arc

Begin by accessing the Azure Portal and entering "Azure Arc" into the search bar. Once the "Azure Arc" option appears, click on it to proceed to the next page. 

On the Azure Arc overview page, you will see various options. Choose "Machines" from the available categories. After selecting "Machines," click on "Add/Create" and then select "Add a Machine".

After selecting "Add a Machine," you will be presented with additional options. Since we are only onboarding a single server for this lab, navigate to the "Add a single server" section and click "Generate script."

First up, we need to pick a region. Go ahead and select the one that's closest to your location.

After selecting the region, it's important to set up a resource group. Keeping a separate group for your servers is a good practice for organization. For this lab, let's create a new resource group and name it "Windows-Servers."

With the region and resource group in place, click on the "Download and run script" button to move on to the next page. Here, we'll find the generated PowerShell script. Click on the copy icon to copy it to our clipboard. This script will be pasted into our Windows Server in the next step to initiate the onboarding process.

### Important Note for Best Practices
- Adhere to best security practices by avoiding the use of an Azure account with Owner access. Instead, use an account with Azure Connected Machine Onboarding or Azure Connected Machine Resource Administrator role assignments. [Learn more](https://aka.ms/ArcServerOnboardingSecurityPractices)

Once we've configured the region and resource group, we'll move on to the next page. Here, we'll find the generated PowerShell script. Click on the copy icon next to the script to copy it to our clipboard. This script will be pasted into our Windows Server in the next step to initiate the onboarding process.

Now, let's shift our focus to our Windows Server. To run the script, open PowerShell ISE. Make sure to run it as an administrator.

Create a new script then paste the copied script into PowerShell ISE. Then, either click the play button or press F5 to run the script.
After executing the script, wait for it to complete. You'll be prompted to log in to our Azure account.

Once the login process is completed, return to the Azure Portal. Navigate to Azure Arc and select "Servers." We should now see our machine listed there.

## Integrating with Microsoft Defender for Servers

After successfully onboarding our server to Azure Arc, the next crucial step is to integrate it with Microsoft Defender for Servers.

Start by navigating to the Azure Portal and searching for "Microsoft Defender for Cloud." Once you've selected it from the results, move to "Environment Settings" in the Defender console and choose the subscription where we onboarded our Windows machine.

After selecting the appropriate subscription, click on the three dots (...) and choose "Edit settings." This action will take you to the Defender plans section. Here, enable "Servers" under "Cloud Workload Protection." Upon enabling this feature, a new option will appear under "Monitoring Coverage." Click on "Settings."

Within the settings, you'll find various functionalities of Microsoft Defender for Servers. The main change we need to make here is related to the log analytics agents. Click on "Edit configuration."

Please note, Microsoft has announced plans to retire the Log Analytics Agent by August 2024. More information can be found [here](https://techcommunity.microsoft.com/t5/microsoft-defender-for-cloud/microsoft-defender-for-cloud-strategy-and-plan-towards-log/ba-p/3883341).

Next, change the workspace from the default setting to a Custom Workspace where Microsoft Sentinel is located. In my case, I have my Sentinel in a workspace is named "Security-Monitoring." After making these changes, click "Apply" and then "Save."

## Configuring Data Collection in Microsoft Sentinel

Now that we've integrated our server with Microsoft Defender for Servers, the next step is to specify which data to collect. We'll achieve this by creating a data collection rule inside Microsoft Sentinel.

Begin by searching for "Microsoft Sentinel" in the Azure Portal and navigating to "Data connectors." Look for "Windows Security Events via AMA."

**Note:** If you don't see this connector, you'll need to install it from the Content Hub. To do this, go to Content Hub, search for "Windows Security Events," and click "Install." Once the installation is complete, return to "Data Connectors."

In the Data Connectors section, you'll now see two options. Choose "Windows Security Events via AMA (Azure Monitor Agent)." Ensure that you're not selecting the legacy agent, which is the Log Analytics Agent. So, make sure to opt for AMA.

Next, click on "Open connector page," followed by "Create data collection rule." Provide a descriptive name for the rule; for instance, "Windows-Server." Proceed to the "Resources" section and select the resource group associated with our Windows machine.

Click "Next: Collect." Here, you can specify which events to collect. For the purpose of this demonstration, we'll choose "All Security Events."

**Note:** If you wish to collect specific custom events, you can find the event IDs [here](https://learn.microsoft.com/en-us/azure/sentinel/windows-security-event-id-reference).

Finally, click "Review + Create" to finalize the data collection rule.

## Verification and Testing

To ensure that everything is set up correctly, let's perform some verification steps.

Navigate to Microsoft Defender for Cloud and click on "Inventory." You should be able to see our machine listed here, with Defender for Cloud now enabled.

Query "SecurityEvent" in Microsoft Sentinel. You should see all the security events from our machine listed.

**Note:** With Defender for Server Plan 2, we are allocated 500 MB per VM per day.

To generate some telemetry, try signing out of the server and then attempt to sign back in with an incorrect password. This action should generate a log in Sentinel indicating a "failed login attempt."

By following these verification and testing steps, we can confirm that we have successfully onboarded our machine to Microsoft Defender for Servers using Azure Arc.
