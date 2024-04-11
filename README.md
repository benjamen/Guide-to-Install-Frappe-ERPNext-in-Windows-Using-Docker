# Guide-to-Install-Frappe-ERPNext-in-Windows-11-Using-Docker

A complete Guide to Install Frappe Bench in Windows 11 Using Docker and install Frappe/ERPNext Application
These have been forked and updated based on my Windows 10 set up and now works without any errors.

### Pre-requisites 

      Docker Desktop(Running)
      git
      Wnidows 11 (or 10)
      VS Code
      Redis - this can be tricky too if anything fails
      MariaDB this can be tricky too if anything fails (you might need to delete databases via Docker if steps fail)
      	mysql -u root -p 
      	SHOW DATABASES; 
       	DROP DATABASE <db_name>;
      sudo apt update && sudo apt install supervisor
    

### STEP 1 Check Docker version
    docker version
    git version

### STEP 2  Clone frappe_docker and move to frappe_docker folder

    git clone https://github.com/frappe/frappe_docker.git
    cd frappe_docker

### STEP 3

   Copy example devcontainer config from 
    
    devcontainer-example folder to .devcontainer folder
    
   Copy example vscode config for devcontainer from 
    
    development/vscode-example folder to development/.vscode folder
   
### STEP 4 Install VSCode Remote Containers extension
    
    Open vscode and install 'Dev Containers' extension
    
###  STEP 5 After the extensions are installed, you can

  Open frappe_docker folder in VS Code.
  
  Launch the command, from Command Palette (Ctrl + Shift + P) Remote-Containers: Reopen in Container. You can also click in the bottom left corner to access the remote   container menu.
  
### Note: 
   if this error in running contaners try the below commnad in CMD
   
   Error starting userland proxy: listen tcp 0.0.0.0:80: bind: An attempt was made to access a socket in a way forbidden by its access permissions
	
    netsh http add iplisten ipaddress=::
                
   The development directory is ignored by git. It is mounted and available inside the container. Create all your benches (installations of bench, the tool that          manages frappe) inside this directory.
   Node v14 and v10 are installed. Check with nvm ls. Node v14 is used by default.
                
    
### STEP 6 Initilase frappe bench with frappe version 15 and Switch directory

    
    bench init --skip-redis-config-generation --frappe-branch version-15 frappe-bench
    cd frappe-bench
    
    
### STEP 7 Setup hosts
    
   We need to tell bench to use the right containers instead of localhost. Run the following commands inside the container:

    bench set-config -g db_host mariadb
    bench set-config -g redis_cache redis://redis-cache:6379
    bench set-config -g redis_queue redis://redis-queue:6379
    bench set-config -g redis_socketio redis://redis-socketio:6379
  For any reason the above commands fail, set the values in common_site_config.json manually.

    {
      "db_host": "mariadb",
      "redis_cache": "redis://redis-cache:6379",
      "redis_queue": "redis://redis-queue:6379",
      "redis_socketio": "redis://redis-socketio:6379"
    }
    
### STEP 8 Create a new site
   sitename MUST end with .localhost for trying deployments locally.
   MariaDB root password: 123
    
    bench new-site {yoururlname}.localhost --no-mariadb-socket 
    Remember MariaDB root password: 123 (make sure you enter this else half this command works and then you have delete the site and database)
    
    
### STEP 9 Set bench developer mode on the new site
    
    bench --site {yoururlname}.localhost set-config developer_mode 1
    bench --site {yoururlname}.localhost clear-cache
    sudo service supervisor stop
    sudo nano /etc/supervisor/supervisord.conf

(Add these lines under [unix_http_server])

chmod=0760
chown=frappe:frappe
    sudo service supervisor start
    
    
### STEP 10 Install ERPNext
    
    bench get-app --branch version-14 --resolve-deps erpnext
    bnech get-app payments (required for this verison)
    bench --site {yoururlname}.localhost install-app erpnext
    
 ### STEP 11 Go to Docker frappe_docker_devcontainer-frappe-1
    
    stop container
    go to files -> etc/hosts
    add {yoururlname}.localhost
    start container
    
### STEP 12 Start Frappe bench 
    
    sudo service supervisor stop
    sudo service supervisor start
    bench start
    
 ### STEP 13 Navigate to and Log In
    
    You can now login with username (not email): Administrator 
    and the password you choose when creating the site. 
    Your website will now be accessible at location {yoururlname}.localhost:8000

  ### STEP 14 Install other Frappe Apps (Optional)
    
    Make sure bench is runing in other terminal - running your site and open a new terminal and cd in into the frappe-bench folder:
    	#chat
     	bench get-app chat
     	bench --site {yoururlname}.localhost install-app chat

 	#woocommerce
	bench get-app https://github.com/libracore/woocommerceconnector.git
 	bench --site {yoururlname}.localhost install-app woocommerceconnector

 	#healthcare
	bench get-app healthcare
 	bench --site {yoururlname}.localhost install-app healthcare

	bench get-app hospitality
 	bench --site {yoururlname}.localhost install-app hospitality

 	bench get-app education
   	bench --site {yoururlname}.localhost install-app education

 	bench get-app lending
	bench --site {yoururlname}.localhost install-app lending

 $ bench get-app agriculture
$ bench --site demo.com install-app agriculture

$ bench get-app hrms
$ bench --site sitename install-app hrms

$ bench get-app non_profit
$ bench --site demo.com install-app non_profit

  ### STEP 15 Install other Frappe sites (Optional)

  	bench new-site lms.test
	bench get-app lms
	bench --site lms.test install-app lms
	bench --site lms.test add-to-hosts

	Install Frappe CRM app:
	$ bench get-app crm
	Create a site with the crm app:
	$ bench --site sitename.localhost install-app crm
	Open the site in the browser:
	$ bench browse sitename.localhost --user Administrator
	Access the crm page at sitename.localhost:8000/crm in your web browser.

	Start the server by running bench start
	In a separate terminal window, create a new site by running bench new-site insights.test
	Map your site to localhost with the command bench --site insights.test add-to-hosts
	Get the Insights app. Run bench get-app https://github.com/frappe/insights
	Run bench --site insights.test install-app insights.
	Now open the URL http://insights.test:8000/insights in your browser, you should see the app running

 	Install bench and setup a frappe-bench directory by following the Installation Steps
	Start the server by running
	bench start
	In a separate terminal window, create a new site by running
	bench new-site print-designer.test
	Map your site to localhost with the command
	bench --site print-designer.test add-to-hosts
	Get the Print Designer app
	bench get-app https://github.com/frappe/print_designer
	Install the app on the site.
	bench --site print-designer.test install-app print_designer
	Open http://print-designer.test:8000/ in your browser and go through the setup wizard.
	
	After the setup is complete now open http://print-designer.test:8000/app/print-designer/

	 Install and setup bench by following this guide
	
	In the bench directory, run bench start and keep it running
	
	Open another terminal in bench directory, and run these commands
	
	bench get-app helpdesk
	bench new-site helpdesk.test
	bench --site helpdesk.test install-app helpdesk
	bench --site helpdesk.test add-to-hosts
	You can now access Helpdesk at http://helpdesk.test

	 Once ERPNext is installed, add the webshop app to your bench by running
	
	$ bench get-app webshop
	After that, you can install the webshop app on the required site by running
	
	$ bench --site sitename install-app webshop

	 # get app
	$ bench get-app https://github.com/frappe/wiki
	
	# install on site
	$ bench --site sitename install-app wiki


	Install Bench.
	Install Frappe Builder app:
	$ bench get-app builder
	Create a site with the builder app:
	$ bench --site sitename.localhost install-app builder
	Open the site in the browser:
	$ bench browse sitename.localhost --user Administrator
	Access the builder page at sitename.localhost:8000/builder in your web browser.

	bench new-site gameplan.test
	bench get-app gameplan
	bench --site gameplan.test install-app gameplan
	bench --site gameplan.test add-to-hosts
	bench --site gameplan.test browse --user Administrator
	Now, open a new terminal session and cd into frappe-bench/apps/gameplan, and run the following commands:
	yarn
	yarn dev
	Now, you can access the site on vite dev server at http://gameplan.test:8080


	bench get-app https://github.com/frappe/drive
	Create a new site
	
	bench new-site drive.site
	Map your site to localhost
	
	bench --site drive.site add-to-hosts
	Install the app onto your site
	
	bench --site drive.site install-app drive
	Start the bench server
	
	bench start
	Start the frontend development server
	
	cd apps/drive && yarn dev
	Finally, open the URL http://drive.site:8000/drive in your browser to see the app running.
	
### STEP 16 Restart and Migrate Apps

### Errors with redis? Try:
	./env/bin/pip3 install --upgrade redis
	then:
	bench start

	bench use {yoururlname}.localhost
	bench migrate
	sudo service supervisor start
	bench --site {yoururlname}.localhost clear-website-cache
	bench start

 	bench doctor
 	bench update --requirements
	bench setup socketio
	bench setup redis
	bench retry-upgrade
	sudo supervisorctl restart
	sudo service nginx restart
 

 https://codewithkarani.com/2024/01/28/resolving-frappe-application-startup-issue-no-module-named-redis-commands/
   
