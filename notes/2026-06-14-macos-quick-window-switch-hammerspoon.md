# macOS Quick Window Switching with Hammerspoon

Hammerspoon provides the window-switching layer for this setup. The config lives in `~/.hammerspoon/init.lua`, and the app is available from the official site: https://www.hammerspoon.org/.

The pattern uses `hyper + letter` bindings to manage an app in three states:

1. If the app is not running, launch it.
2. If the app is running but not frontmost, focus its main window.
3. If the app is already frontmost, cycle to the next visible window.

The helper also restores minimized windows before focusing them, which avoids the macOS behavior where an app is selected but its window stays hidden.

If `hyper` is not already mapped on the keyboard, it can be assigned to Caps Lock with Karabiner-Elements. See [macOS Caps Lock to Hyper with Karabiner-Elements](macos-caps-lock-to-hyper.md) for the setup.

```lua
local hyper = {"ctrl", "alt", "shift", "cmd"}

local function visibleWindows(app)
  local windows = {}
  if not app then
    return windows
  end

  for _, win in ipairs(app:allWindows()) do
    if win and win:isStandard() then
      table.insert(windows, win)
    end
  end

  table.sort(windows, function(a, b)
    return a:id() < b:id()
  end)

  return windows
end

local function focusWindow(win)
  if not win then
    return
  end
  if win:isMinimized() then
    win:unminimize()
  end
  win:raise()
  win:focus()
end

local function firstRunningApp(bundleIDs)
  for _, bundleID in ipairs(bundleIDs) do
    local app = hs.application.get(bundleID)
    if app then
      return app, bundleID
    end
  end
  return nil, nil
end

local function launchApp(bundleIDs, appName)
  local launched = false
  for _, bundleID in ipairs(bundleIDs) do
    launched = hs.application.launchOrFocusByBundleID(bundleID)
    if launched then
      return
    end
  end
  if appName then
    hs.application.launchOrFocus(appName)
  end
end

local function bindCycleOrLaunch(key, bundleIDs, appName)
  hs.hotkey.bind(hyper, key, function()
    local app, activeBundleID = firstRunningApp(bundleIDs)

    if not app then
      launchApp(bundleIDs, appName)
      return
    end

    local windows = visibleWindows(app)
    if #windows == 0 then
      launchApp(bundleIDs, appName)
      return
    end

    local front = hs.window.frontmostWindow()
    local frontApp = front and front:application()

    if frontApp and frontApp:bundleID() == activeBundleID then
      if #windows == 1 then
        focusWindow(windows[1])
        return
      end

      local currentId = front:id()
      local idx = 1
      for i, win in ipairs(windows) do
        if win:id() == currentId then
          idx = i
          break
        end
      end

      local nextIdx = (idx % #windows) + 1
      focusWindow(windows[nextIdx])
      return
    end

    focusWindow(windows[1])
  end)
end

bindCycleOrLaunch("t", {"com.mitchellh.ghostty"}, "Ghostty")
bindCycleOrLaunch("p", {"com.jetbrains.pycharm", "com.jetbrains.pycharm.ce"}, "PyCharm")
bindCycleOrLaunch("s", {"com.tinyspeck.slackmacgap", "com.slack.Slack"}, "Slack")
bindCycleOrLaunch("b", {"com.automattic.beeper.desktop"}, "Beeper")
bindCycleOrLaunch("f", {"com.apple.finder"}, "Finder")
bindCycleOrLaunch("x", {"org.mozilla.firefox"}, "Firefox")
```

Usage summary:

- `hyper + letter` launches the app if needed
- `hyper + letter` focuses the app if it is already running
- repeated presses cycle through that app’s visible windows
- minimized windows are restored before focus

