<template>
    <div id="phone">
      <div class="icons" id="main"></div>
      <div class="icons" id="cover"></div>
    </div>
  </template>
  
  <script>
  export default {
    mounted() {
      function randomInt(low, high) {
        return Math.floor(Math.random() * (high - low)) + low;
      }
  
      // Helper to create: <div class="ball"><i class="material-icons">icon_name</i></div>
      var icons = "cake star thumb_up favorite new_releases comment cast filter_hdr".split(" ");
      function createBall(opt_icon, opt_color) {
        var ball = document.createElement("div");
        ball.className = "ball";
        var icon = opt_icon || icons[randomInt(0, icons.length)];
        ball.innerHTML = '<i class="material-icons">' + icon + "</i>";
        ball.style.background =
          opt_color || "hsl(" + randomInt(200, 360) + ", 100%, 50%)";
        return ball;
      }
  
      // Create icons on the home screen.
      var total = randomInt(7, 12);
      for (var i = 0; i < total; ++i) {
        document.getElementById("main").appendChild(createBall());
      }
  
      // Create icons in the toolbar/bottom bar.
      document.getElementById("cover").appendChild(createBall("phone", "green"));
      for (var i = 0; i < 2; ++i) {
        document.getElementById("cover").appendChild(createBall());
      }
  
      // Controls the duration and sensitivity of the drag.
      var screenHeight = 480;
      var duration = 1200;
  
      // Creates an array of players that represent a phone toolbar moving upwards. Moves the toolbar as
      // well as the icons you see on the home screen.
      function createPlayers(from, callbackWhenDone) {
        var timing = { duration: duration, fill: "forwards", easing: "ease-in-out" };
        var firstPlayer = document
          .getElementById("cover")
          .animate(
            [
              { transform: "none" },
              { transform: "translateY(-80%)" }
            ],
            timing
          );
  
        // When the player finishes, it will either be going forwards (toolbar to top), or backwards
        // (when it is returned to the base).
        firstPlayer.onfinish = function () {
          if (firstPlayer.currentTime <= 0) {
            // The player is going backwards, cancel all players, return to non-animated state.
            players.forEach(function (player) {
              player.cancel();
            });
            callbackWhenDone();
          }
        };
  
        // Animate each ball on the main screen, moving away from the original position.
        timing.easing = "ease-in";
        var players = [].slice
          .call(document.querySelectorAll("#main .ball"))
          .map(function (ball) {
            var rect = ball.getBoundingClientRect();
            var position = {
              x: rect.left + rect.width / 2,
              y: rect.top + rect.height / 2
            };
  
            var x = (position.x - from.x) * (from.y - position.y) / 100;
            var y = -(from.y - position.y);
            var deg = x < 0 ? -25 : +25;
            return ball.animate(
              [
                { transform: "none", opacity: 1 },
                {
                  transform:
                    "translate(" + x + "px, " + y + "px) rotate(" + deg + "deg)",
                  opacity: 0
                }
              ],
              timing
            );
          });
  
        // Record all players and pause them (since we initially just scrubbing via gesture).
        players.push(firstPlayer);
        players.forEach(function (player) {
          player.pause();
        });
        return players;
      }
  
      (function () {
        var players = null;
        var previousAt = undefined;
  
        function getPosition(ev) {
          var target = ev.touches ? ev.touches[0] : ev;
          if (target.screenY !== undefined) {
            return { x: target.pageX, y: target.pageY };
          }
          throw new Error("couldn't find touch on event: " + ev);
        }
        function onDown(ev) {
          var position = getPosition(ev);
          previousAt = position.y;
  
          if (players) {
            if (players[0].playState == "pending") {
              return;
            } // not ready yet
            if (players[0].currentTime > duration) {
              return;
            } // don't flip if over end
  
            // We're already playing, so 'grab' the players, and flip them around (in case they keep
            // playing, say this is just a click without a move).
            players.forEach(function (player) {
              player.playbackRate *= -1;
              player.pause();
            });
          } else {
            // Create new players! Pass a callback, when the animation is finally done, clear stuff.
            players = createPlayers(position, function () {
              players = null;
              previousAt = undefined;
            });
          }
        }
        function onMove(ev) {
          if (previousAt === undefined) {
            return;
          }
  
          // Determine whether the user has moved their pointer.
          var y = getPosition(ev).y;
          var delta = previousAt - y;
          if (!delta) {
            return;
          } // moved left/right, ignore
          previousAt = y;
  
          // Update time based on the movement delta.
          var previousTime = players[0].currentTime;
          var currentTime =
            previousTime + delta / screenHeight * duration;
          var direction = delta > 0 ? +1 : -1;
  
          // Cheat, get the animation to always play to completion (so onfinish/etc runs).
          if (currentTime >= duration) {
            currentTime = duration - 1;
          } else if (currentTime <= 0) {
            currentTime = 1;
          }
  
          // Explicitly set the new time, scrubbed perfectly with the user's pointer.
          players.forEach(function (player) {
            player.currentTime = currentTime;
            player.playbackRate = direction;
          });
        }
        function onUp() {
          if (previousAt === undefined) {
            return;
          }
          previousAt = undefined;
  
          // The user has let go, just let the players play.
          players.forEach(function (player) {
            player.play();
          });
        }
  
        // Handle mouse events.
        document.getElementById("cover").addEventListener("mousedown", onDown);
        document.body.addEventListener("mousemove", onMove);
        document.body.addEventListener("mouseup", onUp);
  
        // Handle touch events.
        document.getElementById("cover").addEventListener("touchstart", onDown);
        document.body.addEventListener("touchmove", onMove);
        document.body.addEventListener("touchend", onUp);
      })();
    }
  };
  </script>
  
  <style scoped>
  #phone {
    width: 300px;
    height: 480px;
    background-color: rgb(74, 216, 180);
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: auto;
    justify-content: center;
  }
  
  .icons {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-content: center;
    width: 200px;
    height: 200px;
    background-color: rgb(255, 255, 255);
    border-radius: 50%;
    padding: 10px;

    gap: 10px;
  }
  
  .ball {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background-color: rgb(31, 23, 23);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .material-icons {
    font-size: 24px;
    color: rgb(31, 23, 23);
  }
  </style>
  