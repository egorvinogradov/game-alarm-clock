# game-alarm-clock
Alarm clock for Mac that is being disabled only after 10 minutes playing online game

### How it works:
1. Create and intall helper extension for Google Chrome.
2. Set computer to wake up at the right time.
3. In terminal, run sound playback.
4. It terminal, run local server in the background.
5. In terminal, open website with game in Google Chrome.
6. When playing the game, the helper extensions counts time (to 10 minutes) and mutes sound. When user stops playing, the sound becomes loud again.
6. When time is over, the extension sends request to background server to disable the alarm clock.
7. Background server runs command to terminate sound playback.

### Detailed algorithm
1. `pmset repeat wakeorpoweron MTWRFSU 07:45:00` — set mac to wake up at specific time
2. `afplay -v 4 /Users/egor/Dropbox/public/misc/sound_2.mp3` — plays sound, the sound should be 10 mins long
3. Node.js server in the background:
  1. listens to `is_playing_game` — makes sound quiet (`osascript -e 'set volume 0'`)
  2. listens to `is_not_playing_game` — makes sound loud  (`osascript -e 'set volume 4'`)
  3. listens to `finished_game` — terminates sound (``kill `ps aux | awk '/afplay/ { print $2 }'` ``) and kills the server
4. `/usr/bin/open -a "/Applications/Google Chrome.app" 'http://hexgl.bkcore.com/play/'` — opens the game in browser
5. Chrome extension:
  1. On keypress event sends `is_playing_game`, starts/continues timer
  2. After no keypress events withing 15 seconds sends `is_not_playing_game`, pauses the timer
  3. After 10 minutes passed, sends `finished_game`, shows "Alarm clock disabled" message in browser

### Misc
Different HTML5 games: http://tutorialzine.com/2015/02/30-amazing-games-made-only-with-html5/
