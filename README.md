# Magento Security Checklist
This is a community sourced checklist of security measures to take before launching your store. Think of it as a pre-flight checklist so you don't forget something important because you've been in a headlong rush for the last few months to get all the features of your eCommerce site built.

_This checklist will apply to only Magento 2.x versions._

## Custom code
* Are all inputs validated on the server side? 
  * (TODO: Link to validation tutorial)
* Are you sanitizing all inputs also? (eg: casting to integers if a number is expected?)
* Are you correctly protecting your code against PHP string comparison vulnerabilities? (eg. == vs ===)
  * [Blog post explaining vulnerability](https://hydrasky.com/network-security/php-string-comparison-vulnerabilities/)
* Are you using Magento built in `form_key` validation on all custom forms? 
  * (TODO: Link for more info on CSRF Anti-forgery tokens)
* Are you properly escaping all output to screen using `escapeHtml()`, `escapeJs()` and similar functions?
* Are you setting `HttpOnly` and `Secure` flag on any custom cookies?
* Are you using the `Magento\Config\Model\Config\Backend\Encrypted` backend model for sensitive API data to be stored in the DB? eg: payment gateway keys.
* Are you using any dynamic code execution functions like `eval`, `shell_exec` or backticks (``)? 
  * [Exploitable PHP functions](https://stackoverflow.com/questions/3115559/exploitable-php-functions)
* Are you building MySQL queries properly and not using direct queries?
* Are all sensitive or system specific settings treated as sensitive configuration? 
  * [DevDocs on sensitive configuration](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/configuration/sensitive-and-environment-settings.html#how-to-specify-values-as-sensitive-or-system-specific)
* Are you adhering to the Magento Technical Guidelines when developing custom code?
  * [Magento Technical Guidelines](https://devdocs.magento.com/guides/v2.3/coding-standards/technical-guidelines.html)
* Are you sanitizing/ filtering user inputs and dynamic data against malicious code to prevent cross-site scripting (XSS)? 
  * [XSS prevention strategies](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/xss-protection.html)

## Extension security checklist
* Do you have the latest version of all the extensions being used on the site?
  * [Magento Vulnerable extensions](https://github.com/gwillem/magevulndb)
* Are all your extensions using Magento ACL properly to limit the scope of users?
  * (TODO: ACL tutorial)
* Did you perform a security audit on 3rd party extensions used on the store? 
  * At the very least execute Extension Code Review against [Magento Coding Standards](https://github.com/magento/magento-coding-standard)
  * Use a [tool](https://packagist.org/packages/roave/security-advisories) that reports the usage of dependencies with known security vulnerabilities 
  * Review if Passwords/API Credentials or other Sensitive Data stored by 3rd Party Extension are not save in Database using `plaintext`!
  * (TODO: Link to more sample tools to help perform security audit on extensions)
* Review the need of using `Magento_Swagger` & `Magento_Version` Modules in Production Environment (consider to `Disable` those CORE Modules or at the very least restrict access to `hostname/magento_version` and `hostname/swagger`)
* Are you updating the 3rd party extensions on a regular basis?

## Magento Admin settings 
* Are you using a custom Magento Admin URL?
* Is the secret key added to admin URLs?
* Is access to your Admin URL IP whitelisted or protected via VPN? 
* If you can't make the Admin URL private, are you using CAPTCHA/ reCAPTCHA for admin login form?
* Is the admin session lifetime set to a reasonable configuration (1-2hrs)?
* Are you reinforcing admin password renewal at least every 3 months?
* Are you disabling the admin account sharing?
* Are you limiting the failed admin login attempts to a reasonable number of times (eg 3)?
* Confirm that Web -> Default Cookie Settings -> Use HTTP Only is not disabled?
* Are you using 2FA for your admin login? 
  * [MageSpecialist TwoFactorAuth module](https://github.com/magespecialist/m2-MSP_TwoFactorAuth) 
* Are there any old/unused admin users which were created for testing or development that should be removed?
* Are you using CAPTCHA/ reCAPTCHA for customer facing web forms?

## Server settings
* Is your site logging information in a format that is easily reviewed by your team?
* Is your site logging any potentially sensitive information?
* Is you TLS certificate properly installed and your OpenSSL configuration set to high standards?
  * [SSL Server Test](https://www.ssllabs.com/ssltest/)
* Do you have notifications turned on for filesystem integrity?
* Disable Server Signature:
  * [Nginx](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_tokens) - `server_tokens off;`
  * [Apache - ServerSignature Directive](https://httpd.apache.org/docs/2.4/mod/core.html#serversignature) - `ServerSignature Off` 
  * [Apache - ServerTokens Directive](https://httpd.apache.org/docs/2.4/mod/core.html#servertokens) - `ServerTokens Prod`
* Set secure PHP settings in `php.ini`
  * Minimize the number of PHP modules you have installed. Remove any not needed by Magento.
  * Disable PHP version exposure: `expose_php = Off`
  * Disable Script Name exposure for `mail()` function: `mail.add_x_header = Off`
  * Disable errors: `display_errors = Off`
  * Set httponly flag as default for cookies: `session.cookie_httpsonly = On`
  * Set secure flag as default for cookies: `session.cookie_secure = On`
  * Disable remote file inclusion: `allow_url_fopen = Off` and `allow_url_include = Off`
  * Set `open_basedir` and `upload_tmp_dir` to appropriate directories for you distro/installation 
* Are there any (development) files or database dumps lying around in the production document root that are not intended for public usage (for example info.php, phpinfo.php, dbdump.sql.gz)?
* Does an unnecessary `.git` folder or `.gitignore` file exist in your production filesystem? 
* Are any required files for server operation readable publicly? eg: `php.ini` or `.user.ini`
* Do all your folders in your docroot have the [appropriate permissions set](https://devdocs.magento.com/guides/v2.3/config-guide/prod/prod_file-sys-perms.html)? (for example var/logs, /var/report, install)?
* Have you analyzed your HTTP response headers for problems?
  * [Analyse your HTTP response headers](https://securityheaders.com/)
* Unless you have specific requirements for them have you limited all requests to `GET` and `PUT`?
* Are you disabling the directory paths indexing/ listing?
* Can you set your Magento php files installation as read-only? 
  *  Do you have a process for matching the production server files with a known good state?
* _TODO: need to add mod_security information_
* Is the webserver & Magento’s app/etc/env.php configured [to use /pub as document root](https://devdocs.magento.com/guides/v2.3/install-gde/tutorials/change-docroot-to-pub.html)?
* Is /media/ accesible?
* Are there any executable files inside /media/?
* Are you using up-to-date services ([where compatibility allows it](https://devdocs.magento.com/guides/v2.3/install-gde/system-requirements-tech.html))?
* Are you disabling or returning 404 the requests to pages with unused forms?

## SSH access
* Are you granting server access only by SSH connection and usage of individual users with individual keys?
* Are you providing access only to the people who actually need it, and only to the resources they need?
* Are you documenting all SSH keys and update them as team members change the keys or are added/ removed to/ from the team?
* Are you reviewing all access by project team members and staff periodically (at least every 3 months)?
* Are you disabling SSH access for root user?
* Is the default SSH port changed from 22 to something else (eg 2233)?
* Is SSH password authentication disabled?

## Regular Maintenance 
* Do you have an Incident Response Plan implemented and tested (mock scenario)? 
  * [Magento Incidence Response Plan template](https://www.github.com/talesh/response)
* Do you have a person with regular scheduled time to review log messages for suspicious behaviour?
  * What is your process for dealing with IP address that are attempted to directory bruteforcing?
* Do you have automated offsite backups for your site enabled?
  * Are these backups encrypted and on a non-public location?
  * Are these backups treated with the same level of security as your existing code?

## Secure Development processes 
* Do you have a process for sanitizing production data for developers?
  * [n98-magerun2 is recommended](https://github.com/netz98/n98-magerun2)
* Do you have a list of developers/support staff with full access to your production systems and a process for accessing them?

## Testing environments
* Are you blocking access to testing machines and use a whitelist for IPs that really need to access these resources?
* Are you keeping a catalog of all IPs - who and why they have access over HTTP to these machines and periodically check if they still need the access?
* Are you using [environment variables](https://devdocs.magento.com/guides/v2.3/config-guide/prod/config-reference-var-name.html) to overwrite any production URL for payment processors, 3rd party API integrations or other sensitive configurations which you might overlook when updating the testing machine database with a backup from production?

## Magento Core 
* Does your site contain the latest security patches and updates?
  * [Magereport community scanner](https://www.magereport.com/)
  * [Magento security scanner](https://account.magento.com/scanner)
* Are you applying the security patches' new standards/ changes to the custom code developed so far? Without this, the patch can be useless (the system is as strong as its weakest link).
* Do you have a person with a subscription to security notices from Magento security blog for urgent patches/updates? 
  * [Magento Security center](https://magento.com/security)
* Do you have a recurring reminder to execute regularly review validity of existing Admin accounts (including the allocated resources)?
  * Recommended monthly
* Are you reviewing on a regular basis (at least every 3 months) the existing API integrations and the allocated resources?

## External security concerns
* If you are using a Web Application Firewall (WAF) does it provide virtual patching and how do you keep it up to date?

## Additional resources
* Magento User Guide's [Security Best Practices](https://magento.com/security/best-practices)
