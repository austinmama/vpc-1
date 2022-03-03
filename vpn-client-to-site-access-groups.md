---

copyright:
  years: 2021
lastupdated: "2021-08-26"

keywords:

subcollection: vpc

---

{{site.data.keyword.attribute-definition-list}}

# Creating an IAM access group and granting the role to connect to the VPN server (Beta)
{: #create-iam-access-group}

Client VPN for VPC is available to all IBM Cloud users. After the Beta period ends, you will be given a time period to migrate your VPN servers to the standard pricing plan to avoid disruption of service.
{: beta}

FOR USER ID AND PASSCODE AUTHENTICATION ONLY

To create an IAM access group and grant the user role to connect to the VPN server, follow these steps:

1. From the IBM Cloud console, navigate to the [Access groups](https://cloud.ibm.com/iam/groups){: external} page (**Manage > Access (IAM) > Access groups**) and click **Create**.
1. Type a name for your access group and optional description, then click **Create**.
1. Click the **Access policies** tab, then click **Assign access**.
1. From the drop-down menu, select **VPC Infrastructure Services**. 

   ![Assigning access to an access group](images/vpn-access-role.png){: caption="Assigning access to an access group" caption-side="bottom"}

1. For Service access, select **Users of the VPN server need this role to connect to the VPN server**, then click **Add**.

   ![Assigning access role to an access group](images/vpn-access.png ){: caption="Assigning an access role to an access group" caption-side="bottom"}

1. Review the Access summary panel and click **Assign**.
1. Add users to this group. 
   * To add existing users to this group:
      * Click the **Users** tab, then click **Add users**.
      * Select the checkbox next to each existing user that requires VPN Client access. Then, click **Add to group**.
   * To add new users to this group:  W-I-P
      * Click **Manage > Access > Invite Users**.
      * Enter the email address of each user that needs to be invited in the "Enter email address" box (up to 100)
      * In the group table, click the "Add" link for the IAM Group that was created in step 11
      * Click the blue "Invite" button on the right and side
      * Each user will receive an email from IBM Cloud asking them to join an account in IBM Cloud

          * In that email there will be a "Join now" link they will need to click on 
          * Following the link, they will need to enter their First Name, Last Name, and Password (country can be changed if needed)
          * Clicking "Join Account" will create their own IBMid User and add it to the correct account

For more information, see [Setting up access groups](/docs/account?topic=account-groups). For IAM required permissions and the minimum IAM role to perform a task, see [Client-to-site VPN server tasks](/docs/vpc?topic=vpc-resource-authorizations-required-for-api-and-cli-calls#vpn-server-authorizations-required-for-api-and-cli-calls).
{: note}
