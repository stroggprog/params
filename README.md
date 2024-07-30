# parameters
This is a simple PHP class for accessing GET and POST parameters, allowing values to be overridden without overwriting them (allowing the original values to be recovered).

In practice, it works like this:
```
include_once("params.php");

$params = new parameters();

$value = $params->input; // get value of $_POST or $_GET variable named 'input'

$params->solder = "Fluff"; // assign a value to variable named 'solder'
```

If `input` was passed as a POST or GET variable, `$value` will reflect its value, otherwise it will be blank string.

The variable `solder` will get the value `Fluff`, but this is stored to an internal array, the $_POST and $_GET arrays are unchanged

When fetching values, the order of precedence is:
  - internal associative array
  - $_POST
  - $_GET

Consider the following:
```
// $_GET["variable1"] has the value "Example"
//
$v1 = $params->variable1; // value = "Example"

$params->variable1 = "Some Other Value";

$v2 = $params->variable1; // value = "Some Other Value"

$v3 = $_GET["variable1"]; // value = "Example"

// add a variable that doesn't exist in $_GET or $_POST arrays:
$params->myVar = new Date();
```

As you can see, it is perfectly legal to add a new variable, and you can also change a variable type. For example, you may have a $_GET variable named 'date' which contains the string `2024-07-30`. You can do this:
```
$params->date = new DateTime( $params->date );
```
In this case, it _fetches_ the date string from the $_GET (or $_POST) variable, uses it to create a `DateTime` object and stores that in the internal array. The original value is still available at `$_GET['date']`, but subsequent calls to `$params->date` will return the object.

There are some support methods available:
```
$params->unset( "name" ); // deletes variable 'name' from internal array ($_GET and $_POST are preserved)

$keys = $params->listKeys(); // get a list of the keys from the internal array

$masked = $params->maskedKeys(); // returns a list of $_GET and $_POST variables masked by values in the internal array
```
You can use the `unset()` method to unmask a masked variable. This only ever affects the internal array.
