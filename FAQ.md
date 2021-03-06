

### What is the best way to learn Htmleasy? ###

Pop over to [Using Htmleasy](UsingHtmleasy.md) and start exploring the patterns. For those familiar with JAX-RS (Jersey, Resteasy, CFX), then you'll feel comfortable straight away. For those new to REST and JAX-RS, also take some time to learn JAX-RS in general.  There are many good tutorials on the internet.

You'll also find a pre-built Google App Engine Java (GAE/J) project called Htmleasy Playground. Playing with the code is a great way to learn.  Grab it from the Downloads section and have a play!

### When should I use Htmleasy over XYZ framework? ###

Most of the popular Java web frameworks such as [Wicket](http://wicket.apache.org/), [Tapestry](http://tapestry.apache.org/), JSF have a long pedigree and have grown up in a Web 1.0 world. They work hard to abstract away aspects of the HTTP request/response model and manage all manner of state automatically.  Htmleasy is a step closer to the HTTP world.  The focus is on REST - HTML verbs, clean URLs and AJAX/JSON. Htmleasy is very light on the server-side and this makes it ideal for Web 2.0 projects where more of the heavy lifting will be done via AJAX on the client-side.

Htmleasy is a perfect server-side framework for projects leveraging the growing number of client-side Javascript options such as JQuery and its ecosystem, Backbone.js, Sproutcore, etc. REST is natural, HTML is native, and it doesn't carry much server-side baggage.

If you've come from a world like Rails, Sinatra, Django, or MVC3, then you'll feel much more at home with Htmleasy than a heavy Java web framework.

### It seems to be missing the basics. How do I say add a paginated table of results, or validate a form with field-level feedback? ###

That's correct. Isn't it great! You're now free to do it in the way you feel is most appropriate; AJAX scrolling... A Smart Client table... JQuery form validation... etc.

### What is the difference between Htmleasy and Spring MVC? ###

Both are push MVC environments. Htmleasy uses standard JAX-RS annotations to define paths and map methods to requests. This allows you to leverage the same REST API across all areas of your application whether it be JSON REST or plain HTML requests or pages.  Spring of course provides a lot more services out of the box.

### What features are planned for the future? ###

Not a lot because the goal of Htmleasy is to remain very simple and elegant.  If you have any ideas that fit within this ethos please make them know on the discussion forum.  A few currently under consideration include.

  * ~~An example "Playground" project (Google App Engine) demonstrating core pattens/usage.~~
  * ~~Maven/Ivy POM~~
  * Plugins and use-pattens for popular Template languages. (DONE [Soy Templates - Silken](https://github.com/codedance/silken))
  * Maybe views could be resolver automatically based on path annotations or class names (i.e. convention over configuration).

### If I use Htmleasy, am I putting all my eggs in one basket? ###

This is an important consideration when selecting any framework. Fortunately 95% of what Htmleasy provides is Resteasy - a popular standards compliant JAX-RS implementation developed and supported by the JBoss team.  Htmleasy is simply a thin shim over Resteasy to facilitate elegant HTML view-model.  It's very light and in fact a developer could read and understand all the code in 30 minutes. You're not coupling your code with a heavy weight framework.

Htmleasy, like JAX-RS is also often used alongside other frameworks.  This is possible because it's lightweight and does not impose other aspects onto your code such as a DI environment, session requirements, etc.

### Can I use (Guice|Spring|Hibernate|Mongo|JSR299|Cool-thing-of-the-month) with Htmleasy? ###

Yes. Htmleasy simply focuses on REST and MVC so you're free to use what ever you like in other areas your code.

### I've heard that Htmleasy is now part of Resteasy? ###

[The View part of Htmleasy](http://resteasy.svn.sourceforge.net/viewvc/resteasy/trunk/jaxrs/providers/resteasy-html/src/main/java/org/jboss/resteasy/plugins/providers/html/) can now be seen in the Resteasy codebase (plugin providers).  This is great news.  We would love to see all pattens and concepts in Htmleasy become part of Resteasy as they prove to be useful and mature within this project.

### What dependencies does Htmleasy have? ###

The only dependancy is Resteasy itself.

### Is there a maven repository for the project? ###

Yes! You can read about it here: UsingMaven

### What view/template technologies are recommended? ###

JSP is a strong option as it's "always there". [Silken](https://github.com/codedance/silken) (Google Closure Templates) is another good option as it's built specifically to work well with Htmleasy.

JSP has a bad rep and this is mainly a result of abuse of it's power!  If you restrict yourself to using JPS as a simple template system in Htmleasy's push-MVC style, then it's hard to shoot yourself.  ie.  don't put logic in your page that calls out to other code. Only use the data passed in by the model.

The fact that Htmleasy is decoupled from view systems is one of its strengths.