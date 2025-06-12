# safewebdrop
## Secure https-based encrypted message exchange mechanism

Originally developed for the <a href="https://crypto-bone.com">Crypto Bone</a>, 
the <a href="https://safewebdrop.com">SafeWebdrop message exchange mechanism</a>
can be adapted for a large number of projects that require secure, ie encrypted and
authenticated, message and file exchange.

It is a foundation for internal communication demands within an organisation that can be
established with a minimal invasive software product on a server, that is already up and running
and can be accessed via HTTPS.

To ensure true end-to-end encryption, it is absolutely necessary to start a secure message
exchange with a shared initial secret that has been agreed on by two people in a personal contact.
Once two people have decided on such an initial secret, this initial secret must be protected
on their endpoint devices which is not easy to achieve.

But the exchange of AES encrypted messages and files must also be secured to make sure
that only the two legitimate people (sender and recipient) can access the encrypted data.

This secure message exchange is provided by the SafeWebdrop software in a specific way.


## Principles of Secure Message Exchange 

*  Providing the SafeWebdrop service on a https-enabled server should be <b>non-invasive</b>.
   That means, it must not be necessary to have people, using the service, introduced as users on the server.
   Unlike ssh the SafeWebdrop service creates only a single directory /home/safewebdrop/username,
   nothing else.

*  Every user is enabled only <b>if an administrator accepts a user's public RSA key</b> that
   the registration software on the user's endpoint device produces. For this to 
   happen, one personal contact between the user and the server administrator is required
   in which the user presents a registration code to the administrator that is stored together
   with his public key on the server.
   The administrator will then run a script that creates the user's directory and copies
   the public RSA key into it.
   The administrator's involvement ends with the registration, everything else is done by
   the <a href="https://safewebdrop.com/source/safewebdrop-2.0-TAR/server">server scripts</a>.
   
   Once registered, the user can avail of the SafeWebdrop service as long as the admin
   does not delete their directory or RSA public key.

*  Registered users receive <b>a SafeWebdrop address</b> consisting of their user name and the
   server's domain name (or IP address) separated with a %-sign. Those addresses look similar to
   email addresses, (Alice.Johnson%example.com) but they are used over HTTPS to send
   encrypted messages and files to other users on the same (local) server.
   This https-based message exchange reduces the meta data that would be visible with an
   ordinary encrypted email exchange.

*  All interactions with the server (reading, writing, deleting) require that the requests
   being sent to the server are <b>signed with the user's corresponding RSA private key</b>,
   which is stored (and protected) on the user's endpoint device. The SafeWebdrop
   <a href="https://safewebdrop.com/source/safewebdrop-2.0-TAR/client">client scripts</a> 
   take care of this.

*  If (and only if) the administrator of a SafeWebdrop service allows message exchange
   to users on a different SafeWebdrop server, then the process of accepting a remote user's
   public key is safeguarded by the initial secret, these two users have agreed on.
   And on the fact that the remote administrator has accepted the other user's RSA
   public key on his server.

   Note, that no contact between the two administrators nor the use of any
   PKI is involved to establish cross-server message exchange. 
   The conclusion of acceptance of a certain cross-server contact relies 
   entirely on the initial secret, both users have agreed on, which is part of the hash value
   that triggers the storage of the remote user's public key by the server scripts.


## Testing the Server-Side Software

The six server-side scripts are all coded in pure bash and won't interfere with the 
server's orginal functionality. All messages that are stored and retrieved by users live under
a single directory /home/safewebdrop.

You can test whether or not all necessary server-side commands are already in place
with the test program check-server-utils on your server. And you'll find examples
and advice how to test the functionality of the server-side code on your own server 
with these <a href="https://safewebdrop.com/source/safewebdrop-2.0-TAR/tests">test scripts</a>. 

## Using SafeWebdrop Exchange for Your Own Project

Of course, the SafeWebdrop exchange mechanism can also be used as a foundation for your
own projects. The server-side scripts need not be changed at all. 

But you have to be extremely cautious when you are developing your own client-side code.
If you don't avail of the CryptoBone daemon to secure access to the message encryption keys,
you have to think hard how to safeguard the encryption keys that are used in your own code.
The secret, that protects the user's RSA private key must also be protected as much as 
possible, as every request is signed using this private RSA key. 

In the client-side code you'll find lines like this
```
PASS=$(/usr/bin/echo "get-element webdropsecret" | /usr/bin/socat -t15 - UNIX-connect:$SOCK 
```
that retrieve secret information from the cryptobone daemon. If you don't want to use
<a href="https://crypto-bone.com/release/root/src/cryptoboned/">this daemon</a> in your own code,
you need to take extra care to safeguard these secrets.
For details see the project's <a href="https://safewebdrop.com/security.php">security page</a>.

## Code Review

Every code review is most welcome, however in order to maintain the stability and reliability
of the code base I will carefully vet any contribution and I may chose to rewrite it,
add comments and documentation, rather than taking it as is. 
Needless to say that code changes will happen only after conclusion of extensive tests.

