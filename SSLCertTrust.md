

# Trust CA Cert
Remove the certificate warnings by trusing the CA certificate.


Download the cert - http://gateway.ed/CA.crt

You can install it, or install it into the group policy (trusted CA certs) on your AD server

This should work for IE and Chrome.

Add the CA Cert here in your group policy:
GPO Path - Computer Policies --> Windows Settings --> Security Settings --> Public Key Policies --> Trusted Root Certificate Authorities

# Firefox

Try setting the option in firefox to make it automatically import trusted system certs.

Make 2 files on a share w read permissions for Domain Computers (e.g. sysvol?)
Add them to the AD files rule at "Computer Configuration -> Preferences -> Windows Settings -> Files"

## local-settings.js  Save to:
### c:\Program Files\Mozilla Firefox\defaults\pref\local-settings.js
### AND c:\Program Files\Mozilla Firefox (x86)\defaults\pref\local-settings.js
pref("general.config.obscure_value", 0);
pref("general.config.filename", "umbrella.cfg");


## umbrella.cfg  Save to:
### c:\Program Files\Mozilla Firefox\umbrella.cfg
### AND save to:  c:\Program Files (x86)\Mozilla Firefox\umbrella.cfg
lockPref("security.enterprise_roots.enabled", true);


