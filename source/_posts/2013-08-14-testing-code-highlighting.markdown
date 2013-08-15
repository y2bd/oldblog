---
layout: post
title: "Testing Code Highlighting"
date: 2013-08-14 19:19
comments: true
categories: [programming, lua]
---

Let's see how syntax-highlighting works with this.

<!-- more -->

Here is some Lua code for a Love2D-based game:

``` lua
-- mapper.lua
-- loads the maps and draws them to the screen

-- requires
local tileLoader = require("libs.advtileloader.Loader")

-- constants
local TOTAL_LEVELS = 1

-- local vars
local function buildMapName(index)
  return "level_" .. index .. ".tmx"
end

-- module
local Mapper = 
{
  currentLevelIndex = 1,
  maps = {}
}

function Mapper.draw()
  love.graphics.setColor(255,255,255) -- apparently we need to do this to prevent tint
  Mapper.maps[Mapper.currentLevelIndex]:draw()
end

function Mapper.getCurrentMap()
  return maps[currentLevelIndex]
end

-- init and return
local function init()
  tileLoader.path = "maps/"

  for i=1, TOTAL_LEVELS do
    local map = tileLoader.load(buildMapName(i))

    map:setDrawRange(0,0,love.graphics.getWidth(), love.graphics.getHeight())
    map.drawObjects = false

    Mapper.maps[i] = map
  end

  return Mapper
end

return init()
```

And that's it.
