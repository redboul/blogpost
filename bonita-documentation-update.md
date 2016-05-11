# Bonita documentation site

### tl;dr

Looking for a new magical documentation site/framework/tool/potatoe that meets every requirement is quite difficult.  
We have therefore... created a new one!

## Current state

We currently use Drupal to host all of our contents (customer, partnership, community portals) and for the documentation contents too.  
It allows us to use several plugins to manage features like search, authentication, taxonomy and so on.  
It is a great tool but here in the R&D team, we wanted to use a simpler way to manage the documentation content and specifically not writing complex html.  
Furthermore, Drupal stores all its content on database so the only simple way to edit the content is to use the drupal management portal which can be cumbersome.  

It also provides a lot of useless feature we do not actually need or that weighted our day to day use :

 * documentation site view
   * Full page reload on each click
   * social share
   * i18n
   * single page PDF export
   * erratic version navigation
 * documentation site edition
   * no simple way to preview content
   * global layout management
   * home page topics 
   * Summary section
   * loads of unknown properties (meta-tags, url path settings, publishing information,...)

Maybe, there has been someday where we had the knowledge to lighten up the administration portal to ease the content management process but not anymore.  
Knowing the drawbacks of our the current site. We wanted to get rid of it.

## Document like we code

We are using git and github to manage our source code and we felt it natural to have the documentation content being hosted the same way.  
At first, we even thought of having the documentation being directly written in the source code and have it extracted with tools like doxygen or dgeni or simple javadoc. But given the amount of project/repositories we have, we gave that idea up...

On a second thought, we wanted to create the documentation in markdown format managed via github and have the documentation site generated from it.  
We looked through differents frameworks/tools that would do the work for us: 

 * Sphinx/ReadTheDoc
 * Gitbook
 * Jekyll

## Requirements

We listed what was slowing us down with the current docummentation tool but we needed to admit that some of the Drupal features are very useful and we need to confront these essential feature to our documentation candidates.  

By priority, here they are :

 * Must have
   * Markdown or similar format (asciidoc, reStructuredText,...)
   * Full text search
   * Corporate layout
   * Inject beacons
   * Multiple versions
   * Taxonomy management/navigation
   * Hosted on our side
 * Nice to have
   * Use our authentication system

## Global work to do

On the overall view of every tool, we knew that we needed to some custom templating so we needed to ramp up on the tool we would chose.  
We also needed to ramp up on the integration of this tool in a push to production environment and the lesser technology we add to our production environment, the better.

### Sphinx/ReadTheDoc

![Add some pictures]()

Sphinx is a great tool that generates a whole documentation site from documents in the _reStructuredText_ format.  
It creates a nice taxonomy from the different namespaces used in the documentation content.  
Moreover, integrated with ReadTheDoc, it offers a great connectivity with github that allows to create a site directly with the administration portal.

![Readthedoc portal picture]()

However, we were afraid that using a big tool like that would lead to the same issues we are facing with Drupal or add some other...
It also suffers from a lack of a performant full text search and it would need to do some custom development to plug it to a better one.

Another major problem of it is python based and hosted via Django and we do not want to spend time to ramp up on these technologies.  

### Jekyll



It is quite clear that the requirements are quite high and 

## Yadt (Yet another documentation tool)
