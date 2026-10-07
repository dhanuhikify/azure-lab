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

images

### Step 4: Upload Sample Images

1. Open the `images` container.
2. Click **Upload**.
3. Select multiple image files from your computer.
4. Click **Upload**.

Example input images:


image1.jpg
image2.jpg
image3.jpg

### Step 5: Get Connection String

1. Open the created **Storage Account**.
2. Go to **Security + networking → Access keys**.
3. Copy the **Connection string**.
4. Keep the connection string securely for use in the Python program.

> **Important:** Do not upload your Azure connection string or access keys to GitHub.

### Step 6: Install Required Libraries

Open Command Prompt or Terminal and execute:

pip install azure-storage-blob pillow
### Step 7: Write Multithreaded Python Code

Create a Python file named `app.py` and add the following code:

```python
import threading
from azure.storage.blob import BlobServiceClient
from PIL import Image
import io

# Azure connection
connection_string = "YOUR_CONNECTION_STRING"
container_name = "images"

blob_service_client = BlobServiceClient.from_connection_string(
    connection_string
)


def process_image(blob_name):

    print(f"Processing: {blob_name}")

    # Get blob client
    blob_client = blob_service_client.get_blob_client(
        container=container_name,
        blob=blob_name
    )

    # Download image
    data = blob_client.download_blob().readall()

    # Create image stream
    stream = io.BytesIO(data)

    # Open and process image
    img = Image.open(stream)
    img = img.convert("RGB")
    img = img.resize((200, 200))

    # Save processed image
    output = io.BytesIO()
    img.save(output, format="JPEG")
    output.seek(0)

    # Create new image name
    new_name = "processed_" + blob_name

    # Upload processed image
    blob_service_client.get_blob_client(
        container=container_name,
        blob=new_name
    ).upload_blob(
        output,
        overwrite=True
    )

    print(f"Completed: {blob_name} -> {new_name}")


def main():

    # Get container client
    container_client = blob_service_client.get_container_client(
        container_name
    )

    # List all blobs
    blobs = container_client.list_blobs()

    # Store threads
    threads = []

    # Create and start a thread for each image
    for blob in blobs:

        # Skip already processed images
        if blob.name.startswith("processed_"):
            continue

        thread = threading.Thread(
            target=process_image,
            args=(blob.name,)
        )

        threads.append(thread)
        thread.start()

    # Wait for all threads to complete
    for thread in threads:
        thread.join()

    print("\nAll images processed successfully")


if __name__ == "__main__":
    main()
```
### Step 8: Run the Application

1. Replace:


connection_string = "YOUR_CONNECTION_STRING"


### Step 9: Verify the Output

1. Go to the **Azure Portal**.
2. Open the created **Storage Account**.
3. Navigate to **Data Storage → Containers**.
4. Open the `images` container.
5. Verify that the processed images have been created.

The `images` container should contain:


image1.jpg
image2.jpg
image3.jpg
processed_image1.jpg
processed_image2.jpg
processed_image3.jpg

## Output

### Terminal Output


Processing: image1.jpg
Processing: image2.jpg
Processing: image3.jpg

Completed: image1.jpg -> processed_image1.jpg
Completed: image2.jpg -> processed_image2.jpg
Completed: image3.jpg -> processed_image3.jpg

All images processed successfully
### Processed Image Details

| Property | Details |
|---|---|
| Input Images | image1.jpg, image2.jpg, image3.jpg |
| Processing Method | Python Multithreading |
| Image Processing | Image Resizing |
| Original Location | Azure Blob Storage |
| Output Location | Azure Blob Storage |
| Output Image Size | 200 × 200 pixels |
| Output Format | JPEG |
| Output Naming | `processed_<original_filename>` |
| Number of Threads | One thread per image |
| Status | Successfully Processed |
