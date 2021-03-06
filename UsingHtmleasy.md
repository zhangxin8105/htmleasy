<a href='http://htmleasy.googlecode.com/files/HtmleasyPlayground-2013-03-04.zip'><img src='https://github.com/codedance/HtmleasyPlayground/raw/master/screenshot.png' align='right' alt='Download the Htmleasy Playground Project' /></a>
# Getting Started #

To quickly get a feel for **Htmleasy** try downloading the preassembled **[Htmleasy Playground](http://htmleasy.googlecode.com/files/HtmleasyPlayground-2013-03-04.zip)** project (a Google App Engine project).

# Installation #

Please read the [installation notes](Installation.md) to find out how to add Htmleasy to your project or existing Resteasy configuration.  Setup is relatively simple as Htmleasy is a simple Resteasy plugin.

# Prerequisites #

Readers will find knowledge of JAX-RS and/or [Resteasy](http://www.jboss.org/resteasy) helpful.

# Patterns #

There are several ways to use Htmleasy.	 These examples will start simple and get progressively more complex.  These examples skip getters and setters for readability; you can eliminate getters and setters and much other boilerplate from your code permanently with [Project Lombok](http://www.projectlombok.org/).

## Trivial View ##

Sometimes all you want to do is render some html.

```
@Path("/about")
public class About
{
	@GET @Path("/privacy")
	public View privacy()
	{
		return new View("/privacy_policy.jsp");
	}
}
```

## View With Model ##

Static html isn't very interesting, even if you do get clean URLs with JAX-RS.  You can add a model to a view for rendering:

```
@Path("/time")
public class About
{
	@GET @Path("/now")
	public View whatTimeIsIt()
	{
		return new View("/calendar.jsp", new Date());
	}
}
```

The date object will be placed in the request attributes with the key name `model`.  You can customize the name in the View constructor, if you choose.

## Alternate View System ##
Htmleasy does not mandate a template/view technology. JSP is not a requirement. You are free to use your template technology of choice.

```
@Path("/time")
public class About
{
	@GET @Path("/now")
	public View whatTimeIsIt()
	{
                String now = SimpleDateFormat.getDateTimeInstance().format(new Date());
                // Render using Silken (Google Closure Templates)
		return new View("/soy/com.myorg.pages.time", ImmutableMap.of("time", now));
	}
}
```

Htmleasy will work with any view technology available under your servlet context. Views can access the model data via the servlet HTTP request attributes under a key name of '"model"' (changeable default). Some options, such as [Silken](https://github.com/codedance/silken) (Google Closure Templates) has been specifically developed for Htmleasy. Other you could consider include [Freemaker](http://freemarker.sourceforge.net/), [Velocity](http://velocity.apache.org/), [Cambridge Templates](https://github.com/erdincyilmazel/Cambridge) or [String Templates](http://www.stringtemplate.org/) if wrapped in a bit of servlet code.

## Custom Viewable ##

You can return an object that implements the Viewable interface to render anything you want:

```
@Path("/hello")
public class Greeting
{
	@GET
	public Viewable world()
	{
		return new Viewable() {
			public void render(HttpServletRequest request, HttpServletResponse response)
			{
				response.getWriter().println("Hello, World");
			}
		};
	}
}
```

## Annotation-Based Views ##

You can annotate arbitrary data objects that should be rendered as HTML.

```
@ViewWith("/car.jsp")
public class Car
{
	Long id;
	String color;
	int numberOfDoors;
}
```

```
@Path("/car")
public class CarResource
{
	@GET @Path("/{id}")
	@Produces(MediaType.TEXT_HTML)
	@Produces(MediaType.APPLICATION_JSON)
	public Car getCar(@PathParam("id") Long id)
	{
		return // ...load car from database
	}
}
```

```
// car.jsp
<p>The car is ${model.color} and has ${model.numberOfDoors} doors.</p>
```

Normally JAX-RS can produce JSON, XML, or any of several other formats from the getCar() method. Now in addition, the same method will produce HTML if the client (browser) asks for it.

Note that the name of the model in the request context can be changed using the `modelName` attribute on the `@ViewWith` annotation.

## View Override ##

The `@ViewWith` annotation on a data class is just a default. You can override it on a per-method basis:

```
@Path("/car")
public class CarResource
{
	@GET @Path("/{id}")
	public Car getCar(@PathParam("id") Long id)
	{
		return // ...load car from database
	}
	
	@GET @Path("/{id}/edit")
	@ViewWith("/car_edit.jsp")
	public Car editCar(@PathParam("id") Long id)
	{
		return this.getCar(id);
	}
}
```

## Polymorphism ##

The view for rendering is dynamically chosen based on the object returned:

```
@Path("/vehicle")
public class VehicleResource
{
	@GET @Path("/{id}")
	@ViewWith(ifClass=Boat.class, value="/boat_special.jsp")
	public Vehicle getVehicle(@PathParam("id") Long id)
	{
		return // ...load from database; could be Car, Boat, Airplane, etc. (subclasses of Vehicle)
	}
}
```

Note that you can add one or more `@ViewWith` annotations with an `ifClass` Attribute by annotating your method with an `@ViewSet`. This will override the default (or missing) `@ViewWith` on objects of a particular class or superclass.  Note that the order of annotations is relevant here.

```
@Path("/boat")
public class BoatController
{
	@GET @Path("/{id}")
        @ViewSet({
            @ViewWith(ifClass=PowerBoat.class, value="/power_boat.jsp"),
            @ViewWith(ifClass=SailingBoat.class, value="/sailing_boat.jsp"),
            @ViewWith(ifClass=Dinghy.class, value="/dinghy.jsp")
         })
	public Boat getBoat(@PathParam("id") Long id)
	{
		return // ...load from database; could be PowerBoat, SailingBoat, or Dinghy.
	}
}
```

## Safe Paths ##

Controller paths (i.e. routes) are defined by class and method annotations. Htmleasy supports refactor/type safe path references at all MVC layers.  Rather than using a static String to reference a controller path, your code can reference the controller class or class/method directly using `Path.to()`.


```
// home.jsp
<ul>
  <li><a href="<%= Path.to(BoatController.class) %>">All boats</a></li>
  <li><a href="<%= Path.to(BoatController.class, "findMoorings") %>">Find a mooring</a></li>
  <li><a href="/">Home</a></li>
</ul>
```

The `RedirectException` may also be constructed with class or class/method references (as well as static Strings or URIs).

```
@Path("/boat")
public class BoatController
{
	@POST
	public void newBoat(@Form Boat boat)
	{
                if (boat.getType() != BoatType.SAIL)
                {
                     // Nicer than RedirectionException("/security/information/invalid_access")!
                     throw new RedirectException(InvalidAccessController.class);
                }
		// ...
	}
}
```

## AJAX alongside rather than "offside" ##
In modern Web 2.0 applications each page often has a number of supporting server-side AJAX/JSON calls.  For example a user signup form may have AJAX support to check the username availability as they type. Htmleasy allows you to place these supporting functions right alongside your view controllers.

```
@Path("/signup")
public class SignupController {
	
	@GET
	public View showSignupForm()
        {
		return new View("signup_form.jsp");
	}
	
        /** The signup form will call this via AJAX to offer as-you-type feedback */ 
	@POST @Path("/check_username")
	@Produces("application/json")
	public ImmutableMap<String, ? extends Object> validateUsername(
                              @FormParam("username") String username) 
        {
		if (username.startsWith("admin")) 
                {	
			return ImmutableMap.of(
					"valid", false,
					"msg", "Sorry. This username is already taken."
					);	
		}
		
		return null;
	}

        // ... post form handlers, other validates, etc. all logically grouped here.
}
```

Also notice how testable these methods are. After all they are just Java methods with some pretty annotations!

(Example above will work if you've added [RestEasy's Jackson support](http://docs.jboss.org/resteasy/docs/2.3.1.GA/userguide/html/json.html).)

## Form Handling ##

In modern Web 2.0 applications, traditional form handling is slowly being replaced with smart javascript forms.	 Nevertheless, Htmleasy provides several options for managing the page flow of form submission.

These examples assume that:
  * On success, the client should be redirected to a new location.
  * On failure, the client should receive the form again with any errors highlighted.

There is no one way to do form handling, but here are some options:

### Basic Form Handling ###

This is the simplest possible approach to form handling.  It may seem slightly odd at first but quickly becomes natural, especially if you continue to think of these as REST calls.

```
@ViewWith("/car.jsp")
public class Car
{
	@PathParam("id") Long id;
	@FormParam("color") String color;
	@FormParam("numberOfDoors") int numberOfDoors;
	
	Map<String, String> errors;
	public void addError(String key, String msg) 
	{
		if (errors == null) errors = new HashMap<String, String>();
		errors.put(key, msg);
	}
}
```

```
@Path("/car")
public class CarResource
{
	@GET @Path("/{id}")
	public Car getCar(@PathParam("id") Long id)
	{
		return // ...load car from database
	}
	
	@GET @Path("/{id}/edit")
	@ViewWith("/car_edit.jsp")
	public Car editCar(@PathParam("id") Long id)
	{
		return this.getCar();
	}
	
	@POST @Path("/{id}")
	@ViewWith("/car_edit.jsp")
	public Car saveCar(@Form Car car)
	{
		if (!"black".equals(car.color))
			car.addError("color", "You can have any color you want as long as it is black");
		
		if (car.numberOfDoors > 5)
			car.addError("numberOfDoors", "I don't believe you");
		
		if (car.errors != null)
			return car;
		else
		{
			// Save the car data
			throw new RedirectException("/car/" + car.id);
		}
	}
}
```

```
// car_edit.jsp
<form method="post" action="/car/${model.id}">
	<input type="text" name="color" value="${model.color}"/> ${model.errors.color}
	<br/>
	<input type="text" name="numberOfDoors" value="${model.numberOfDoors}"/> ${model.errors.numberOfDoors}
	<br/>
	<input type="submit"/>
</form>
```

### Alternate Form Handling With JSR299 ###

If you are using JSR299 with Resteasy, some alternative approaches are possible.  This is one.	It's not necessarily better... use what you prefer.

```
@Path("/car")
public class CarSubmitResource
{
	@Form @RequestScoped @Named("model") Car car;
	
	@POST @Path("/{id}")
	public View saveCar()
	{
		if (!"black".equals(car.color))
			car.addError("color", "You can have any color you want as long as it is black");
		
		if (car.numberOfDoors > 5)
			car.addError("numberOfDoors", "I don't believe you");
		
		if (car.errors != null)
			return new View("/car_edit.jsp");
		else
		{
			// Save the car data
			throw new RedirectException("/car/" + car.id);
		}
	}
}
```

## Request/Response Context, Cookies, Authentication and more... ##

Pop over to [Resteasy](http://docs.jboss.org/resteasy/docs/2.2.1.GA/userguide/html_single/index.html) and see all the options.

# More #

  * [Frequently Asked Questions](FAQ.md)
  * [Installation/setup instructions](Installation.md)