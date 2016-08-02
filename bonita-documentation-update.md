# Bonita documentation site

### tl;dr

At Bonitasoft, developpers are writing the product documentation. But current documentation tool is based on Drupal a CMS plaftorm.  
However, this tool is not meant to be used by developpers.  
Developpers usually like to have dirty hands and in the engine and it is not what Drupals offers.

We, therefore, decided to take the hand on the documentation tool.  
We wanted it to have the features we use daily to produce code:
* Pull Request
* Review by pair
* Continuous Integration
* Versioning
* Hosted on GitHub
* Possible to open to contributors (internal or external)

At first, we looked for a new documentation site/framework/tool/potatoe that would allow us to do all of this.
We failed (maybe we are quite demanding...)  
We have decided to create one!

## Motivation

We currently use Drupal to host all of our contents (customer, partnership, community portals) and for the documentation contents too.  
It allows us to use several plugins to manage features like search, authentication, taxonomy and so on.  
It is a great tool but here in the R&D team, we wanted to use a simpler way to manage the documentation content and specifically not writing complex html.  
Furthermore, Drupal stores all its content in a database so the only simple way to edit the content is to use the drupal management portal which can be cumbersome.  

It also provides a lot of useless features we do not actually need or that weighted our day to day usage :

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
Knowing the drawbacks of our current site. We wanted to get rid of it.

### Requirements

We listed what was slowing us down with the current docummentation tool but we needed to admit that some of the Drupal features are very useful and we need to confront these essential features to our documentation candidates.  

By priority, here they are :

 * Must have
   * Markdown or similar format (asciidoc, reStructuredText,...)
   * Full text search
   * Corporate layout (the closer to the current one, the better)
   * Inject beacons
   * Multiple versions
   * Taxonomy management/navigation
   * Hosted on our site
 * Nice to have
   * Use our authentication system

### Global work to do

On the overall view of every tool, we knew that we needed to do some custom templating so we needed to ramp up on the tool we would choose.  
We also needed to ramp up on the integration of this tool in a push to production environment and the lesser technology we add to our production environment, the better.


### Document like we code

We are using git and github to manage our source code and we felt it natural to have the documentation content being hosted the same way.  
At first, we even thought of having the documentation being directly written in the source code and have it extracted with tools like doxygen or dgeni or simple javadoc. But given the amount of project/repositories we have, we gave that idea up...

On a second thought, we wanted to create the documentation in markdown format managed via github and have the documentation site generated from it.  
We looked through differents frameworks/tools that would do the work for us: 

 * Sphinx/ReadTheDoc
 * Gitbook
 * Jekyll

## Evaluated Tools

### Sphinx/ReadTheDoc

![Add some pictures]()

Sphinx is a great tool that generates a whole documentation site from documents in the _reStructuredText_ format.  
It creates a nice taxonomy from the different namespaces used in the documentation content.  
Moreover, integrated with ReadTheDoc, it offers a great connectivity with github that allows to create a site directly with the administration portal.

![Readthedoc portal picture]()

However, we were afraid that using a tool like that would lead to the same issues we are facing with Drupal or add some other...
It also suffers from a lack of a performant full text search and it would need to do some custom development to plug it to a better one.

Another major problem of it is that it is python based and hosted via Django and we do not have peolpe with these skills in our team and we do not want to spend time to ramp up on these technologies because it will not be reused in some other major R&D project yet.  

### Jekyll

When you search for tool generating documentation from markdown, you hit **Jekyll**.  
It is a simple website that converts the markdown read from the file system and renders it in html. It is used by github to render pages so it can be reliable. It has the advantage of being quite light and has a lot of out of the box feature we can add via [plugins](https://jekyllrb.com/docs/plugins/).  
You can even customize your look and feel via templates

But, if we go back our requirement, there is a _table of content_ plugin that would allow us to generate content from H1 and H2 tags but that would not be expandable/collapsable.  
And there is no embedded search tool... A plugin provide a search capability but no demo is available and this plugin does not have any test so, not so reliable.
We would also need to manage a new environment we do not master because Jekyll is written in Ruby.

### Gitbook

Gitbook allows to produce documentation site written from markdown and even host it. It can manage your documentation content directly from github using git commands.  
It produces a complete website with taxonomy on a right panel from a global summary.md file that contains markdown formatted list of accessible pages.  
A search feature is available directly when the site has been generated. It supports custom templating too.  
It is available via npm and we are quite used to manage javascript/node environment.

But, even if it has met a lot of our expectation, we did not chose it.

The only real drawback is that the provided search feature is very limited compared to what we need...  
It is a simple frontend search from a reversed index send to the browser. Its limit is a few megabytes and our documentation content have reach this limit.  
We could have raised it (it is said to be possible but we did not succeed to make it with the version we tested) but that would mean sending approximately 10 megabytes of data every time the documentation site is displayed. This was not acceptable.

The solution would have to add some out-of-the-box plugin that would send request to a search tool, retrieve results, display them on a dedicated page and be able to clic on a result to access the full content inside Gitbook.  

Knowing that the custom templating would still be needed, it would have required us to ramp up on the Gitbook templating engine. Therefore, we have decided to create our own tool...

## Our own tool : Let's get some work then

We decided to create our own static site, generating html static from the github repository using Single page application in order to only load the appropriate content for each clic (just like Gitbook does).  
As we are very familiar with AngularJS, we decided to have it as our site framework.

### Performance

One of the key feature for us as developper is to have pages served as fast as possible.  
So for AngularJS to load the generated html, we used some simple **ng-include** tags pointing to the page the user wants to load.

The drawback of this is that the navigation inside a page is difficult because anchor could not be used easily as is (AngularJS uses # part URL to manage its navigation).  
We added to an automatic table of content generator on each page display to ease navigation but currently, we can no more navigate from a page to another page anchor.

#### Versionning

The major feature that was not in Gitbook or Jekyll was the versionning.  
We need to be able to switch from a version to another directly from a menu in the documentation site.  
So, we add one that allowed us to navigate back to the former documentation site to access the version of the documentation that are not in the new version.

![menu image]()

The actual consequence of the version change is in the path where the **ng-include** link points to. Because the different version are just different folder on the site, it is simple to switch to another version.

#### Taxonomy

The Gitbook feature to generate a navigation tree from a summary.md file is quite good and we decided to do the same creating a json file from the markdown.  
The build navigation tree has ability to expand/collapse each section.

![navigation gif]()

#### Templating

Since, we started from scratch and wanted to have the corporate look and feel, we decided to base our development from the former documentation site and iterate look and feel over it.

#### Search tool

Because we need a performant search, we need to use production ready tool to do that. We chose Solr to do this because it allows to index content from static html files (no indexation of markdown files). 
So, after setting the Solr server up, we created a service that submit search request to Solr and display the result matching the page result names.

