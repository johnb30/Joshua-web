This package serves as API and UI for a Joshua-decoder based translation service. Joshua-decoder is a Machine Translation ToolKit, for more details, see http://joshua-decoder.org/

This application assumes you have the Joshua-decoder service installed and that you can call Joshua scripts.

##Setup

Change Joshua/setting.py `JOSHUA_SCRIPT_EXECUTABLE`, JOSHUA_SCRIPT_FILENAME and JOSHUA_WEB_API_SUPPORT_LANGUAGES to reflect your Joshua-decoder server setup.  See conf/translate_input.sh for sample scripts that read from stdin and netcat to Joshua-decoder server


The following python dependences are required.
This was developed and tested with python 2.7+

```
curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
python get-pip.py
pip install django==1.8
pip install markdown defusedxml
pip install lxml
pip install dateutil
pip install pyyaml
pip install django-tastypie
```
 
Depending on the version of tastypie you have installed, you might also need to copy files in the patch folder to the `$Python/dist-packages/tastypie` folder:
 
	To support UTF8 encoding of result data from API for json format, modify 
	utils/mime.py to comment out 
	if format in ('application/json', 'text/javascript'):
 		    return format
 	or simply copy patch/tastpie/utils/mime.py to $Python/dist-packages/tastype/utils folder.
 	
 	copy patch/tastpie/resources.py to $Python/dist-packages/tastype/utils if see run time error on resources.py.
 	(tastypie>0.12.1 should have fix the issue).
 	
 	

##Running 

To start the Django server:

copy Joshua folder to the server where Joshua script is avaialble. 

```
cd Joshua 
python manage.py runserver 0.0.0.0:80
```
 
The API will allow both GET and POST method, they are available at :

```
http://localhost/api/translation/schema/?format=xml
http://localhost/api/translation/?format=json&orig_language=x&orig_text=" " , 
```

it also support format=xml

example:


```
curl http://localhost/api/translation/?format=json&orig_language=ar&orig_text="كيف حال الطقس اليوم"
```
  
```
curl -H "Content-Type: application/json;charset=UTF-8" -X POST -d '{"orig_language": "ar", "orig_text": "كيف حال الطقس اليوم"}' http://localhost/api/translation/
```

 http://localhost will return a user interface to allow user interact with server using textarea.

To start up django server automatically in centos, install supervisord in centos and put `conf/supervisord/django_cms.conf` to the include folder. See https://rayed.com/wordpress/?p=1496 for detail of how to install and setup supervisor.


For a production configuration, you might want to deploying Django with Apache and mod_wsgi:

 install mod_wsgi to your Apache server, append your Apache serverâ€™s 
 httpd.conf file with conf/apache/httpd.conf file.
 this assume, you are copying Joshua-web folder to /var/www/ folder.
 
 tested configuration in  ubuntu:
	 apt-get install libapache2-mod-wsgi
	 nano /etc/apache2/sites-enabled/000-default
	  
	 and add  following lines just below <VirtualHost *:80>
	
		WSGIDaemonProcess Joshua-web 		
		WSGIProcessGroup Joshua-web
		WSGIScriptAlias / /var/www/Joshua-web/conf/apache/django.wsgi
 		Alias /static /var/www/Joshua-web/static
   		Alias /media   /var/www/Joshua-web/static/
   		


    	service apache2 restart
   


