# Simple Blog Plugin

This plugin provides a simple blog feature, allowing users to create posts
with a WYSIWYG editor and tag them. Users can append comments to posts and subscribe to the blog via RSS/Atom.

## Dependency

This plugin has the following dependencies:

* [http://www.grails.org/plugin/taggable](Taggable Plugin)
* [http://www.grails.org/plugin/commentable](Commentable Plugin)
* [http://grails.org/plugin/feeds](Feeds Plugin)

## Usage

### Installation

Add the following to `BuildConfig.groovy`

````groovy
runtime ":simple-blog:0.3.1"
````

After installation you will have a controller to access:

    http://localhost:8080/<your-application>/blog

### User Evaluator

In order to use this plugin you need to define a user evaluator in `grails-app/conf/Config.groovy`

    grails.blog.author.evaluator = { session.user }

If your user information is passed through request use

    grails.blog.author.evaluator = { request.user }

One other option is to define a simple string to be added:

    grails.blog.author.evaluator = { "defaultUser" }

The Commentable plugin uses the same mechanism for determining the current user. If the Spring Security plugin is
also installed the evaluator for both can be established with:

````groovy
def evaluator = {

    def principal = SecurityContextHolder.context.authentication.principal

    if (principal.hasProperty('id')) {

        def currentUserId = principal.id
        if (currentUserId) {
            User.get(currentUserId)
        }
    }
}

grails.blog.author.evaluator = evaluator
grails.commentable.poster.evaluator = evaluator
````

### URL Mappings

Useful available links:

* `/blog/$author?/$year?/$month?/$day?` - List entries (author and date range optional args)
* `/blog/tagged/$tag` - List entries with tag
* `/blog/feed/[rss|atom]` - Rss Feed

### Blog Taglib

#### &lt;blog:renderEntries&gt;

To render the last 3 entries:

    <blog:renderEntries number="3" />

The rendering of each entry is specified by the template `/blogEntry/recentEntries`. If the `number` attribute is
omitted, it defaults to 5.

#### &lt;blog:recentEntryLinks&gt;

To render links to the last 3 entries:

````
<ul>
    <blog:recentEntryLinks number="3">
        <li>
            <g:link controller="blog" action="showEntry" params="[title: it.title, author: it.author]">
                ${it.title}, <g:formatDate date="${it.publishedOn}"/>
            </g:link>
        </li>
    </blog:recentEntryLinks>
</ul>
````

The tag body is passed an instance of `BlogPostSummary` that may be referenced as `${it}` in order to render
each entry. If the `number` attribute is omitted, links to all published entries will be rendered.

#### &lt;blog:countEntries&gt;

Returns the number of published blog entries.

### Skinning

The plugin doesn't come with a layout or css defined, you will need to define these within your application by providing
styles for CSS classes like "blogControls", "blogQuickLinks", "blogTags", etc

## Plugin History

### Version 0.2.2
* Blog post URLs no longer change after entry is updated

### Version 0.2.1
* Bug fix: blank search query string was throwing a org.apache.lucene.queryParser.ParseException

### Version 0.2.0
* Using Grails 2.0.1
* Added ability to delete blog entries
* Added missing dependencies

### Version 0.1.4
* Using Grails 1.3
* Created Wiki documentation
* Improvements on urlMapping
* Created unit/integration tests
* Plugin now works more out of the box (straight forward installation and usage)

