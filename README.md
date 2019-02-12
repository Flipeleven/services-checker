# Services Checker

Bash Script to check if services are running and restart them if they are not. Sends an email to you if something happened.

Tested with Ubuntu 14.04, 16.04 and 18.04. Gmail integration based on https://www.systutorials.com/1411/sending-email-from-mailx-command-in-linux-using-gmails-smtp/

## Server Prerequisites

You need to have **mailx** installed on the server:
`sudo apt-get install heirloom-mailx`

## Installation and Configuration

1. Clone this repository into an appropriate scripts folder. For example:
```
$ cd /usr/local/sbin/scripts
$ git clone https://github.com/Flipeleven/services-checker.git
$ cd services-checker
```
2. Make sure only the root user can access the script, because you will be putting a password in there:
```
$ sudo chmod 700 restart-services
$ ls -l
```
Make sure the listing shows root as the owner. Should look something like:
```
-rwx------ 1 root root 2274 Apr 12 17:30 restart-services
```

3. Open `restart-services` in a text editor and edit these lines:
```
## set email address to receive the message
TO_EMAIL="you@example.com"

## set SMTP authentication and name
SMTP_EMAIL="someone@domain.com"
SMTP_NICENAME="John Smith"
SMTP_PASSWORD="abcde12345"
```
4. Set the services you want to keep an eye on (by default it has **mysql** and **apache2**. You can add or take away whatever you need).
5. Save your changes and exit.
6. Create a cronjob as root by typing `sudo crontab -e` and add something like this, which runs every five minutes (adjust to your needs):
```
#check on services
*/5 * * * * /usr/local/sbin/scripts/services-checker/restart-services
```

The script will check the status of each service. If the service is stopped, it tries to restart the service. If the service starts, it sends you an email saying the service stopped but was restarted.

If the service does not start for some reason, it sends you an email telling you it was not started.

After that, it will continue to try and start, but not send any more emails until the service is finally started.

## Verification

You can test if this works for you by manually stopping a service. For example:
```
$ sudo service mysql stop
```
If the script works, you should get an email the next time your cron job runs telling you that the service was successfully restarted.