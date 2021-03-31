# Azure Fundementals: Identity Step - by - Step

**Contents**

<!-- TOC -->

- [Azure Fundementals: Identity Step - by - Step](#azure-fundementals-identity-step---by---step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Exercise 1: Integrate an Active Directory forest with an Azure Active Directory tenant](#exercise-1-integrate-an-active-directory-forest-with-an-azure-active-directory-tenant)
    - [Task 1: Create an Azure Active Directory tenant and activate an EMS E5 trial](#task-1-create-an-azure-active-directory-tenant-and-activate-an-ems-e5-trial)
    - [Task 2: Create and configure Azure AD users](#task-2-create-and-configure-azure-ad-users)
    - [Task 3: Purchase a custom domain name  **OPTIONAL**](#task-3-purchase-a-custom-domain-name--optional)
    - [Task 4: Assign a custom domain name to the Contoso Azure AD tenant **OPTIONAL**](#task-4-assign-a-custom-domain-name-to-the-contoso-azure-ad-tenant-optional)
    - [Task 5: Configure DNS suffix in the Contoso Active Directory forest](#task-5-configure-dns-suffix-in-the-contoso-active-directory-forest)
    - [Task 6: Install Azure AD Connect](#task-6-install-azure-ad-connect)
    - [Task 7: Enable Active Directory Recycle Bin](#task-7-enable-active-directory-recycle-bin)
    - [Task 8: Configure Azure AD Connect attribute-level filtering](#task-8-configure-azure-ad-connect-attribute-level-filtering)
    - [Task 9: Initiate and verify directory synchronization](#task-9-initiate-and-verify-directory-synchronization)
    - [Task 10: Configure Hybrid Azure AD join](#task-10-configure-hybrid-azure-ad-join)
    - [Task 11: Perform Hybrid Azure AD join (OPTIONAL)](#task-11-perform-hybrid-azure-ad-join-optional)
  - [Exercise 2: Manage Authentication, Authorization, and Access Control in Hybrid Scenarios](#exercise-2-manage-authentication-authorization-and-access-control-in-hybrid-scenarios)
    - [Task 1: Create Active Directory groups](#task-1-create-active-directory-groups)
    - [Task 2: Assign EMS E5 licenses to Azure AD users](#task-2-assign-ems-e5-licenses-to-azure-ad-users)
    - [Task 3: Enable Azure AD Multi-Factor Authentication](#task-3-enable-azure-ad-multi-factor-authentication)
    - [Task 4: Enable password writeback and Self-Service Password Reset](#task-4-enable-password-writeback-and-self-service-password-reset)
    - [Task 5: Implement Azure AD Password Protection for Windows Server Active Directory **OPTIONAL**](#task-5-implement-azure-ad-password-protection-for-windows-server-active-directory-optional)
    - [Task 6: Enable Azure Active Directory Identity Protection](#task-6-enable-azure-active-directory-identity-protection)
    - [Task 7: Enable Automatic Intune Enrollment **OPTIONAL**](#task-7-enable-automatic-intune-enrollment-optional)
    - [Task 8: Enable Enterprise-State-Roaming **OPTIONAL**](#task-8-enable-enterprise-state-roaming-optional)
    - [Task 9: Implement Azure AD Conditional Access Policies](#task-9-implement-azure-ad-conditional-access-policies)
    - [Task 10: Implement Azure AD Privileged Identity Management **OPTIONAL**](#task-10-implement-azure-ad-privileged-identity-management-optional)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Delete resources](#task-1-delete-resources)

<!-- /TOC -->

## Abstract and learning objectives 

In this hands-on lab you will setup and configure a number of different hybrid identity scenarios. The scenarios involve an Active Directory single-domain forest named contoso.local, which in this lab environment, consists (for simplicity reasons) of a single domain controller named DC1 and a single domain member server named APP1. The intention is to explore Azure AD-related capabilities that allow you to integrate Active Directory with Azure Active Directory, optimize hybrid authentication and authorization, and provide secure access to on-premises resources from Internet for both organizational users and users who are members of partner organizations. 

## Overview

Contoso has asked you to integrate their on-premises Active Directory single-domain forest named contoso.local with Azure AD and implement all necessary prerequisites to allow them to benefit from such Azure AD features as single sign-on to cloud and on-premises applications, enhanced sign-in security with Multi-Factor Authentication and Windows Hello for Business, Hybrid Azure AD join, Self-Service Password Reset and Password Protection, automatic enrollment of Windows 10 devices into Microsoft Intune, and Azure AD Privileged Identity Protection. They want to also provide secure access to their on-premises, Windows Integrated Authentication-based applications from Internet for both organizational users and users who are members of partner organizations, although they also want to be able to loosen restrictions when access originates from Hybrid Azure AD joined computers residing in their on-premises data centers. The same applications need to also be made available to Contoso's business partners. 

## Solution architecture

From the architectural standpoint, the deployment will consist of the following components:

-   On-premises Active Directory environment consisting of a single domain controller (DC1) and one domain member server (APP1), running Windows Server 2016 operating system. 

-   Contoso Azure AD tenant

-   Fabrikam Azure AD tenant

    ![High level architecture consisting of the on-premises environment represented by a rectangle on the left-hand side, two cloud outlines representing the Azure AD tenant of Contoso and Fabrikam on the right-hand side, and the Microsoft Intune icon in the middle. The on-premises environment contains an icon representing Active Directory domain controllers, providing such functionality as Azure AD Connect-based synchronization with attribute level filtering and password writeback, Azure AD Application Proxy with its on-premises connector, Service Connection Point for Hybrid Azure AD join, and Password Protection DC Agent. There is also a web server icon, representing the hybrid Azure AD joined server hosting the APP1 application, used also as the Password Application Proxy. The Contoso Azure AD tenant provides such functionality as Azure AD application proxy, My Apps portal, Automatic Intune enrollment, Enterprise State Roaming, Conditional Access, Azure AD Identity Protection, Azure AD Privileged Identity Management, Azure AD MFA, and Self-Service Password Reset.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/HOL_Architecture_Overview.png "Architecture Overview")

## Exercise 1: Integrate an Active Directory forest with an Azure Active Directory tenant

Duration: 150 minutes

**Overview**

In this exercise, you will integrate an Active Directory forest with an Azure Active Directory tenant by creating an Azure Active Directory tenant and activating an Enterprise Mobility + Security E5 trial, creating and configuring an Azure AD user, purchasing a custom domain name, assigning a custom domain name to the Contoso Azure AD tenant, configuring DNS suffix in the Contoso Active Directory forest, installing Azure AD Connect, enable Active Directory Recycle Bin, configuring Azure AD Connect attribute-level filtering, initiating and verifying directory synchronization, configuring Hybrid Azure AD join, and performing Hybrid Azure AD join of a Windows Server 2016 VM.

### Task 1: Create an Azure Active Directory tenant and activate an EMS E5 trial

In this task, you will create an Azure Active Directory tenant with the following settings: 

-   Organization name: **Contoso**

-   Initial domain name: any valid, unique domain name.

-   Country or region: **United States**

1. From the lab computer, start a new Web browser window and navigate to the Azure portal at <https://portal.azure.com> if you haven't already.

2. When prompted, sign into the Azure subscription into which you deployed resources in the Before Hands-On Lab exercises.

3. On the lab computer, in the Azure portal, select **+ Create a resource**.

4. On the **New** blade, in the **Search the Marketplace** text box, type **Azure Active Directory** and, in the list of results, select **Azure Active Directory**.

    ![A screenshot that depicts the New blade. In the New blade, Azure Active Directory is searched for in the Search the Marketplace text box and selected from the search results.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SearchAD.png "Search for and select Azure Active Directory")

5. On the **Azure Active Directory** blade, select **Create**.

6. On the **Create directory** blade, specify the following settings and select **Create**:

    -   Organization name: **Contoso**

    -   Initial domain name: any valid, unique domain name.

    -   Country or region: **United States**

7. Once it's created, navigate to your subscription blade. Select **Change directory**.

    ![A screenshot that depicts the Subscription page. On the Subscription page, the 'Change directory' button is selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ChangeDirectory.png "Select the Change Directory button")

8. In the **Change the directory** blade on the right, select **Contoso** in the dropdown and select **Change**. 

    ![A screenshot that depicts the Change the Directory blade. On the Change the Directory blade, Contoso is selected in the dropdown and the 'Change' button is selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ChangeContoso.png "Select Contoso and the Change button")

9. In the portal's left navigation, select **Azure Active Directory**. 

10. In the **Azure Active Directory** blade, select **Switch tenant** then select the **Contoso** box and select **Switch**. 

    >**Note:** It may take a few minutes for everything to display properly.

    ![A screenshot that depicts the 'Switch tenant' blade. On the 'Switch tenant' blade, the Contoso directory is selected and the Switch button is selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/line148Update.png "Switch to the Contoso directory")

11. On the **Contoso - Overview** blade, select **Licenses** under **Manage** on the left navigation.

12. On the **Contoso - Licenses**, blade, select **All Products** and select **+ Try/Buy**.

13. On the **Activate** blade, in the **ENTERPRISE MOBILITY + SECURITY E5** section, select **Free trial** and then select **Activate**.

    ![A screenshot that depicts the Licenses - All products blade with the Active blade open. On the Active blade, under 'Enterprise Mobility + Security E5 free trial', then Activate button is selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ActivateTrial.png "Activate Enterprise Mobility + Security E5 free trial")

   > **Note**: Activation typically takes about 5 minutes.

### Task 2: Create and configure Azure AD users

In this task, you will configure Azure AD user accounts in the newly created Azure AD tenant with the following settings. This will include assigning EM+S E5 licenses to the user account you are using for this lab as well as creating a new Azure AD user account with the following settings and assigning to it the Global Administrator role as well as the EM+S E5 license.

- Name: **john.doe**

- First name: **John**

- Last name: **Doe**
    
- Password: **Auto-generate password**
    
- Show Password: **Enabled**

- Groups: **0 group selected**
    
- Roles: **Global Administrator**
    
- Block sign in: **No**
    
- Usage location: **United States**
    
- Job title: leave blank
    
- Department: leave blank

1. From the lab computer, in the Azure portal, navigate back to the **Contoso - Overview** blade.

2. On the **Contoso - Overview** blade, select **Users** under **Manage** in the left navigation.

3. On the **Users - All users** blade, select the entry representing your user account.

4. On the **Profile** blade of your user account, select **Edit**.

    ![Screenshot that depicts the Profile blade with the Edit button select](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SelectEdit.png "Select Edit on the user account Profile blade")

5. In the **Settings** section, in the **Usage location** drop-down list, select the **United States** entry and select **Save**.

    ![Screenshot that depicts United States being selected in the Usage location dropdown menu of the Profile blade](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SettingsUsageLocation.png "Select United States for the Usage location")

6. On the **Profile** blade of your user account, select **Licenses** under **Manage** on the left. 

7. On the **Licenses** blade, select **+ Assignments**.

8. On the **Update license assignments** blade, enable the **Enterprise Mobility + Security E5** checkbox, ensure that all the corresponding license options are enabled, and select **Save**.

    ![Screenshot that depicts the Update license assignments blade with Enterprise Mobility + Security E5 and it's subsequent license options selected. The Save button is also selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/UpdateLicenseAssignments.png "Update license assignments")

9. On the **Users - All users** blade, select **+ New user**.

10. On the **New user** blade, ensure that the **Create user** option is selected, specify the following settings, and select **Create**:

    - User name: **john.doe\@*your Azure AD tenant domain name*** where ***your Azure AD tenant domain name*** is the domain name you specified when creating the Contoso Azure AD tenant.

    - Name: **john.doe**

    - First name: **John**

    - Last name: **Doe**
    
    - Password: **Auto-generate password**
    
    - Show Password: **Enabled**

    - Groups: **0 group selected**
    
    - Roles: **Global Administrator**
    
    - Block sign in: **No**
    
    - Usage location: **United States**
    
    - Job title: **leave blank**
    
    - Department: **leave blank**

    ![Screenshot that depicts the New user blade with all the specified settings selected and the Create button selected](images/Hands-onlabstep-bystep-HybridIdentityImages/media/NewUser.png "Add a new user with the listed settings")

    > **Note**: Copy the **User name** and **Password** values into Notepad. You will need them later in this lab.

11. On the **Users - All users** blade, select the entry representing the newly created user account.

12. On the **john.doe - Profile** blade, select **Licenses** under **Manage** on the left.

13. On the **john.doe - Licenses** blade, select **+ Assignments**.

14. On the **Update license assignments** blade, enable the **Enterprise Mobility + Security E5** checkbox, ensure that all the corresponding license options are enabled, and select **Save**.

    ![Screenshot that depicts the Update license assignments blade with Enterprise Mobility + Security E5 and it's subsequent license options selected. The Save button is also selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/UpdateLicenseAssignments2.png "Update license assignments")

### Task 3: Purchase a custom domain name  **OPTIONAL**

**THIS STEP IS OPTIONAL**
In this task, you will purchase a custom DNS domain name by leveraging the functionality described at <https://docs.microsoft.com/en-us/azure/app-service/manage-custom-dns-buy-domain>.

1. On the lab computer, in the browser displaying the Azure portal, navigate to your subscription blade and select **Change directory**. In the **Change the directory** blade on the right, select **Default Directory** in the dropdown and select **Change**. 
   
2. Return to the **Azure Active Directory** overview blade. Select **Switch tenant** and select the **Default Directory** associated with the Azure subscription into which you deployed resources in the Before Hands-On Lab exercises then select **Switch**. 

3. In the Azure portal's left navigation, select **+ Create a resource**.

4. On the **New** blade, select **Web App**.

    ![Screenshot that depicts the New blade with 'Web App' selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SelectWebApp.png "Select web app resource")

5. On the **Basics** tab of the **Web App** blade, specify the following settings and select **Next: Monitoring**:

    - Subscription: the name of the Azure subscription into which you deployed resources in the Before Hands-On Lab exercises.

    - Resource Group: **(Create new)ncontosohilab-RG**.

    - Name: any valid, globally unique name.
    
    - Publish: **Code**

    - Runtime stack: **.NET Core 3.1 (LTS)**

    - Operating system: **Windows**

    - Region: any Azure region in which you can create Azure Web Apps in the target subscription.

    - App Service plan: accept the default.

    - SKU and size: **Shared D1** (If necessary, select **Change size**, select Dev/Test, select **D1** and select **Apply**)

    ![Screenshot that depicts the Basics tab of the Web App blade with the above listed settings selected and the Next: Monitoring button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/15juneupdate2.png "Web app basics information")
  
6. On the **Monitoring** tab of the **Web App** blade, specify the following setting and select **Review + create** then **Create**:

    - Enable Application Insights: **No**

7. In the Azure portal, search for and select **App Service Domains** on the top search bar.

    ![Screenshot that depicts the Azure portal with App Service Domains searched for and selected at the top of the search bar.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ASD.png "Select App Service Domains service")

8. On the **App Service Domains** blade, select **Create App Service Domain**.

    ![Screenshot that depicts the App Service Domains blade with the Create App Service Domain button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SelectBuyDomain.png "Create App Service Domain page")

9. On the **Create App Service Domains** blade, select the **contosohilab-RG** resource group. Then in the **Search for domains...** text box, type the domain name you want to purchase and select the box next to one of the available domain names listed below the text box. Make sure you make note of the domain you choose. 

    ![Screenshot that depicts the Create App Service Domains blade with the required settings selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/DomainList.png "Select domain from list of available domains")

10. Select **Next: Contact information**, type required information.

11. Select **Next: Advanced**, ensure that **Enable privacy protection** is set to **Disable**.

12. Select **Review + Create** then **Create**. 

### Task 4: Assign a custom domain name to the Contoso Azure AD tenant **OPTIONAL**

**THIS STEP IS OPTIONAL, ONLY PROCEED IF YOU COMPLETED TASK 3 ABOVE**
In this task, you will assign a newly purchased custom DNS domain name to the Contoso Azure AD tenant. 

1. On the lab computer, in the Azure portal, select the **Directory + Subscription** icon in the toolbar of the Azure portal (to the right of the **Cloud Shell** icon) and switch to the Contoso Azure AD tenant. 
   
   ![In this screenshot, the Directory + Subscription blade is depicted with the Contoso directory selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ContosoSwitch.png "Contoso directory switch")

2. In the Azure portal's left navigation, select **Azure Active Directory** to navigate to the **Contoso - Overview** blade.

3. On the **Contoso - Overview** blade, select **Custom domain names** under **Manage** on the left.

4. On the **Contoso - Custom domain names** blade, select **+ Add custom domain**.

5. On the **Add custom domain** blade that appears on the right, in the **Custom domain name** text box, type the domain name you purchased in the previous task and select **Add domain**. You will be redirected to a new blade displaying your custom domain name settings.

6. Identify the value of the **TXT** record on the custom domain name blade. 

7. On the lab computer, start another browser tab and navigate to the Azure portal.

8. In the Azure portal, select the **Directory + Subscription** icon in the toolbar of the Azure portal (to the right of the **Cloud Shell** icon) to switch to the Azure AD tenant associated with the Azure subscription into which you deployed resources in the Before Hands-On Lab exercises (the **Default Directory**). 

9.  In the Azure portal, select **All services** in the portal's left navigation. In the **Search All** textbox, type **DNS zones**, and then select the **DNS zones** entry in the listing of search results.

    ![In this screenshot, the All services blade is depicted with DNS zones searched for in the search box and selected in the search results.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SearchDNSZones.png "DNS Zones search and select")

10. On the **DNS zones** blade, select the entry with the name matching the custom domain name you purchased in the previous task.

11. On the DNS zone blade, select **+ Record set**. 

12. On the **Add record set** blade, specify the following settings and select **OK**:

    - Name: **\@**

    - Type: **TXT**

    - TTL: **1**

    - TTL unit: **Hours**

    - Value: the value of **DESTINATION OR POINTS TO ADDRESS** entry you identified on the **Custom domain name** blade.

13. Switch back to the browser window displaying the custom domain name blade and select **Verify**. Ensure that the verification was successful. 

14. Select **Make primary** and confirm the change when prompted. 

    ![In this screenshot, the Custom domain name blade is depicted with the Make primary button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/MakePrimary.png "Select Make primary on Custom domain name blade")

### Task 5: Configure DNS suffix in the Contoso Active Directory forest

**THIS STEP IS OPTIONAL, ONLY PROCEED IF YOU COMPLETED TASK 4 ABOVE**
In this task, you will configure the DNS suffix of the Contoso Active Directory forest to match the newly verified Azure AD custom domain name.

1. On the lab computer, in the Azure portal, verify that you are signed into the Azure AD tenant associated with the Azure subscription into which you deployed resources in the Before Hands-On Lab exercises (the **Default Directory**). If not, select the **Directory + Subscription** icon in the toolbar of the Azure portal (to the right of the **Cloud Shell** icon) to switch to that Azure AD tenant. 

2. In the Azure portal, navigate to the blade of the **DC1** virtual machine.

3. On the **DC1** virtual machine blade, connect to **DC1** via Remote Desktop. When prompted to sign in, use the **demouser** name and the **demo\@pass123** password. 

4. Within the Remote Desktop session to **DC1**, on the **Server Manager** window, start the **Active Directory Domains and Trusts** console under **Tools**. 

    ![In this screenshot, Server Manager is depicted with the Active Directory Domains and Trusts console selected on the Tools dropdown menu.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ADDTConsole.png "Stare Active Directory Domains and trusts console on Server Manager window")

5. In the **Active Directory Domains and Trusts** console, right-click **Active Directory Domains and Trusts [DC1.contoso.local]** on the left and select **Properties**.

    ![In this screenshot, the Active Directory Domains and Trusts console is depicted with 'Active Directory Domains and Trusts' right-clicked on the left with the Properties button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/NodeProperties.png "Open Active Directory Domains and Trusts Properties")

6. On the **UPN Suffixes** tab of the **Active Directory Domains and Trusts [DC1.contoso.local]** window, in the **Alternative UPN suffixes** textbox, type the name of the custom domain you verified in the previous task, select **Add**, and then select **OK**.

    ![In this screenshot, the Active Directory Domains and Trusts [DC1.contoso.local] window is depicted with the custom domain verified in the previous task added to the Alternative UPN suffixes selected. The OK button is then selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/UPNSuffix.png "Add UPN suffix")

7. Within the Remote Desktop session to **DC1**, on the **Server Manager** window, start the **Active Directory Users and Computers** console under **Tools**. 

    ![In this screenshot, Server Manager is depicted with the Active Directory Users and Computers console selected on the Tools dropdown menu.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ADUCConsole.png "Start Active Directory Users and Computers console on Server Manager window")

8. In the **Active Directory Users and Computers** console, expand **contoso.local** on the left and examine the organizational unit hierarchy of the domain and the group membership of the domain groups. 

    ![In this screenshot, on the Active Directory Users and Computers console, constoso.local under Active Directory Users and Computers on the left is expanded. The Domain and group hierarchy an now be viewed.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/NodeHierarchy.png "Domain and group hierarchy")

9.  Within the Remote Desktop session to **DC1**, start Windows PowerShell ISE and, on the Script pane, run the following to replace the UPN suffix of all users who are members of the **Engineering** group with the one matching the custom verified domain name of the Contoso Azure AD tenant (replace the placeholder `<custom_domain_name>` with the actual name of the custom verified domain name you assigned to the Contoso Azure AD tenant). 

    ```pwsh
    $domainName = '<custom_domain_name>'
    $users = Get-ADGroupMember -Identity 'Engineering' -Recursive | Where-Object {$_.objectClass -eq 'user'}

    foreach ($user in $users) {
        $user = Get-ADUser -Identity $User.SamAccountName
        $userName = $user.UserPrincipalName.Split('@')[0] 
        $upn = $userName + "@" + $domainName 
        $user | Set-ADUser -UserPrincipalName $upn
    }
    ```

### Task 6: Install Azure AD Connect

In this task, you will install Azure AD Connect.

1. Within the Remote Desktop session to **DC1**, in Server Manager, select **Local Server**, and ensure that **IE Enhanced Security Configuration** is disabled. If not, then select the **On** link next to **IE Enhanced Security Configuration**, set the **Administrators** settings to **Off**, and select **OK**.

2. Within the Remote Desktop session to **DC1**, start Internet Explorer and navigate to the Azure portal at <https://portal.azure.com>.

3. When prompted to sign in, enter the credentials of the **john.doe** Azure AD user account, which you copied into Notepad earlier in this exercise.

4. When prompted, change the password for the **john.doe** user account. 
  
    > **Note**: If you receive the message **We've seen that password too many times before. Choose something harder to guess**, you'll need to modify the password until it is unique enough to be accepted.

5. If prompted whether to **Stay signed in?"** select **No**. You will be redirected to the Azure portal interface. 

6. If presented with the **Welcome to Microsoft Azure** dialog box, select **Maybe later**. 

7. In the Azure portal, select **Azure Active Directory** on the portal's left navigation to navigate to the **Contoso - Overview** blade.

8. On the **Contoso - Overview** blade, select **Azure AD Connect** under **Manage** on the left.

9.  On the **Azure AD Connect** blade, select the **Download Azure AD Connect** link.

    ![In this screenshot, the Azure AD Connect blade is depicted with the Download Azure AD Connect link selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/DownloadADC.png "Download Azure AD Connect")

10. On the **Microsoft Azure Active Directory Connect** web page of the Microsoft Downloads site, select **Download**.

11. When prompted whether to run or save **AzureADConnect.msi**, select **Run**. This will download the file and automatically start the **Microsoft Azure Active Directory Connect** wizard. 

12. On the **Welcome to Azure AD Connect** page, check the **I agree to the license terms and privacy notice** box and select **Continue**.

    ![In this screenshot, the Welcome to Azure AD Connect page is depicted with the 'I agree to the license terms and privacy notice' box checked and the Continue button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ADCWelcomeWizard.png "Agree to terms and select Next")

13. On the **Express Settings** page, select the **Customize** button.

14. On the **Install required components** page, leave all optional configuration options deselected and select **Install**.

15. On the **User sign-in** page, select the **Pass-through authentication** option and the **Enable single sign-on** checkboxes, and select **Next**.

    ![In this screenshot, the User sign-in page of the Microsoft Azure AD Connect wizard is depicted with the Pass-through authentication option and the Enable single sign-on checkboxes selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_UserSign-in.png "Select sign-in options and select Next")

16. On the **Connect to Azure AD** page, sign in by using the credentials of the **john.doe** account and select **Next**.

17. On the **Connect your directories** page, ensure that the **contoso.local** entry appears in the **FOREST** drop-down list and select **Add Directory**. In the **AD forest account**, ensure that the **Create new AD account** option is selected, in the **ENTERPRISE ADMIN USERNAME** textbox, type **CONTOSO\\demouser**, in the **PASSWORD** textbox, type **demo\@pass123**, and select **OK**.

    ![In this screenshot, the Connect your directories page of the Microsoft Azure AD Connect wizard is depicted with contoso.local having been added.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_ConnectyourDirectories.png "Connect your Contoso directory")

18. Back on the **Connect your directories** page, select **Next**.

19. On the **Azure AD sign-in configuration** page, ensure that your custom domain name is listed as the verified **Active Directory UPN Suffix**, and that the **userPrincipalName** entry appears in the **USER PRINCIPAL NAME** drop-down list. Note the warning stating **Users will not be able to sign into Azure AD with on-premises credentials if the UPN suffix does not match a verified domain name**. Check the **Continue without matching all UPN suffixes to verified domain** box and select **Next**. 

    >**Note**: This is expected, since some users are still configured with the **contoso.local** UPN suffix, which is not routable and cannot be configured as a verified custom domain name of an Azure AD tenant.

    ![In this screenshot, the Azure AD sign-in configuration page of the Microsoft Azure AD Connect wizard is depicted with the custom domain name listed as verified, and the userPrincipalName is listed as the attribute to use as the AzureAD username. The Next button is then selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_AzureADsign-inconfiguration.png "Configure sign-in and select Next")

20. On the **Domain and OU filtering** page, ensure that only the **DemoAccounts** OU and all of its children OUs are selected and select **Next**. 

    ![In this screenshot, on the Domain and OU filtering page of the Microsoft Azure AD Connect wizard, the Demo Accounts OU and all of its child OUs are selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_DomainandOUFiltering.png "Select Demo Accounts and Child OUs then select Next")

21. On the **Uniquely identifying your users** page, accept the default settings and select **Next**. 

    ![In this screenshot, on the Uniquely identifying your users page of the Microsoft Azure AD Connect wizard, the default settings are displayed. The Next button is then selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_UniquelyIdentifyingyourusers.png "Uniquely Identifying your Users")

22. On the **Filter users and devices** page, accept the default settings and select **Next**. 

    ![In this screenshot, on the Filter users and devices page of Microsoft Azure AD Connect wizard, the default settings are displayed. The Next button is then selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_FilterUsersandDevices.png "Filter Users and Devices")

23. On the **Optional features** page, accept the default settings and select **Next**.

    ![In this screenshot, on the Optional features page of the Microsoft Azure AD Connect wizard, the default settings are displayed. The Next button is then selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_OptionalFeatures.png "Optional Features")

24. On the **Enable single sign-on** page, select **Enter credentials**, in the **Forest credentials** dialog box, sign in with the **CONTOSO\\demouser** username and **demo\@pass123** password, and select **Next**.

    ![In this screenshot, on the Enable single sign-on page of the Microsoft Azure AD Connect wizard, the prompt for forest credentials is displayed with the credentials listed above entered.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_EnableSingleSign-on.png "Enable Single Sign-on")

25. On the **Ready to configure** page, ensure that the **Start the synchronization process when configuration completes** checkbox is **NOT** selected and select **Install**.

    ![In this screenshot, on the Enable single sign-on page of the Microsoft Azure AD Connect wizard, the Start the synchronization process when configuration completes is not selected and the Install button is selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_ReadytoConfigure.png "Ready to Configure")

   > **Note**: You will configure attribute-level filtering before enabling the synchronization process.

   > **Note**: Installation should take about 2 minutes.

26. On the **Configuration complete** page, select **Exit**.

    ![In this screenshot, on the Configuration complete page of the Microsoft Azure AD Connect wizard, the status of the installation is displayed as Complete and the Exit button is selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConnect_ConfigurationComplete.png "Configuration Complete")


### Task 7: Enable Active Directory Recycle Bin

In this task, you will enable Recycle Bin in the Contoso Active Directory domain. 

1. Within the Remote Desktop session to **DC1**, on the Tools menu in the Server Manager console, start **Active Directory Administrative Center**.

    ![In this screenshot, the Server Manager console is depicted with the Tools menu open and the Start Active Directory Administrative Center console selected in the Tools menu dropdown.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/StartADAC.png "Start Active Directory Administrative Center")

2. In the **Active Directory Administrative Center** console, right-click **contoso (local)** on the left and select **Enable Recycle Bin**. When prompted to confirm, select **OK**.

    ![In this screenshot, the Active Directory Administrative Center console is depicted with 'contoso (local)' right-clicked and the Enable Recycle Bin option selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AD_EnableRecycleBin.png "Select Enable Recycle Bin for the local domain")

3. When prompted to refresh AD Administrative Center, select **OK**.

   > **Note**: For information regarding benefits of the Recycle Bin in hybrid scenarios, refer to <https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sync-recycle-bin>

### Task 8: Configure Azure AD Connect attribute-level filtering

In this task, you will configure Azure AD Connect attribute level filtering that will limit synchronization of user accounts to those with the UPN suffix matching the custom domain name of the target Azure AD tenant.

   > **Note**: The positive filtering option requires at least two sync rules. One of them determines the correct scope of objects to synchronize. The second catch-all sync rule filters out all objects that have not yet been identified as an object that should be synchronized.

1. Within the Remote Desktop session to **DC1**, start **Synchronization Rules Editor** under **Azure AD Connect** in the Start menu.

    ![In this screenshot, the Start menu is open within the Remote Desktop session to DC1 and under Azure AD Connect, Synchronization Rules Editor is selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/StartSRE.png "Open Synchronization Rules Editor")

2. In the Synchronization Rules Editor window, on the **View and manage your synchronization rules** page, ensure that **Inbound** appears in the **Direction** drop-down list and select **Add new rule**. This will launch the **Create inbound synchronization rule** wizard.

    ![In this screenshot, the Synchronization Rules Editor window is depicted with the Inbound rules displayed, Inbound selected on the Direction dropdown menu, and the 'Add new rule' button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SynchronizationRulesEditor_AddNewRule.png "Add New Inbound Rule")

3. On the **Create inbound synchronization rule - Description** page, specify the following settings and select **Next**:

    - Name: **Custom In from AD - UPN Filter**

    - Description: **Custom Inbound Rule - includes users with UPN set to match the Azure AD custom domain**

    - Connected System: **contoso.local**

    - Connected System Object Type: **user**

    - Metaverse Object Type: **person**

    - Link Type: **join**

    - Precedence: **50**

    - Tag: Leave empty

    - Enable Password Sync: Leave empty

    - Disabled: Leave empty

    ![In this screenshot, the description page of the Create inbound synchronization rule wizard is depicted with the required configuration settings and the Next button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/CreateInboundSynchronizationRule_Description.png "Enter Sync Rule Description information")

4. On the **Create inbound scoping filter** page, select **Add Group**, select **Add clause** specify the following, and select **Next**:

    - Attribute: **userPrincipalName**

    - Operator: **ENDSWITH**

    - Value: **\@\<your custom domain name>**

    ![In this screenshot, the scoping filter page of the Create inbound synchronization rule wizard is depicted with the require the configuration settings and the Next button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/CreateInboundSynchronizationRule_ScopingFilter.png "Create inbound scoping filter")

5. On the **Join Rules** page, select **Next**.

6. On the **Transformations** page, select **Add transformation** specify the following and select **Add**:

    - FlowType: **Constant**

    - Target Attribute: **cloudFiltered**

    - Source: **False**

    ![In this screenshot, the Transformations page of the Create inbound synchronization rule wizard is depicted with the required configuration settings selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/CreateInboundSynchronizationRule_Transformations.png "Add transformation")

7. When presented with a **Warning** dialog box displaying that message stating that **A full import and full synchronization will be run on 'contoso.local' during your next synchronization cycle**, select **OK**.

   > **Note**: This should bring you back to the View and manage your synchronization rules interface, with the new rule listed at the top of the rule list. 

    ![In this screenshot, the synchronization rules editor with the newly created Custom in from AD - UPN Filter rule is highlighted.](images/2020-04-27-23-44-31.png "View new rule")

8. Back in the **Synchronization Rules Editor** window, on the **View and manage your synchronization rules** page, ensure that **Inbound** appears in the **Direction** drop-down list and select **Add new rule** again. This will launch the **Create inbound synchronization rule** wizard.

9. On the **Description** page, specify the following settings and select **Next**:

    - Name: **Custom In from AD - Catch-all Filter**

    - Description: **Custom Inbound Rule - excludes all users with UPN not set to match the Azure AD custom domain**

    - Connected System: **contoso.local**

    - Connected System Object Type: **user**

    - Metaverse Object Type: **person**

    - Link Type: **join**

    - Precedence: **51**

    - Tag: Leave empty

    - Enable Password Sync: Leave empty

    - Disabled: Leave empty

    ![In this screenshot, the description page of the Create inbound synchronization rule wizard is depicted, with the configuration settings selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/CreateInboundSynchronizationRule_DescriptionCatchAll.png "Create Catch All Description inbound sync rule")

10. On the **Scoping filer** page, select **Next**.

11. On the **Join Rules** page, select **Next**.

12. On the **Transformations** page, select **Add transformation** specify the following and select **Add**:

    - FlowType: **Constant**

    - Target Attribute: **cloudFiltered**

    - Source: **True**

    ![In this screenshot, the Transformations page of the Create inbound synchronization rule wizard is depicted with the required configuration settings selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/CreateInboundSynchronizationRule_TransformationsCatchAll.png "Add transformation to rule")

13. When presented with a **Warning** dialog box displaying a message stating that **A full import and full synchronization will be run on 'contoso.local' during your next synchronization cycle**, select **OK**.

    >**Note**: This should bring you back to the **View and manage your synchronization rules** interface, with the new rules listed at the top of the rule list. 

    ![In this screenshot, the Synchronization Rules Editor is depicted displaying Inbound rules, including the two newly created inbound rules.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SynchronizationRulesEditor_AddNewRule_withRules.png "Displayed rules")

### Task 9: Initiate and verify directory synchronization

1. Within the Remote Desktop session to **DC1**, double-click the **Azure AD Connect** desktop shortcut.

2. On the **Welcome to Azure AD Connect** page, select **Configure**. 

3. On the **Additional tasks** page, select **Customize synchronization options** and select **Next**.

    ![In this screenshot, the 'Additional tasks' page of the Azure AD Connect wizard is depicted with the 'Customize synchronization options' option and Next button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/CustomizeSyncOptions.png "Customize synchronization options task")

4. On the **Connect to Azure AD** page, sign in by using the credentials of the **john.doe** account and select **Next**.

5. On the **Connect your directories** page, select **Next**.

6. On the **Domain and OU filtering** page, select **Next**. 

7. On the **Optional features** page, accept the default settings and select **Next**.

8. On the **Enable single sign-on** page, select **Next**.

9. On the **Ready to configure** page, select the **Start the synchronization process when configuration completes** checkbox and select **Configure**.

    ![In this screenshot, the 'Ready to configure' page of the Azure AD Connect wizard is depicted with the 'Start the synchronization process when configuration completes' checkbox and the Configure button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ReadyConfigurePage.png "Ready to configure page")

10. On the **Configuration complete** page, select **Exit**.

11. Within the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the **Users - All users** blade of the Contoso Azure AD tenant.

12. On the **Users - All users** blade, note that the list of user objects includes all user accounts with the UPN suffix matching the custom domain name of the Azure AD tenant. You may need to refresh the page or wait a few minutes to see the change.

    ![In this screenshot, the 'Users - All users' blade of the Azure portal is depicted with all the user objects with the custom domain UPN suffixes listed.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/UserObjects.png "User objects shown with custom domain UPN suffixes")

13. In the Azure portal, navigate to the **Groups - All groups** blade of the Contoso Azure AD tenant and note that all of the contoso.local domain groups have been synchronized as well. 

14. In the Azure portal, navigate to the **Contoso - Azure AD Connect** blade and select **Azure AD Connect** on the left. Verify that the following settings are set: 

    - Azure AD Connect Sync Status: **Enabled** 
  
    - Last Sync: **This should be a timestamp of some sort**. 
  
    - Password Hash Sync: **Disabled** 
  
    - Federation: **Disabled**
   
    - Seamless single sign-on: **Enabled for 1 domain** 
  
    - Pass-through authentication: **Enabled with 1 agent**

   > **Note**: In a production environment, you would install additional agents for redundancy. For more information, refer to <https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-pta-quick-start>.

### Task 10: Configure Hybrid Azure AD join

In this task, you will configure Azure AD Connect device synchronization options.

1. Within the Remote Desktop session to **DC1**, double-click the **Azure AD Connect** desktop shortcut.

2. On the **Welcome to Azure AD Connect** page, select **Configure**. 

3. On the **Additional tasks** page, select **Configure device options** and select **Next**.

4. On the **Overview** page, review the information regarding **Hybrid Azure AD join** and **Device writeback**, and select **Next**.

5. On the **Connect to Azure AD** page, sign in by using the credentials of the **john.doe** account and select **Next**.

6. On the **Device options** page, ensure that the **Configure Hybrid Azure AD join** option is selected and select **Next**. 

    ![In this screenshot, the 'Device options' page of the Azure AD Connect configuration wizard is depicted with the 'Configure Hybrid Azure AD join' option and the Next button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ConfigureHybridAzureADJoin.png "Configure Hybrid Azure AD join option is selected")

7. On the **Device operating system** page, select the **Windows 10 or later domain-joined devices** and **Supported Windows down-level domain-joined devices** checkboxes, and select **Next**. 

   > **Note**: Windows down-level devices are supported only if you are using Seamless SSO for managed domains or a federation service such as AD FS for federated domains.

8. On the **SCP configuration** page, check the **contoso.local** Active Directory forest box, select the **Azure Active Directory** entry in the **Authentication Service** dropdown list, and select **Add**.

    ![In this screenshot, the 'SCP configuration' page of the Azure AD Connect configuration wizard is depicted with the required settings and the Add button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SCPConfig.png "SCP configuration page with proper selections")

9. When prompted for Enterprise Admin Credentials for contoso.local, in the **Windows Security** dialog box, sign in with the **CONTOSO\\demouser** user name and **demo\@pass123** password.

10. Back on the **SCP configuration** page, select **Next**.

11. On the **Ready to configure** page, select **Configure**.

12. On the **Configuration complete** page verify that the task completed successfully and select **Exit**.

   > **Note**: For more information regarding configuring hybrid Azure Active Directory join for managed domains, refer to <https://docs.microsoft.com/en-us/azure/active-directory/devices/hybrid-azuread-join-managed-domains#configure-hybrid-azure-ad-join>.


### Task 11: Perform Hybrid Azure AD join (OPTIONAL)
**THIS STEP IS OPTIONAL, ONLY PROCEED IF YOU CREATED A CUSTOM DOMAIN NAME**

1. On the lab computer, in the Azure portal, verify that you are signed into the Azure AD tenant associated with the Azure subscription into which you deployed resources in the Before Hands-On Lab exercises (the **Default directory**). If not, select the **Directory + Subscription** icon in the toolbar of the Azure portal (to the right of the **Cloud Shell** icon) to switch to that Azure AD tenant. 

2. In the Azure portal, navigate to the blade of the **APP1** virtual machine.

3. On the **APP1** virtual machine blade, connect to **APP1** via Remote Desktop. When prompted to sign in, use the **AGAyers\@<custom_domain_name>** user name with the **demo@pass123** password (where **<custom_domain_name>** placeholder represents the custom DNS domain name you assigned to the Contoso Azure AD tenant earlier in this exercise.

4. Within the Remote Desktop session to **APP1**, on the **Server Manager** window, start **Task Scheduler** under **Tools**. 

    ![In this screenshot, the Server Manager window on the Remote Desktop connection to APP1 is depicted with the Tools menu open and the Task Scheduler option selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/TaskScheduler.png "Star Task scheduler via Server Manager")

5. In the **Task Scheduler** console, navigate to **Task Scheduler Library** > **Microsoft** > **Windows** > **Workplace Join**. From there, enable then run the **Automatic-Device-Join** task. 

    ![In this screenshot, the Task Scheduler console window is depicted with the Workplace Join node selected on the left and the 'Start device join' task and the Enable button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/StartDeviceJoin.png "Start device join task in Task Scheduler")

6. Switch to the Remote Desktop session to **DC1** and, from the console pane of the Windows PowerShell ISE window, start Azure AD Connect delta synchronization by running the following:

   ```pwsh
   Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'
   
   Start-ADSyncSyncCycle -PolicyType Delta
   ```

7. Switch back to the Remote Desktop session to **APP1** and start a **Command Prompt**.

8. From the Command Prompt window, check the Azure AD registration status of APP1 by running the following: 

   ```
   dsregcmd /status
   ```

9. Verify that the output of the command resembles the following:

   ```
   +----------------------------------------------------------------------+
   | Device State                                                         |
   +----------------------------------------------------------------------+

        AzureAdJoined : YES
     EnterpriseJoined : NO
             DeviceId : 61eea2b8-efbe-43d9-b267-126433c8ee34
           Thumbprint : BBAAA0FB4A55E880388851BED955A2669A961A96
       KeyContainerId : 2eb75eb8-0a1d-437b-99d9-9dd161ca0d90
          KeyProvider : Microsoft Software Key Storage Provider
         TpmProtected : NO
         KeySignTest: : PASSED
                  Idp : login.windows.net
             TenantId : xxxxxxx-xxxx-xxx-xxxx-xxxxxxxxxx
           TenantName : xxxxxxx-xxxx-xxx-xxxx-xxxxxxxxxx
          AuthCodeUrl : https://login.microsoftonline.com/xxxxxxx-xxxx-xxx-xxxx-xxxxxxxxxx/oauth2/authorize
       AccessTokenUrl : https://login.microsoftonline.com/xxxxxxx-xxxx-xxx-xxxx-xxxxxxxxxx/oauth2/token
               MdmUrl :
            MdmTouUrl :
     MdmComplianceUrl :
          SettingsUrl :
       JoinSrvVersion : 1.0
           JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
            JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
        KeySrvVersion : 1.0
            KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
             KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
         DomainJoined : YES
           DomainName : CONTOSO

   +----------------------------------------------------------------------+
   | User State                                                           |
   +----------------------------------------------------------------------+

               NgcSet : NO
      WorkplaceJoined : NO
        WamDefaultSet : NO
           AzureAdPrt : NO

   +----------------------------------------------------------------------+
   | Ngc Prerequisite Check                                               |
   +----------------------------------------------------------------------+

        IsUserAzureAD : NO
        PolicyEnabled : NO
       DeviceEligible : YES
   SessionIsNotRemote : NO
     X509CertRequired : NO
         PreReqResult : WillNotProvision

   ```
11. Switch back to the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the **Devices - All devices** blade of the Contoso Azure AD tenant and verify that there is an entry representing the APP1 server, with the **Join Type** set to **Hybrid Azure AD joined**.

   > **Note**: You might need to wait until the Azure AD registration status is correctly reported and its Azure AD object appears in the Azure portal.

   ![In this screenshot, the 'Devices - All devices' blade of the Azure portal is depicted with an entry representing the APP1 server with the 'Join Type' set to 'Hybrid Azure AD joined'](images/Hands-onlabstep-bystep-HybridIdentityImages/media/APP1_HybridAzureADjoined.png "APP1 server entry is shown")


**Summary**

In this exercise, you integrated an Active Directory forest with an Azure Active Directory tenant by creating an Azure Active Directory tenant and activating an Enterprise Mobility + Security E5 trial, creating and configuring an Azure AD user, purchasing a custom domain name, assigning a custom domain name to the Contoso Azure AD tenant, configuring DNS suffix in the Contoso Active Directory forest, installing Azure AD Connect, enable Active Directory Recycle Bin, configuring Azure AD Connect attribute-level filtering, initiating and verifying directory synchronization, configuring Hybrid Azure AD join, and performing Hybrid Azure AD join of a Windows Server 2016 VM.


## Exercise 2: Manage Authentication, Authorization, and Access Control in Hybrid Scenarios

Duration: 150 minutes

**Overview**

In this exercise, you will optimize authentication, authorization, and access control for Contoso Active Directory environment integrated with the Contoso Azure AD tenant by enabling Azure AD Multi-Factor Authentication, enabling Azure AD password writeback and Self-Service Password Reset, implementing, Azure AD Password Protection, enabling Azure Active Directory Identity Protection, enabling Automatic Intune Enrollment, as well as implementing Azure AD Privileged Identity Management and Azure AD Conditional Access Policies.


### Task 1: Create Active Directory groups

In this task, you will create and configure Active Directory groups that will be used to control authentication, authorization, and access control and synchronize them to the Contoso Azure AD tenant. 

1. Within the Remote Desktop session to **DC1**, on the **Server Manager** window, under **Tools** start the **Active Directory Users and Computers** console. 

2. In the **Active Directory Users and Computers** console, expand **contoso.local** on the left and navigate to **Demo Accounts > Groups**. 

    ![In this screenshot, the Active Directory Users and Computers console is depicted with the left navigation expanded to 'contoso.local' > 'Demo Accounts' > 'Groups' with 'Groups' selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/NodeNavigation.png "Navigate to DemoAccounts > Groups in Active Directory Users and Computers")

3. In the **Groups** directory, right-click then select **New** then select **Group**. Create a new group with the following settings and select **OK**:

    - Group name: **Engineering - Mandatory MFA**

    - Group name (pre-Windows 2000): **Engineering - Mandatory MFA**

    - Group scope: **Global**

    - Group type: **Security**

    ![In this screenshot, the 'New Object - Group' dialog is depicted with the required settings selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/CreateGroupDirectory.png "Create new group in directory")

4. Open the **Properties** window of the **Engineering - Mandatory MFA** group, in the **Description** text box, type **Engineering users with user state-based MFA enforcement (without Conditional Access)** then select **Apply** then **OK**.

5. Within the Remote Desktop session to **DC1**, on the Script pane of the Windows PowerShell ISE window, run the following to add designated users to the newly created group (replace the placeholder `<custom_domain_name>` with the actual name of the custom verified domain name you assigned to the Contoso Azure AD tenant).

    ```pwsh
    $domainName = '<custom_domain_name>'
    $users = Get-ADGroupMember -Identity 'Engineering' -Recursive | Where-Object {($_.objectClass -eq 'user') -and ($_.distinguishedName -like "*OU=NY,OU=US,OU=Users,OU=Demo Accounts,DC=contoso,DC=local")}
    foreach ($user in $users) {
        $user = Get-ADUser -Identity $User.SamAccountName
        Add-ADGroupMember -Identity 'Engineering - Mandatory MFA' -Members $user
    }
    ```

6. On the console pane of the Windows PowerShell ISE window, start Azure AD Connect delta synchronization by running the following:

   ```pwsh
   Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'
   
   Start-ADSyncSyncCycle -PolicyType Delta
   ```

7. In the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the **Contoso - Overview** blade of the Contoso Azure AD tenant and select **Groups** on the left.

8. On the **Groups - All groups** blade, verify that there is an entry representing the **Engineering - Mandatory MFA** group containing the Azure AD user accounts matching Active Directory user accounts which are members of the Active Directory **Engineering - Mandatory MFA** group.


### Task 2: Assign EMS E5 licenses to Azure AD users

In this task, you assign a value to the **UsageLocation** attribute of each user account and assign an Azure AD Premium license to each user. This is necessary in order to implement Azure AD-based Multi-Factor Authentication for these users.

1. Within the Remote Desktop session to **DC1**, on the Script pane of the Windows PowerShell ISE window, run the following to install Azure AD Graph PowerShell module for Graph (select **Yes to All** when prompted whether to proceed with the installation):

    ```pwsh
    Install-Module AzureAD
    ```

2. On the Script pane of the Windows PowerShell ISE window, run the following to sign into the Contoso Azure AD tenant. When prompted, sign in with the **john.doe** Azure AD user account, which you created in the previous exercise.

    ```pwsh
    Connect-AzureAD
    ```

3. On the Script pane of the Windows PowerShell ISE window, run the following to set the **Location** attribute to **United States** for all Azure AD user accounts with the UPN suffix matching the custom verified domain name of the Contoso Azure AD tenant. 

    ```pwsh
    $domainName = (Get-AzureADDomain | Where-Object IsDefault -eq 'True').Name
    Get-AzureADUser | Where-Object {$_.UserPrincipalName -like "*@$domainName"} | Set-AzureADUser -UsageLocation 'US'
    ```
4. On the Script pane of the Windows PowerShell ISE window, run the following to assign the EM+S E5 trial licenses to all Azure AD user accounts with the UPN suffix matching the custom verified domain name of the Contoso Azure AD tenant. 

    ```pwsh
    $license = New-Object -TypeName Microsoft.Open.AzureAD.Model.AssignedLicense
    $license.SkuId = (Get-AzureADSubscribedSku | Where-Object {$_.SkuPartNumber -eq 'EMSPREMIUM'}).SkuId
    $licensesToAssign = New-Object -TypeName Microsoft.Open.AzureAD.Model.AssignedLicenses
    $licensesToAssign.AddLicenses = $license
    $users = Get-AzureADUser | Where-Object {$_.UserPrincipalName -like "*@$domainName"}
    foreach($user in $users) { 
        Set-AzureADUserLicense -ObjectId $user.ObjectId -AssignedLicenses $licensesToAssign
    }
    ```

### Task 3: Enable Azure AD Multi-Factor Authentication

In this task, you will configure user state-based Azure AD Multi-Factor Authentication for members of the **Engineering - Mandatory MFA** group.

   > **Note**: In general, enabling Azure Multi-Factor Authentication using Conditional Access policies is the recommended approach, since it offers more flexibility. Setting Multi-Factor Authentication by modifying the user state requires users to perform two-step verification every time they sign in and overrides Conditional Access policies. On the other hand, this approach might be required if existing licensing arrangement does not include Conditional Access.

1. Within the Remote Desktop session to **DC1**, on the Script pane of the Windows PowerShell ISE window, run the following to install Azure AD MSOnline PowerShell module for Graph (select **Yes to All** when prompted whether to proceed with the installation):

    ```pwsh
    Install-Module MSOnline
    ```

2. On the Script pane of the Windows PowerShell ISE window, run the following to sign into the Contoso Azure AD tenant. When prompted, sign in with the **john.doe** Azure AD user account, which you created in the previous exercise.

    ```pwsh
    Connect-MsolService
    ```

3. On the Script pane of the Windows PowerShell ISE window, run the following to enable Azure AD Multi-Factor Authentication for all Azure AD user accounts with the UPN suffix matching the custom verified domain name of the Contoso Azure AD tenant. 

    ```pwsh
    $objectIdGUID = (Get-MsolGroup | Where-Object {$_.DisplayName -eq 'Engineering - Mandatory MFA'}).objectId.Guid
    $users = Get-MsolGroupMember -GroupObjectId $objectIdGUID
    $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
    $st.RelyingParty = "*"
    $st.State = "Enabled"
    $sta = @($st)
    foreach($user in $users) { 
        Set-MsolUser -ObjectId $user.ObjectId -StrongAuthenticationRequirements $sta
    }
    ```

4. To verify the outcome, within the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the **Users - All users** blade of the Contoso Azure AD tenant and select **Multi-Factor Authentication** (you might need to select **...** first). This will open a new tab in the Internet Explorer window displaying the **multi-factor authentication** portal.

    ![In this screenshot, the 'Users - All users' blade of the Azure portal is depicted with the '...' icon selected and the 'Multi-Factor Authentication' option selected in the dropdown menu.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SelectMFA.png "Select Multi-Factor Authentication")

5. In the **multi-factor authentication** portal, on the **users** tab, ensure that all users have the **MULTI-FACTOR AUTH STATUS** set to **Enabled**. If all or some do not have multi-factor authentication enabled, select all users that have it disabled, then select **Enable** on the right. Select **enable multi-factor auth** when prompted. Wait for the operation to complete. 

    ![In this screenshot, the 'multi-factor authentication' portal's users tab is depicted with all users without multi-factor authentication enabled selected and the Enable button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/EnableMFA.png "Enabling MFA")


### Task 4: Enable password writeback and Self-Service Password Reset

In this task, you will enable password writeback and Self-Service Password Reset (SSPR) for Contoso users that had their accounts synchronized to the Contoso Azure AD tenant.

1. Within the Remote Desktop session to **DC1**, double-click the **Azure AD Connect** desktop shortcut.

2. On the **Welcome to Azure AD Connect** page, select **Configure**. 

3. On the **Additional tasks** page, select **Customize synchronization options** and select **Next**.

4. On the **Connect to Azure AD** page, sign in by using the credentials of the **john.doe** account and select **Next**.

5. On the **Connect your directories** page, select **Next**.

6. On the **Domain and OU filtering** page, select **Next**. 

7. On the **Optional features** page, check the **Password writeback** box and select **Next**.

8. On the **Enable single sign-on** page, select **Next**.

9.  On the **Ready to configure** page, ensure that the **Start the synchronization process when configuration completes** checkbox is selected and select **Configure**.

10. On the **Configuration complete** page, select **Exit**.

11. Within the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the **Contoso - Overview** blade of the Contoso Azure AD tenant.

12. On the **Contoso - Overview** blade, select **Password reset** on the left under **Manage**. 

13. On the **Password reset - Properties** blade, set **Self-service password reset enabled** to **Selected**, choose **No groups selected**. On the **Default password reset policy** blade that appears on the right, select **Engineering**, choose **Select**, and select **Save**.

14. On the **Password reset - Properties** blade, select **Authentication methods** on the left under **Manage**.

15. On the **Password reset - Authentication methods** blade, set **Number of methods required to reset** to **2**, enable all **Methods available to users**, including **Mobile app notification**, **Mobile app code**, **Email**, **Mobile phone (SMS only)**, and **Security questions**. 

    ![In this screenshot, the 'Password reset - Authentication methods' blade of the Azure portal is depicted with the required authentication method settings listed above are selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SettingAuthenticationMethods.png "Setting authentication methods and requirements")

   > **Note**: The **Office phone** method is not available in trial subscriptions.

16. Set **Number of security questions required to register** and **Number of questions required to reset** to **3**. 

    ![In this screenshot, the 'Password reset - Authentication methods' blade of the Azure portal is depicted with the required authentication method settings listed above are selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SetSecurityQuestions.png "Set security questions and requirements")

17. Choose **No security questions configured**. On the **Select security questions** blade that appears, select **+ Predefined**. On the **Add predefined security questions** blade that appears on the right, select any 5 questions, select **OK** twice, and, back on the **Password reset - Authentication methods** blade, select **Save**.

    ![In this screenshot, the 'Select security questions' blade of the Azure portal is depicted with the '+ Predefined' button selected with the required predefined security questions selected on the 'Add predefined security questions' blade on the right.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SelectSecurityQuestions.png "Select security questions")

18. On the **Password reset - Authentication methods** blade, select **Registration** on the left and ensure that **Require users to register when signing in** is set to **Yes** and that **Number of days before users are asked to re-confirm their authentication information** is set to **180**.

19. On the **Password reset** blade, select **On-premises integration** on the left and verify that the **Write back passwords to your on-premises directory** setting is set to **Yes**. Note that you have the option to **Allow users to unlock accounts without resetting their passwords**.


### Task 5: Implement Azure AD Password Protection for Windows Server Active Directory **OPTIONAL**

In this task, you will implement Azure AD password Protection for Windows Server Active Directory.

1. Within the Remote Desktop session to **DC1**, on the **Server Manager** window, under **Tools** start **Group Policy Management** console. 

2. In the **Group Policy Management** console, navigate to **Forest: contoso.local > Domains > contoso.local** on the left, right-click **Default Domain Policy** and select **Edit**. 

    ![In this screenshot, the Group Policy Management console window is depicted with 'Forest: contoso.local' > 'Domains' > 'contoso.local' expanded on the left navigation and 'Default Domain Policy' right-clicked with the Edit option selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/EditDefaultDomainPolicy.png "Edit default domain policy")

3. In the **Group Policy Management Editor**, navigate to **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy**. 

4. Set the value of the **Account lockout threshold** to **10** and select **OK** and accept the settings in the **Suggested Value Changes**. 

    ![In this screenshot, the Group Policy Management Editor is depicted with the 'Account lockout threshold properties' dialog open with the required settings selected and the 'Suggested Value Changes' dialog open with the OK button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADPasswordProtectionPolicy_ADLockout.png "Group policy management")

5. Within the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the **Contoso - Overview** blade of the Contoso Azure AD tenant.

6. On the **Contoso - Overview** blade, select **Security** under **Manage** on the left.

7. On the **Security - Getting started** blade, select **Authentication methods** under **Manage** on the left.

8. On the **Authentication methods - Policies** blade, select **Password protection** under **Manage** on the left.

9.  On the **Authentication methods - Password protection** blade, specify the following settings and select **Save**:

    - Lockout threshold: **5**

    - Lockout duration in seconds: **3600**

    - Enforce custom list: **Yes**

    - Custom banned password list: **Contoso**, **password**, **pass** (type each word on a separate line without commas).

    - Enable password protection on Windows Server Active Directory: **Yes**

    - Mode: **Audit**

    ![In this screenshot, the 'Authentication methods - Password protection' blade in the Azure portal is depicted with the required settings and the Save button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SetPasswordProtection.png "Set password protection")

10. Switch to the Remote Desktop session to **APP1** virtual machine, where you are signed in as the user **AGAyers** with the **demo@pass123** password. 

11. Within the Remote Desktop session to **APP1**, start Internet Explorer, navigate to the **Azure AD Password Protection for Windows Server Active Directory** page at the below listed URL. Click **Download** under **Azure AD Password Protection for Windows Server Active Directory**. Download and install **AzureADPasswordProtectionProxySetup.exe** with the default options.

    ```
    https://www.microsoft.com/download/details.aspx?id=57071
    ```

    **Note:** You may have to enable file download on Internet Explorer. To do this, select the **Tools** icon that's shaped like a gear in the top right of the window and select **Internet Options**. From there select the **Security** tab then select **Custom level**. In the pop-up, scroll to the downloads section. Select **Enable** under **File download** and then select **OK**. Select **Yes** at the confirmation prompt then select **OK** again. 

12.  Within the Remote Desktop session to **APP1**, start Windows PowerShell ISE as Administrator and, on the scripting pane, run the following to register the proxy (replace the `<domain_name>` placeholder with the name of the default domain name associated with the Contoso Azure AD tenant). When prompted, sign into the Contoso Azure AD tenant using the credentials of the **john.doe** user account.
13.  
    ```pwsh
    Import-Module AzureADPasswordProtection
    Register-AzureADPasswordProtectionProxy -AccountUpn 'john.doe@<domain_name>.onmicrosoft.com'
    ```

14. On the Windows PowerShell ISE console, run the following to register the Active Directory forest (replace the `<domain_name>` placeholder with the name of the default domain name associated with the Contoso Azure AD tenant):

    ```pwsh
    Register-AzureADPasswordProtectionForest -AccountUpn 'john.doe@<domain_name>.onmicrosoft.com'
    ```

15. Switch to the Remote Desktop session to **DC1** virtual machine, where you are signed in as the user **CONTOSO\demouser** with the **demo@pass123** password. 

16. Within the Remote Desktop session to **DC1**, start Internet Explorer, navigate to the **Azure AD Password Protection for Windows Server Active Directory** page at the below listed URL. Click **Download** under **Azure AD Password Protection for Windows Server Active Directory**. Download and install **AzureADPasswordProtectionProxySetup.exe** with the default options.

    ```
    https://www.microsoft.com/download/details.aspx?id=57071
    ```

    **Note:** You may have to perform the instructions to enable file download on Internet Explorer listed earlier. 

17. Restart DC1 once the setup completes.

### Task 6: Enable Azure Active Directory Identity Protection

In this task, you will enable Azure AD Identity Protection

1. On the lab computer, in the Azure portal, navigate to the blade of the **DC1** virtual machine. You will need to be in the **Default Directory**. 

2. On the **DC1** virtual machine blade, connect to **DC1** via Remote Desktop. When prompted to sign in, use the **demouser** name with the **demo\@pass123** password. 

3. Within the Remote Desktop session to **DC1**, start Internet Explorer and navigate to the Azure portal at the below URL.

    ```
    portal.azure.com
    ```

4. When prompted to sign in, enter the credentials of the **john.doe** Azure AD user account, which you copied into Notepad earlier in this exercise.

5. In the Azure portal, search for and select **Azure AD Identity Protection** in the search bar at the top of the page.

6. On the **Azure AD Identity Protection** blade, select **MFA registration policy** on the left under **Protect**.

7. On the **Azure AD Identity Protection - MFA registration policy** blade, in the **Assignments** section, select **All users**. 

    ![In this screenshot, the 'Azure AD Identity Protection - MFA registration policy' blade of the Azure portal is depicted with the 'All users' button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SelectUsers.png "Select All users")

8. On the **Include** tab that opens on the right, choose **Select individual users and groups**. On the **Select users** blade, in the **Select** text box, type **Engineering**, in the list of results, select **Engineering** and choose **Select**.

    ![In this screenshot, the 'Azure AD Identity Protection - MFA registration policy' blade of the Azure portal is depicted with the Include tab of the Users blade selected with the 'Select individual users and groups' option selected and the 'Select users' blade open with the 'Engineering' group and 'Select' button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/IdentityProtection_MFAregistrationpolicy_UsersInclude.png "Include Engineering group")

9. On the **Exclude** tab, choose **0 users and groups selected**. On the **Select users** blade, in the **Select** text box, type **Engineering - Mandatory MFA**, in the list of results, select **Engineering - Mandatory MFA**, choose **Select**.

    ![In this screenshot, the 'Azure AD Identity Protection - MFA registration policy' blade of the Azure portal is depicted with the Exclude tab of the Users blade selected with the '0 users and groups selected' button selected and the 'Select users' blade open with the 'Engineering - Mandatory MFA' group and 'Select' button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/IdentityProtection_MFAregistrationpolicy_UsersExclude.png "Exclude groups settings")

10. Back on the **Azure AD Identity Protection - MFA registration policy** blade, in the **Controls** section, ensure that the **Require Azure MFA registration** checkbox is selected. Set **Enforce Policy** to **On** and select **Save**.

    ![In this screenshot, the 'Azure AD Identity Protection - MFA registration policy' blade of the Azure portal is depicted with the 'Require Azure MFA registration' checkbox selected and the 'Enforce Policy' set to On.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/IdentityProtection_MFAregistrationpolicy_EnforcePolicy.png "Enforce policy and require MFA registration")

11. On the **Azure AD Identity Protection** blade, select **User risk policy** on the left under **Protect**.

12. On the **Azure AD Identity Protection - User risk policy** blade, configure the **User risk remediation policy** with the following settings and save your configuration:

    - Assignments: 

        - Users: **All users**

        - User risk: **Low and above**

    - Controls:

        - Access: **Allow access** and **Require password change**

    - Enforce Policy: **On**

    ![In this screenshot, the 'Azure AD Identity Protection - User risk policy' blade of the Azure portal is depicted with the required settings and the Save button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/IdentityProtection_UserRiskPolicy.png "Set the user risk policy")

### Task 7: Enable Automatic Intune Enrollment **OPTIONAL**

In this task, you will enable automatic enrollment of hybrid Azure AD devices into Intune. 

1. Within the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the following URL in a tab. 

    ```
    https://endpoint.microsoft.com
    ```
2. On the **Microsoft Endpoint Manager admin center** page, select **Devices** on the left navigation.

3. On the **Devices** blade, select **Enrolled devices** under **Device enrollment** on the left.

4. On the **Windows enrollment** blade, select **Automatic Enrollment**.

5. On the **Configure** blade, set **MDM user scope** to **All** and select **Save**.

    ![In this screenshot, the Configure blade of the Azure portal is depicted with the 'MDM user scope" setting set to all and the Save button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SetMDMUserScope.png "Set MDM user scope on Configure blade")

### Task 8: Enable Enterprise-State-Roaming **OPTIONAL**

1. Within the Remote Desktop session to **DC1**, in the Internet Explorer tab displaying the Azure portal, navigate to the blade of the Contoso Azure AD tenant.

2. On the **Contoso - Overview** blade, select **Devices** under **Manage** on the left.

3. On the **Devices - All devices** blade, select **Enterprise State Roaming** on the left.

4. On the **Devices - Enterprise State Roaming** blade, for **Users may sync settings and app data across devices**, select **Selected**. Choose **No member selected** below. Select **+ Add** then select the **Engineering** group from the list of Azure AD tenant users and groups that appears on the right. Choose **Select**, then **Ok**, then **Save**. 

    ![In this screenshot, the 'Members allowed to sync settings and app data' blade is depicted with the '+ Add' button selected and the 'Add members' blade open with the Engineering group searched for and selected along with the Select button.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/SelectEngineering.png "Select Engineering group")

### Task 9: Implement Azure AD Conditional Access Policies

In this task, you will implement Azure AD Conditional Access Policies.

1. Within the Remote Desktop session to **DC1**, in the Internet Explorer tab displaying the Azure portal, navigate to the **Contoso - Overview** blade of the Contoso Azure AD tenant.

2. On the **Contoso - Overview** blade, select **Properties** under **Manage** on the left. Select **Manage Security defaults**. On the **Enable security defaults** blade that appears on the right, select **No** under **Enable security defaults** then check the other box and enter some random characters. Then select **Save**. Return to the **Contoso - Overview** blade.

    ![In this screenshot, the 'Contoso - Overview' blade is depicted with the Properties and 'Manage security defaults' buttons selected and with the 'Enable security defaults' blade open on the right with the required configuration listed above selected along with the Save button.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/DisableSD.png "Disabling the security defaults")

3. On the **Contoso - Overview** blade, select **Security** under **Manage** on the left.

4. On the **Security - Getting started** blade, select **Named locations** under **Manage** on the left.

5. On the **Security - Named locations** blade, select **+ New location**.

6. On the **New named location** blade, specify the following settings and select **Create**:

    - Name: **Contoso Headquarters**

    - Define the location using: **IP ranges**

    - Mark as trusted location: **Enabled**

    - IP ranges: **The public IP address of the APP1 Azure VM (this can be found on its page in the portal) in the CIDR notation (i.e., x.x.x.x/32).**

    ![In this screenshot, the 'New named location' blade of the Azure portal is depicted with the required settings listed above selected and the Create button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_Namedlocations.png "Named Location settings")

7. On the **Security - Named locations** blade, select **Configure MFA trusted IPs**. This will open a new tab in the Internet Explorer window displaying the **service settings** tab of the **multi-factor authentication** portal.

    ![In this screenshot, the MFA portal is depicted with the 'service settings' tab selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_MFA_servicesettings.png "MFA Service Settings")

8. On the **service settings** tab, in the **trusted IP** text box, type the same public IP address of the APP1 Azure VM (this can be found on its page in the portal) in the CIDR notation (i.e., x.x.x.x/32). and select **Save** at the bottom.

9.  Navigate back to the **Security - Getting started** blade on the Azure portal and select **Conditional Access** under **Protect** on the left.

10. On the **Conditional Access - Policies** blade, select **+ New policy**.

11. On the **New** blade, in the **Name** text box, type **Contoso Engineering On-Premises Conditional Access Policy**.

12. On the **New** blade, in the **Assignments** section, select **0 users and groups selected**.

13. On the **Include** tab, choose the **Select users and groups** option, select the **Users and groups** checkbox. On the **Select** blade, type **Engineering**, in the list of results, select **Engineering**, and choose **Select**.

    ![In this screenshot, the New blade is depicted with the Include tab selected along with the 'Select users and groups' and 'Users and groups' options and the Select blade is open with the Engineering group searched for and selected along with the Select button.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_UsersandGroupsInclude.png "Include Configuration Settings")

14. On the **Exclude** tab, select the **Users and groups** checkbox. On the **Select excluded users** blade, type **Engineering**. In the list of results, select **Engineering - Mandatory MFA**, and choose **Select**.

    ![In this screenshot, the New blade is depicted with the Exclude tab selected along with the 'Users and groups' option and the Select blade is open with the 'Engineering - Mandatory MFA' group searched for and selected along with the Select button.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_UsersandGroupsExclude.png "Exclude configuration settings and selections")

15. On the **New** blade, in the **Assignments** section, select **No cloud apps or actions selected**.

16. On the **Include** tab that appears, choose the **Select apps** option. On the **Select** blade, select the **Microsoft Azure Management** checkbox, choose **Select**.

    ![In this screenshot, the Include tab is depicted with the 'Select apps' option selected and the Select blade is open with the 'Microsoft Azure Management' a[[]] searched for and selected along with the Select button.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_CloudppsandActionsInclude.png "Select the Microsoft Azure Management app")

   > **Note**: Review the warning stating **Don't lock yourself out! This policy impacts the Azure portal. Before you continue, ensure that you or someone else will be able to get back into the portal**. Disregard this warning if you are configuring persistent browser session policy that works correctly only if "All cloud apps" are selected.

17. On the **New** blade, in the **Assignments** section, select **0 conditions selected**.

18. Select **Not configured** under **Sign-in risk**. In the **Sign-in risk** blade, set **Configure** to **Yes**, check the **No risk** box, and choose **Select**.

    ![In this screenshot, 'Not configured' under 'Sign-in risk' is selected and the 'Sign-in risk' blade is open with the Configure and 'No risk' options selected along with the Select button.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_SigninRisk.png "Configure sign-in risk settings")

19. Select **Not configured** under **Device platforms**. On the **Device platforms** blade, set **Configure** to **Yes**, on the **Include** tab, choose **Select device platforms**. Check the **Windows** box, and select **Done**.

    ![In this screenshot, 'Not configured' under 'Device platforms' is selected and the 'Device platforms' blade is open with the Configure, 'Select device platform', and 'Windows' options selected under the Include tab along with the Done button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_DevicePlatforms.png "Device platform settings")

20. Select **Not configured** under **Locations**. On the blade that appears, set **Configure** to **Yes**. On the **Include** tab, choose **Selected locations**. On the **Select** blade, check the **Contoso Headquarters** box, choose **Select**.
    
    ![In this screenshot, the Select blade of the Azure portal is depicted with the Contoso Headquarters box checked and the Select button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_Locations.png "Select conditional access locations")

21. Select **Not configured** under **Client apps**, on the **Client apps** blade, set **Configure** to **Yes**. Check the **Browser**, **Mobile apps and desktop clients**, **Exchange ActiveSync clients**, and **Other clients** boxes, and select **Done**.

    ![In this screenshot, 'Not configured' under 'Client apps' is selected and the 'Client apps' blade is open on the right with Configure set to Yes and the options listed above selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_Clientapps.png "Conditional access client apps selections")

22. Select **Not configured** under **Device state (Preview)**. On the **Device state (Preview)** blade, set **Configure** to **Yes**, on the **Include** tab, select the **All device state** option and select **Done**.

    ![In this screenshot, 'Not configured' under 'Device state (Preview)' is selected and the 'Device state (Preview)' blade is open on the right with Configure set to Yes and the Include tab is selected with the All 'device state' option.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_Devicestate.png "Conditional access device state settings")

23. Back on the **New** blade, in the **Access controls** section, select **0 controls selected** under **Grant**.

24. On the **Grant** blade, ensure that the **Grant access** option is selected. Check the **Require Hybrid Azure AD joined device** box, accept the default choice of **Require all the selected controls** under **For multiple controls** and choose **Select**.

    > **Note**: Review the warning **Don't lock yourself out! Make sure that your device is Hybrid Azure AD Joined**.

    ![In this screenshot, the New blade of the Azure portal is depicted with the '0 controls selected' button selected and the Grant blade open with the required options listed above selected along with the Select button.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_AccesscontrolsGrant.png "Access controls Grant settings")

25. Back on the **New** blade, in the **Access controls** section, select **0 controls selected** under **Session**. 

26. Review the **Session** blade settings but do not modify them. Close it when you are finished.

27. On the **New** blade, set **Enable policy** to **On** and select **Create**.

    ![In this screenshot, the New blade of the Azure portal is depicted with 'Enable policy' set to On and the Create button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_Final.png "Conditional access enable policy and create")

28. Back on the **Conditional Access - Policies** blade, select **What If**.

29. On the **What If** blade, specify the following settings and select **What If**:

    - User: **Teresa F. Bell**

    - Cloud apps or actions: **Microsoft Azure Management**

    - IP address: **The public IP address of the APP1 Azure VM**

    - Country or region: **United States**

    - Device platform: **Windows**

    - Client apps: **Browser**

    - Device state (Preview): **Device Hybrid AD Joined**

    - Sign-in risk: **No risk**

30. Review the evaluation results and note the policy and the grant controls that will apply.

    ![In this screenshot, within the Azure portal the What If settings and the evaluation results of the newly created conditional access policy are depicted.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AzureADConditionalAccess_WhatIf_parameters.png "What if settings and results")

31. Re-run the evaluation but first change the **Sign-in risk** to **Low** and review the evaluation results.


### Task 10: Implement Azure AD Privileged Identity Management **OPTIONAL**

In this task, you will implement Azure AD Privileged Identity Management.

1. Within the Remote Desktop session to **DC1**, in the Internet Explorer window displaying the Azure portal, navigate to the **All services** blade.

2. In the search text box, type **Privileged Identity Management** and, in the list of results, select **Azure AD Privileged Identity Management**.

3. On the **Privileged Identity Management** blade, select **Azure AD roles** under **Manage** on the left. 

4. On the **Contoso - Quick start** blade, select **Assign eligibility**.

5. On the **Contoso - Roles** blade, select **+ Add assignments**.

6. On the **Add assignments** blade, specify the following settings to designate **Ann G. Ayers** as an eligible member of the **Authentication Administrator** role then select **Next** then **Assign**. 

    - Select role: **Authentication Administrator**

    - Select members: **Ann G. Ayers**

   > **Note**: **Authentication Administrator** role grants privileges to set or reset non-password credentials and update passwords for all users. Authentication Administrators can require users to re-register against existing non-password credential (for example, MFA or FIDO) and revoke remember MFA on the device, which prompts for MFA on the next sign-in.

   ![In this screenshot, the 'Add assignments' blade of the Azure portal is depicted with the required settings listed above configured and the Next button selected.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/AddAAAssignment.png "Adding the Authentication Administrator role assignment")

7. On the **Contoso - Roles** blade, select **Assignments** under **Manage** on the left and note that **Ann G. Ayers** is listed as eligible for the **Authentication Administrator** role.

8. Switch to the Remote Desktop session to **APP1**, start Internet Explorer, and browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com). From here sign-in as Ann G. Ayers. The username can be found on the **Users - All users** page in the Azure portal window on the lab computer. The password will be **demo@pass123**. 

9. When prompted to provide additional information, select **Next**, on the **Keep your account secure** page, select **I want to set up a different method**. At the **Choose a different method** dialog, select **Phone** on the dropdown then select **Confirm**. 

    - On the **Phone** page, enter your phone number and select **Next**.

    - Enter the code in the email you received, select **Next**.

10. When prompted again to provide additional information, select **Next**, on the **Keep your account secure** page, select **I want to set up a different method**. At the **Choose a different method** dialog, select **Email** on the dropdown then select **Confirm**. 

    - On the **Email** page, enter an email address you can access and select **Next**.

    - Enter the code in the text message you received, select **Next**.

11. Back on the **Keep your account secured** page, select **Done**. You will now be able to use to Azure portal. 

12. In the Azure portal, navigate to the **Privileged Identity Management - Quick start** blade. 

13. On the **Privileged Identity Management - Quick start** blade, select **My roles** under **Tasks** on the left.

14. On the **My roles - Azure AD roles** blade, on the **Eligible assignments** tab, select **Activate** next to the **Authentication Administrator** role entry. 

15. On the **Activate - Authentication Administrator** blade, enter some random next in the **Reason** box then select **Activate**.

16. On the **Activation status**, monitor changes to your activation request. Once the activation is completed, select the **Sign out** link and then sign in back to the Azure portal to start using your newly activated role.

17. In the Azure portal, navigate back to the **My roles - Azure AD roles** blade.

18. On the **My roles - Azure AD roles** blade, on the **Active assignments** tab, note that the role assignment has been activated. 

    ![In this screenshot, the 'My roles - Azure AD roles' blade of the Azure portal is depicted with the 'Active assignments' tab selected and the Activated state of the role assignment highlighted.](images/Hands-onlabstep-bystep-HybridIdentityImages/media/ActiveAssignment.png "The role assignment is active")

**Summary**

In this exercise, you optimized authentication, authorization, and access protection for Contoso Active Directory environment integrated with the Contoso Azure AD tenant by enabling Azure AD Multi-Factor Authentication, enabling Azure AD password writeback and Self-Service Password Reset, implementing, Azure AD Password Protection, enabling Azure Active Directory Identity Protection, enabling Automatic Intune Enrollment, as well as implementing Azure AD Privileged Identity Management and Azure AD Conditional Access Policies.



## After the hands-on lab

Duration: 20 Minutes

### Task 1: Delete resources

1. Now that the HOL is complete, go ahead and delete all of the Resource Groups created for this HOL. You will no longer need those resources, and it will be beneficial to clean up your Azure Subscription. In addition, remove the verified domain from the Contoso Azure AD tenant.

You should follow all steps provided *after* attending the Hands-on lab.

