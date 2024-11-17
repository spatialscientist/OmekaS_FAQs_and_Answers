# OmekaS FAQs and Answers
I intend to, where I can, collate all Omeka S FAQs received privately and provide anonymized answers publicly

**Important:** 
  - I advise that you post your questions in the [Omeka S Forum](https://forum.omeka.org/), but you can also reach out directly if you want. Where I have given an answer previously, I may anonymize and list my answer for others.
  - Please check the [Omeka S Forum](https://forum.omeka.org/) space to explore FAQs before contacting me.
    
## Question 1 (17-11-2024)

Firstly, I have to thank you for your great Docker images [Omeka S 411](https://github.com/spatialscientist/omeka-s411-docker) and [Omeka S 410](https://github.com/spatialscientist/omeka-s410-docker) versions. I installed it on Docker Desktop on Windows and it is working perfectly.

About the questions - in fact, they are not directly about the image because it is working as I said, but maybe you already had a similar situation so you know the solution.

1. **How do I edit the `php.ini` file in Docker to allow for uploading larger images in Omeka?**  
2. **How do I upgrade an Omeka module, specifically the "Common" module, to a newer version?**


## Answer 1 (17-11-2024)

To upload larger images on Omeka, editing the `php.ini` file is necessary. Here's how to do it:

#### Option 1: Using a Custom `php.ini` File

1. Create a custom `php.ini` file on your host machine with the desired configuration, for example:
   ```ini
   upload_max_filesize = 50M
   post_max_size = 50M
   ```

2. Mount the custom `php.ini` file to the appropriate location inside the container using the `-v` option:
   ```bash
   docker run -d -v /path/to/your/php.ini:/usr/local/etc/php/php.ini your-docker-image
   ```

3. Restart your container to apply changes:
   ```bash
   docker stop <container_id>
   docker rm <container_id>
   docker run -d -v /path/to/your/php.ini:/usr/local/etc/php/php.ini your-docker-image
   ```

#### Option 2: Editing `php.ini` Inside the Container

1. Access the running container:
   ```bash
   docker exec -it <container_id> bash
   ```

2. Navigate to the location of `php.ini` (usually `/usr/local/etc/php/`) and edit it using an editor like `vi` or `nano`.

3. Restart the container after saving changes:
   ```bash
   docker restart <container_id>
   ```

### 2. Updating Omeka Modules

To update the "Common" module or any other Omeka module, follow these steps:

1. **Download the Latest Version**:
   - Visit the Omeka Modules Repository and download the latest version of the module.

2. **Backup the Existing Module**:
   - Inside the container, navigate to the modules directory:
     ```bash
     docker exec -it <container_id> bash
     cd /var/www/html/modules
     mv Common Common_backup
     ```

3. **Install the New Version**:
   - Copy the new module version into the container:
     ```bash
     docker cp /path/to/new/Common.zip <container_id>:/var/www/html/modules
     ```

   - Unzip the new version:
     ```bash
     unzip Common.zip -d /var/www/html/modules/
     ```

4. **Check and Activate**:
   - Log in to Omeka’s admin panel and go to the Modules section.
   - Ensure the new version is recognized and activate it if necessary.
   - You may have to update other modules affected by the activation of the new version using similar approach outlined above.

5. **Remove the Backup**:
   - After confirming everything works as expected, remove the backup:
     ```bash
     rm -rf /var/www/html/modules/Common_backup
     ```

## Further comment

- Using a mounted `php.ini` file (`-v` option) is more Docker-friendly and avoids direct editing of the container.
- Always back up your Omeka installation and database before performing significant changes.

