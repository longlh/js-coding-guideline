## Avoid call `ractive.set` many times
Each time `ractive.set` is called, template will render. So it is bad for performance if `ractive.set` is called many times.
```
// Bad: view.set is called 3 times
view.set('keypath1', value1);
view.set('keypath2, value2);
view.set('keypath3, value3);

// Good: view.set is called only 1 time
view.set({
	keypath1: value1,
	keypath2: value2,
	keypath3: value3,
});
```

## Components
- Handle event only in `onrender`
- Dynamic values should be passed by template
- Internal values and methods should be initialized in `ractiveOptions`
- Methods should be independencied with outside scope
- Always set `isolated: true` if possible

```
// Bad
component({
	template: 'date-picker',
	ractive: {
		onrender: function() {
			var date = new Date();
			
			// Bad: declare method in `onrender`
			this.selectDate = function(utc) {
				// Bad: use outside-scope variables
				date.setTime(utc);
			};
			
			// Bad: set internal method in `onrender`
			this.set('date', date);
		},
	}, // Bad: not set isolated = true
});

// Good
component({
	template: 'date-picker',
	ractive: {
		isolated: true, // set isolated = true
		selectDate: function(utc) {
			// isolated scope, do not use outside-scope variables
			this.get('date').setTime(utc);
		},
		data: function() {
			// internal values initialized here
			return {
				date: new Date(),
			};
		},
	}
});
```

## Require components
`services/component` return a promise, so it is good to use `async-object` to import multiple components
```
view({
	template: 'app-form',
	ractive: asyncObject({
		DatePicker: require('../components/date-picker'),
		TimePicker: require('../components/time-picker'),
	}).then(function(componentDef) {
		return {
			components: componentDef,
		};
	}),
});
```

## Teardown
On `teardown` event, free all pointers and de-register all event listener
```
function handleClick() {
	// do some logic here
}

// declare a sample decorator
function(node) {
	node.addEventListener('click', handleClick);
	
	return {
		teardown: function() {
			node.removeEventListener('click', handleClick);
		},
	};
}
```
