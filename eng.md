# Magic Numbers in CSS

Despite the super fun sounding name, magic numbers are a bad thing. It is an
[old school programming term][1] for "unnamed numerical constant". As in, just
some number plunked into the code that is probably vital to things working
correctly but are very difficult for anyone not intimately familiar with the
code to understand what it is for. CSS is loaded with unnamed numerical
constants, but they are usually paired with properties and in the context of a
selector so there is little mystery. There are magic numbers in CSS though, and
they are still bad.

Magic numbers in CSS refer to values which "work" under some circumstances but
are frail and prone to break when those circumstances change. **They are usually
always related to fonts** in some way or another. They are created by an author
who likely only tested in their own browser under ideal conditions. Let's take a
look at some examples so we all know what they are and hopefully can avoid them
in the future.

Look at this simple set of tabs:

![Tabs1][Simple set of tabs]

Each of the tabs is set to `width: 100px;`. In this example `100px` is our
"magic number." There are any number of things that can go wrong with this.
Simply adding another tab with longer text demonstrates that:

![Tabs2][Another tab with a longer name demonstrates the disadvantages of fixed width]

A bit awkward and likely undesirable. We could prevent the wrapping with `white-
space: nowrap;` but that's possibly worse:

![Tabs3][The effect of white-space: nowrap]

Our tabs would be less prone to breakage if we use min-width instead:

![Tabs4][The look of the tabs with min-width set]

Or perhaps no width at all:

![Tabs5][The look of the tabs with no width set]

If you were dead-set on having all the tabs the same size, you could do
`overflow: hidden;` and `text-overflow: ellipsis;` perhaps:

![Tabs6][The effect of overflow: hidden; and text-overflow: ellipsis;]

In that case, you'd probably want a `title` attribute so there is some way to
reveal the entire tab name. You might think this could be solved from the
Content Management System side by only allowing tab names to be a certain number
of characters long. But what about users who increase font-size from their end
for accessibility reasons? This fixed sizing might be hurting them.

In a recent post [Line-On-Sides Headers][2], I used a line-height value that was
a magic number. Let's say you used the technique around text with a fancy @font-
face font. Let's say that font doesn't load or the user overrides it or the page
is being viewed in a browser that don't support @font-face. The fallback font
will load, and that fallback font might have drastically different sizing than
the custom font. The lines on the outside of the fallback font are now awkwardly
placed, not centered like we wanted. Magic number fail.

![Fonts][Comparison of the Line-On-Sides technique applied to different fonts]

[This particular example][3] is a bit contrived, but I'm sure you've all seen 
custom fonts that have super crazy x-heights and stuff.

Let's say you have a bunch of boxes with different amounts of content in them.
You want to arrange them into a grid, so you float them left. Kind of a mess:

![Blocks1][Blocks with different height when floated left]

Well hey if they were all the same height this wouldn't be a problem!

![Blocks2][Blocks with fixed height when floated left]

That is, if the user viewing the site has the exact same font-size setting as
you. But users can change that.

![Chrome][Font settings in Chrome]

And now a big sad trombone:

![Blocks3][The text that doesn't fit in the containing block with fixed height overlaps other blocks]

[min-height][4] instead would prevent the overlapping weirdness, but then the 
boxes are of different size and the float problem happens again. I'm not going 
to go too far into solutions because this is so abstract already, but perhaps 
you could use scrolling on the boxes, or use some JavaScript to adjust sizes, or 
user some other kind of layout.

Harry Roberts points out a classic example of a magic number in his article 
[Code smells in CSS][5].

    .site-nav > li:hover .dropdown{
      position: absolute;
      top: 37px;
      left: 0;
    }

This would be for a CSS powered dropdown menu. The menu is hidden off-screen
until the parent list item is hovered, then the dropdown moves into view. It
should be placed at the bottom of the parent menu item. For the developer that
wrote this code, in their current browser, that menu item was 37px tall. I'm
sure you can imagine that isn't always true. 37px is a magic number. Harry
suggests `top: 100%` instead meaning "all the way from the top," which is far
less prone to breakage.

In the article [Fighting the Space Between Inline-Block Elements][6], -4px is a
number quoted for margin that can close those gaps. That is definitely a magic
number. 4px just happens to the width of a space in a good number of fonts at
the default 16px `font-size`.

Change that `font-family` to something like Monaco? Broken. Change the font-size
to anything larger or smaller? Broken.

![Breakage][The technique of applying -4px as margin value to fight the space between inline-block elements fails when font-family or font size is changed]

See [that article][7] for other fixes.

## Definition Confusion

Because we're trying to say "don't use these," it is important we define the
term properly as it relates to CSS. I've seen a number of threads in the past
where not everyone is on the same page. [(1)][8] [(2)][9] [(3)][10].

Here's some examples:

**CSS**

    -webkit-transform: translateZ(0);

Magic number? Nah. Just a little hack we used to use for performance.

**CSS**

    .parent {
      padding: 22px;
    }
    .child {
      bottom: 22px;
      left: 22px;
    }

Magic number? Nah. It's a weird number but it's not magic. The child element is
being positioned to the bottom left corner of an element, sans-padding. Because
those numbers match up, it makes sense what is going on. If the were all
different, that would mean there is likely little tweaky bits going on related
to `font-size` and it would be a magic number.

**CSS**

    top: 37px;

Let's say this is a magic number, like from the dropdown menu example above. Can
was solve it with Sass?

**SCSS**

    $topDistance: 37px;

    .dropdown {
      top: $topDistance;
    }

It's no longer an "unnamed" numerical constant right, because we named it? It's
still a magic number in CSS. It's just as fragile as it was before.

**CSS**

    letter-spacing: -.05em;

Magic number? Nah. If it was in pixels it probably would be, because the pixel
value remains constant so the effect of it changes depending on the current
font-size. Huge font-size, barely any change, small font-size, big change. The
fact that this is in a relative unit makes it less fragile.

## AND IN CONCLUSION

Man I hate this section of blog posts but it's nice to have a little dumping
ground for little things that didn't work themselves in elsewhere.

* The WordPress CSS Coding Standards [says][11] you shouldn't use them. Do any 
others?

* If you're using icon fonts, remember they are fonts, so they change size. You 
can't "reserve space" for them somewhere in pixel values, as the font size can 
change but the pixel value won't.

* I'd like to keep this blog post updated with good examples of magic number 
fails, so if you have any classics, comment below.

* What if you are trying to center an image and text. [vertical-align][12] does 
pretty good, but I find it's often 1px off or so so I `postion: relative; top: 
1px;` it. Is that a magic number? Not sure.

[1]: http://en.wikipedia.org/wiki/Magic_number_%28programming%29#Unnamed_numerical_constants
[2]: http://css-tricks.com/line-on-sides-headers/
[3]: http://codepen.io/chriscoyier/pen/KdIxo
[4]: http://css-tricks.com/almanac/properties/m/min-height/
[5]: http://csswizardry.com/2012/11/code-smells-in-css/
[6]: http://css-tricks.com/fighting-the-space-between-inline-block-elements/
[7]: http://css-tricks.com/fighting-the-space-between-inline-block-elements/
[8]: http://stackoverflow.com/questions/12015888/refactor-css-to-eliminate-magic-numbers
[9]: http://css-tricks.com/forums/discussion/22450/what-does-a-magic-number-in-css-really-mean
[10]: https://twitter.com/chriscoyier/status/299983970899197952
[11]: http://make.wordpress.org/core/handbook/coding-standards/css/#best-practices
[12]: http://css-tricks.com/what-is-vertical-align/

[Simple set of tabs]: img/tabs-1.png?raw=true&amp;repo=magic-numbers-in-css
[Another tab with a longer name demonstrates the disadvantages of fixed width]: img/tabs-2.png?raw=true&amp;repo=magic-numbers-in-css
[The effect of white-space: nowrap]: img/tabs-3.png?raw=true&amp;repo=magic-numbers-in-css
[The look of the tabs with min-width set]: img/tabs-4.png?raw=true&amp;repo=magic-numbers-in-css
[The look of the tabs with no width set]: img/tabs-5.png?raw=true&amp;repo=magic-numbers-in-css
[The effect of overflow: hidden; and text-overflow: ellipsis;]: img/tabs-6.png?raw=true&amp;repo=magic-numbers-in-css
[Comparison of the Line-On-Sides technique applied to different fonts]: img/custom-font-magic-number.png?raw=true&amp;repo=magic-numbers-in-css
[Blocks with different height when floated left]: img/grid-mess.png?raw=true&amp;repo=magic-numbers-in-css
[Blocks with fixed height when floated left]: img/Screen-Shot-2013-04-22-at-8.19.55-AM.png?raw=true&amp;repo=magic-numbers-in-css
[Font settings in Chrome]: img/chrome-font-size.png?raw=true&amp;repo=magic-numbers-in-css
[The text that doesn't fit in the containing block with fixed height overlaps other blocks]: img/Screen-Shot-2013-04-22-at-8.22.34-AM.png?raw=true&amp;repo=magic-numbers-in-css
[The technique of applying -4px as margin value to fight the space between inline-block elements fails when font-family or font size is changed]: img/inline-block.png?raw=true&amp;repo=magic-numbers-in-css