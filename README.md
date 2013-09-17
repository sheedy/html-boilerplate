# HTML Boilerplate

A starting point for web projects

# Reference

## HTML 

[Skeleton framework](https://github.com/dhg/Skeleton)

## Naming conventions

[http://thesassway.com/advanced/modular-css-naming-conventions](http://thesassway.com/advanced/modular-css-naming-conventions)

## File structure guide

[The SASS Way – How to structure a Sass project](http://thesassway.com/beginner/how-to-structure-a-sass-project)

## Sass Style Guide

Source: [CSS-Tricks - Sass Style Guide](http://css-tricks.com/sass-style-guide/)

- Be consistant with indentation
- Be consistant about where spaces before/after colons/braces go
- One selector per line, One rule per line
- List related properties together
- Have a plan for class name naming
- Don't use ID's #hotdrama
- etc

### List @extend(s) First

    .weather {
        @extends %module; 
        ...
    }

### List "Regular" Styles Next

    .weather {
        @extends %module; 
        background: LightCyan;
        ..
    }

### List @include(s) Next

    .weather {
        @extends %module; 
        background: LightCyan;
        @include transition(all 0.3s ease-out);
        ...
    }

### Nested Selectors Last

    .weather {
        @extends %module; 
        background: LightCyan;
        @include transition(all 0.3s ease);
        > h3 {
            border-bottom: 1px solid white;
            @include transform(rotate(90deg));
        }
    }

### All Vendor Prefixes Use @mixins

Vendor prefixes are a time-sensitive thing. As browsers update over time, the need for them will fall away. You can update mixins (or the libraries you use will update) to reflect those changes. Even if the mixin ends up being a one-liner, that's OK.

The only time I wouldn't @mixin a vendor prefix is when it's super proprietary, unlikely to be standardized as is, and so including other vendor prefixes or the non-prefixed version is likely to cause more harm that good. I'm thinking of things like ``-webkit-line-clamp`` or ``-ms-content-zoom-chaining`` or things like that that.

### Maximum Nesting: Three Levels Deep

    .weather {
        .cities {
            li {
                // no more!
            }
        }
    }

Chances are, if you're deeper than that, you're writing a crappy selector. Crappy in that it's too reliant on HTML structure (fragile), overly specific (too powerful), and not very reusable (not useful). It's also on the edge of being difficult to understand.

If you really want to use tag selectors because the class thing is getting too much for you, you may want to get pretty specific about it to avoid undesired cascading. And possibly even make use of extend so it has the benefit on the CSS side of re-usability.

    .weather
        > h3 {
            @extend %line-under;
        }
    }


### Maximum Nesting: 50 Lines

If a nested block of Sass is longer than that, there is a good chance it doesn't fit on one code editor screen, and starts becoming difficult to understand. The whole point of nesting is convenience and to assist in mental grouping. Don't use it if it hurts that.

#### Global and Section-Specific Sass Files Are just Table of Contents

In other words, no styles directly in them. Force yourself to keep all styles organized into component parts.

### List Vendor/Global Dependancies First, Then Author Dependancies, Then Patterns, Then Parts

So the "table of contents" things comes together like:

    /* Vendor Dependencies */
    @import "compass";

    /* Authored Dependencies */
    @import "global/colors";
    @import "global/mixins";

    /* Patterns */
    @import "global/tabs";
    @import "global/modals";

    /* Sections */
    @import "global/header";
    @import "global/footer";

The dependencies like Compass, colors, and mixins generate no compiled CSS at all, they are purely code dependancies. Listing the patterns next means that more specific "parts", which come after, have the power to override patterns without having a specificity war.

### Break Into As Many Small Files As Makes Sense

There is no penalty to splitting into many small files. Do it as much as feels good to the project. I know I find it easier to jump to small specific files and navigate through them than fewer/larger ones.

    ...

    @import "global/header/header/";
    @import "global/header/logo/";
    @import "global/header/dropdowns/";
    @import "global/header/nav/";
    @import "global/header/really-specific-thingy/";

I'd probably do this right in the global.scss, rather than have global @import a _header.scss file which has its own sub-imports. All that sub-importing could get out of hand.

Globbing might help if there starts to be too many to list.

### Partials are named _partial.scss

This is a common naming convention that indicates this file isn't meant to be compiled by itself. It likely has dependancies that would make it impossible to compile by itself. Personally I like dashes in the "actual" filename though, like _dropdown-menu.scss.

### Locally, Compile Expanded with Line Mapping

See here. This means dev tools can tell you exactly what file and on what line rules are coming from, even if it is an imported partial.

### In Deployment, Compile Compressed

Live websites should only ever have compressed CSS.

### Don't Even Commit .css Files

This might take some DevOps work, but it's pretty nice if .css files aren't even in your repository. The compilation happens during deployment. So the only thing you see in the repo are your nicely formatted hand authored Sass files. This makes the diffs useful as well. A diff is a comparison view of what changed provided by version control providers. The diff for a compressed CSS file is useless.

### Be Generous With Comments

It is rare to regret leaving a comment in code. It is either helpful or easily ignorable. Comments get stripped when compiling to compressed code, so there is no cost.

    .overlay {
        /* modals are 6000, saving messages are 5500, header is 2000 */
        z-index: 5000; 
    }
And speaking of comments, you may want to standardize on that. The // syntax in Sass is pretty nice especially for blocks of comments, so it is easier to comment/uncomment individual lines.

### Variablize All Common Numbers, and Numbers with Meaning

If you find yourself using a number other than 0 or 100% over and over, it likely deserves a variable. Since it likely has meaning and controls consistency, being able to tweak it enmasse may be useful.

If a number clearly has strong meaning, that's a use case for variablizing as well.

    $zHeader: 2000;
    $zOverlay: 5000;
    $zMessage: 5050;

    .header {
        z-index: $zHeader;
    }
    .overlay {
        z-index: $zOverlay;
    }
    .message {
        z-index: $zMessage;
    }

Those numbers are probably in a separate file @import-ed as a dependency. That way you can keep track of your whole z-index stack in one place.

### Variablize All Colors

Except perhaps white and black. Chances are a color isn't one-off, and even if you think it is, once it's in a variable you might see uses for it elsewhere. Variations on that color can often be handled by the Sass color functions like lighten() and darken() - which make updating colors easier (change in one place, whole color scheme updates).

### Nest and Name Your Media Queries

The ability to nest media queries in Sass means 1) you don't have to re-write the selector somewhere else which can be error prone 2) the rules that you are overriding are very clear and obvious, which is usually not the case when they are at the bottom of your CSS or in a different file.

    .sidebar {
        float: right;
        width: 33.33%;
        @include bp(mama-bear) {
            width: 25%;
        }
    }
More on this and the importance of naming them well.

### Shame Last

In your global stylesheet, @import a _shame.scss file last.

    @import "compass"

    ...

    @import "shame"
    
If you need to make a quick fix, you can do it here. Later when you have proper time, you can move the fix into the proper structure/organization. See more.