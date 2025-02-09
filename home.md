---
published: true
---

```dataview
TABLE 
	round(average(choice(rows.exercise , 1, 0))*100, 2) + "%" AS "Avg Exercise"
FROM "pipes/days"
GROUP BY dateformat(date(file.frontmatter.date), "WW") AS Week
```

```dataviewjs
// Get all pages from the "pipes/days" folder
const pages = dv.pages('"pipes/days"');

// Group pages by week number
const weeklyGroups = {};
pages.forEach(page => {
    const date = page.file.frontmatter.date;
    if (date) {
        const weekNumber = window.moment(date).format('WW');
        if (!weeklyGroups[weekNumber]) weeklyGroups[weekNumber] = [];

        // If 'exercise' exists in the page, push it, else assume 0
        weeklyGroups[weekNumber].push(page.exercise ? 1 : 0);
    }
});

// Calculate average exercise per week
const weeks = Object.keys(weeklyGroups).sort();
const avgExercisePerWeek = weeks.map(week => {
    const exercises = weeklyGroups[week];
    const average = (exercises.reduce((sum, val) => sum + val, 0) / exercises.length) * 100;
    return Math.round(average * 100) / 100; // Round to 2 decimal places
});

// Prepare the Chart.js data for a line plot
const chartData = {
    type: 'line',
    data: {
        labels: weeks,
        datasets: [{
            label: 'Avg Exercise per Week (%)',
            data: avgExercisePerWeek,
            fill: false,
            borderColor: 'rgba(75, 192, 192, 1)',
            backgroundColor: 'rgba(75, 192, 192, 0.2)',
            tension: 0.1
        }]
    },
    options: {
        scales: {
            y: {
                beginAtZero: true,
                max: 100,
                title: {
                    display: true,
                    text: 'Average Exercise (%)'
                }
            },
            x: {
                title: {
                    display: true,
                    text: 'Week Number'
                }
            }
        }
    }
};

// Render the chart
window.renderChart(chartData, this.container);

```

```dataview 
TABLE tags, published, file.inlinks 
AS "Mentions" 
FROM "ideas" 
```


https://github.com/mulfok/periodic-note-templates/tree/main

```dataview
LIST rows
FROM "pipes/days"
GROUP BY dateformat(date(file.frontmatter.date), "WW") AS Week
FLATTEN rows as R
```
