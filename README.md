# azure-lab
Step 1: Create Azure Account

Go to Microsoft Azure.

Sign in using an existing Microsoft account or create a new account.

Open the Azure Portal.

Step 2: Create Storage Account

In the Azure Portal, search for Storage Accounts.

Click Create.

Enter the required details:

Resource Group: Create a new resource group.

Storage Account Name: imagestorage123

Region: Select the nearest region, such as Central India.

Click Review + Create.

Click Create.

Note: The storage account name must be globally unique. If the name is unavailable, use another unique name.

Step 3: Create Blob Container

Open the created Storage Account.

Go to Data Storage → Containers.

Click + Container.

Enter the container name:

images

Set the access level to Private.

Click Create.

Step 4: Upload Sample Images

Open the images container.

Click Upload.

Select multiple image files from your computer.

Click Upload.

Example input images:

image1.jpg
image2.jpg
image3.jpg

Step 5: Get Connection String

Open the created Storage Account.

Go to Security + networking → Access keys.

Copy the Connection string.

Keep the connection string securely for use in the Python program.

Important: Do not upload your Azure connection string or access keys to GitHub.

Step 6: Install Required Libraries

Open Command Prompt or Terminal and execute:

pip install azure-storage-blob pillow

The required libraries are:

azure-storage-blob

Pillow

Step 7: Write Multithreaded Python Code

Create a file named app.py and add the following code:

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

    blob_client = blob_service_client.get_blob_client(
        container=container_name,
        blob=blob_name
    )

    # Download image
    data = blob_client.download_blob().readall()

    stream = io.BytesIO(data)

    # Open and process image
    img = Image.open(stream)
    img = img.convert("RGB")
    img = img.resize((200, 200))

    # Save processed image
    output = io.BytesIO()
    img.save(output, format="JPEG")
    output.seek(0)

    # Upload processed image
    new_name = "processed_" + blob_name

    blob_service_client.get_blob_client(
        container=container_name,
        blob=new_name
    ).upload_blob(output, overwrite=True)

    print(f"Completed: {blob_name} -> {new_name}")


def main():

    container_client = blob_service_client.get_container_client(
        container_name
    )

    blobs = container_client.list_blobs()

    threads = []

    # Create and start threads
    for blob in blobs:

        # Skip already processed images
        if blob.name.startswith("processed_"):
            continue

        t = threading.Thread(
            target=process_image,
            args=(blob.name,)
        )

        threads.append(t)
        t.start()

    # Wait for all threads to complete
    for t in threads:
        t.join()

    print("\nAll images processed successfully")


if __name__ == "__main__":
    main()

Step 8: Run the Application

Replace:

connection_string = "YOUR_CONNECTION_STRING"

with your actual Azure Storage connection string.

Save the app.py file.

Open Command Prompt or Terminal in the project directory.

Execute:

python app.py

Step 9: Verify the Output

Go to the Azure Portal.

Open the created Storage Account.

Navigate to Containers → images.

Verify that the processed images have been created.

Output

Terminal Output

Processing: image1.jpg
Processing: image2.jpg
Processing: image3.jpg

Completed: image1.jpg -> processed_image1.jpg
Completed: image2.jpg -> processed_image2.jpg
Completed: image3.jpg -> processed_image3.jpg

All images processed successfully

Azure Blob Storage Output

The images container will contain:

image1.jpg
image2.jpg
image3.jpg
processed_image1.jpg
processed_image2.jpg
processed_image3.jpg

The processed images are resized to:

Width  : 200 pixels
Height : 200 pixels
Format : JPEG
