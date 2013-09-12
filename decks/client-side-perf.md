---
title: Building For Speed
layout: springone13
---
# <i class="icon-bolt"></i> Building for Speed: Tips and Tricks for Client-Side Performance

Jeremy Grelle, 2013  
Twitter + Github: `@jeremyg484`

## The Spring Web Dude
<table>
    <tr>
        <td><img src="images/SpringWebDude.jpg"></img>
        <td>
        	<ul>
        		<li>Jeremy Grelle</li>
        		<li>Abiding for Spring Since 2007</li>
        		<li>Spring Web Flow, Spring Faces, Spring JS, Spring Flex, Spring MVC, cujoJS, etc...</li>
        	</ul>
    	</td>
    </tr>
</table>

## Web App Performance

![It's Complicated](images/LEBOWSKI-Complicated.jpg)

>This is a very complicated case. You know, a lotta ins, lotta outs, lotta what-have-you's.

## Why does it matter?

![Delay vs User Reaction](images/DelayAndUserReaction.png)

http://www.useit.com/papers/responsetime.html

http://www.igvita.com/posa/high-performance-networking-in-google-chrome/

## Breaking the 1000ms Time to Glass Mobile Barrier

<iframe width="560" height="315" src="//www.youtube.com/embed/Il4swGfTOSM" frameborder="0" allowfullscreen></iframe>

http://youtu.be/Il4swGfTOSM

## Performance == $$$

>> "Studies from Google, Microsoft, and Amazon all show that web performance translates directly to dollars and cents—e.g., a 2,000 ms delay on Bing search pages decreased per-user revenue by 4.3%!"

> High Performance Browser Networking

## Performance == $$$

>> "An Aberdeen study of over 160 organizations determined that an extra one-second delay in page load times led to 7% loss in conversions, 11% fewer page views, and a 16% decrease in customer satisfaction!" 

> High Performance Browser Networking

##

![Nihlists](images/LEBOWSKI-Nihlists.jpg)

>Ve vant ze money, Lebowski

## Speed is a Feature

Faster web apps lead to:

* Increased page views
* Better user engagement
* Higher conversion rates

## Necessary Ingredients

![Half and Half](images/LEBOWSKI-HalfAndHalf.jpg)

## Know Your Browser

Browser processing pipeline: HTML, CSS, and JavaScript

![Rendering Pipeline](images/RenderingPipeline.png)

## Chasing Waterfalls

![Waterfall](images/Waterfall.png)

## Know Your Dev Tools

Chrome, Safari, Firefox, IE

* All have useful developer tools (yes, even IE)
* All have a resource loading waterfall

## Performance Analysis

YSlow

* Open Source Browser Extension (+more)
* Analyzes and grades page performance
* Suggests improvements

http://yslow.org

## Performance Analysis

Google PageSpeed

* Similar analysis to YSlow
* Browser extension & web service options
* Plugins for Apache and Nginx to auto-apply performance improvements

https://developers.google.com/speed

## Follow the rules!

![Rules](images/LEBOWSKI-Rules.jpg)

## Avoid angry users

![This Is What Happens](images/LEBOWSKI-ThisIsWhatHappens.jpg)

> Do you see what happens, Larry?

## Tips and Tricks

![The Dude Abides](images/LEBOWSKI-Abides1.jpg)

> Have you heard about Spring?

## Tip

Maximize asset caching

## Tip

Don't break save-refresh

## Trick

Use Spring MVC's resource support to:

* Serve resources with a far-future Expires header
* Set a correct Last-Modified header
* Respond correctly to conditional GET requests

## Trick

```java
@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {

  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/resources/**").
    	addResourceLocations("/public-resources/").
    	setCachePeriod(31556926);
  }

}
```

## Tip

Use fingerprinted URLs for smooth application updates

* Generate content-based fingerprints dynamically at dev time
* Write fingerprinted file names to disk for production

## Trick

Use upcoming Spring MVC resource support enhancements

```java
public interface ResourceResolver {

	public Resource resolve(HttpServletRequest request, 
							String path, List<Resource> locations, 
							ResourceResolverChain chain);
	
	public String resolveUrl(String resourcePath, 
							 List<Resource> locations,
							 ResourceResolverChain chain);
}
```

## Trick

```java
@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {

  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/resources/**").
    	addResourceLocations("/public-resources/").
    	addResourceResolver(new FingerprintingResourceResolver()).
    	setCachePeriod(31556926);
  }

}
```

## Trick

Use a ServletFilter to ensure resource URLs get re-written

* `/resources/foo.css` written as `/resources/foo-82b6bc440914c01297b99b4bca641a5d.css`

## Tip

Serve resources with GZip compression to clients that support it

* Compress on-the-fly at dev time
* Write gzipped versions of files to disk for production

Reduces payload sent over-the-wire

## Trick

Use a `ResourceTransformer` at dev time

```java
public interface ResourceTransformer {

	public Resource transform(Resource original) throws IOException;
	
	public boolean handles(HttpServletRequest request, 
						   Resource original);
	
}
```

## Trick

```java
@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {

  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/resources/**").
    	addResourceLocations("/public-resources/").
    	addResourceResolver(new FingerprintingResourceResolver()).
    	addResourceResolver(new GzipResourceResolver()).
    	addResourceTransformer(new GzipResourceTransformer()).
    	setCachePeriod(31556926);
  }

}
```

## Tip

Use a CDN to edge cache resources

* Minimizes network hops from browser to server

## Trick

Use Amazon Cloudfront or similar to

* Serve static files with minimum overhead
* Forward requests for "missing" files to app server
* Preserve cache headers, gzip, etc

## Tip

Minimize round-trip times

* Combine JS files
* Combine CSS files

## Trick

Use simple Sprockets-style directives in source files, process with a combining `ResourceTransformer`

`main.js`

```javascript
//= require /resources/home
//= require /resources/moovinator
//= require /resources/slider
//= require /resources/phonebox
```

## Trick

Use a `ResourceTransformer` to replace CSS @import statements

`main.css`

```css
@import /resources/homepage.css
@import /resources/tools.css
@import /resources/projects.css
```

## Tip

Minify JS and CSS resources

## Trick

Use `ResourceTransformer` and/or build integration to apply:

* Google Closure compiler for JS resources
* YUI Compressor for CSS resources

## Tip

Only load resources that are actually needed

* Load the minimum needed to render the page

## Trick

Use AMD to modularize and load only necessary JS and CSS resources

Use an AMD build step to combine resources for production

## Tip

Manage external resource dependencies as part of your build

## Trick

Use WebJars to:

* Specify external JS and CSS as Maven (or Ivy, Gradle, SBT, etc) dependencies

Use Spring to:

* Serve resources directly from the classpath _AT DEVELOPMENT TIME_

Use a build step to: 

* Extract resources and minify + combine for production

## Trick

```xml
<dependency>
   	<groupId>org.webjars</groupId>
	<artifactId>bootstrap</artifactId>
	<version>2.1.1</version>
</dependency>
```

## Trick

```java
@EnableWebMvc
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {

  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/resources/**").
      addResourceLocations("/", 
      "classpath:/META-INF/resources/webjars").
      setCachePeriod(31556926);
  }

}
```

## Tip

Don't use bootstrap.min.css

* Potentially forcing users to load much more than needed
* Especially bad for mobile

## Trick

Use the bootstrap .less files

* Reference only the .less files you need
* Use the LESS compiler to process and combine
* Can be integrated with a `ResourceTransformer` to preserve save-refresh

## Summary

* Keep your users attentive and happy
* Measure, measure, measure
* Use Spring to integrate into your workflow

##

![I Take Comfort In That](images/LEBOWSKI-TakeComfort.jpg)

> Spring abides. I don't know about you but I take comfort in that. It's good knowin' it's out there. Spring. Takin' 'er easy for all us sinners.

##

![Let's Go Bowling](images/LEBOWSKI-LetsGoBowling.jpg)

> F@$* it, Dude. Let's go bowling.

## References

* High Performance Browser Networking - http://chimera.labs.oreilly.com/books/1230000000545
