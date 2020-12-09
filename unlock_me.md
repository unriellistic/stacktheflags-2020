# Unlock Me

### Element Inspections

The first thing to do in a web challenge is always inspect element. Check HTML, CSS, and JS.
In this case, the HTML file had Javascript in it:

    <script>
    $( "#signinForm" ).submit(function( event ) {
      event.preventDefault();
      fetch("login", {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({"username": $( "#inputUsername" ).first().val(), "password": $( "#inputPassword" ).first().val() })
      }).then(function(response) {
        return response.json();
      }).then(function(data) {
        if (data.error) {
          $('#alerts').html('<div class="alert alert-danger" role="alert">'+data.error+'</div>');
        } else {
          fetch("unlock", {
            headers: {
              "Authorization": "Bearer " + data.accessToken
            }
          }).then(function(response) {
            return response.json();
          }).then(function(data) {
            if (data.error) {
              $('#alerts').html('<div class="alert alert-danger" role="alert">'+data.error+'</div>');
            } else {
              $('#alerts').html('<div class="alert alert-success" role="alert">'+data.flag+'</div>');
            }
          }).catch(function(error) {
            $('#alerts').html('<div class="alert alert-danger" role="alert">Request failed.</div>');
          })
        }
      }).catch(function(error) {
        $('#alerts').html('<div class="alert alert-danger" role="alert">Request failed.</div>');
      })
    });
    // TODO: Add client-side verification using public.pem
    </script>

The comment at the bottom mentions `public.pem`. Luckily for us, typing `/public.pem` into the url gives us the file, nice and easy. `public.pem` is a RSA public key, which is used along with a RSA private key (which is private and inaccessible to us) to authenticate the user.

Moving on to the actual login itself, we can take a look at the js code and the Network tab of the inspector to see what's happening. The client sends the username and password to the server at `/login`, and if the credentials match, the server sends back a JSON Web Token (JWT). The JWT is then sent back to the server via a GET request to `/unlock`, which grants access to the flag if admin.

### Check yourself before you JWT yourself

Several exploits relating to JWTs exist. We use one such exploit to pretend to be the admin and retrieve the flag.

We can decode the JWT using an online tool (such as http://calebb.net/) and obtain the info contained within the JWT:
    {
        alg: "RS256",
        typ: "JWT"
    }.
    {
        username: "minion",
        role: "user",
        iat: 1607512591
    }.

The first part is the header, and most importantly tells us the algorithm used to encrypt the JWT, RS256 (RSA, but we already know that because we have an RSA public key.) Next, we see that there's a role entry, which says "user". We can easily change this to "admin", but we would then have to re-encrypt the JWT... with the RSA *private* key. Which we don't have. :(

### Or do we?
