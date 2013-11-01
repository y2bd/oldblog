---
layout: post
title: "Fixing the Long List Selector's Scrollbar Spacing Issue"
date: 2013-10-31 23:53
comments: true
categories: 
---

Windows Phone's Long List Selector has an annoying problem. Its scrollbar takes up actual horizontal space on the layout grid, pushing your list items to the left. Now this normally isn't a problem; you can just offset your list items accordingly. The problem though is that if your list ever has too few elements such that scrolling is not necessary, the scrollbar's `Visibility` will be set to `Collapsed` and no longer take up any horizontal space, meaning that your offsets will cause your items to stick *too far* off of the right edge.

Here is a simple Style that you can add to a Resources file (or just a `phone:PhoneApplicationPage.Resources` block) that will solve this problem:

``` xml

<Style x:Key="LLSFloatingScrollbarStyle"
               TargetType="phone:LongListSelector">
    <Setter Property="Background"
            Value="Transparent" />
    <Setter Property="Foreground"
            Value="{StaticResource PhoneForegroundBrush}" />
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="phone:LongListSelector">
                <Grid Background="{TemplateBinding Background}"
                      d:DesignWidth="480"
                      d:DesignHeight="800">
                    <VisualStateManager.VisualStateGroups>
                        <VisualStateGroup x:Name="ScrollStates">
                            <VisualStateGroup.Transitions>
                                <VisualTransition GeneratedDuration="00:00:00.5" />
                            </VisualStateGroup.Transitions>
                            <VisualState x:Name="Scrolling">
                                <Storyboard>
                                    <DoubleAnimation Duration="0"
                                                     To="1"
                                                     Storyboard.TargetProperty="Opacity"
                                                     Storyboard.TargetName="VerticalScrollBar" />
                                </Storyboard>
                            </VisualState>
                            <VisualState x:Name="NotScrolling" />
                        </VisualStateGroup>
                    </VisualStateManager.VisualStateGroups>
                    <Grid Margin="{TemplateBinding Padding}">

                        <ViewportControl x:Name="ViewportControl"
                                         HorizontalContentAlignment="Stretch"
                                         VerticalAlignment="Top" />

                        <ScrollBar x:Name="VerticalScrollBar"
                                   Margin="4,0,-12,0"
                                   Opacity="0"
                                   HorizontalAlignment="Right"
                                   Orientation="Vertical" />
                    </Grid>
                </Grid>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>

<!-- then on your long list selector... -->

<phone:LongListSelector Style="{StaticResource LLSFloatingScrollbarStyle}" />

```

What this will do is cause the scrollbar to float on top of the long list selector instead of taking up horizontal space, which means that it will never push the list's content to the left. A note though: take heed of the `Margin` attribute of the `Scrollbar` tag. You may need to adjust this to get the scrollbar to go to where you want. I personally like my scrollbars flush against the right side of the screen.