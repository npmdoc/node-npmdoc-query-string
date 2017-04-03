# api documentation for  [query-string (v4.3.2)](https://github.com/sindresorhus/query-string#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-query-string.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-query-string) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-query-string.svg)](https://travis-ci.org/npmdoc/node-npmdoc-query-string)
#### Parse and stringify URL query strings

[![NPM](https://nodei.co/npm/query-string.png?downloads=true)](https://www.npmjs.com/package/query-string)

[![apidoc](https://npmdoc.github.io/node-npmdoc-query-string/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-query-string_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-query-string/build..beta..travis-ci.org/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-query-string/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-query-string/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Sindre Sorhus",
        "email": "sindresorhus@gmail.com",
        "url": "sindresorhus.com"
    },
    "bugs": {
        "url": "https://github.com/sindresorhus/query-string/issues"
    },
    "dependencies": {
        "object-assign": "^4.1.0",
        "strict-uri-encode": "^1.0.0"
    },
    "description": "Parse and stringify URL query strings",
    "devDependencies": {
        "ava": "^0.17.0",
        "xo": "^0.16.0"
    },
    "directories": {},
    "dist": {
        "shasum": "ec0fd765f58a50031a3968c2431386f8947a5cdd",
        "tarball": "https://registry.npmjs.org/query-string/-/query-string-4.3.2.tgz"
    },
    "engines": {
        "node": ">=0.10.0"
    },
    "files": [
        "index.js"
    ],
    "gitHead": "bae3bb136152c10baed2070164e7c22312bc0594",
    "homepage": "https://github.com/sindresorhus/query-string#readme",
    "keywords": [
        "browser",
        "querystring",
        "query",
        "string",
        "qs",
        "param",
        "parameter",
        "url",
        "uri",
        "parse",
        "stringify",
        "encode",
        "decode"
    ],
    "license": "MIT",
    "maintainers": [
        {
            "name": "sindresorhus",
            "email": "sindresorhus@gmail.com"
        }
    ],
    "name": "query-string",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/sindresorhus/query-string.git"
    },
    "scripts": {
        "test": "xo && ava"
    },
    "version": "4.3.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module query-string](#apidoc.module.query-string)
1.  [function <span class="apidocSignatureSpan">query-string.</span>extract (str)](#apidoc.element.query-string.extract)
1.  [function <span class="apidocSignatureSpan">query-string.</span>parse (str, opts)](#apidoc.element.query-string.parse)
1.  [function <span class="apidocSignatureSpan">query-string.</span>stringify (obj, opts)](#apidoc.element.query-string.stringify)



# <a name="apidoc.module.query-string"></a>[module query-string](#apidoc.module.query-string)

#### <a name="apidoc.element.query-string.extract"></a>[function <span class="apidocSignatureSpan">query-string.</span>extract (str)](#apidoc.element.query-string.extract)
- description and source-code
```javascript
extract = function (str) {
	return str.split('?')[1] || '';
}
```
- example usage
```shell
...
- 'none': is the __default__ option and removes any bracket representation, such as:

'''js
queryString.stringify({foo: [1,2,3]});
// => foo=1&foo=2&foo=3
'''

### .extract(*string*)

Extract a query string from a URL that can be passed into '.parse()'.


## Nesting

This module intentionally doesn't support nesting as it's not spec'd and varies between implementations, which causes a lot of [
edge cases](https://github.com/visionmedia/node-querystring/issues).
...
```

#### <a name="apidoc.element.query-string.parse"></a>[function <span class="apidocSignatureSpan">query-string.</span>parse (str, opts)](#apidoc.element.query-string.parse)
- description and source-code
```javascript
parse = function (str, opts) {
	opts = objectAssign({arrayFormat: 'none'}, opts);

	var formatter = parserForArrayFormat(opts);

	// Create an object with no prototype
	// https://github.com/sindresorhus/query-string/issues/47
	var ret = Object.create(null);

	if (typeof str !== 'string') {
		return ret;
	}

	str = str.trim().replace(/^(\?|#|&)/, '');

	if (!str) {
		return ret;
	}

	str.split('&').forEach(function (param) {
		var parts = param.replace(/\+/g, ' ').split('=');
		// Firefox (pre 40) decodes '%3D' to '='
		// https://github.com/sindresorhus/query-string/pull/37
		var key = parts.shift();
		var val = parts.length > 0 ? parts.join('=') : undefined;

		// missing '=' should be 'null':
		// http://w3.org/TR/2012/WD-url-20120524/#collect-url-parameters
		val = val === undefined ? null : decodeURIComponent(val);

		formatter(decodeURIComponent(key), val, ret);
	});

	return Object.keys(ret).sort().reduce(function (result, key) {
		var val = ret[key];
		if (Boolean(val) && typeof val === 'object' && !Array.isArray(val)) {
			// Sort object keys, not values
			result[key] = keysSorter(val);
		} else {
			result[key] = val;
		}

		return result;
	}, Object.create(null));
}
```
- example usage
```shell
...

'''js
const queryString = require('query-string');

console.log(location.search);
//=> '?foo=bar'

const parsed = queryString.parse(location.search);
console.log(parsed);
//=> {foo: 'bar'}

console.log(location.hash);
//=> '#token=bada55cafe'

const parsedHash = queryString.parse(location.hash);
...
```

#### <a name="apidoc.element.query-string.stringify"></a>[function <span class="apidocSignatureSpan">query-string.</span>stringify (obj, opts)](#apidoc.element.query-string.stringify)
- description and source-code
```javascript
stringify = function (obj, opts) {
	var defaults = {
		encode: true,
		strict: true,
		arrayFormat: 'none'
	};

	opts = objectAssign(defaults, opts);

	var formatter = encoderForArrayFormat(opts);

	return obj ? Object.keys(obj).sort().map(function (key) {
		var val = obj[key];

		if (val === undefined) {
			return '';
		}

		if (val === null) {
			return encode(key, opts);
		}

		if (Array.isArray(val)) {
			var result = [];

			val.slice().forEach(function (val2) {
				if (val2 === undefined) {
					return;
				}

				result.push(formatter(key, val2, result.length));
			});

			return result.join('&');
		}

		return encode(key, opts) + '=' + encode(val, opts);
	}).filter(function (x) {
		return x.length > 0;
	}).join('&') : '';
}
```
- example usage
```shell
...
const parsedHash = queryString.parse(location.hash);
console.log(parsedHash);
//=> {token: 'bada55cafe'}

parsed.foo = 'unicorn';
parsed.ilike = 'pizza';

const stringified = queryString.stringify(parsed);
//=> 'foo=unicorn&ilike=pizza'

location.search = stringified;
// note that 'location.search' automatically prepends a question mark
console.log(location.search);
//=> '?foo=unicorn&ilike=pizza'
'''
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
