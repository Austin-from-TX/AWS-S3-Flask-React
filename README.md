# AWS S3 Demo with Flask-React Stack

* PostgreSQL
* Flask
* React

## Set Up

**Packages to install in your backend**

* [boto3](https://github.com/boto/boto3)

```bash
pipenv install boto3
```

**Create your AWS User and Bucket**

1. Navigate to aws.amazon.com and `Create an AWS Account`.
2. Once signed into the AWS console, search for `S3: Scalable Storage in the Cloud` and click the link to go to the S3 Management Console
3. Once there, click `Create Bucket`
4. In the Create Bucket form, enter a name, choose a region, and leave all other options as default.
    * You can opt to make all of your content in this bucket public by unchecking the checkbox by `Block all public access` AND checking the acknowledgment checkbox that shows up below.
    * Create the bucket
    * You're going to need the name of your bucket in a second, so you'll want to keep this tab with your bucket open.
5. In the top search bar, search for `IAM: Manage access to AWS resources` and open the link in a new tab. This should take you to the Identity and Access Management (IAM) Console.
6. Once there, click `Users` under `Access management` in the left sidebar.
7. Click `Add user`.
8. Name the user whatever you like and give the user `Programmatic access`. Click `Next: Permissions`
9. Here is where you'll set up the security policy for your new user.
    * Click `Attach existing policies directly`
    * Click `Create Policy`. This will open up in a new tab.
10. In the new tab, click on the `JSON` tab and paste the following:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1420751757000",
      "Effect": "Allow",
      "Action": ["s3:*"],
      "Resource": "arn:aws:s3:::<NAME OF BUCKET>/*"
    }
  ]
}
```
    * Make sure to replace `<NAME OF BUCKET>` with the name of your bucket.
    * Click `Next: Tags`
    * Click `Next: Review`
    * Give the policy a name (maybe something like `s3-access-to-name-of-project`).
    * Click `Create policy`

11. After creating the policy, go back to the IAM Management Console where you were creating a user.
12. Click the refresh icon in the middle of the screen above the table of policies.
13. Search for your new policy and check the checkbox to the left of it.
14. Click `Next: Tags`
15. Click `Next: Review`
16. Review your new user and make sure the policy you've attached is correct. Then click `Create user`.
17. You will now get the `Access Key ID` and the `Secret Access Key`. Make sure to save both somewhere safe. You can (should) download the `.csv` file. **Store this somewhere safe on your computer.**
    * Note: If you don't somehow get your keys here, you will have to generate new keys through IAM because AWS will not give you an old secret key after this page.

## Set up AWS S3 in your backend

`config.py`

Add the following lines to your Config class inside your config file.

```python
S3_BUCKET = os.environ.get("S3_BUCKET_NAME")
S3_KEY = os.environ.get("S3_ACCESS_KEY")
S3_SECRET = os.environ.get("S3_SECRET_ACCESS_KEY")
S3_LOCATION = f"http://{S3_BUCKET}.s3.amazonaws.com/"
```

Your entire `config.py` file should now look something like this:

```python
import os


class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL')
    SQLALCHEMY_ECHO = True

    S3_BUCKET = os.environ.get("S3_BUCKET_NAME")
    S3_KEY = os.environ.get("S3_ACCESS_KEY")
    S3_SECRET = os.environ.get("S3_SECRET_ACCESS_KEY")
    S3_LOCATION = f"http://{S3_BUCKET}.s3.amazonaws.com/"
```

`awsS3.py`

Make a file called `aws_s3.py` as a module inside of your Flask `app` directory.

In there, you will see that you are using the package `boto3` to connect to your AWS S3 bucket. This is getting your keys from your config file, so make sure to add your keys to you `.env` file.

```env
S3_BUCKET_NAME=
S3_ACCESS_KEY=
S3_SECRET_ACCESS_KEY=
```

##### How to set up your credentials

All you need to do is have the correct configuration in your `config.py` file and have the environment variables set. Once you deploy to Heroku, you will also need to set the keys as environment variables there.

### MAKE SURE TO GITIGNORE YOUR .ENV FILE

## Public File Uploads

If you absolutely don't want your files to be publicly available to just any user, then you want your files to be private. If you don't care if users are able to access those files, then you can set up the files to be publicly readable.

Public upload is recommended for most of the use cases for your portfolio projects.

### Public Files

How to set up uploading and reading public files on the backend.

#### Public File Write Configuration


