---
layout: post
title: "Fixing alignment issues with DataTemplateSelector"
date: 2013-08-16 19:24
comments: true
categories: [windows phone, csharp, programming, quill]
---

I'm currently working on a journaling application for WP8. While designing the page for browsing through past entries, I found that I needed to be able to switch between data templates for the `LongListSelector` housed in the page. Some journal entries would have just text, some would have only a header photo, and some would have both, and I needed to display the entries differently for all three cases.

<!-- more -->

I found [this article](http://www.geekchamp.com/articles/implementing-windows-phone-7-datatemplateselector-and-customdatatemplateselector) describing how to recreate WPF's `DataTemplateSelector` class for Windows Phone. It worked fine--after remaking the base abstract class and extending it for my specific case, all you need to do is specify the different data templates in your xaml:

{% codeblock lang:xml %}
<DataTemplate x:Key="EntryListItemTemplate">
  <sel:EntryItemTemplateSelector>
    <sel:EntryItemTemplateSelector.Both>
      <DataTemplate> <!-- content here --> </DataTemplate>
    </sel:EntryItemTemplateSelector.Both>

    <sel:EntryItemTemplateSelector.OnlyImage>
      <DataTemplate> <!-- content here --> </DataTemplate>
    </sel:EntryItemTemplateSelector.OnlyImage>

    <sel:EntryItemTemplateSelector.OnlyText>
      <DataTemplate> <!-- content here --> </DataTemplate>
    </sel:EntryItemTemplateSelector.OnlyText>
  </sel:EntryItemTemplateSelector>
</DataTemplate>
{% endcodeblock %}

A bit verbose, but it got the job done thankfully. The LongListSelector would now switch between templates as required.

There was a bit of a problem though.

{% img /images/posts/centered.png 'All of the elements are centered and small!' %}

Mordecai has the right idea.

All of the elements in the LongListSelector were no longer listening to the standard alignment and sizing rules, instead being only as wide as their content and staying put in the center.

I tried adjusting margins, hardcoding widths, `HorizontalAlignment=Stretch` *everything*, and either it wouldn't work, or it would make things even worse.

After a lot of vague googling (what exactly would you call this problem? I honestly had no idea), I arrove at the correct solution. My `EntryItemTemplateSelector`, inheriting from `ContentControl`, has a new property named `HorizontalControlAlignment`, which [sets the alignment of the control's *content* rather than the control itself](http://msdn.microsoft.com/en-us/library/system.windows.controls.control.horizontalcontentalignment.aspx). So all you have to do then is

{% codeblock lang:xml %}
<sel:EntryItemTemplateSelector HorizontalContentAlignment="Stretch">
{% endcodeblock %}

which gets you

{% img /images/posts/aligned.png 'Now every element is taking up the correct amount of space!' %}

Nothing satisfies Mordecai though.