
## Express Node js

Command line init

`````bash
mkdir blog
cd blog
npm init
npm install nodemon
npm install express
npm install ejs
npm install cors
npm install bcyrpt
npm install jsonwebtoken


mkdir static
mkdir static/admin
mkdir static/admin/css
mkdir static/admin/js

mkdir views
mkdir views/admin
touch views/admin/dashboard.ejss

touch server.js

--- server.js ----
var express = require("express");
var app = express();
app.listen(3000, function(){
	console.log("Connected");
})
------------------
nodemon server.js
------------------

==========================================================================================

--- server.js ----
Basic one
var express = require("express");
var app = express();

app.use("/static",express.static(__dirname + "/static"));
app.set("view engine","ejs");
app.use(express.urlencoded({extended:true}));
app.use(express.json());

app.get("/",function(req,res){
    res.send("hello world (only res body)");
})

app.get("/home",function(req,res){
    res.render("home/index");
});

app.get("/test", function(req,res){
 res.json({
     "status":"ok",
     "message": "test Successfuly"

 });
});
app.listen(3000,function(){
    console.log("connected");
})
=======================================================================================

<%- include("includes/header") %>
 
<h3>Hello world From HTML</h3>
    
</body>
</html>

=======================================================================================
<link rel="stylesheet" href="static/css/style.css"/>


const express = require("express");
const router = express.Router();



router.use("/view",(req,res)=>{
    
    res.send("About view");


})

module.exports = router;


app.use(view);



`````

