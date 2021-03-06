# cliargs

cliargs is a command-line argument parser for Lua. It supports 3 types of arguments:

1. required arguments
1. optional arguments with different notations
1. optional "flag" arguments (on/off options)

## Usage example

The following script will define 4 arguments for the program called `test.lua`:

* a required argument identified as `ROOT` for the user
* an optional argument that points to an output file which can be specified using `-o FILE`
* another optional argument that points to an input file which can be specified using two different notations:
 * `-i FILE`
 * `--input=FILE`
* an optional flag argument `-v` or `--version` whose value will be set to `true` if it was specified

### Defining arguments

```lua
local cli = require "cliargs"
cli:set_name("test.lua")
cli:add_argument("ROOT", "path to where root scripts can be found")
cli:add_option("-o FILE", "path to the output file")
cli:add_option("-i, --input=FILE", "path to an input file", "input_path")
cli:add_flag("-v, --version", "prints the program's version and exits")

local args = cli:parse_args()
if not args then
  -- something wrong happened and an error was printed
  return
end

-- argument parsing was successful, arguments can be found in `args`
for k,item in pairs(args) do print(k .. " => " .. tostring(item)) end

-- checking for flags: is -v or --version set?
if args["v"] then
  return print("test.lua: version 0.0.0")
end

-- overridden keys:
print("Input file: " .. args["input_path"])
-- default keys:
print("Output file: " .. args["o"])
```

**A note on arguments**

All types of arguments must specify a *key*. In the case of required arguments, the keys are only used in the help listings. However, for optional arguments, they are mandatory and only the *--extended-key* notation is optional. Accessing argument values is always done using the key with the leading `-` omitted.

### Help listings `--help`

A help listing will be automatically generated and accessed using the `--help` argument. You can also force its display in the code using `cli:print_help()`.

This is how it looks like for our example:

```bash
Usage: test.lua [OPTIONS]  root 

Required arguments: 
  root                 path to where root scripts can be found

Optional arguments: 
  -o FILE              path to the output file (default: ) 
  -i, --input=FILE     path to an input file (default: ) 
  -v, --version        prints the program's version and exits 
```

### Argument validation

From a parsing point of view, there are 3 cases that need to be handled which are outlined below. If I missed something, please open a ticket!

**Missing a required argument**

```bash
>> lua test.lua
test.lua: error: missing arguments, at least 1 argument(s) must be specified; re-run with --help for usage.
Usage: test.lua [OPTIONS]  root
```

**Missing value for an optional argument**

```bash
>> lua test.lua /my_root -i
test.lua: error: missing argument value in '-i FILE'; re-run with --help for usage.
```

**Unknown arguments**

```bash
>> lua test.lua /my_root -f
test.lua: error: unknown option -f; re-run with --help for usage.
```

## Reference

A function reference was generated using [LunaDoc](http://jgm.github.com/lunamark/lunadoc.1.html) which can be found [here](http://lua-cliargs.docs.mxvt.net).

## License

I personally don't care what you do with the code. Whether you use it, extend it, or publish it under your own name, you got my blessing!