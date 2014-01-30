---
layout: post
title: "Ordering voice commands on the \"Did You Know?\" page"
date: 2014-01-30 14:05
comments: true
categories: [windows phone, csharp, voice]
---

Just a small tip for a small problem I ran into.

If your WP app has voice commands defined, you'll get a "Did You Know?" page where the user can see examples of all of the
voice commands your app has. If you have a lot of voice commands available, it might get a bit messy.

{% img /images/posts/didyouknow.png 'There\'s no sense of order at all!' %}

What's more confusing is that the order of the commands on this page probably won't match up with the order
you defined them in your VCD file.

It turns out that they're displayed in alphabetical order, with the key being the `Name` attribute of your `Command` object, which means that you can adjust your names to order them how you want, perhaps by prepending them with letters or numbers.

In my particular case, that means that my commands would go from

{% codeblock lang:xml %}
<Command Name="SearchPokemon" />
<Command Name="SearchMoves" />
<Command Name="SearchTypes" />
<Command Name="SearchDualTypes" />
{% endcodeblock %}

to the following with numbered prefixes

{% codeblock lang:xml %}
<Command Name="00_SearchPokemon" />
<Command Name="01_SearchMoves" />
<Command Name="02_SearchTypes" />
<Command Name="03_SearchDualTypes" />
{% endcodeblock %}

If you plan on inserting more commands later on, you'll have to rename older ones to get the order you want, so I'd recommend using `string.Contains("base-name-of-command")` rather than equality checks in your code so that even if the prefix changes, your code will still work without having to do a million find-and-replaces.

