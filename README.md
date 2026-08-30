☁️ AWS LAMP Notes Application

A simple cloud-based Notes application built and deployed using AWS EC2, Apache, API Gateway, AWS Lambda, and Amazon S3.

The project demonstrates how a frontend web application can communicate with a serverless backend through REST APIs and use Amazon S3 as persistent storage for text notes.

📌 Project Overview

The AWS LAMP Notes Application allows users to create and view notes from a web browser.

A user can:

✍️ Enter a note in a text box

💾 Save the note

☁️ Store the note as a .txt object in Amazon S3

🔄 Retrieve previously saved notes

📋 Display all saved notes on the webpage

The frontend is hosted on an AWS EC2 Ubuntu server using Apache. JavaScript communicates with the backend through Amazon API Gateway. API Gateway invokes AWS Lambda functions, which interact with the Amazon S3 bucket.

🏗️ System Architecture

                         USER
                           |
                           | HTTP
                           v
                +----------------------+
                |      Web Browser     |
                |   HTML / CSS / JS    |
                +----------+-----------+
                           |
                           | HTTP Request
                           v
                +----------------------+
                |       AWS EC2        |
                |       Ubuntu         |
                |       Apache         |
                |                      |
                |    index.html        |
                |    style.css         |
                |    script.js         |
                +----------+-----------+
                           |
                           | JavaScript fetch()
                           | HTTPS
                           v
                +----------------------+
                |    API Gateway       |
                |                      |
                |    GET /notes        |
                |    POST /notes       |
                +----------+-----------+
                           |
                  +--------+--------+
                  |                 |
                  v                 v
        +----------------+  +----------------+
        |   GET Lambda   |  |  POST Lambda   |
        |                |  |                |
        | Read notes     |  | Save note      |
        | from S3        |  | to S3          |
        +-------+--------+  +-------+--------+
                |                   |
                +---------+---------+
                          |
                          v
                 +-------------------+
                 |    Amazon S3      |
                 |                   |
                 | Bucket:           |
                 | nihal2004         |
                 |                   |
                 | .txt notes        |
                 +-------------------+

☁️ AWS Services Used

AWS ServicePurpose

Amazon EC2

Hosts the frontend website

Ubuntu

Operating system running on EC2

Apache

Web server used to serve the website

Amazon API Gateway

Provides HTTP API endpoints

AWS Lambda

Processes GET and POST requests

Amazon S3

Stores notes as text files

IAM

Provides permissions for Lambda to access S3

📁 Project Structure

The project contains the following files:

LAMP Application Hosting/
│
├── index.html
├── style.css
├── script.js
└── README.md

index.html

The main webpage of the application.

It contains:

Application title

Text area for entering notes

Save Note button

Saved Notes section

Refresh button

style.css

Contains the styling for the application, including:

Page layout

Text area design

Buttons

Notes cards

Spacing and typography

Responsive layout

script.js

Contains the frontend application logic.

It:

Sends POST requests to save notes

Sends GET requests to retrieve notes

Displays saved notes

Handles errors

Validates empty notes

Automatically loads notes when the page opens

README.md

Contains the documentation for the complete project.

🖥️ EC2 and LAMP Setup

The frontend is hosted on an Ubuntu EC2 instance.

Apache is used as the web server.

The website files are stored in:

/var/www/html/

The expected deployment structure is:

/var/www/html/
│
├── index.html
├── style.css
└── script.js

Apache serves these files to users through the EC2 public IP address.

The application was tested using:

http://3.110.28.239

Note: A production deployment should use HTTPS and a domain name instead of exposing the application directly through an HTTP public IP.

🔌 API Gateway

Amazon API Gateway is used as the communication layer between the frontend and Lambda functions.

The deployed API uses the following base URL:

https://wdl02i5m44.execute-api.ap-south-1.amazonaws.com/prod

The Notes endpoint is:

https://wdl02i5m44.execute-api.ap-south-1.amazonaws.com/prod/notes

The API uses two HTTP methods.

POST /notes

Used to create and save a new note.

POST /notes

Integration:

POSTLambda

Example request:

{
    "note": "My first AWS note"
}

GET /notes

Used to retrieve all saved notes.

GET /notes

Integration:

GETLambda

The GET Lambda reads the note objects from S3 and returns the notes as JSON.

⚡ POST Lambda

The POST Lambda receives a note from API Gateway and stores it in Amazon S3.

The Lambda:

Receives the API request.

Reads the request body.

Extracts the note value.

Creates a timestamp-based filename.

Creates a .txt object.

Uploads the object to the S3 bucket.

Returns a success response.

Example filename:

20260730_083319.txt

Example response:

{
    "statusCode": 200,
    "body": "{\"message\": \"Note Saved\", \"filename\": \"20260730_083319.txt\"}"
}

📖 GET Lambda

The GET Lambda retrieves the saved notes from S3.

The Lambda:

Receives the GET request.

Lists objects in the S3 bucket.

Checks whether objects exist.

Reads each object.

Converts the file contents to text.

Adds the text to a notes array.

Returns the notes as JSON.

Example response:

[
    "My first AWS note",
    "Test note from EC2",
    "Another note"
]

🪣 Amazon S3

The project uses the following S3 bucket:

nihal2004

Notes are stored as text objects inside this bucket.

Example:

nihal2004/
│
├── 20260730_083319.txt
├── 20260730_084501.txt
└── 20260730_090125.txt

Each text file contains the content entered by the user.

For example:

20260730_084501.txt

could contain:

Test note from EC2

🔐 IAM Permissions

The Lambda execution role requires permission to interact with the S3 bucket.

The project uses permissions including:

s3:PutObject
s3:GetObject
s3:ListBucket

The permissions are associated with the Lambda execution role through an IAM policy.

Example policy name used during the project:

LambdaS3NotesAccess

The policy allows the Lambda functions to perform the required S3 operations.

🌐 CORS Configuration

Because the frontend communicates with API Gateway from a browser, CORS is configured for the API.

The configured values include:

Allowed Origin:
*

Allowed Methods:
GET
POST
OPTIONS

Allowed Headers:
Content-Type

This allows the browser-based frontend to communicate with the API.

For a production application, the wildcard origin (*) should be replaced with the specific website origin when appropriate.

🔄 Application Workflow

Saving a Note

The complete POST process is:

User enters note
       |
       v
Clicks "Save Note"
       |
       v
JavaScript
       |
       | POST /notes
       v
API Gateway
       |
       v
POSTLambda
       |
       | PutObject
       v
Amazon S3
       |
       v
Note stored as .txt file
       |
       v
Lambda returns success
       |
       v
Website displays success message

Retrieving Notes

The complete GET process is:

User opens website
       |
       v
JavaScript calls GET /notes
       |
       v
API Gateway
       |
       v
GETLambda
       |
       | List/Get Objects
       v
Amazon S3
       |
       v
Lambda creates JSON response
       |
       v
API Gateway
       |
       v
JavaScript
       |
       v
Saved Notes displayed

🧪 Testing

The application was tested from the EC2-hosted website.

A test note was entered:

Test note from EC2

The user clicked:

Save Note

The note was successfully processed by the backend and then retrieved through the GET API.

After clicking:

Refresh

the saved note appeared under the Saved Notes section.

✅ Test Results

Test CaseExpected ResultStatus

Start EC2 instance

EC2 instance runs successfully

✅ Passed

Start Apache

Apache serves website

✅ Passed

Open website

Notes application appears

✅ Passed

Enter note

User can type a note

✅ Passed

Save Note

POST request is sent

✅ Passed

POST Lambda

Lambda processes note

✅ Passed

S3 upload

.txt file is created

✅ Passed

GET request

Saved notes are retrieved

✅ Passed

GET Lambda

Lambda reads S3 objects

✅ Passed

Display notes

Notes appear on webpage

✅ Passed

Refresh

Latest notes are displayed

✅ Passed

🖼️ Recommended Screenshots for Project Submission

The following screenshots can be included in the project report.

Screenshot 1 — EC2 Instance

Show:

EC2 instance

Running status

Public IP

Region

Suggested caption:

Figure 1: AWS EC2 instance used to host the web application.

Screenshot 2 — Apache Website

Show the Notes application in the browser.

Suggested caption:

Figure 2: Notes application hosted on the EC2 Apache web server.

Screenshot 3 — API Gateway

Show:

GET /notes
POST /notes

Suggested caption:

Figure 3: API Gateway routes connecting the frontend to Lambda functions.

Screenshot 4 — POST Lambda

Show the POST Lambda code and successful test.

Suggested caption:

Figure 4: POST Lambda function used to save notes to Amazon S3.

Screenshot 5 — GET Lambda

Show the GET Lambda code.

Suggested caption:

Figure 5: GET Lambda function used to retrieve notes from Amazon S3.

Screenshot 6 — S3 Bucket

Show:

nihal2004

and the .txt note objects.

Suggested caption:

Figure 6: Amazon S3 bucket containing the saved note files.

Screenshot 7 — Successful Application Test

Show the webpage containing:

Test note from EC2

Suggested caption:

Figure 7: Successful end-to-end test of the Notes application.

🧩 Error Handling

The frontend includes basic error handling.

If saving fails, the application displays:

Failed to save note.

If retrieving notes fails, the application displays:

Unable to load notes.

The browser console also logs errors to help with debugging.

🔒 Security Considerations

The project is intended as an educational AWS/LAMP application.

For a production deployment, the following improvements are recommended:

1. HTTPS

Use HTTPS instead of:

http://3.110.28.239

A domain name and TLS certificate can be configured for a production website.

2. Restrict CORS

Instead of:

*

configure CORS to allow only the application's trusted origin.

3. Least-Privilege IAM

Only grant Lambda the S3 permissions that it actually requires.

4. Protect SSH Keys

Never upload private SSH keys to GitHub.

For example:

aws-key.pem

must not be included in the Git repository.

5. Authentication

A production application should authenticate users before allowing them to create or retrieve notes.

6. Backend Validation

The Lambda function should validate note size and content before storing it.

⚠️ Important Files to Exclude from Git

Do not commit private credentials or infrastructure secrets.

For example:

aws-key.pem

should never be uploaded to GitHub.

A .gitignore file can be used:

*.pem
.env

🚀 Future Improvements

The current application provides the basic note creation and retrieval functionality.

Possible future improvements include:

🗑️ Delete notes

✏️ Edit notes

🔍 Search notes

🏷️ Add categories or tags

📅 Display creation dates

👤 User authentication

🔐 Private notes for individual users

🌐 Custom domain

🔒 HTTPS

📱 Improved mobile interface

📊 CloudWatch monitoring

📝 DynamoDB-based metadata storage

🔔 Notifications

📦 Infrastructure as Code using AWS CloudFormation or Terraform

📊 Project Learning Outcomes

This project demonstrates practical experience with:

AWS EC2

Ubuntu Linux

Apache web server

LAMP-style hosting

HTML

CSS

JavaScript

REST APIs

API Gateway

AWS Lambda

Python

boto3

Amazon S3

IAM roles and policies

CORS

Cloud deployment

Client-server communication

🎯 Project Objective Achievement

The original objective was to create an online Notes application where:

A user enters text into a textbox.

The website sends the note using an HTTP POST request.

API Gateway receives the request.

Lambda processes the note.

Lambda creates a text file.

The text file is stored in the S3 bucket.

A GET request retrieves the previous notes.

The website displays the saved notes.

All of these core requirements have been implemented and tested successfully.

🏁 Conclusion

The AWS LAMP Notes Application successfully demonstrates the integration of a traditional web server with AWS serverless and cloud storage services.

The frontend is hosted on an Ubuntu EC2 instance using Apache. JavaScript communicates with API Gateway through HTTP requests. API Gateway routes the requests to separate Lambda functions for creating and retrieving notes. Amazon S3 provides persistent storage for the notes.

The application was successfully tested by creating a note such as:

Test note from EC2

and retrieving it through the application.

This project provides a practical example of combining EC2, Apache, API Gateway, Lambda, IAM, and S3 to create a functional cloud-based web application.

👨‍💻 Project Information

Project: AWS LAMP Notes Application

Frontend: HTML, CSS, JavaScript

Web Server: Apache

Operating System: Ubuntu

Cloud Platform: Amazon Web Services (AWS)

Storage: Amazon S3

Backend: AWS Lambda

API: Amazon API Gateway

S3 Bucket: nihal2004

AWS Region: ap-south-1 (Mumbai)

Website: http://3.110.28.239

API Endpoint:

https://wdl02i5m44.execute-api.ap-south-1.amazonaws.com/prod/notes

📄 License

This project was created for educational and project-submission purposes.
