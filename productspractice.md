# Practice with MongoDB

##Inserting Products

### 1.)

  db.products.insert({name:"Hammer", price:9.99, department:"Hardware",color:"red",sales:80,stock:50})

### 2.)

  db.products.insert([
    {name:"Screwdriver", price:19.99, department:"Hardware",color:"green",sales:75,stock:50},
    {name:"Wrench", price:21.99, department:"Hardware",color:"orange",sales:70,stock:50}
   ])

##Updating Products
### 1.)

  db.products.update(
    {
      department:"Hardware"
    },
    {
      $set:{department:"Harware Store"}
    },
    {
      multi:true
    }
  )

### 2-4.)

  db.products.update(
    {
      department:"Hardware Tools"
    },
    {
      $inc: {price:10},
      $max: {sales:50},
      $set:{department:"Hardware"}
    },
    {
      multi:true
    }
  )

### 5,6

  db.products.update(
    {
      department:"Hardware"
    },
    {
      $inc: {price:-10},
      $min: {sales:10}
    },
    {
      multi:true
    }
  )

### 7

  db.products.update(
    {
      department:"Hardware"
    },
    {
      $inc: {sales:1}
    }
  )


  db.products.find({department: "Hardware"})

##Removing Products

###1.
Remove the first product in the "Hardware" department

```javascript
db.products.deleteOne({department: "Hardware"})
```

###2.
Remove all products in the "Hardware" department

```javascript
db.products.remove({department: "Hardware"})
```

##Finding Products

###1.
Find the names of all the products that are out of stock

```
db.products.find({stock: 0}, {_id: 0, name: 1})
```

###2.
Find the stock count of all the products with a price below $100
```
db.products.find({price: {$lt: 100}}, {_id: 0, name: 1, stock: 1})
```

###3.
Find the name, color and department of all the products with a price between $100 and $1000
```
db.products.find({price: {$lt: 1000, $gt: 100}}, {_id: 0, name: 1, color: 1, department: 1, price: 1})
```

###4.
Find the names of all the red products
```
db.products.find({color: 'red'}, {_id: 0, name: 1, color: 1})
```

###5.
Find only the IDs of all the red and blue products
```
db.products.find({
  $or: [
    {color: 'red'},
    {color: 'blue'}
  ]}, {_id: 1, name: 1, color: 1})
```

###6.
Find the names of all the products that are not red or blue
```
db.products.find({
  $and: [
    {color: { $ne: 'blue'}},
    {color: { $ne: 'red'}}
  ]}, {_id: 1, name: 1, color: 1})
```

###7.
Find the names of all the products that are not in the Sports or Games departments
```
db.products.find({
  $and: [
    {department: { $ne: 'Sports'}},
    {department: { $ne: 'Games'}}
  ]}, {_id: 0, name: 1, department: 1})
```

###8.
Find the name and price of all the products with names that begin with the letter F and end with the letter S and ignore case

```js
db.products.find({
  name: { $regex: /^f.*s$/, $options: 'i' }}, {_id: 0, name: 1})
```

### 9.
Using $where, find all the product names that begin with T

```js
db.products.find({ $where: "this.name[0] == 'T'"}, {_id: 0, name: 1})
```

###10.
Using $where, find all the product names that begin with capital F or end with lowercase S

```js
db.products.find({ $where: "this.name[0] === 'F' || this.name[0] === 's'"}, {_id: 0, name: 1})
```

###11.
Using $where, find all the product names that begin with capital T and have a price less than $100

```js
db.products.find({ $where: "this.name[0] === 'T' && this.price < 100"}, {_id: 0, name: 1})
```

###12.
Using $where, find all the product names and prices of products that either start with A and have a price of at least $100 or start with B and have a price of at most $100

```js
db.products.find({ $where: "(this.name[0] === 'A' && this.price >= 100) || (this.name[0] === 'B' && this.price <= 100)"}, {_id: 0, name: 1})
```

##Aggregating Products


With the Aggregation Pipeline

###1.
Find the total number of sales each department made and sort the results by the department name

```
db.products.aggregate([
  {$group: {
    _id: "$department",
    sales: {$sum: '$sales'}
    }},
  {$sort: {_id: 1}}
  ])
```

###2.
Find the total number of sales each department made of a product with a price of at least $100 and sort the results by the department name

```
db.products.aggregate([
  {$match: {
    price: {$gte: 100}
    }},
  {$group: {
    _id: "$department",
    sales: {$sum: '$sales'}
    }},
  {$sort: {_id: 1}}
  ])
```

###3.
Find the number of out of stock products in each department and sort the results by the department name

```
db.products.aggregate([
  {$match: {
    stock: 0
    }},
  {$group: {
    _id: "$department",
    sales: {$sum: '$sales'},
    stock: {$sum: '$stock'}
    }},
  {$sort: {_id: 1}}
  ])
```

With Map-Reduce

###1.
Find the number of products with each color

```
db.products.mapReduce(
  function(){emit(this.color, 1)},
  function(key, values){ return Array.sum(values)},
  {
    query: {},
    out: "colors"
  }
  ).find()
```

###2.
Find the total revenue of each department (how much did each department make in sales?)

```
db.products.mapReduce(
  function(){emit(this.department, this.sales)},
  function(key, values){ return Array.sum(values)},
  {
    query: {},
    out: "departmentsales"
  }
  ).find()
```

###3.
Find the potential revenue of each product (how much can each product make if the entire remaining stock is sold?)

```js
db.products.mapReduce(
  function(){emit(this._id, this.price * this.stock)},
  function(key, values){ return values},
  {
    query: {},
    out: "departmentsales"
  }
  ).find()
```
###4.

```
db.products.mapReduce(
  function(){emit(this.department, this.sales)},
  function(key, values){ return Array.sum(values)},
  {
    query: {},
    out: "departmentsales"
  }
  ).find()
```

```js
db.products.mapReduce(
  function(){emit(this._id, this.price * this.stock + this.sales * this.price)},
  function(key, values){ return values},
  {
    query: {},
    out: {inline:1}
  }
  ).find()
```
## Single purpose aggregation
How many products are there?
```js
db.products.count({})
```
How many products are out of stock?

```js
db.products.count({
  stock:0
})
```
How many products are fully stocked? (100)

```js
db.products.count({
  stock:100
})
```
How many products are almost out of stock? (>= 5)

```js
db.products.count({
  stock:{$lte:5}
})
```

What are all the unique names of all the departments?

```js
db.products.distinct('department');
```
What are all the unique names of product colors?

```js
db.products.distinct('color')
```

Find the total number of out of stock products for each department.

```js
db.products.group({
  key: {department: 1},
  cond: { stock: {$eq:0}},
  reduce: function(cur, result) {result.count += 1},
  initial: {count:0}
})

```
