#  Shy Mouse Playwright

A simple npm package to humanize mouse movements in Playwright or Patchright.

A version for **Puppeteer** is currently being **developed** and will be released once it has passed all tests.



##  Install



###  Package managers

npm: `npm i @ab6162/shy-mouse-playwright`

##  Usage

Using this package is quite easy; you just need to call it and pass a Page. Available methods:

- **`click(element, options)`** - Click on an element with human-like movement
- **`move(options)`** - Generate a random movement across the viewport
- **`scrollToElement(element, options)`** - Scroll to bring an element into view (also called automatically by click)
- **`moveToPosition(x, y, options)`** - Move to specific coordinates
- **`getMovementStats()`** - Get statistics about movement patterns (useful for debugging)
- **`reset()`** - Reset internal state (fatigue, attention span, motion tracking)

All movements include realistic timing, velocity profiles, micro-corrections, and other human behavior patterns.

This small package was created with the aim of avoiding detection by non-human movements as much as possible. Combined with Patchright, it becomes a powerful automation tool.

The package doesn't collect any kind of data, which you can see in the source code published in [GitHub](https://github.com/ab6162/shy-mouse-playwright)



Here's a quick example:

``` javascript
const { chromium } = require('patchright');
const MouseHelper = require('@ab6162/shy-mouse-playwright');

(async () => {
	const browser = await chromium.launch({ headless: false });
	const page = await browser.newPage();
	await page.goto('https://example.com');

	const mouseHelper = new MouseHelper(page);

	// Click on an element (automatically scrolls if needed)
	await mouseHelper.click('button#myButton', {
		clickPadding: 0.7,
		viewPadMin: 30,
		viewPadMax: 80
	});

	// Random movement
	await mouseHelper.move();

	// Scroll to element without clicking
	const element = await page.$('footer');
	await mouseHelper.scrollToElement(element, {
		targetPosition: 'center',
		overshootProb: 0.2
	});

	// Move to specific coordinates
	await mouseHelper.moveToPosition(500, 300);

	// Another click
	await mouseHelper.click('a#nextLink');

	// Get stats (optional, for debugging)
	const stats = mouseHelper.getMovementStats();
	if (stats) {
		console.log('Average speed:', stats.averageSpeed.toFixed(2));
		console.log('Fatigue level:', stats.fatigueLevel);
	}

	await browser.close();
})();

```

##  How it works

The core idea is simple: make mouse movements that look and feel human, not robotic.

Most bots get caught because their movements are too perfect or follow weird patterns. This package tackles that by combining several techniques that mimic how humans actually move a mouse.

**The main stuff:**

Firstly, it uses **Bezier curves** to create smooth paths, but not just any curves - the control points are randomized with realistic deviations so each movement is unique. Humans don't move in straight lines or perfect arcs.

**Fitts's Law** is critical here. It's basically a formula that predicts how long it takes humans to move to a target based on distance and target size. The package calculates proper timing (MT = a + b·ID) so movements don't happen impossibly fast or unnaturally slow.

Beyond that, there's a bunch of smaller details that add up:
- **Velocity profiles** follow a bell curve (fast in the middle, slower at start/end), not constant speed
- **Overshoot and correction** - sometimes humans go a bit past the target and correct back
- **Fatigue simulation** - after many actions, movements get slightly slower and less precise
- Mouse **polling rate variation** (60-144Hz) with temporal correlation, not fixed intervals
- **Jerk smoothing** - acceleration changes are gradual, not instant
- Random **micro-corrections** and **hesitations** during movement
- **Perlin noise** for natural entropy instead of pure random jitter

The scroll behavior also mimics humans: logarithmic deceleration, occasional overshoots, variable step sizes, and small mouse movements while scrolling.

Everything has some randomness but it's **constrained randomness** - the kind of variation you'd see in real human behavior, not chaos. The goal is to pass both simple heuristics and more sophisticated ML-based detection systems.

Obviously nothing is 100% undetectable, but this makes it significantly harder to distinguish from real users compared to basic automation.

##  Issues

If there are any bugs, questions or improvements open a new issue


##  License

MIT