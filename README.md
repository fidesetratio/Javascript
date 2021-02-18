#	Javascript tips and trick

## Ajax post

````html
<html>
 <form method="POST" action="" onsubmit="return do_submit(this);">
        <div class="form-group">
            <label class="control-label" for="firstname">FirstName</label>
			<input type="text" name="username" value="" id="firstname"/>
        </div>
        <div class="form-group">
            <label class="control-label" for="lastname">LastName</label>
			<input type="text" name="password" value="" id="lastname"/>           
        </div>
        <button name="submit" id="submit" name="submit" class="btn-primary">
            Submit
        </button>
    </form>    
</html>
````

````javascript
  function do_submit(form){
  				form.submit.setAttribute("disabled","disabled");
  				form.submit.innerHTML = "<span>Loading....</loading>";
  				var ajax = new XMLHttpRequest();
  				ajax.open("POST","/post/login",true);
  				
  				ajax.onreadystatechange = function(){
  					if(this.readyState == 4 && this.status == 200){
  						form.submit.removeAttribute("disabled");
  						form.submit.innerHTML = "<span>Submit</span>";
  						var response = JSON.parse(this.responseText);
  						alert(response.message);
  						if(response.status == "success"){
  							window.location.href="/dashboard.html";
  						}
  					}
  					
  				}
  				ajax.send(new FormData(form));
  				return false;
  			} 			
````

Java

````java
@PostMapping("/login")
		public Response post(@RequestParam(name = "username") String username, @RequestParam(name = "password")  String password) {
			System.out.println("username:"+username);
			System.out.println("password:"+password);
			return new Response("success","success");

		}
		
````



## Ajax post body content

````html

  <div class="container">
  <h3>Ajax Body</h3>
  		<div class="row">
  				<div class="col-md-6">
  				 <form method="POST" action="" onsubmit="return do_submit_body(this);">
        <div class="form-group">
            <label class="control-label" for="firstname">FirstName</label>
			<input type="text" name="username" value="" id="firstname"/>
        </div>
        <div class="form-group">
            <label class="control-label" for="lastname">LastName</label>
			<input type="text" name="password" value="" id="lastname"/>
            
        </div>
        <button name="submit" id="submit" name="submit" class="btn-primary">
            Submit
        </button>
    </form>
  	
  		
  	</div>
  		</div>
  </div>
````

```` javascript
function do_submit_body(form){
  				form.submit.setAttribute("disabled","disabled");
  				form.submit.innerHTML = "<span>Loading....</loading>";
  				var json = {};
  				json.username = form.username.value;
  				json.password = form.password.value;
  				alert(JSON.stringify(json));
  				var ajax = new XMLHttpRequest();
  				ajax.open("POST","/post/loginbody",true);

  				ajax.onreadystatechange = function(){
  					if(this.readyState == 4 && this.status == 200){
  						form.submit.removeAttribute("disabled");
  						form.submit.innerHTML = "<span>Submit</span>";
  						var response = JSON.parse(this.responseText);
  						alert(response.message);
  						if(response.status == "success"){
  							window.location.href="/dashboard.html";
  						}
  					}
  					
  				}
  				ajax.setRequestHeader('Content-Type', 'application/json; charset=utf-8');
  				ajax.send(JSON.stringify(json));
  				return false;
  			}
````

Java

````java
public class RequestTable {
	
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	private String username;
	private String password;

}

	@PostMapping("/loginbody")
		public Response loginbody(@RequestBody RequestTable request) {
			System.out.println("username loginbody:"+request.getUsername());
			System.out.println("password loginbody:"+request.getPassword());
			return new Response("success","success");

		}
````

## On Change Form

```` javascript
function FormChanges(form) {

	// get form
	if (typeof form == "string") form = document.getElementById(form);
	if (!form || !form.nodeName || form.nodeName.toLowerCase() != "form") return null;
	
	// find changed elements
	var changed = [], n, c, def, o, ol, opt;
	for (var e = 0, el = form.elements.length; e < el; e++) {
		n = form.elements[e];
		c = false;
		
		switch (n.nodeName.toLowerCase()) {
		
			// select boxes
			case "select":
				def = 0;
				for (o = 0, ol = n.options.length; o < ol; o++) {
					opt = n.options[o];
					c = c || (opt.selected != opt.defaultSelected);
					if (opt.defaultSelected) def = o;
				}
				if (c && !n.multiple) c = (def != n.selectedIndex);
				break;
			
			// input / textarea
			case "textarea":
			case "input":
				
				switch (n.type.toLowerCase()) {
					case "checkbox":
					case "radio":
						// checkbox / radio
						c = (n.checked != n.defaultChecked);
						break;
					default:
						// standard values
						c = (n.value != n.defaultValue);
						break;				
				}
				break;
		}
		
		if (c) changed.push(n);
	}
	
	return changed;

}
````



```` html
<form id="myform" action="index.html" method="post">
<fieldset>
	<legend>Your profile</legend>
	<input type="hidden" id="changed" name="changed" value="yes" />
	<div>
		<label for="name">name:</label>
		<input type="text" id="name" name="name" value="Jonny Dough" />
	</div>
	<div>
		<label for="job">job title:</label>
		<select id="job" name="job">
			<option>web designer</option>
			<option selected="selected">web developer</option>
			<option>graphic artist</option>
			<option>IT professional</option>
			<option>other</option>
		</select>
	</div>
	<div>
		<button type="submit">Update Profile</button>
	</div>
</fieldset>
</form>
````

Example of usage

```` javascript
var form = document.getElementById("myform");
form.onsubmit = function() {
	if (FormChanges(form).length == 0) {
		document.getElementById("changed").value = "no";
	}
	return true;
}
````

