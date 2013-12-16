clintish
========

Command line helper and arguments parser, based upon clint and primeish

## Installing

```sh
npm install clintish colors --save
```

## Use

```javascript
var clint = require('clintish')();
require('colors');

var bool = function(value){
	// a boolean helper if a value is present
	if (value === 'no' || value === 'false'){
		return false;
	}
	if (value === 'yes' || value === 'true'){
		return true;
	}
	return value;
};

// register full cmd line arg and aliases with some help text
clint.command('--auto', '-a', 'Watch for changes in less files, recompiles main', bool);
clint.command('--now', '-n', 'Do not keep running, instead - compile now and quit', bool);
clint.command('--no-compress', '-nc', 'Disable compression settings for less output, enabled by default, eg. ' + '--no-compress'.green + ' to disable', bool);

// register actual args
clint.command('--log', '-l', 'Set log level, eg. ' + '-l 3'.green);


var cliHelp = function(){
	// uses command definitions from above
	console.log(clint.help(2, ' : '.grey));
};

// now parser of args
var processArguments = function(args){
	args = args || process.argv.splice(2);

	var options = {
		compress: true,
		auto: false,
		now: false,
		log: 0
	};

	if (!args.length){
		// call some cli helper.
		return cliHelp();
	}

	clint.on('command', function(name, value){
		switch (name){
			case '--no-compress':
				options.compress = false;
				break;
			case '--now':
				options.now = true;
				break;
			case '--auto':
				options.auto = true;
				break;
			case '--log':
				options.log = Number(value);
		}
	});

	clint.on('complete', function(){
		runApp(options);
	});

	clint.parse(args);
};

processArguments();
```

That's about all you need to get going and develop node.js CLI apps.