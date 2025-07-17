# Image Pixelation Project

This project automates the pixelation of uploaded images into various resolutions. When a user uploads an image to a designated S3 bucket, an AWS Lambda function is triggered. This function processes the image, generating multiple pixelated versions (e.g., 64x64, 48x48, 32x32, etc.), and then uploads these processed images to a separate output S3 bucket.


## Project Overview

The core idea is to provide a seamless image pixelation service. Users simply upload their images, and the system automatically handles the processing and delivers the pixelated outputs. This can be useful for various applications such as generating avatars, creating low-resolution previews, or artistic effects.

## Architecture

The project leverages the following AWS services:

- **Amazon S3 (`image-receiver-psk-bucket`):** This bucket acts as the entry point for user-uploaded images.
- **AWS Lambda:** A serverless function that is triggered by new image uploads to `image-receiver-psk-bucket`. It contains the image processing logic.
- **Amazon S3 (`output-image-psk-bucket`):** This bucket stores the various pixelated versions of the original images.

Here's a simplified diagram of the architecture:
This project is referenced by Yeshwanthlm.
reference repo link : (github repository)[https://github.com/yeshwanthlm/image-pixelater-aws-project]
