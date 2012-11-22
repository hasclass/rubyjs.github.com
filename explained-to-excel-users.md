---
layout: rubyjsmain
title: RubyJS
canonical: http://rubyjs.org
---

Imagine you start working for a new company. There they do not use Excel (=Ruby) but a similar application from a different vendor, called Jaxcel (Javascript). You are annoyed to find out that there's significantly less formulas and that they behave differently than their counterparts.

Nevertheless you start working on a new spreadsheet and need to round a number to two decimals. In Excel you would have written `=ROUND(A1, 2)` and expect `2.34` however in Jaxcel you get `2`. Because Jaxcels formula always rounds to a full number. After a quick google you find on a forum that you can instead write `=ROUND(A1 * 100)/100`. _Am I paid to write rounding formulas?_

Because this is such a common task you want to reuse it in other spreadsheets. With Jaxcel you can put formulas into _templates_ and reuse them elsewhere. So you share your new `=CUSTOM_ROUND()` formula. What follows is an internal debate on how the formula should be named (maybe `=SMART_ROUND(A1, 2)`), on various edge cases and whether it follows a certain Jaxcel paradigm.

Soon you see that your version of the formula differs from excel (ie `=ROUND(123.34, -1)` should return 120). You really want this behaviour, but changing it would also means making sure every document using the old rounding formula still work. So you just create a new one `=ROUND_WITH_NEGATIVE`.

RubyJS in this story would be a _template_ with all Excel formulas (written in Jaxcel formulas). So you could write `=RubyJS.ROUND(A1, 2)` and get the exact same behaviour of Ruby while still being able to use the original Jaxcel formulas. While the benefit for ruby developers is obvious. But what about Javascript developers that dont know Ruby?

Javascript has about 10 basic formulas for dealing with text, e.g. slicing, replacing parts. Ruby over 70 thereof. RubyJS brings all these convenience methods to Javascript. They are written in Javascript so work on all browsers, mobile phones, etc. Besides





