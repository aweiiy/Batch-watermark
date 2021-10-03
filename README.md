# Creating a shifting watermark overlay for multiple videos with FFmpeg

Create a dynamic, shifting watermark that moves around for multiple videos at once using FFmpeg.

This is similar to what TikTok uses and helps prevent the watermark from easily being blurred out or overlaid.

## The command
The full command with the breakdown below:

    for %%a in ("*.m*") do ffmpeg -i "%%a" -i watermark-image.png -filter_complex "[1]colorchannelmixer=aa=0.4,scale=iw*0.8:-1[a];[0][a]overlay=x='if(lt(mod(t\,16)\,8)\,W-w-W*10/100\,W*10/100)':y='if(lt(mod(t+4\,16)\,8)\,H-h-H*5/100\,H*5/100)'" -codec:a copy "newfiles\%%~na.mp4"
    pause

(Put the videos in the same folder as the watermark.bat file and run it.)
## Breaking it down
Call FFmpeg and define the input videos and the watermark image to use:

    for %%a in ("*.m*") do ffmpeg -i "%%a" -i watermark-image.png
Set the watermark image opacity to 0.4 and scale down 80%

    -filter_complex "[1]colorchannelmixer=aa=0.4,scale=iw*0.8:-1[a];
The watermark movement, every 2 seconds move corner to corner (anti-clockwise):

    [0][a]overlay=x='if(lt(mod(t\,8)\,4)\,W-w-W*10/100\,W*10/100)':y='if(lt(mod(t+2\,8)\,4)\,H-h-H*10/100\,H*10/100)'"
This is a position change of 2 seconds because it has 8 seconds for a full rotation cycle (corner to corner). 4 corners means 8/4 = 2. So 2 seconds per position change.

This is the example for 6 second changes:

    overlay=x='if(lt(mod(t\,24)\,12)\,W-w-W*10/100\,W*10/100)':y='if(lt(mod(t+6\,24)\,12)\,H-h-H*5/100\,H*5/100)'
24/4 = 6 and 12/2 = 6.

This part defines the position from the edge on the width and height axis which is the width * 10 / 100 or height * 5 / 100.

    W-w-W*10/100\,W*10/100 .... H-h-H*5/100\,H*5/100
Finally saving the watermarked video:

    -codec:a copy "newfiles\%%~na.mp4"
