## Hardware Requirement 
    1. Raspberry Pi/Pi2/Pi3  (or any development board that can run Node.js 4.2.x+ )
    2. Micro SD (16G+)
    3. USB Sound Card
    4. Microphone 
    5. Speaker 
    6. PL2303HXD USB To TTL Serial Cable (used to login raspberry pi. Optional)
    

# Setup Humix Think

## 1. Create your bluemix account

If you don't have bluemix account, you can follow the steps in this [link](https://www.ibm.com/developerworks/cloud/library/cl-bluemix-fundamentals-start-your-free-trial/) to create one. 


Once login, set your region to US South:

<img border="0" height="280" src="https://1.bp.blogspot.com/-wnsU8Sj6xyI/Vw81z3pRSlI/AAAAAAAAABs/PtqygkrMWAowDsHq5ZqtZ5cmM_WLuc7-gCLcB/s1600/IBM%2BBluemix%2B-region2.png" width="400" /> 

## 2. Deploy Humix Think 

You can run Humix Think either on bluemix or run it locally. First, clone the repository to your local machine

    git clone https://github.com/project-humix/humix-think.git
   

### Deploy on bluemix

The first step is to pick a name for your application. So enter the think directory and modify the manifest.xml

    cd humix-ng/think
    vim manifest.yml

<img border="0" height="280" src="https://4.bp.blogspot.com/-DG2AZWai6XI/Vw9RbQ6jfBI/AAAAAAAAACA/Z-qpv-dcEncJl_QmZy2swW_GR8kqD83RACKgB/s1600/humix-ng-think_manifest.png" width="400" /> <br>
 <br>
 
update the `name` and `host` properties. Make sure the name is unique, otherwise the process will fail when trying to deploy on bluemix. 


example manifest.yml : <br>
``` 
applications:
- path: .
      memory: 512M
      instances: 1
      domain: mybluemix.net
      name: humix-pi2
      host: humix-pi2
      disk_quota: 1024M
      services:
      - Humix-Cloudant-Service
      - Humix-Dialog-Service
      - Humix-NLC-Service
      - Humix-Speech-Service
      command: node --max-old-space-size=384 app.js --settings ./bluemix-settings.js -v 
 ```

 ** Note : You can remove "Humix-Dialog-Service" and "Humix-NLC-Service" if you don't want to use these services for designing the conversation. These are optional. 

 #### install cf client 
 
 You will need cf client to push the application into bluemix. If you don't cf-client install, you can get it [here](https://github.com/cloudfoundry/cli)

 #### Push application to bluemix 
 
 Next, we will deploy the humix think to blueimx. We will execute a script that automatically create associated services needed and bind them to humix think. The script will also deploy the humix think on bluemix. 
 
     cd humix-ng/think
     ./deployThink.sh
     
     
```
    API endpoint: https://api.ng.bluemix.net
    Email> liuch@tw.ibm.com
    Password>

    Authenticating...
    OK
    Targeted org liuch@tw.ibm.com
    Select a space (or press enter to skip):
    1. dev
    2. demo
    3. personal
    
    Space> 1

    Targeted space dev
    
    API endpoint:   https://api.ng.bluemix.net (API version: 2.40.0)
    User:           liuch@tw.ibm.com
    Org:            liuch@tw.ibm.com
    Space:          dev
    Creating service instance Humix-Cloudant-Service in org liuch@tw.ibm.com / space dev as liuch@tw.ibm.com... 
```
  if everything works well, you can find your application in the following link (change your application name)

__http://< your_app_name >.mybluemix.net__

<img border="0" height="280" src="https://3.bp.blogspot.com/-ntpV9i7u44g/VxEyXVlCufI/AAAAAAAAAG4/dSGYiqs_ZGIpSqAPBB2aHZlZyt9NkjKgwCLcB/s1600/humix-pi2-addsense.png" width="400" />



### Deploy Locally

Alternatively, you can run humix think locally.

    cd humix-ng/think
    npm install
    npm start
    

Then you can visit your local `humix think` via http://localhost:3000

TODO: Add steps to use local fs as persistent storage

    
 # Setup Humix Sense
 
 We have simplified the steps required to setup Humix Sense by providing a base image. You can download the image here
 
Image location ：[humix-sense.img](http://119.81.185.45/humix_image/20160330-humix-jessie-alpha.img.gz) 

To save disk space, the image is compressed. So to copy the image into your SD card ( at least 16G ), run ( you need to replace the path and your sd disk number)

    gzip -dc /path/to/image.gz | dd of=/dev/sdx
    

## Config Network

Configure network setting of raspberry pi as you normally do. I'll skip the steps here. 


## Config humix sense config

The next step is to tell humix sense where the humix think located. Assuming you have deployed humix think at http:/humix-pi2.mybluemix.net

    cd ~/humix-ng/sense
    vi config.js
    
    
**example:**
```
    module.exports = {
        thinkURL : 'http://humix-pi2.mybluemix.net/',
        senseId  : 'humix-pi2'
    }
```

## Config Watson STT & TTS credentials
Next, you need to provide the credential of the Speech-Recognition and Text-To-Speech ( You should have created these two services in previous steps)

    cd ~/humix-ng/sense/modules/core/humix-dialog-module/lib/
    vi config.js


<img border="0" height="280" src="https://4.bp.blogspot.com/-DLIadhPYcgU/Vw95gLGNfkI/AAAAAAAAAC8/gSkUB4RErfASbhQ8Bx1KybxyiaS4EL0tACLcB/s1600/IBM%2BBluemix%2B-watson.png" width="400" /> <br>


> **< Note > get the credential of Watson STT on bluemix <br>
<img border="0" height="280" src="https://1.bp.blogspot.com/-zrmCCnDEXGw/Vw99ew2egWI/AAAAAAAAADg/FCckacR_BfoIIUx4s1qEvPScVAi7IYBLwCLcB/s1600/IBM%2BBluemix%2Bapp2.png" width="400" /> <br>
<img border="0" height="280" src="https://3.bp.blogspot.com/-vWl2kRxMyek/Vw97S0Yis6I/AAAAAAAAADM/Va-5-Jb8OdAsMpiPL26sySTLsXxs-y90ACLcB/s1600/IBM%2BBluemix%2B-environment.png" width="400" /> <br>
<img border="0" height="280" src="https://3.bp.blogspot.com/-BQOqL-H3xNc/Vw9-Cvod2gI/AAAAAAAAADo/cNa0HT6Qp_4ektVlPy3iuxTy3_I43p0XACLcB/s1600/humix-ng-think_pws.png" width="400" /> <br>


## Lauch Humix Sense

To run humix sense, simply run

    cd humix-ng/sense
    npm start
    
When you see the follow result, then your `humix sense` has been successfully connected to `humix think`

<img border="0" height="280" src="https://1.bp.blogspot.com/--SaSvdNwxAc/VxDCiCZr2YI/AAAAAAAAAEU/qii75kWgaG46QD--q2HGQ-ihNE-v-MefwCLcB/s1600/pi--humix-ng-sense.png" width="400" /> <br>


You can now config the basic flow on `humix think`

   



