---
layout: post
title: "Rendering BitmapImages with the Nokia Imaging SDK"
date: 2013-09-15 14:04
comments: true
categories: [windows phone, nokia, sofa]
---

So I was working on integrating the Nokia Imaging SDK for my journal app, [Sofa](http://blog.y2bd.me/sofa/), for the [Nokia Create contest](http://developer.nokia.com/create/), essentially letting the user crop and enhance their photos before attaching them to an entry. 

I found myself using the SDK in my ViewModel and wondering how I was supposed to get the rendered image into my View. The SDK itself provides methods for rendering to JPEG, raw Bitmaps, WriteableBitmaps, and even Image controls. The Image controls option (`renderToImageAsync()`) sounded like the way to go, but I realized that in the VM I don't have access to the Image control in my view. Instead, I have access to a BitmapImage that binds to the Source property of the Image control. So how do you get a BitmapImage out of the editing session?

Well after some searching I found that you can coax a BitmapImage out of a WriteableBitmap. Here's an extension method that lets you do exactly that:

``` csharp
public static async Task<BitmapImage> RenderToBitmapImageAsync(this IEditingSession session, int width, int height, OutputOption outputOption = OutputOption.PreserveAspectRatio)
{
  var bi = new BitmapImage();

  var wbp = new WriteableBitmap(width, height);
  await session.RenderToWriteableBitmapAsync(wbp, outputOption);

  using (var ms = new MemoryStream())
  {
    wbp.SaveJpeg(ms, width, height, 0, 100);
    bi.SetSource(ms);
  }

  // not super necessary
  wbp = null;

  return bi;
}
```

Just put this into a static class (perhaps an Extensions.cs?) and then call

``` csharp
myBindedImage = await _myEditSession.RenderToBitmapImageAsync(someWidth, someHeight, outputOptionIfYouWant);
```

Note that [there may be memory issues with WriteableBitmaps](https://www.google.com/search?q=writeablebitmap+memory+leak), so be wary of calling this often, especially with high widths and heights. You may need to force garbage collection after a few calls.

Because of that, I'm definitely not 100% keen on this method. It definitely works (wait for Sofa v1.1 to prove that) but I'll be on the lookout for less memory-intensive options.
