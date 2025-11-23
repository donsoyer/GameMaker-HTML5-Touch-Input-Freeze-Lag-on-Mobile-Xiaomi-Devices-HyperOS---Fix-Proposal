# HTML5-Touch-Input-Freeze-Lag-on-Mobile-Xiaomi-Devices-HyperOS---Fix-Proposal
HTML5 Touch Input Freeze/Lag on Mobile (Xiaomi) Devices/ HyperOS - Fix Proposal

Issue Description:
On some mobile devices f.e. Xiaomi devices running MIUI/HyperOS (e.g., Mi Note 10 Pro),  HTML5 games exported from GameMaker suffer from a severe "freeze" (main thread lock) for approximately 300ms immediately upon touching the screen.
Crucial Findings - Failed Attempts:
I want to emphasize that standard solutions provided in the documentation DO NOT work for this specific issue. I have tested the following without success:
1.	device_mouse_dbclick_enable(false) - Using this GML function has NO EFFECT on the lag.
2.	Standard CSS touch-action: none - This is ignored by the specific browser implementation on these devices unless accompanied by JS event modification.
3.	Phone Settings - Disabling System Gestures, Edge/Palm Rejection, or Game Turbo features in Android settings does not fix the issue.
The Root Cause & Fix:
The issue is caused by the browser defaulting to passive: true listeners, waiting for the OS to check for gestures.
The ONLY working solution is to manually inject a JavaScript event listener with the { passive: false } option explicitly set.

Code:
// This MUST be added to the index.html template to fix it
window.addEventListener('load', function() { 
  var canvas = document.getElementById("canvas"); 
  if (canvas) { 
    var preventDefaultScroll = function(e) { 
      e.preventDefault(); 
    }; 
    // { passive: false } is required to override device’s default browser behavior canvas.addEventListener('touchstart', preventDefaultScroll, { passive: false }); canvas.addEventListener('touchmove', preventDefaultScroll, { passive: false });
  	canvas.addEventListener('touchend', preventDefaultScroll, { passive: false });
  }
});

Attachments & Implementation Guide:
I have attached two modified template files that resolve the issue:
1.	index_fix1.html – Contains the critical JavaScript injection (forcing { passive: false }) that eliminates the input lag. This is the minimal required fix.
2.	index_fix2.html – Contains the JavaScript fix from file #1 PLUS additional CSS optimizations for the Canvas element (removing tap-highlight-color, blocking user-select, etc.) to further improve visual responsiveness on Android.
How to apply the fix globally:
To fix this issue for all future projects, rename the chosen file (I recommend index_fix2.html) to index.html and replace the default file in the GameMaker Runtimes directory.
For the current latest version, the path is:
C:\ProgramData\GameMakerStudio2\Cache\runtimes\runtime-2024.14.1.253\html5
(Note: The runtime version number 2024.14.1.253 may vary depending on updates; the file should be placed in the folder with the highest version number).

Version Compatibility:
lease note that these modified HTML template files have been tested and verified to work with the latest GameMaker Runtime version: 2024.14.1.253.
Universal Compatibility:
However, the JavaScript fix code provided above is universal. It can be manually injected into the index.html template of any GameMaker Runtime version to resolve the issue, regardless of the specific Runtime build.
 
