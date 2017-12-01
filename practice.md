# Practice with MongoDB

## 1.)
  
  db.products.insert({name:"Hammer", price:9.99, department:"Hardware",color:"red",sales:80,stock:50})

## 2.)

  db.products.insert([
    {name:"Screwdriver", price:19.99, department:"Hardware",color:"green",sales:75,stock:50},
    {name:"Wrench", price:21.99, department:"Hardware",color:"orange",sales:70,stock:50}
   ])

## 2.) 

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

## 3.)

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

## 5,6 

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

## 7

  db.products.update(
    {
      department:"Hardware"
    },
    {
      $inc: {sales:1}
    }
  )


  db.products.find({})
