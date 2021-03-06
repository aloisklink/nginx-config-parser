# nginx-config-parser

```js

var ConfigParser = require('@webantic/nginx-config-parser')
var parser = new ConfigParser()

// parse straight from file. by default, will try to resolve includes
var config = parser.readConfigFile('/path/to/file.conf')

// to keep deterministic behaviour, set parseIncludes = false in the options
var configWithoutIncludes = parser.readConfigFile('/path/to/file.conf', { parseIncludes: false })

// write direct to file (overwriting existing one)
parser.writeConfigFile('/path/to/newfile.conf', config, true)


var sampleConfig = {
  "server": {
    "server_name": "_",
    "location /": {
      "try_files": "*.html"
    }
  }
}

// to multi-line config string
var configString = parser.toConf(sampleConfig)
// and back again
var configJson = parser.toJSON(configString)

// shorthand (will change object --> string and string --> object)
parser.parse(configString)
```

## Notes

`.readConfigFile()` will attempt to resolve includes and bundle them in the generated JavaScript object by default. If you call `.toConf()` (or `.parse()`) on the generated object, the generated conf string will differ from the original one as there is no way to replace the included content with the original `include ...` line. To control this behaviour, supply an `options` argument setting `parseIncludes` to `false`.

```js

parser.readConfigFile(filePath, callback, options)
// or
parser.readConfigFile(filePath, options)

```

By default, the `.toJSON()` method will not attempt to resolve includes (because the module has no idea where to look for the included files when it is only supplied a conf string instead of a file path). To force the module to attempt to resolve includes, you must set `options.parseIncludes` to `true` when calling the method. If you supply a value for `options.includesRoot`, the module will use that as the base path to search in. If you do not provide a value for `options.includesRoot`, the module will attempt to resolve the files in the CWD.