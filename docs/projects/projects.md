---
disqus: jojoduquartier
---

1. [Guest Parking Registration Automation](https://github.com/jojoduquartier/automated-guest-parking)
    <details>
        <summary>Click to Expand</summary>

    I recently found out that there are services used by apartment complexes to register guest cars. In my case, I have a website that needs to be filled with the tenant information and the visiting guests' car plate and color. This prevents my guests' car from being towed away as parking is only limited to residents.

    Instead of repeating the process everytime, I **created a GUI** to go to the website and register cars for my regular visitors.

    I am happy with where this GUI is right now as it is very cool and does exactly what I need it to do. In the future, I may spend more time on this to make it into something that uses facial recognition to load guest profiles! I can easily have this on a Raspberry Pi right by the door to allow visitors with cars to register their cars in the parking spots
    </details>

2. [Credit Card Payment Optimization](https://github.com/jojoduquartier/creditpaymentallocation)
    <details>
        <summary>Click to Expand</summary>

    I wanted to play around with optimizing credit car payments. Basically if I have a fixed budget, how should I split it to minimize the amount of interest paid over time. To do this, I had to use the **FastApi** package in Python with **Linear Programming** provided by the PuLP package.

    I even created a very simple webapp that uses the API in the background to show some payment projection. The app is hosted on heroku @ https://pymtallocation.herokuapp.com/index.html
    
    </details>