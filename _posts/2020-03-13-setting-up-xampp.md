---
layout: post
title: "Setting Up XAMPP for Local Development"
date: 2020-03-13
tags: [php, mysql, apache, webdev]
---

Before Docker was the default answer to "how do I run a local dev environment," there was XAMPP — and honestly, it's still one of the fastest ways to get Apache, PHP, and MySQL running on a machine without fighting your OS for an hour first. It's been my go-to starter kit for years, so here's exactly how I set it up, quirks and all.

## Installing XAMPP

1. Download XAMPP for your OS (Windows, in this walkthrough)
2. Run the installer — feel free to ignore the UAC prompt
3. Pick your install directory. I avoid the C: drive and put it on a dedicated dev drive instead — keeps things tidy and easier to wipe/reinstall later
4. Fire up the XAMPP Control Panel

## Configuring Apache, PHP, and MySQL

### Accessing phpMyAdmin over a network

Out of the box, trying to hit phpMyAdmin from another machine on your network gets you slapped with:
```bash
Access forbidden!
New XAMPP security concept:
Access to the requested directory is only available from the local network.
```
**Fix:**
1. Open the XAMPP Control Panel
2. Click the Apache config button, open `httpd-xampp.conf`
3. Find `Require local`, change it to `Require all granted`
4. Restart Apache

### Setting phpMyAdmin credentials

1. Open the XAMPP Control Panel
2. Click the Apache config button, open `config.inc.php`
3. Add a Blowfish secret key:

```php
$cfg['blowfish_secret'] = 'yourblowfishkeyhere12345';
```

4. Update the auth settings:

```php
$cfg['Servers'][$i]['auth_type'] = 'cookie';
$cfg['Servers'][$i]['user'] = 'root';
$cfg['Servers'][$i]['password'] = 'yourpasswordhere';
$cfg['Servers'][$i]['AllowNoPassword'] = false;
```

5. Restart Apache

### Granting MySQL access over the network

By default, MySQL's root user is locked to localhost only. To open it up:

1. Open phpMyAdmin or log in via CLI
2. Run:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'yourpassword' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

### Configuring PHP's timezone

XAMPP ships with PHP's timezone defaulted to `Europe/Berlin`, which is rarely useful unless you're actually in Berlin.

1. Open the XAMPP Control Panel, click Apache config, open `php.ini`
2. Find `date.timezone` and set it to yours:

```ini
date.timezone=Asia/Manila
```

3. Save and restart Apache

### Changing Apache's port

If port 80 is already taken — commonly by IIS or Nginx — you'll need to move Apache off it:

1. Open the XAMPP Control Panel
2. Go to **Config → Service and Port Settings → Apache**
3. Open `httpd.conf`, find `Listen 80`, swap in your preferred port
4. Restart Apache

## Configuring the FileZilla FTP Server

XAMPP bundles a FileZilla Server for local file transfers, useful when you want an FTP-based workflow into your dev environment:

1. Start FileZilla Server from the XAMPP panel
2. Log into Admin (`127.0.0.1`, port `14147`, no password by default)
3. Go to **Edit → Settings** and set an admin password immediately
4. Create an FTP group — Group icon → Add Group → point it at your project directory under Shared Folders
5. Create an FTP user, assign it to that group
6. Test by connecting to `ftp://yourhostserver`

## Configuring Tomcat for Java apps

Tomcat gives you a Java web server environment running on port `8080` by default, but user roles need to be set up before it's usable.

Add this to `tomcat-users.xml`:

```xml
<tomcat-users>
  <role rolename="admin-gui"/>
  <role rolename="manager-gui"/>
  <user username="username" password="yourpasswordhere" roles="admin-gui,manager-gui"/>
</tomcat-users>
```

1. Open the XAMPP Control Panel
2. Config → `tomcat-users.xml`
3. Paste the snippet in
4. Restart the Tomcat service

## All set

And that's the full stack — Apache, PHP, MySQL, FTP, and even Tomcat if you need Java in the mix — configured and ready for local development. It's not the flashiest setup in 2026, but there's a reason it's stuck around this long: it just works, and it gets out of your way.
