#Finding Restaurants

Find the first 5 restaurants returning only the name
```
db.restaurants.find({}, {_id: 0, name: 1}).limit(5)
```

Find the name of all restaurants with at least 1 grade of A or B

```
db.restaurants.find({
  grades: {$elemMatch: {grade: 'A'}}
  }, {_id: 0, name: 1})
```

Find the name of all restaurants with at least 1 score above 20

```
db.restaurants.find({
  grades: {$elemMatch: {score: {$gte: 20}}}
  }, {_id: 0, name: 1})
```

Find the unique types of cuisine in restaurants in the Bronx

```
db.restaurants.distinct('cuisine', {
  borough: "Bronx"
  })
```
Find all the names and addresses of all the Spanish restaurants in Queens

```
db.restaurants.find({
  $and: [
    {borough: 'Queens'},
    {cuisine: 'Spanish'}
  ]}, {_id: 0, name: 1, address: 1})
```
Find all the names and addresses of all the restaurants in Manhattan that are not a Bakery, Spanish, Italian or Irish

```
db.restaurants.find({
  $and: [
    {cuisine: {$ne: 'Bakery'}},
    {cuisine: {$ne: 'Spanish'}},
    {cuisine: {$ne: 'Italian'}},
    {cuisine: {$ne: 'Irish'}},
    {borough: {$ne: 'Manhattan'}}
  ]}, {_id: 0, name: 1, address: 1})
```
Find the name and address of the first alphabetically named Asian restaurant a grade of A

```
db.restaurants.find({
  $and: [
    {cuisine: 'Asian'},
    {grades: { $elemMatch: {grade: 'A'} }},
  ]}, {_id: 0, name: 1, address: 1, cuisine: 1}).sort({name: 1}).limit(1)
```

#Aggregating Restaurants

List the number of restaurants under each zipcode

```
db.restaurants.aggregate([
    {$group: {
      _id: zipcode
      number: {$sum: 1}
      }}
  ])

List unique names of all restaurants with at least 1 grade of A or B sorted in reverse alphabetical order
