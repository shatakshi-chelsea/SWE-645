# Assignment Submission: Hosting Web Application on AWS S3 and EC2

Name: Shatakshi Rathore  
GNo:  G01545382
GId:  Srathor
Course: SWE-645 


---

## URLs
- Homepage (S3 hosted):   http://shatakshi-rathore.s3-website.us-east-2.amazonaws.com
- Homepage (EC2 hosted):  http://3.144.145.60

---

## Files Included
- `index.html` – Homepage with "About Me", "Journey", and link to Student Survey
- `survey.html` – Student Survey form
- `Me.jpeg` – Profile image used on homepage
- `error.html` - Page that handles error
- `README.md` – Instructions and deployment details

---

## Steps to Deploy on AWS S3

1. Log in to [AWS Management Console](https://aws.amazon.com/).
2. Create an S3 bucket with a globally unique name. (shatakshi-rathore) 
   - Enable **Static Website Hosting**.  
   - Set index document to `index.html`.
3. Upload `index.html`, `urvey.html`, and `Me.jpeg` to the bucket.
4. Update the **Bucket Policy** to allow public read access for all objects.  
  
   ```json```
  {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::shatakshi-rathore/*"
        }
    ]
}







## Steps to Deploy on AWS EC2

1. *Step 1:* -- Launch an EC2 Instance


    a. Sign in to your AWS Console.

    b. Search for EC2 → click EC2.

    c. Click Launch Instances.

    d. Name: shatakshi-rathore.

    e. AMI (Amazon Machine Image): Choose Amazon Linux 2 AMI (64-bit).

    f. Instance type: t3.micro (Free Tier eligible) → Click Next.

    g. Key Pair:

        Create a new key pair → name it shatakshi-key.

        Download the .pem file → keep it safe, you’ll need it to SSH.

    h. Network Settings (Security Group):

        Add HTTP (port 80) and SSH (port 22) rules.

        Source for HTTP: 0.0.0.0/0 (allow public access)

        Source for SSH: Your IP only (for security)

        Click Add rule → Select Type = SSH

        AWS automatically sets: Protocol = TCP, Port = 22


    i. Click Launch Instance.



2. *Step 2:* -- Connect to Your EC2 Instance
  
    a. After the instance is running, click Connect → Instructions for SSH.

    b. Open a terminal (Mac/Linux) 

    c. Use your .pem file to connect:

      ```chmod 400 shatakshi-key.pem```
      ```ssh -i "shatakshi-key.pem" ec2-user@3.144.145.60```




3. *Step 3:* -- Install Apache Web Server

  a. Once connected via SSH, run:

      ```sudo yum update -y```
      ```sudo yum install httpd -y```


  b. Start Apache:

    ```sudo systemctl start httpd```
    ```sudo systemctl enable httpd```


  c. Check if Apache is running:

    ```sudo systemctl status httpd```





4. *Step 4:* -- Upload Your Website Files


  a. Using SCP (Command Line)

    From your local machine terminal:

    ```scp -i shatakshi-key.pem index.html survey.html Me.jpeg ec2-user@3.144.145.60:/home/ec2-user/```




5. *Step 5:* -- Move Files to Apache Document Root

  a. Apache default root: /var/www/html.

  b. Move files:

    ```sudo mv /home/ec2-user/index.html /var/www/html/```
    ```sudo mv /home/ec2-user/survey.html /var/www/html/```
    ```sudo mv /home/ec2-user/Me.jpeg /var/www/html/```


  c. Set proper permissions:

    ```sudo chown apache:apache /var/www/html/*```
    ```sudo chmod 644 /var/www/html/*```



