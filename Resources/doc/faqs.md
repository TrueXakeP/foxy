FAQs
====

What version required of Composer?
----------------------------------

See the documentation: [Installation](index.md#installation).

What is the difference between Foxy and Fxp Composer Asset Plugin?
------------------------------------------------------------------

When [Fxp Composer Asset Plugin](https://github.com/fxpio/composer-asset-plugin) has been created,
it lacked some important functionality to NPM and Bower as a true lock file, the access to
private repositories, the management of organizations (scope), and the assets was limited to a simple
download of the packages. The solution was to use the SAT solver, the VCS Repositories and the
Composer lock file to manage the asset dependencies of the PHP libraries. However, there are 3 major
disadvantages to this approach:

1. The plugin must be installed in global mode
2. Nodejs must be used more and more to compile some libraries
3. The use of VCS Repositories coupled with the SAT Solver architecture of Composer is much less
   efficient than NPM, despite the optimizations of the plugin to avoid the imports

Now, Bower has been depreciated, NPM has a true lock file (since 5.x), as well as the possibility
of using the private repositories, Yarn arrived with his big performances, and more and more javascript
library requires a compilation because they use Babel, Typescript, SCSS, Less, etc...

Nodejs filling its gaps, and becoming more and more required, a plugin could finally perform the reverse operation,
retaining the benefits of Fxp Composer Asset Plugin and NPM. So, conversely, Foxy creates package mocks for NPM
in local directory, containing only the `package.json` file from the PHP library, and adding the path of the
mock package to the project's `package.json` file. The entire validation and installation process is left
to NPM or Yarn. However, the plugin manages the fallback if there is an error of the asset manager.

To conclude, given that there is not a backward compatibility, and that it is impossible to have a version
of the plugin installed globally, and another version installed in the project - because Composer will
install the plugin in the project, but will only use the plugin installed globally - the Fxp Composer Asset
Plugin was become Foxy.

How does the plugin work?
-------------------------

Foxy creates the mocks of Composer packages for NPM in local directory, containing only the `package.json`
file from the PHP library, and adding the package path to the `package.json` file of the project.

The name of the Composer package is converted to a format compatible with NPM, using the NPM scope
`@composer-asset` and replacing the separation slash `/` between the vendor and the package name by
2 dashes `--`, giving consequently, the following format `@composer-asset/<php-package-vendor>--<php-package-name>`
(this scope is reserved by Foxy in the registry of NPM and in Github).

NPM will install in the `node_modules/@composer-asset` folder an updated copy of each Composer package mock
that is located by default in the folder `vendor/foxy/composer-asset`.

For more details, the plugin work in this order:

1. Validation of the asset manager installation, then checking of the compatible asset manager version (optional)
2. Saving the status of project
3. Installing/updating of the PHP dependencies by Composer
4. Retrieving the entire list of installed packages
5. Retains only PHP dependencies with the `foxy/foxy` dependency in the `require` or `require-dev` section of
   the `composer.json` file and with the presence of the `package.json` file
6. Checking the lock file of asset manager
7. Comparing the difference between the installed asset dependencies and the new asset dependencies, to determine
   whether the dependency must be installed, updated, or removed
8. Creating, updating, or deleting of the mock asset libraries in local directory, containing only the
   `package.json` file of the PHP library, with a formatted name as:
   `@composer-asset/<php-package-vendor>--<php-package-name>`
9. Adding, updating, or deleting the mock asset library in the `package.json` file of the project
10. Running the install or update command of asset manager
11. Restoring the `package.json` file with the previous dependencies if the asset manager terminates with an error
12. Restoring the `composer.lock` file and all PHP dependencies if the asset manager terminates with an error

Is Foxy useful if my asset dependencies are defined only in my project?
-----------------------------------------------------------------------

Foxy is mainly focused on automating of the asset management of the PHP libraries, avoiding potentially conflicting
manual management.

Given that Foxy makes it possible to ensure that the entire management process is valid whether it is for Composer
and NPM or Yarn, you can use Foxy even if all of your asset dependencies are only defined in the `package.json` file
of your project. However, the value added by Foxy in this configuration will be low, and will be limited to the
management of the fallback of the PHP dependencies if there is an error of the asset manager.

NPM/Yarn does not find the mock of the Composer dependencies
------------------------------------------------------------

The advantage of Foxy, is that it allows you to keep the workflows of each tool. However, Foxy creates PHP
package mocks for NPM, and in this case, Composer must be launched before NPM or Yarn. After, nothing prevents
you to using all available commands of your favorite asset manager.

How to increase the PHP memory limit?
-------------------------------------

See the official documentation of Composer: [Memory limits errors](https://getcomposer.org/doc/articles/troubleshooting.md#memory-limit-errors).