# The art of Cross Site Scripting (XSS)
Cross-Site Scripting (XSS) attacks are a type of injection, in which malicious scripts are injected into otherwise benign and trusted web sites. XSS attacks occur when an attacker uses a web application to send malicious code, generally in the form of a browser side script, to a different end user. Flaws that allow these attacks to succeed are quite widespread and occur anywhere a web application uses input from a user within the output it generates without validating or encoding it.

## Cross-Site Scripting (XSS) attacks occur when:

* Data enters a Web application through an untrusted source, most frequently a web request.
* The data is included in dynamic content that is sent to a web user without being validated for malicious content.

## Stored XSS Attacks

Stored attacks are those where the injected script is permanently stored on the target servers, such as in a database, in a message forum, visitor log, comment field, etc. The victim then retrieves the malicious script from the server when it requests the stored information. Stored XSS is also sometimes referred to as Persistent or Type-I XSS.

## Reflected XSS Attacks



## Sources
* https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)
