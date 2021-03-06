# Nextpack (Drivers Mode)

[![Latest Stable Version](https://poser.pugx.org/nextpack/nextpack/v/stable)](https://packagist.org/packages/nextpack/nextpack) 
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/nextpack/nextpack?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
[![License](https://poser.pugx.org/nextpack/nextpack/license)](https://packagist.org/packages/nextpack/nextpack)
[![Mahmoud Zalt](https://img.shields.io/badge/Author-Mahmoud%20Zalt-orange.svg)](http://www.zalt.me)

**A PHP Package Framework** (Designed to help you build high quality PHP Packages faster).

The Nextpack (Drivers Mode) is a start package specific for the drivers driven packages! Example: when a package needs to support multiple similar drivers (providers) shuch as Bit.ly and Goo.gl or Twitter and Facebook..

Nextpack strives to facilitates and boosts the development process of PHP Packages. And it highly recommend producing framework agnostic packages.




### Vision

Nextpack was created to help PHP developers producing more Open Source Composer Packages with the least amount of time and effort.

**Where this comes from!** I found myself doing the same things _(Setup, Structure, Configuration, Basic Functionality)_ over and over, everytime I start developing a PHP package. And there where the idea of combining all those common things as a Framework came to my mind, and the `Nextpack` project was born.





### Questions?
If you have any questions please share it with us on [Gitter](https://gitter.im/nextpack/nextpack) or email me on (mahmoud@zalt.me).






## Contents

- [Highlights](#Highlights)
- [Installation](#installation)
- [Customization](#Customization)
- [Documentation](#Documentation)
  - [Create public API class](#cpac) 
  - [Read configuration file](#rtcf)
  - [Create Driver class](#cadc)
  - [Initialize Driver](#inad)
  - [Dependency Injection](#dpin)
  - [Add Tests](#adte)
- [Tutorial](#Tutorial)




<a name="Highlights"></a>
## Highlights

__Nextpack includes:__

- **Rich package skeleton**, (containing common files required by almost every PHP package)
- **Clean folder structure**
- **Code samples**, (demonstrating _HOW_ and _WHERE_ the code should be implemented)
- **Test samples**, (with PHPUnit)
- **Basic configurations**, (for the most popular required tools)
  - Version Control: **Git** (`.gitattributes`, `.gitignore`)
  - Continuous Integration: **Travis** and **Scrutinizer** (`.scrutinizer.yml`, `.travis.yml`)
  - Testing: **PHPUnit** (`phpunit.xml`)
  - Package Manager: **Composer** (`composer.json`)
- **Common functionalities**, (provided by the **Nextpack Library**).
  - **Read config files**, and serve it in the package classes
  - **Initialize drivers automatically** _"in case the package supports multiple drivers/providers"_

  
  
  
  
  
  
  
  





<a name="Installation"></a>
## Installation


##### Software Requirement
- Git
- Composer


##### Installation Steps

1. `git clone https://github.com/nextpack/nextpack.git`
2. `composer update`
3. make sure everything is OK by running the tests `phpunit`




<a name="Customization"></a>
## Customization

After you install a fresh copy of Nextpack, the only thing you need to do is customizing it to meet your needs, before start codig your package.

But before the customization steps, if this is the first time you use the package it is recommended to read the [Tutorial](#tutorial) first, It explains the sample code shipped with the package.


### Steps:

The steps include removing the code samples shipped with the Nextpack:

1. Change the namespace of the application from `Nextpack\Nextpack` to your `Vendor-name\Package-name`. *(you can do this using the [Replace All] feature of your IDE).*
2. Update the following values in `composer.json`:  `name`, `description`, `keywords`, `authors`, `autoload` and don't forget to update the `namespaces`. (you might need to run `composer dump-autoload` after the changes).
3. Run `composer install`
4. Delete the files `Say.php` and `Sing.php`, then add your `Custom.php` API class.
5. Delete `English.php`, `French.php` and `SayInterface.php`, then add your `Custom.php` Driver classes (if necessary).
6. Delete `NameValidator.php` and `MissedNameException.php`.
7. Rename `SayFacadeAccessor.php` and update the returned string inside the `getFacadeAccessor()` function.
8. Rename `NextpackServiceProvider` and update the content of the following functions: `facadeBindings()`, `configPublisher()` and `implementationBindings()`.
9. Update the config file `nextpack.php`, (or remove it if not necessary).
10. Delete this `README.md` file. And rename the `README.md.READY` to `README.md`.
11. Update `CONTRIBUTING.md` and `LICENSE` by replacing `::Vendor-Name` and `::Package-Name` with your vendor and package names.
12. Open the new `README.md` and replace the following: 
13. Delete everytihng in the `tests` directory except the `TestCase.php` (then add your tests from scratch).
14. Update the "testsuite" name in the `phpunit.xml`.







<a name="Documentation"></a>
## Documentation


<a name="cpac"></a>
### Create public API class

The API classes are normal classes, usually exist on the root of the `src` directory, and they all extend from `Nextpack\Nextpack\Handler` abstract class.

Example:

```php
   class Phone extends Handler
   {
      public function call($number)
      {
          // business logic..
          return 'Alo Alo ' . $number;
      }
   }
```
The usage of this class `Phone` will be:

```php
  $phone = new Phone();
  $message = phone->call('96171137427');
  print message;
```





<a name="rtcf"></a>
### Read configuration file




#### From API Class:

To read the values of your default config file form an API class, you need to inject `Nextpack\Library\Config` class in your constructor. 
And then call the function `get('fileName.key');`

```php
    public function __construct(Config $config)
    {
        // read all the configuration file
        $this->all = $config->get('nextpack'); // 'nextpack' is the name if the default config file you are using
        
        // read something specific from the configuration file
        $this->format = $config->get('nextpack.drivers.english.format');
     }

```




#### From Driver Class:

You can access the driver configurations as you access the class properties `$this->property`.

However a safer way to access these class properties is by using the `get()` function `$this->get('property')` (to prevent accessing undefined property, in case it's removed from the config file).

To access values from outside the driver specific configurations scope, you can call the `$this->getAll()` and get all the config file as array.

```php
// access configuration value as attribute
$accessToken = $this->accessToken;
  
//  access configuration value using a safe function [best way]
$accessToken = $this->get('accessToken');
  
// access configuration value exist outside the scope of the driver in the config file 
$allConfigurations = $this->getAll();   // (returns all the config file)
```








<a name="cadc"></a>
### Create a Driver class

The driver classes are normal classes, usually exist in the `src/Drivers` directory, and they all extend from `Nextpack\Nextpack\Drivers\Driver` abstract class.

Example:

```php
   class Vodafone extends Driver
   {
      public function call($number)
      {
          // business logic..
          return 'Alo, This is the Vodafone Mobile Operator';
      }
   }
```





<a name="inad"></a>
### Initialize a Driver

To initialize an instance of the default driver selected in the config file all you have to do is call `$this->driver()` function from any of your API classes.

Config file example:

```json
    /*
    |--------------------------------------------------------------------------
    | Default Driver name
    |--------------------------------------------------------------------------
    |
    | The Supported Providers are:
    | - vodafone
    | - verizon
    |
    */
    'default' => 'vodafone',

```

>Note: the name of the driver is the same as the name of the class, in lowercase.

Usage example from the API class:

```php
    $driver = $this->driver();
    $result = $driver->call('96171137427');
```
>Note: The package user must not initialize object of the Drivers. He should only access the API classes.










<a name="dpin"></a>
### Dependency Injection
You can inject any class you want in any API or Driver's class.

In this example I am injecting a `Validator` class.

```php
    public function __construct(Validator $validator)
    {
        $this->validator = $validator;
    }
```







<a name="adte"></a>
### Add Tests

The test classes are normal classes, they can only exist in the `tests` directory, and they all extend from `Nextpack\Nextpack\Tests\TestCase` class.

Example:

```php
class SingTest extends TestCase
{
    public function testSingingSong()
    {
        $input = 'Bang Bang';
        $expectedOutput = "Can you hear me singing $input :P";

        $output = (new Sing())->song($input);

        $this->assertEquals($output, $expectedOutput);
    }
}
```












<a name="Tutorial"></a>
## Tutorial   


Nextpack is shipped with a samples code, to help you get an overall idea about where to place your code.

The two Sample Features are:

**Feature 1:** Sings a song by it's `$name`

**Feature 2:** Say `Hello` to `$name` with any supported language (`English` or `French`). 

> In the terminology of Nextpack, everything that can be supported such as (Providers, API's, Third parties...) are called `Drivers`. 
> 
> In this particular case the languages are the drivers. (because you can support many languages).


When you open the `src` directory, you will find `Say.php` and `Sing.php` they are the package API's (Entry points). Those two API's will be called by the user to get the package do what it's expected to do.


##### Feature 1: (Sing a Song)
Feature 1 demonstrates the simplest scenario, it shows how to write the business logic inside the API class. (no drivers involved).

```php
class Sing extends Handler
{
    public function song($songName)
    {
        return "Can you hear me singing $songName :P";
    }
}
```
The usage of this API class will be as follow:

```php
print $song = (new Sing())->song('Bang Bang');
```

The user create an instance of `Sing` API then call the function `song()` of the class passing a string and getting a string back.


##### Feature 2: (Say Hello)
Feature 2 demonstrates the drivers scenario, it shows how to write the business logic inside a driver. _(this gives the users the ability to select their drivers)_.

```php
class Say extends Handler
{
    public function hello($name)
    {
        return $this->driver()->hello($name);
    }
}
```

An instance of a driver is initialized using `$this->driver()`, then a driver function `->hello($name)` is called on the instance.

The driver initialized is the default driver selected in the `config` file (`'default' => 'english',`) 

```php
return [
    'default' => 'english',
    'namespace' => 'Nextpack\\Nextpack\\Drivers\\',
    'drivers' => [
        'english' => [
            'format' => '%s, %s!',
        ],
        'french' => [
            'format' => '%s, %s :)',
        ],
    ],
];
```

All the drivers classes exist in the `src/Drivers`, alongside the `Driver.php` which every driver extends from.














## Test

To run the tests, run the following command from the project folder.

``` bash
$ ./vendor/bin/phpunit
```













## Contributing

Please check out our contribution [Guidelines](https://github.com/nextpack/nextpack/blob/master/CONTRIBUTING.md) for details.








## Support

[Issues](https://github.com/nextpack/nextpack/issues) on Github.








## Credits

- [Mahmoud Zalt](https://github.com/Mahmoudz)
- [All Contributors](../../contributors)








## License

The MIT License (MIT). See the [License File](https://github.com/nextpack/nextpack/blob/master/LICENSE) for more information.







