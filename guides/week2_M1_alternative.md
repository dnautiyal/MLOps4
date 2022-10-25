# M1 Alternative Setup


Due to the issues with the M1 chip, we've created an AWS Elastic Compute Cloud (EC2) server for each student experiencing issues installing docker.

This setup will take roughly 10 minutes


## Cloning and PEM File Permissions
First let's create a fourthbrain directory

```
mkdir fourtbrain
```

**Please see image below as well**

```
cd fourthbrain
```

For cloning the repository, we use https in the next line. You can also use SSH as in the image.

**Note that this is repository is unique in that it contains the .pem file we will need for EC2**

```
git clone https://github.com/Ali-SC/week2_awesomeness.git
```

```
cd week2_awesomeness
```

Change permissions on the .pem file

```
chmod 400 rmali.pem
```

![1](https://user-images.githubusercontent.com/37101144/192112000-a3247613-c34e-4504-9f37-12b8dc148d22.png)

## VS Code - Remote SSH
Install the `Remote SSH` extension

<img width="1920" alt="2" src="https://user-images.githubusercontent.com/37101144/192112001-5bc29382-4c22-403c-b95f-5206972d4a16.png">

Open up the `Command Palette` using <kbd>⌘</kbd>+<kbd>SHIFT</kbd>+<kbd>P</kbd> or by navigating to `View`>`Command Palette` in the top toolbar.

Type `remote ssh config` and click on the highlighted selection in the image.

<img width="1290" alt="3" src="https://user-images.githubusercontent.com/37101144/192112002-2da88c59-f622-4013-868a-15dcbee77596.png">

Select the highlighted config that beings with `/Users/.../..`


<img width="680" alt="Screen Shot 2022-09-24 at 12 29 57 PM" src="https://user-images.githubusercontent.com/37101144/192112050-d20b68b7-ef67-4c3f-b33a-561ce1d6a1e4.png">

Modify the config file with server information that was sent to you. Remember to give the directory of your .pem file in the `IdentityFile`.

**Save this file when done with <kbd>⌘</kbd>+<kbd>S</kbd>**

<img width="750" alt="Screen Shot 2022-09-24 at 12 32 54 PM" src="https://user-images.githubusercontent.com/37101144/192112051-7fdcf47f-7d69-4849-b490-88389c45266a.png">

**Please make sure to save the above file!** ^^

Open up the `Command Palette` again using <kbd>⌘</kbd>+<kbd>SHIFT</kbd>+<kbd>P</kbd> or by navigating to `View`>`Command Palette` in the top toolbar.

Type `remote ssh connect` and select the blue highlighted selection in the image `Remote SSH: Connect to Host...`

<img width="750" alt="Screen Shot 2022-09-24 at 12 33 41 PM" src="https://user-images.githubusercontent.com/37101144/192112052-e09d1d1d-8c3a-452a-9b8a-88d86b152d6f.png">

Choose the host you just created `aws-week2-server`

<img width="750" alt="Screen Shot 2022-09-24 at 12 33 50 PM" src="https://user-images.githubusercontent.com/37101144/192112053-0228bf6a-88d5-49ae-9228-3b458aad525c.png">

A new window will open, click `Continue`
<img width="1920" alt="Screen Shot 2022-09-24 at 12 34 17 PM" src="https://user-images.githubusercontent.com/37101144/192112054-75fc08aa-5001-42bf-ae82-996bbf6185ed.png">

After a minute, confirm that you are connected. You can check in the bottom left to see if you're connected.
<img width="1920" alt="Screen Shot 2022-09-24 at 12 37 42 PM" src="https://user-images.githubusercontent.com/37101144/192112200-c626b1ab-f779-40ff-bcd6-16fa9af1ed82.png">

Click on the `File Explorer` icon in the top left (circled below). 

Then click on the blue `Open Folder` button on the left. 

Select the default directory `..`, show in the arrow below.
![Screen Shot 2022-09-24 at 12 39 09 PM](https://user-images.githubusercontent.com/37101144/192113263-78cc70d1-250c-4697-aa0d-b2296c933944.png)

This will open a new window and allow you see enter the contents of the server. 

Check the box for `Trust the authors of all files in the parent folder 'home'`

Click the blue, `Yes, I trust the authors` button.

<img width="1920" alt="Screen Shot 2022-09-24 at 12 39 27 PM" src="https://user-images.githubusercontent.com/37101144/192113357-2dc3de8c-74e9-4f2b-9fc1-44285acbd9b2.png">

To show the terminal, click on `View` in the top toolbar, and select `Terminal`.

<img width="1920" alt="Screen Shot 2022-09-24 at 12 39 44 PM" src="https://user-images.githubusercontent.com/37101144/192113431-2bd3c123-3bd1-42fb-8ca7-866513e5ba6d.png">

You should now be all set! Please install all requirements as needed including tensorflow, docker, etc. 

<img width="1920" alt="Screen Shot 2022-09-24 at 12 40 19 PM" src="https://user-images.githubusercontent.com/37101144/192113475-0035ec85-0e4a-47b8-a7b3-efd5eca3c3e9.png">

