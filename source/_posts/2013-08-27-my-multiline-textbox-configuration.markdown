---
layout: post
title: "My multiline Textbox configuration"
date: 2013-08-27 20:04
comments: true
categories: [windows phone, programming]
---

I'm still working on Quill, but I figured this construct was useful enough for me to save in its
purest form, before I uglify it with orientation hacks.

This is an implementation of a properly-behaving multiline TextBox for Windows Phone. The original implementation
was sourced from [KlingDigital](http://klingdigital.net/2013/06/scrollviewer-and-multiline-textbox-windowsphone).
I just made extremely minor modifications, mostly refactoring to make it easier to modify (adding an input scope, 
supporting landscape, etc).

Here is the XAML:

``` xml
<phone:PhoneApplicationPage
    x:Class="Autocomplete.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:phone="clr-namespace:Microsoft.Phone.Controls;assembly=Microsoft.Phone"
    xmlns:shell="clr-namespace:Microsoft.Phone.Shell;assembly=Microsoft.Phone"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    FontFamily="{StaticResource PhoneFontFamilyNormal}"
    FontSize="{StaticResource PhoneFontSizeNormal}"
    Foreground="{StaticResource PhoneForegroundBrush}"
    SupportedOrientations="Portrait" Orientation="Portrait"
    shell:SystemTray.IsVisible="True"
    Loaded="PhoneApplicationPage_Loaded">

    <!--LayoutRoot is the root grid where all page content is placed-->
    <Grid x:Name="LayoutRoot" Background="Transparent">
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <ScrollViewer x:Name="Scroller"
                      Background="#1ba1e2"
                      Grid.Row="0">
            <StackPanel VerticalAlignment="Top">
                <TextBox x:Name="ScrollerInput"
                         TextWrapping="Wrap"
                         AcceptsReturn="True" 
                         TextChanged="ScrollerInput_TextChanged"
                         GotFocus="ScrollerInput_GotFocus"
                         LostFocus="ScrollerInput_LostFocus"
                         Tap="ScrollerInput_Tap"/>
            </StackPanel>
        </ScrollViewer>

        <Grid x:Name="KeyboardPlaceholder"
              Grid.Row="1"
              Visibility="Collapsed" />
    </Grid>    

</phone:PhoneApplicationPage>
```

and here is the codebehind:

``` csharp
using Microsoft.Phone.Controls;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Media;

namespace Autocomplete
{
  public partial class MainPage : PhoneApplicationPage
  {
    private const int PortraitHeight = 336;

    private double _inputHeight = 0;
    private double _tapHeight = 0;

    // Constructor
    public MainPage()
    {
      InitializeComponent();
    }

    private void SizePlaceholder()
    {
      KeyboardPlaceholder.Height = PortraitHeight;
    }

    private void ForceLayout()
    {
      App.RootFrame.RenderTransform = new CompositeTransform();

      LayoutRoot.UpdateLayout();
    }

    private void PhoneApplicationPage_Loaded(object sender, RoutedEventArgs e)
    {

    }

    private void ScrollerInput_TextChanged(object sender, TextChangedEventArgs e)
    {
      // updates seem to be late if we don't invoke them with the dispatcher
      Dispatcher.BeginInvoke(() =>
      {
        double currentInputHeight = ScrollerInput.ActualHeight;

        if (currentInputHeight > _inputHeight)
        {
          // scroll up by the difference between the current box size and the old box size
          Scroller.ScrollToVerticalOffset(Scroller.VerticalOffset + currentInputHeight - _inputHeight);
        }

        _inputHeight = currentInputHeight;
      });
    }

    private void ScrollerInput_GotFocus(object sender, RoutedEventArgs e)
    {
      KeyboardPlaceholder.Visibility = Visibility.Visible;
      SizePlaceholder();

      ForceLayout();

      Scroller.ScrollToVerticalOffset(_tapHeight);
    }

    private void ScrollerInput_LostFocus(object sender, RoutedEventArgs e)
    {
      KeyboardPlaceholder.Visibility = Visibility.Collapsed;
    }

    private void ScrollerInput_Tap(object sender, System.Windows.Input.GestureEventArgs e)
    {
      _tapHeight = e.GetPosition(ScrollerInput).Y - 120;
    }
  }
}
```

Hopefully this template will prove useful in the future.