Quick and dirty script to generate data

To run, do

```
cd genData
npm install && node generateData.js
```

That should populate the database with a few users and a route that leaves every other hours for today.
The script assumes the database is empty. If the users are already created you can simply comment out all the lines with
`await register...`.
