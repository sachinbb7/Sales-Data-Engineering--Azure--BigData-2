## 🔹 Creating a Unity Catalog Metastore Manually in Azure Databricks

1. Retrieve your **User Principal Name (UPN)** from Microsoft Entra ID (Azure AD).  
2. Log in to **Azure Databricks** using this UPN.  
   - You will be recognized as an **account admin**, and the **Admin Console** will open.  
3. In the Admin Console, add your user account as an **admin**.  
4. Switch back to your regular Azure Databricks workspace account.  
   - You should now see a new option: **Manage Account** (top-right corner, next to your username).  
5. Click on **Manage Account → Catalog**.  
6. From the Catalog view, click **Create Metastore** and complete the setup.

✅ After this, the Unity Catalog metastore will be available to link with your Databricks workspaces.
