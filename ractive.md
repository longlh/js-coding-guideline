## Components
- Handle event only in `onrender`
- Dynamic values should be passed by template
- Internal values and methods should be initialized in `ractiveOptions`
- Always set `isolated: true` if possible

```
// Bad
component({
	template: 'date-picker',
	ractive: {
		onrender: function() {
			var date = new Date();
			
			this.selectDate = function(utc) {
				date.setTime(utc);
			};
			
			this.set('date', date);
		},
	},
});

// Good
component({
	template: 'date-picker',
	ractive: {
		isolated: true, // set isolated = true
		selectDate: function(utc) {
			// isolate scope, do not use out-scope variable
			this.get('date').setTime(utc);
		},
		data: function() {
			// internal value initialized here
			return {
				date: new Date(),
			};
		},
	}
});
```
