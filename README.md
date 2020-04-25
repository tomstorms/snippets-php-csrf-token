# tomstorms / Snippets / PHP CSRF Token

Snippet code demonstrating simple Cross Site Request Forgery (CSRF) token validation for your PHP projects.

Note: PHP session variables do expire! 

Example: If you set this up and a user loads your page, walks away from their desk and comes back in 1 hour and submits the form, their token will fail to validate. For more information on this, check out ```session.gc_maxlifetime``` and/or ```session.cookie_lifetime```.


## Requirements

- PHP 5.6+


## Setup the Token

You would typically set this up the token at the top of your main PHP page. 

Example: index.php


```
<?php

// Initialise your PHP Session
session_start();
if (empty($_SESSION['token'])) {
    $_SESSION['token'] = bin2hex(random_bytes(32));
}
$token = $_SESSION['token'];

?>
```

You would then use the ```$token``` variable to send to the backend. 

See the following examples:


### Using jQuery

Note: If you are using jQuery, you need to initialise the JS variable on your PHP page (ie index.php).

Example: on your index.php:

```
	<script type="text/javascript">
		$(function () {
			// Set CSRF token
			window.ajax_token = '<?php echo $token; ?>';
		});
	</script>
```

If you're using AJAX, you can use this variable inside your ```script.js``` file for example like so:

```
function doAjax() {
    $.ajax({
        type: 'POST',
        url: '/ajax.php',
        data: {
        	o: 'load',
        	token: window.ajax_token
        },
        success: function(response) {
        	console.log('successful');
        },
    });
}
```


## HTML Form

You can use the ```$token``` variable in HTML forms as well:

```
<form method="POST" action="/ajax.php">
	<input type="hidden" name="o" value="load" />
	<input type="hidden" name="token" value="<?=$token;?>" />
	<input type="submit" value="Submit Form" />
</form>
```


## Backend Code

In the previous examples, we've been posting to ```ajax.php```.
In this file, it would have the following code to validate the user's CSRF token:

```
<?php

session_start();

if (!empty($_POST['token'])) {
	try {
	    if (@hash_equals($_SESSION['token'], $_POST['token'])) {

	    	// the CSRF token was a match

	    }
	    else {

	    	// The session has expired, or the CSRF token does not match

	    }
	}
}

```


