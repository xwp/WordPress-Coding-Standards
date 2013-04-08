### WordPress Coding Standards for PHP_CodeSniffer

From the [PHP_CodeSniffer](http://pear.php.net/package/PHP_CodeSniffer) (phpcs) package information on PEAR:

    PHP_CodeSniffer tokenises PHP, JavaScript and CSS files and detects violations of a defined set of coding standards.

This project is a collection of PHP_CodeSniffer rules (sniffs) to validate code developed for WordPress.

This is a fork of the WordPress Coding Standards project from [Urban Giraffe](http://urbangiraffe.com/articles/wordpress-codesniffer-standard/) published in 2009, at which time Matt Mullenweg gave it a [shoutout](http://ma.tt/2009/04/wordpress-codesniffer/). A couple years later, the project was picked up by [Chris Adams](http://chrisadams.me.uk/) who published it to a [repo](https://github.com/mrchrisadams/WordPress-Coding-Standards) on GitHub in May 2011. Initially Chris added a missing `ruleset.xml` file which prevented the rules from being detected by phpcs. Since that time there have been around a dozen [contributions](https://github.com/mrchrisadams/WordPress-Coding-Standards/commits/master) to improve the project. It is surprising that there has not been more community involvement in developing these sniffs, as it is a very useful tool to ensure code quality and adherence to coding conventions, especially the official [WordPress Coding Standards](http://codex.wordpress.org/WordPress_Coding_Standards) which are currently only partially accounted for by the sniffs. [X-Team](http://x-team.com/) has forked the project and is dedicating resources to further develop it and make it even more useful to the WordPress community at large.

The sniffs were developed for phpcs 1.3; work will be done to ensure compatibility with the latest version, which is 1.4.

Ongoing development will be done in the `development` with merges done into `master` once considered stable. Development of unit tests is needed, per [issue 21](https://github.com/x-team/WordPress-Coding-Standards/issues/21).

### How to use this

Once you've installed PEAR, install Codesniffer:

    pear install --alldeps PHP_CodeSniffer

Then install WordPress standards

    git clone git://github.com/x-team/WordPress-Coding-Standards.git $(pear config-get php_dir)/PHP/CodeSniffer/Standards/WordPress

Normally when working with PEAR, the `pear install` command is used, but GitHub automatically names the files in a way that will confuse the `pear install`, so we're falling back to git instead.

Then run the PHP code sniffer commandline tool on a given file, for example `wp-cron.php`.

    phpcs --standard=WordPress -s wp-cron.php

You can use this to sniff individual files, or use different flags to recursively scan all the directories in a project. This command will show you each file it's scanning, and how many errors it's finding:

    phpcs -p -s -v --standard=WordPress .

Output will like this:

    Registering sniffs in WordPress standard... DONE (11 sniffs registered)
    Creating file list... DONE (705 files in queue)
    Processing index.php [47 tokens in 31 lines]... DONE in < 1 second (2 errors, 0 warnings)
    Processing wp-activate.php [750 tokens in 102 lines]... DONE in < 1 second (47 errors, 2 warnings)
    Processing admin-ajax.php [14523 tokens in 1475 lines]... DONE in 2 seconds (449 errors, 44 warnings)
    Processing admin-footer.php [183 tokens in 43 lines]... DONE in < 1 second (19 errors, 0 warnings)
    Processing admin-functions.php [43 tokens in 16 lines]... DONE in < 1 second (2 errors, 0 warnings)
    Processing admin-header.php [1619 tokens in 196 lines]... DONE in < 1 second (110 errors, 1 warnings)
    Processing admin-post.php [144 tokens in 33 lines]... DONE in < 1 second (8 errors, 0 warnings)
    Processing admin.php [1906 tokens in 238 lines]... DONE in 1 second (128 errors, 1 warnings)
    Processing async-upload.php [623 tokens in 70 lines]... DONE in < 1 second (41 errors, 0 warnings)
    Processing comment.php [2241 tokens in 289 lines]... DONE in < 1 second (110 errors, 3 warnings)
    Processing colors-classic-rtl.css [517 tokens in 1 lines]... DONE in < 1 second (0 errors, 0 warnings)
    Processing colors-classic-rtl.dev.css [661 tokens in 79 lines]... DONE in < 1 second (0 errors, 0 warnings)
    Processing colors-classic.css ^C

    ... and so on...

### Using the WordPress standard on projects

Lots of WordPress's own code doesn't conform to these standards, so running this on your entire codebase will generate lots, and lots of errors.

Instead, try installing the WordPress standard, then invoking it from a project specific codesniffer ruleset instead, like in the supplied example file.

Remove the `.example` suffix from project.ruleset.xml and run it in your
project root, pointing at a given file:

    mv project.ruleset.xml.example project.ruleset.xml
    phpcs -s -v -p --standard=./project.ruleset.xml a-sample-file.php

A tiny subset of the options available to codesniffer have been used in this example, and there's much more that can be done in a `ruleset.xml` file. Check the [phpcs documentation](http://pear.php.net/manual/en/package.php.php-codesniffer.php) to see a [fully annotated example to build upon](http://pear.php.net/manual/en/package.php.php-codesniffer.coding-standard-tutorial.php).

### Troubleshooting

Check your `PATH` if it includes new binaries added into the pear directories. You may have to add `:/usr/local/php/bin` before you can call `phpcs` on the command line.

Remember that you can see where PEAR is looking for stuff, and putting things, by calling `pear config-show`. This is how to find where the `phpcs` binary was added, and where the PEAR library is by default.

### Unit Testing

TL;DR

Make sure you have `phpunit` installed and available in your `PATH`.

~~~text
$ mkdir phpcs
$ cd phpcs
$ git clone git@github.com:squizlabs/PHP_CodeSniffer.git .
$ git checkout 1.4.4
$ git clone git@github.com:x-team/WordPress-Coding-Standards.git CodeSniffer/Standards/WordPress
$ phpunit --filter WordPress tests/AllTests.php
~~~

Expected output:

~~~text
PHPUnit 3.7.18 by Sebastian Bergmann.

...............

Time: 1 second, Memory: 37.00Mb

OK (15 tests, 0 assertions)
~~~

You can ignore any skipped tests as these are for `PHP_CodeSniffer` external tools.

The reason why we need to checkout from `PHP_CodeSniffer` git repo to run the tests is because
PEAR installation is intended for ready-to-use not for development. At some point `WordPress-Coding-Standards`
might be submitted to `PHP_CodeSniffer` repo and using their existing convention for unit tests
will eventually help them to test the code before merging in.

#### Unit Testing conventions

If you see inside the `WordPress/Tests`, the structure mimics the `WordPress/Sniffs`. For example,
the `WordPress/Sniffs/Arrays/ArrayDeclarationSniff.php` sniff has unit test class defined in
`WordPress/Tests/Arrays/ArrayDeclarationUnitTest.php` that check `WordPress/Tests/Arrays/ArrayDeclarationUnitTest.inc`
file. See the file naming convention? Lets take a look what inside `ArrayDeclarationUnitTest.php`:

~~~php
...
class WordPress_Tests_Arrays_ArrayDeclarationUnitTest extends AbstractSniffUnitTest
{
    public function getErrorList()
    {
        return array(
                3 => 1,
                7 => 1,
                9 => 1,
                16 => 1,
                31 => 2,
               );

    }//end getErrorList()
}
...
~~~

Also note the class name convention. The method `getErrorList` MUST return an array of line numbers
indicating errors (when running `phpcs`) found in `WordPress/Tests/Arrays/ArrayDeclarationUnitTest.inc`.
If you run:

~~~text
$ cd /path-to-cloned/phpcs
$ ./scripts/phpcs --standard=Wordpress -s CodeSniffer/Standards/WordPress/Tests/Arrays/ArrayDeclarationUnitTest.inc
...
--------------------------------------------------------------------------------
FOUND 8 ERROR(S) AND 2 WARNING(S) AFFECTING 6 LINE(S)
--------------------------------------------------------------------------------
  3 | ERROR   | Array keyword should be lower case; expected "array" but found
    |         | "Array" (WordPress.Arrays.ArrayDeclaration)
  7 | ERROR   | There must be no space between the Array keyword and the
    |         | opening parenthesis (WordPress.Arrays.ArrayDeclaration)
  9 | ERROR   | Empty array declaration must have no space between the
    |         | parentheses (WordPress.Arrays.ArrayDeclaration)
 12 | WARNING | No space after opening parenthesis of array is bad style
    |         | (WordPress.Arrays.ArrayDeclaration)
 12 | WARNING | No space before closing parenthesis of array is bad style
    |         | (WordPress.Arrays.ArrayDeclaration)
 16 | ERROR   | Each line in an array declaration must end in a comma
    |         | (WordPress.Arrays.ArrayDeclaration)
 31 | ERROR   | Expected 1 space between "'type'" and double arrow; 0 found
    |         | (WordPress.Arrays.ArrayDeclaration)
 31 | ERROR   | Expected 1 space between double arrow and "'post'"; 0 found
    |         | (WordPress.Arrays.ArrayDeclaration)
 31 | ERROR   | Expected 1 space before "=>"; 0 found
    |         | (WordPress.WhiteSpace.OperatorSpacing)
 31 | ERROR   | Expected 1 space after "=>"; 0 found
    |         | (WordPress.WhiteSpace.OperatorSpacing)
--------------------------------------------------------------------------------
....
~~~

You'll see the line number and number of ERRORs we need to return in `getErrorList` method.
In line #31 there are two ERRORs belong to `WordPress.WhiteSpace.OperatorSpacing` sniff and
it MUST not included in `ArrayDeclarationUnitTest` (that's why we only return 2 errros for line #31).
Also there's `getWarningList` method in unit test class that returns an array of line numbers
indicating WARNINGs. 
