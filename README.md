# Image Pixelation Project

This project automates the pixelation of uploaded images into various resolutions. When a user uploads an image to a designated S3 bucket, an AWS Lambda function is triggered. This function processes the image, generating multiple pixelated versions (e.g., 64x64, 48x48, 32x32, etc.), and then uploads these processed images to a separate output S3 bucket.



### Deployment Steps

Follow these steps to set up and deploy the Image Pixelation Project:

1.  **Create S3 Bucket for Receiver (`image-receiver-psk-bucket`):**
    * Go to the S3 console and click "Create bucket".
    * For "Bucket name", i choose to enter `image-receiver-psk-bucket`.
    * Leave other settings as default.
    * Ensure "Block all public access" is enabled (this is the default for new buckets).
    * Click "Create bucket".

2.  **Create S3 Bucket for Output Images (`output-image-psk-bucket`):**
    * Go to the S3 console and click "Create bucket".
    * For "Bucket name", enter `output-image-psk-bucket`.
    * Leave other settings as default.
    * Ensure "Block all public access" is enabled.
    * Click "Create bucket".

3.  **Create Lambda Function:**
    * Go to the Lambda console and click "Create function".
    * Select "Author from scratch".
    * For "Function name", provide a descriptive name (e.g., `image-pixelator-function`).
    * For "Runtime", select `Python 3.9`. (other your function will not work well , make sure it is 3.9 version)
    * For "Architecture", select `arm64` (Graviton2).
    * Under "Change default execution role", select "Create a new role with basic Lambda permissions". This will create an IAM role for your Lambda function.
    * Click "Create function".

    3.1. **Replace Python Code:**
    * Once the function is created, scroll down to the "Code source" section.
    * Upload your Lambda function [code](https://github.com/pyaesoekyaw/serverless-image-bluring/blob/main/lambda_python.py) to replace the default code.

    3.2. **Edit Timeout:**
    * Go to the "Configuration" tab of your Lambda function.
    * Click "General configuration" on the left panel, then "Edit".
    * Change the "Timeout" value to `5 min`.
    * Click "Save".

    3.3. **Add Environment Variable:**
    * In the "Configuration" tab, click "Environment variables" on the left panel, then "Edit".
    * Click "Add environment variable".
    * For "Key", enter `processed_bucket`.
    * For "Value", enter `output-image-psk-bucket`.
    * Click "Save".

    3.4. **Create Layer:**
    * Go to the Lambda console, click "Layers" on the left navigation, then "Create layer".
    * For "Name", provide a name (e.g., `image-processing-libraries`).
    * For "Description", add a brief description.
    * Click "Upload a .zip file" and upload your [python.zip](https://github.com/pyaesoekyaw/serverless-image-bluring/tree/main/Layer_file).
    * For "Compatible runtimes", select `Python 3.9`.
    * For "Compatible architectures", select `arm64`.
    * Click "Create".

    3.5. **Add Layer to Lambda Function and Deploy:**
    * Go back to your Lambda function (`image-pixelator-function`).
    * Scroll down to the "Layers" section and click "Add a layer".
    * Select "Custom layers".
    * Choose the layer you just created (e.g., `image-processing-libraries`).
    * Select the appropriate version.(for us , it is `1`)
    * Click "Add".
    * After adding the layer, click the "Deploy" button to apply the changes.

4.  **Configure IAM Role Policy:**
    * Go to the IAM console.
    * Click "Roles" on the left navigation and search for the role created for your Lambda function (e.g., `image-pixelator-function-role-xxxxxxxx`).
    * Under the "Permissions" tab, click "Add permissions" then "Create inline policy".
    * Select "JSON" tab and paste the [policies](https://github.com/pyaesoekyaw/serverless-image-bluring/blob/main/lambda-role-policy.txt).
    * Click "Review policy", give it a name (e.g., `S3AccessForPixelator`), and click "Create policy".

5.  **Configure S3 Trigger for Lambda:**
    * Go back to your Lambda function (`image-pixelator-function`).
    * In the "Function overview" section, click "Add trigger".
    * Select "S3" as the trigger source.
    * For "Bucket", choose `image-receiver-psk-bucket`.
    * For "Event types", select "All object create events".
    * (Optional) You can add a prefix or suffix if you only want to process specific file types (e.g., `.jpg`, `.png`).
    * Check "I acknowledge that using the same S3 bucket for input and output might create a recursive loop." (This project uses separate buckets, so this is just a general warning acknowledgment).
    * Click "Add".

Once the setup is complete, using the project is straightforward:

1.  **Upload an Image:** Simply upload an image file (e.g., a `.jpg` or `.png` file) to the `image-receiver-psk-bucket` S3 bucket.
2.  **Monitor Lambda CloudWatch:** Go to the Lambda function console, click on "Monitor" tab, then "View logs in CloudWatch". You can observe the execution logs of your Lambda function here to check for any errors or progress.
3.  **Verify Output:** After a short processing time, the various pixelated versions of your image will appear in the `output-image-psk-bucket` S3 bucket. The file names will likely indicate their resolution (e.g., `original_image_name_64x64.png`).


This project is referenced by Yeshwanthlm.
reference repo link : [github repository](https://github.com/yeshwanthlm/image-pixelater-aws-project)
