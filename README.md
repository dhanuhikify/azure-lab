# azure-lab
# Implementation of Thread-Based Image Processing Application in Microsoft Azure

### Step 1: Create Azure Account

1. Go to [Microsoft Azure](https://azure.microsoft.com/).
2. Sign in using an existing Microsoft account or create a new account.
3. Open the **Azure Portal**.

### Step 2: Create Storage Account

1. In the Azure Portal, search for **Storage Accounts**.
2. Click **Create**.
3. Enter the required details:
   - **Resource Group:** Create a new resource group.
   - **Storage Account Name:** `imagestorage123`
   - **Region:** Select the nearest region, such as **Central India**.
4. Click **Review + Create**.
5. Click **Create**.

> **Note:** The storage account name must be globally unique. If the name is unavailable, use another unique name.

### Step 3: Create Blob Container

1. Open the created **Storage Account**.
2. Go to **Data Storage → Containers**.
3. Click **+ Container**.
4. Enter the container name:

```text
images
