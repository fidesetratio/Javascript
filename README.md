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

## Wizard Form & Validation

```` html
 <form id="regForm" action="">

<h1>Register:</h1>

<!-- One "tab" for each step in the form: -->
<div class="tab">Name:
  <p><input placeholder="First name..." oninput="this.className = ''"></p>
  <p><input placeholder="Last name..." oninput="this.className = ''"></p>
</div>

<div class="tab">Contact Info:
  <p><input placeholder="E-mail..." oninput="this.className = ''"></p>
  <p><input placeholder="Phone..." oninput="this.className = ''"></p>
</div>

<div class="tab">Birthday:
  <p><input placeholder="dd" oninput="this.className = ''"></p>
  <p><input placeholder="mm" oninput="this.className = ''"></p>
  <p><input placeholder="yyyy" oninput="this.className = ''"></p>
</div>

<div class="tab">Login Info:
  <p><input placeholder="Username..." oninput="this.className = ''"></p>
  <p><input placeholder="Password..." oninput="this.className = ''"></p>
</div>

<div style="overflow:auto;">
  <div style="float:right;">
    <button type="button" id="prevBtn" onclick="nextPrev(-1)">Previous</button>
    <button type="button" id="nextBtn" onclick="nextPrev(1)">Next</button>
  </div>
</div>

<!-- Circles which indicates the steps of the form: -->
<div style="text-align:center;margin-top:40px;">
  <span class="step"></span>
  <span class="step"></span>
  <span class="step"></span>
  <span class="step"></span>
</div>

</form> 
````



```` css
 /* Style the form */
#regForm {
  background-color: #ffffff;
  margin: 100px auto;
  padding: 40px;
  width: 70%;
  min-width: 300px;
}

/* Style the input fields */
input {
  padding: 10px;
  width: 100%;
  font-size: 17px;
  font-family: Raleway;
  border: 1px solid #aaaaaa;
}

/* Mark input boxes that gets an error on validation: */
input.invalid {
  background-color: #ffdddd;
}

/* Hide all steps by default: */
.tab {
  display: none;
}

/* Make circles that indicate the steps of the form: */
.step {
  height: 15px;
  width: 15px;
  margin: 0 2px;
  background-color: #bbbbbb;
  border: none;
  border-radius: 50%;
  display: inline-block;
  opacity: 0.5;
}

/* Mark the active step: */
.step.active {
  opacity: 1;
}

/* Mark the steps that are finished and valid: */
.step.finish {
  background-color: #4CAF50;
} 
````

```` javascript
var currentTab = 0; // Current tab is set to be the first tab (0)
showTab(currentTab); // Display the current tab

function showTab(n) {
  // This function will display the specified tab of the form ...
  var x = document.getElementsByClassName("tab");
  x[n].style.display = "block";
  // ... and fix the Previous/Next buttons:
  if (n == 0) {
    document.getElementById("prevBtn").style.display = "none";
  } else {
    document.getElementById("prevBtn").style.display = "inline";
  }
  if (n == (x.length - 1)) {
    document.getElementById("nextBtn").innerHTML = "Submit";
  } else {
    document.getElementById("nextBtn").innerHTML = "Next";
  }
  // ... and run a function that displays the correct step indicator:
  fixStepIndicator(n)
}

function nextPrev(n) {
  // This function will figure out which tab to display
  var x = document.getElementsByClassName("tab");
  // Exit the function if any field in the current tab is invalid:
  if (n == 1 && !validateForm()) return false;
  // Hide the current tab:
  x[currentTab].style.display = "none";
  // Increase or decrease the current tab by 1:
  currentTab = currentTab + n;
  // if you have reached the end of the form... :
  if (currentTab >= x.length) {
    //...the form gets submitted:
    document.getElementById("regForm").submit();
    return false;
  }
  // Otherwise, display the correct tab:
  showTab(currentTab);
}

function validateForm() {
  // This function deals with validation of the form fields
  var x, y, i, valid = true;
  x = document.getElementsByClassName("tab");
  y = x[currentTab].getElementsByTagName("input");
  // A loop that checks every input field in the current tab:
  for (i = 0; i < y.length; i++) {
    // If a field is empty...
    if (y[i].value == "") {
      // add an "invalid" class to the field:
      y[i].className += " invalid";
      // and set the current valid status to false:
      valid = false;
    }
  }
  // If the valid status is true, mark the step as finished and valid:
  if (valid) {
    document.getElementsByClassName("step")[currentTab].className += " finish";
  }
  return valid; // return the valid status
}

function fixStepIndicator(n) {
  // This function removes the "active" class of all steps...
  var i, x = document.getElementsByClassName("step");
  for (i = 0; i < x.length; i++) {
    x[i].className = x[i].className.replace(" active", "");
  }
  //... and adds the "active" class to the current step:
  x[n].className += " active";
}

````

