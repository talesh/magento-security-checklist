# Magento Security Checklist
This is a community sourced checklist of security measures to take before launching your store. Think of it as a pre-flight checklist so you don't forget something important because you've been in a headlong rush for the last few months to get all the features of your eCommerce site built.

_This checklist will apply to only Magento 2.x versions._

## Custom code
* Are all inputs validated on the server side? (Link to volidation tutorial)
* Are you sanitizing all inputs also? (eg: casting to integers if a number is expected?)
* Are you using Magento built in `form_key` validation on all custom forms? (Link for more info on CSRF Anti-forgery tokens)
* Are you properly escaping all output to screen using `escapeHtml()`, `escapeJs()` and similar functions?
* Are you setting `HttpOnly` and `Secure` flag on any custom cookies?
* Are you using the `Magento\Config\Model\Config\Backend\Encrypted` backend model for sensitive API data to be stored in the DB? eg: payment gateway keys.
* Are you using any dynamic code execution functions like `eval`, `shell_exec`? (Link to bad functions php SO)
* Are all sensitive or system specific settings treated as sensitive configuration? ([DevDocs on sensitive configuration](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/configuration/sensitive-and-environment-settings.html#how-to-specify-values-as-sensitive-or-system-specific))

## Extension security checklist
* Do you have the latest version of all the extesions being used on the site?
* Are all you extensions using Magento ACL properly to limit the scope of users?

## Magento Admin settings 
* Are you using a custom Magento Admin URL?
* Is access to your Admin URL IP whitelisted or protected via VPN?
* Confirm that Web -> Default Cookie Settings -> Use HTTP Only is not disabled?

## Server settings
* Is your site logging information in a format that is easily reviewed by your team?
* Is your site logging any potentially sensitive information?
* Is everything running on HTTPS?
* Do you have notifications turned on for filesystem integrity?

## Regular Maintenance 
* Do you have an Incident Response Plan implemented and tested (mock scenario)? ([Magento Incidence Response Plan template](https://www.github.com/talesh/response)
* Do you have a person with regular scheduled time to review log messages for suspicious behaviour?
* Do you have a person with a subscription to security notices from Magento security blog for urgent patches/updates? (Link to security blog)


## External security concerns
