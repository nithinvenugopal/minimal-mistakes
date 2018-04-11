---
published: true
---
Boto is the Amazon Web Services (AWS) SDK for Python, which allows Python developers to write software that use AWS services. Boto provides an easy to use, object-oriented API as well as low-level direct service access. Boto is urrently in its third version and hence called boto3. [Here](https://boto3.readthedocs.io/en/latest/) is the link to official boto3 website/documentation.

Installing boto3 on macOS is easy but sometimes users may face some issues. Below are the clear steps you need to install boto3.


1. Download pip

    `curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`
    
2. Install pip

    `python get-pip.py`
    
3. Install boto3 using pip. Make sure you use --user option to avoid operation not permitted error

    `pip install boto3 --user`
    
    
Now you can use the boto3 sdk by importing in your python script as below-

    import boto3
