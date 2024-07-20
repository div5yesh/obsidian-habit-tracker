```dataviewjs
const date = dv.luxon.DateTime.now();
//const vdate = dv.luxon.DateTime.fromISO("2024-06-14T09:10:23-09:00");
const vdate = date;
const sow = vdate.startOf('week')
const eow = vdate.endOf('week');

let tracker = dv.page("Habit Tracker"); 
var habitArray = tracker.habits.map(n => [n]); 

function streak(habit) {
	const tasks = dv.page("Habit Tracker").file.tasks.filter(t => t.text.includes(habit));
	const completed = tasks.filter(t => t.completed).length;
	return completed/tasks.length > .5 ? "🔥" : "🚨";
}

const tasks = tracker.file.tasks
	.filter(t => t.text.includes("#habit"))
	.filter(t => +t.scheduled >= sow)
	.sort(t => dv.date(t.due));

for (let i = 0; i < habitArray.length; i++) {
	for (let j = 0; j < 7; j++) {
		const xdate = sow.plus({ days: j });
		const isDone = tasks.filter(t => 
			t.text.includes(habitArray[i][0]) && t.completed && +t.scheduled == +xdate
		).length == 1

		if (+xdate <= +date) {
			habitArray[i][j + 1] = "❌";
			if (isDone) habitArray[i][j + 1] = "✅";
		}
	}
}

for (let i = 0; i < habitArray.length; i++) {
	habitArray[i][0] = streak(habitArray[i][0]) + habitArray[i][0];
}

dv.table( ["[[Habit Tracker | Habits]]", "M","T","W","T","F","S", "S"], habitArray);
```
```tasks
not done
scheduled today
tags include #habit 
hide scheduled date
hide edit button
hide postpone button
hide recurrence rule
hide tags
hide backlink
hide task count
```