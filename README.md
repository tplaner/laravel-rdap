
[<img src="https://github-ads.s3.eu-central-1.amazonaws.com/support-ukraine.svg?t=1" />](https://supportukrainenow.org)

# Perform RDAP queries in a Laravel app

[![Latest Version on Packagist](https://img.shields.io/packagist/v/spatie/laravel-rdap.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-rdap)
[![GitHub Tests Action Status](https://img.shields.io/github/workflow/status/spatie/laravel-rdap/run-tests?label=tests)](https://github.com/spatie/laravel-rdap/actions?query=workflow%3Arun-tests+branch%3Amain)
[![GitHub Code Style Action Status](https://img.shields.io/github/workflow/status/spatie/laravel-rdap/Check%20&%20fix%20styling?label=code%20style)](https://github.com/spatie/laravel-rdap/actions?query=workflow%3A"Check+%26+fix+styling"+branch%3Amain)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/laravel-rdap.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-rdap)

RDAP is a protocol to query domain registration data. It is seen as the successor to WHOIS. The main advantage of WHOIS is that the returned data is standardized and structured as JSON. A downside of RDAP is that, at the moment of writing, not all TLDs are supported.

This package contains a few classes to query basic data from RDAP. It also provides caching of the responses out of the box.

## Support us

[<img src="https://github-ads.s3.eu-central-1.amazonaws.com/laravel-rdap.jpg?t=1" width="419px" />](https://spatie.be/github-ad-click/laravel-rdap)

We invest a lot of resources into creating [best in class open source packages](https://spatie.be/open-source). You can support us by [buying one of our paid products](https://spatie.be/open-source/support-us).

We highly appreciate you sending us a postcard from your hometown, mentioning which of our package(s) you are using. You'll find our address on [our contact page](https://spatie.be/about-us). We publish all received postcards on [our virtual postcard wall](https://spatie.be/open-source/postcards).

## Installation

You can install the package via composer:

```bash
composer require spatie/laravel-rdap
```


You can publish the config file with:

```bash
php artisan vendor:publish --tag="laravel-rdap-config"
```

This is the contents of the published config file:

```php
return [
    /*
     * When making an RDAP query, we first have got to make a request to determine
     *  the server responsible for the tld of the query. Here you can specify
     * how long we should cache the server URLs.
     */

    'tld_servers_cache' => [
        'store_name' => null,
        'duration_in_seconds' => CarbonInterval::week()->totalSeconds,
    ],
];
```

## Usage

You can get resolve a configured `Rdap` instance from the container:

```php
$rdap = app(Rdap::class);
```

## Perform a domain query

To get information about a domain, call `domain()`.

```php
$domain = $rdap->domain('google.com'); // returns an instance of `Spatie\Rdap\Responses\DomainResponse`
```

If you pass a non-existing domain, then the `domain()` function will return `null`.

### Get various dates

On an instance of `DomainResponse` you can call various methods to fetch various dates. All of these methods return an instance of `Carbon\Carbon`.

```
$domain->registrationDate();
$domain->expirationDate();
$domain->lastChangedDate();
$domain->lastUpdateOfRdapDb();
```

### Getting all domain properties

You can get all properties of a `DomainResponse` using `all()`.

```php
$properties = $domain->all(); // returns an array
```

To know which properties get returned, take a look at [this json containing the response for google.com](https://github.com/spatie/laravel-rdap/blob/b37a323a2743d7ae21c397367446160900aad517/tests/TestSupport/stubs/google-domain.json).

### Getting a specific domain property

Use `get()` to get a specific domain property.

```php
$domain->get('objectClassName'); // returns 'domain'
```

You can use dot notation to reach deeper in the properties.

```php
$domain->get('links.0.value'); // returns 'https://rdap.verisign.com/com/v1/domain/GOOGLE.COM'
```

## Working with RDAP DNS

For each TLD a specific server is used to respond to domain queries. Such a server is called a "DNS server". The official list of all RDAP DNS server is available as JSON [here](https://data.iana.org/rdap/dns.json).

The `Spatie\Rdap\RdapDns` class can fetch information from that JSON file. Because all above domain methods need to search the approriate DNS server, we cache the list with available DNS servers. By default, the response will be cached for a week. You can configure this caching period in the `rdap` config file.

You can resolve a configured instance from the container.

```php
$rdapDns = app(RdapDns::class);
```

### Get the DNS server URL

To get the DNS server URL for a specific domain call  `getServerForDomain`:

```php
$rdapDns->getServerForDomain('google.com'); // returns "https://rdap.verisign.com/com/v1/"
```

Alternatively, you can use `getServerForTld` and pass a TLD.

```php
$rdapDns->getServerForDomain('com'); // returns "https://rdap.verisign.com/com/v1/"
```

If you pass a domain or tld that is not supported, the above methods will return `null`.

### Get all supported TLDs

To get a list of all supported TLDs, call `supportedTlds`.

```php
$rdapDns->supportedTlds(); // returns an array with all supported TLDs
```

## Testing

```bash
composer test
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](https://github.com/spatie/.github/blob/main/CONTRIBUTING.md) for details.

## Security Vulnerabilities

Please review [our security policy](../../security/policy) on how to report security vulnerabilities.

## Credits

- [Freek Van der Herten](https://github.com/freekmurze)
- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
