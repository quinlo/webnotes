# Semantic HTML

Many web sites contain HTML code like: `<div id="nav">` `<div class="header">` `<div id="footer">`
to indicate navigation, header, and footer.

HTML5 offers new semantic elements to define different parts of a web page:

Element | Description
-- | --
section | A `<section>` is a thematic grouping of content, typically with a heading. A home page could normally be split into sections for introduction, content, and contact information.
article | The `<article>` element specifies independent, self-contained content. An article should make sense on its own, and it should be possible to read it independently from the rest of the web site. Examples of where it can be used include a Forum post, a blog post, a newspaper article.
header | The `<header>` element specifies a header for a document or section.  It should be used as a container for introductory content.  You can have several `<header>` elements in one document.
footer | The `<footer>` element specifies a footer for a ducment or section.  A `<footer>` element should contain information about its containing element... typically the author, copyright information, links to terms of use, contact information, etc.  You can have several `<footer>` elements in one document.
nav | The `<nav>` element defines a set of navigation links
aside | The `<aside>` element defines some content aside from the content it is placed in (like a sidebar). The    `<aside>` content should be related to the surrounding content.
figure and figcaption | The purpose of a figure caption is to add a visual explanation to an image.  In HTML5, an image and caption can be grouped together in a `<figure>` element: 
details | defines additional details that the user can view or hide
main | Specifies the main content of a document
mark | Defines marked/highlighted text
summary | Defines a visible heading for a details element
time | Defines a date/time