# Uninstall vScrawl

This section provides a step-by-step walkthrough for uninstalling **vScrawl** on an in-house server.  Before uninstalling vScrawl, always consider taking a full backup of the applicaiton files, logs and the database.

## Starting the Script
Go to the directory **uninstall-scripts** within the extracted deployment package and execute **uninstall.sh** script:

```bash
cd uninstall-scripts
./uninstall.sh
```

## Uninstall Options

The following uninstall options are available:

```bash
##########################################################################
###                                                                    ###
###                            WARNING                                 ###
###                                                                    ###
### You will loose all the data and will have to redo the installation ###
### from scratch if it is needed again. It is advised to take backups  ###
### of the application, logs and the database.                         ###
###                                                                    ###
##########################################################################
This is a critical operation. Proceed with caution.
Please select an option:
 0) Exit
 1) Remove Services
 2) Remove Docker
 3) Remove Certbot and Nginx
 4) Remove MySQL
 5) Complete Uninstall
```

> **Note**: As warned by the wizard, make sure you have taken necessary backups of the applicaiton files, Logs and the database.

Carefully choose one of the available **uninstall** options. You may choose:

- **1) Remove Services** if it is required to only remove the installed vScrawl Services.
- **2) Remove Docker** if it is additionally required to remove Docker installation, and it will remove services as well as Docker.
- **3) Remove Certbot and Nginx** to remove Lets Encrypt SSL as needed
- **4) Remove MySQL** to remove MySQL database server as needed
- **5) Complete Uninstall** if, for a strong reason, you choose to completely remove the whole installation.

If you select the desired option and hit Enter button, the relevant components will get removed and there will be a notification about completion of the uninstall operation.