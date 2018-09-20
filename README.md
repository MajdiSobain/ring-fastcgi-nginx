# Run Ring Web Applications On NGINX Server (UBUNTU)

In this repo I will share the way that I used to run ring web application on NGINX server + FastCGI using FastCGI Wrapper [(fcgiwrap)](https://github.com/gnosek/fcgiwrap) tweak.

        Note: these steps will not cover how to install nginx server and how to create a website
        to work on with nginx. You should take your time doing that with googling.

## Steps

1- Install FastCGI wrapper on the system by using this command:

        sudo apt-get install fcgiwrap

2- Install spawn-fcgi software (Used to spawn a new process to run fastcgi wrapper)

        sudo apt-get install spawn-fcgi

3- Download "spawn-fcgiwrap" shell script from this repo and move it to "/etc/init.d" path to make it run each time system boots. Assuming that you have cloned this repo locally and started a terminal from inside its folder, you can move the script with this command:

        sudo mv spawn-fcgiwrap /etc/init.d/spawn-fcgiwrap

4- Grant this script executaion permission and initialize it using these commands:

        sudo chmod +x /etc/init.d/spawn-fcgiwrap
        update-rc.d spawn-fcgiwrap defaults

5- We can start and stop this script any time by using these commands:

        sudo /etc/init.d/spawn-fcgiwrap start
        # OR
        sudo /etc/init.d/spawn-fcgiwrap stop

6- Add next code into the location block of your website server block in the website configuration file. If you have created and configured your website before, this file should be in "/etc/nginx/sites-available/" path. Open your website configuration file and add this code inside server block:

                location ~ \.ring$ {
                        include /etc/nginx/fastcgi_params;
                        fastcgi_index  index.ring;
                        fastcgi_pass   unix:/tmp/cgi.sock;
                        fastcgi_param  SCRIPT_FILENAME  /var/www/html$fastcgi_script_name;
                }

** Note that you have to change the path "/var/www/html" part in the last line of the previous code to be as same as your website folder's path.

** Note: We should copy ring binary into a folder just next to or inside the website folder and change the path to ring that's written in the first line of each ring code file to point to it. Till now I could not run any ring application that points to "ring" as a command or even "/usr/bin/ring" may be this is due to some permission problems.

### Spawning fcgiwrap using native system socket and service implementation

There is another way to spawn a new fcgiwrap process and connect it to FastCGI using native system socket and service implementation.

This method has been clearly described in [this link](http://redmine.lighttpd.net/projects/spawn-fcgi/wiki/Systemd)

If you want to use the second method you can follow the previous steps but replace steps (2-5) with what has been mentioned in the new method and it should work as expected.


Have a nice time :)
