*This repository is a mirror of the [component](http://component.io) module [mattcg/tiny-deferred](http://github.com/mattcg/tiny-deferred). It has been modified to work with NPM+Browserify. You can install it using the command `npm install npmcomponent/mattcg-tiny-deferred`. Please do not open issues or send pull requests against this repo. If you have issues with this repo, report it to [npmcomponent](https://github.com/airportyh/npmcomponent).*
# tiny-deferred

Does what it says on the tin and does it well. That is all.

## Howto

The only events are `done`, `fail` and `always`.

```javascript
var def = new Deferred()
	.done(function() {
			console.log('Great success!');
		})
	.fail(function() {
			console.log('Dang.');
		})
	.always(function() {
			console.log('Always clean up after yourself.');
		});
```

Once an instance has been resolved or rejected, it can't be resolved or rejected again. However, adding a `done`, `fail` or `always` callback to a rejected object will result in the callback function being called immediately with the expected arguments and context.

```javascript
def.resolve({
	message: 'Very important message.'
});

// Many lines of code later...
def.done(function(response) {
		console.log(response.message); // Logs 'Very important message' immediately.
	})
	.fail(function() {
		console.log(response.error); // Will never be called. In fact, the callback is discarded immediately.
	})
	.always(function() {
		console.log('All over.'); // Called immediately after the done callback above.
	});
```

Returning any kind of value (null included) from a callback will cause that value to be passed as the first argument to subsequent callbacks. This feature allows us to create an interesting onion-skin pattern, morphing data as it passes through the layers of callbacks.

```javascript
def.done(function(response) {
		return templateEngine.render('my-template', response);
	});

// Many lines of code later...
def.done(function(html) {
		document.body.innerHTML = html;
	});
```

Context can be supplied to callbacks by calling the `resolveWith` or `rejectWith` methods. The context is the same for all callbacks, even those added and fired after resolution or rejection.

```javascript
new Deferred()
	.done(function(message) {
		this.log(message); // Logs 'Hai' to the console.
	})
	.resolveWith(console, 'Hai');
```

Arrays of functions may be passed to the callback methods.