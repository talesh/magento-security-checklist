# Magento Security Checklist
This is a community sourced checklist of security measures to take before launching your store. Think of it as a pre-flight checklist so you don't forget something important because you've been in a headlong rush for the last few months to get all the features of your eCommerce site built.

_This checklist will apply to only Magento 2.x versions._

## Custom code
* Are all inputs validated on the server side? (Link to volidation tutorial)
* Are you sanitizing all inputs also? (eg: casting to integers if a number is expected?)
* Are you correctly protecting your code against [PHP string comparison vulnerabilities](https://hydrasky.com/network-security/php-string-comparison-vulnerabilities/)? (eg. == vs ===)
* Are you using Magento built in `form_key` validation on all custom forms? (Link for more info on CSRF Anti-forgery tokens)
* Are you properly escaping all output to screen using `escapeHtml()`, `escapeJs()` and similar functions?
* Are you setting `HttpOnly` and `Secure` flag on any custom cookies?
* Are you using the `Magento\Config\Model\Config\Backend\Encrypted` backend model for sensitive API data to be stored in the DB? eg: payment gateway keys.
* Are you using any dynamic code execution functions like `eval`, `shell_exec`? ([Exploitable PHP functions](https://stackoverflow.com/questions/3115559/exploitable-php-functions))
* Are you building MySQL queries properly, and not using direct queries?
* Are all sensitive or system specific settings treated as sensitive configuration? ([DevDocs on sensitive configuration](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/configuration/sensitive-and-environment-settings.html#how-to-specify-values-as-sensitive-or-system-specific))

## Extension security checklist
* Do you have the latest version of all the extesions being used on the site?
* Are all you extensions using Magento ACL properly to limit the scope of users?
* Did you perform a security audit on 3rd party extensions used on the store? (Link to sample tools to help perform security audit on extensions)
  * At the very least execute Extension Code Review against [Magento Coding Standards](https://github.com/magento/magento-coding-standard)
  * Review if Passwords/API Credentials or other Sensitive Data stored by 3rd Party Extension are not save in Database using `plaintext`!
* Review the need of using `Magento_Swagger` & `Magento_Version` Modules in Production Environment (consider to `Disable` those CORE Modules or at the very least restrict access to `hostname/magento_version` and `hostname/swagger`)

## Magento Admin settings 
* Are you using a custom Magento Admin URL?
* Is access to your Admin URL IP whitelisted or protected via VPN?
* Confirm that Web -> Default Cookie Settings -> Use HTTP Only is not disabled?
* Are you using 2FA for your admin login? [For example MageSpecialist TwoFactorAuth module](https://github.com/magespecialist/m2-MSP_TwoFactorAuth) 
* Are there any old/unused admin users which were created for testing or development that should be removed?

## Server settings
* Is your site logging information in a format that is easily reviewed by your team?
* Is your site logging any potentially sensitive information?
* Is everything running on HTTPS?
* Do you have notifications turned on for filesystem integrity?
* Disable Server Signature:
  * [Nginx](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_tokens) - `server_tokens off;`
  * [Apache - ServerSignature Directive](https://httpd.apache.org/docs/2.4/mod/core.html#serversignature) - `ServerSignature Off` 
  * [Apache - ServerTokens Directive](https://httpd.apache.org/docs/2.4/mod/core.html#servertokens) - `ServerTokens Prod`
* Disable PHP Signature - edit `php.ini` and set `expose_php = Off`

## Regular Maintenance 
* Do you have an Incident Response Plan implemented and tested (mock scenario)? ([Magento Incidence Response Plan template](https://www.github.com/talesh/response)
* Do you have a person with regular scheduled time to review log messages for suspicious behaviour?
* Do you have a person with a subscription to security notices from Magento security blog for urgent patches/updates? (Link to security blog)
* Re-certification Process of Admin Accounts - Review Validity of Accounts eg. once/month

## Web-Application
* Are there any (development) files or database dumps lying around in the document root that are not intended for public useage (for example info.php, phpinfo.php, dbdump.sql.gz)?

## External security concerns
* Consider to use `WAF` - Web Application Firewall Solution