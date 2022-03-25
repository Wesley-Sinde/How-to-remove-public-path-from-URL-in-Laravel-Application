

# PLEASE NOTE when serving a Laravel project with Docker: you won't need to do any of this. Only use this option when your root (or more commonly: public_html) directory of your website is your Laravel project (this is not the case when you're using Docker).

<h1>DON'T!</h2>
YOU REALLY SHOULD NOT rename server.php in your Laravel root folder to index.php and copy the .htaccess file from the /public directory to your Laravel root folder!!!

This way everyone can access some of your files (.env for example). Try it yourself. You don't want that!

# DO
Instead, you should create an .htaccess file in your root like this:

```php
RewriteEngine On
RewriteCond %{REQUEST_URI} !^/public/
RewriteRule ^(.*)$ /public/$1 [L,QSA]

```

This will silently rewrite all your base URIs to the /public folder. Even all Headers, for example the HTTP Authorization Header, and all optional URI parameters will silently be passed on to the /public folder as well.

# Other Methods 

While hosting on the Laravel project on cPanel, the traditional problem that lots of developers get is, /public path is appended to the URL. Because in most cases, we put a project directly into the public_html folder, so public_html is our root for the website and that's where our laravel application is also placed.

But to run the Laravel application, we need to point our domain root to the public folder of the laravel. It is possible to do it with cPanel but you need to go through some steps which are not known by most of the people and also the tedious process. So to make it simple, what you can do is, there is a way we can do it via the .htaccess file in our root folder.

We can copy the .htaccess file from our public folder and then make modifications to it to work with the direct root folder and route every request to the public folder.

Here is the final .htaccess file,

```php
<IfModule mod_rewrite.c>     
<IfModule mod_negotiation.c>        
Options -MultiViews -Indexes    
</IfModule>
    RewriteEngine On

    # Handle Authorization MemberHeader
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Redirect Trailing Slashes If Not A Folder...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} (.+)/$
    RewriteRule ^ %1 [L,R=301]

    # Remove public URL from the path
    RewriteCond %{REQUEST_URI} !^/public/
    RewriteRule ^(.*)$ /public/$1 [L,QSA]

    # Handle Front Controller...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]
    
   
   ```
    
By adding the above file to the root folder we can use laravel projects without the public path. Check out the following two lines:

```php
  RewriteCond %{REQUEST_URI} !^/public/   
  RewriteRule ^(.*)$ /public/$1 [L,QSA]
  ```
These two lines make magic and our application will work without public path in URL.
