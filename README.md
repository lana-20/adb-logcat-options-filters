# ADB <code>logcat</code>: Options, Filters & More

I follow the practice of supporting my bug reports with screenshots and videos. But it’s even better and more helpful to my devs, when I attach the ADB [logcat](https://developer.android.com/studio/command-line/logcat) output to justify the unexpected behavior. In the logs the devs can find useful info that is printed by the system, or the additional info they themselves have added to logging. Often devs add their own logs, and those get printed in the output.

For example, I have the newly-built .apk file from my devs. I want to install the AUT on my running emulator and capture the logs from it.

	% adb devices
	List of devices attached
	emulator-5554	device
	 ~
	% adb install /Users/lanabegunova/Desktop/asf/mobile/myapp.apk
	Performing Streamed Install
	Success


Upon installation, the app attempts to launch and crashes on-start instead. I capture a quick screenshot for the bug report and move onto the retrieval of the crash summary:

	% adb shell screencap -p > ~/Desktop/app_crash.png
	 ~
	% adb -s emulator-5554 logcat

There’s a lot of output in the terminal, including historical data from a minute or a day ago, depending on the size of my logcat output buffer. It means that o device has a storage space of a certain size allocated to the logging. Normally it’s 256 KB, and it goes up to 1 MB with the Developer Options turned on, but the number may vary. New data keeps getting added to the log file. When it reaches its size limit, it starts pushing out the older info. Simply put, I have a log buffer with historical data, but that data is still relatively recent. Comparatively, a computer server log has a much longer life time than a mobile device log. Server might have information stored for months, depending on how they are configured.

I can adjust the logger buffer size under the device’s System > Developer options > Logger buffer sizes:
	
<img src="https://user-images.githubusercontent.com/70295997/219821682-5bcd6bfd-3772-4227-9ed5-44dad7c45f71.png" width=300>

Nevertheless, the default size and life time of mobile device logcat buffer is sufficient when I encounter a crash on a disconnected device and act promptly to connect it to my computer. Then, I run the <code>adb logcat -b crash</code> command. I definitely still have the device data from a few minutes ago. That data keeps getting pushed out with newer traces in the log file. So, it’s crucial to be fast on my toes in such cases.


I stop the logcat execution with CTRL+C. Then I filter out the log buffer for crashes. The <code>-b</code> option stands for buffer.

	% adb logcat -b crash
	--------- beginning of crash
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: FATAL EXCEPTION: main
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: Process: com.myapp.android, PID: 3753
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: java.lang.RuntimeException: Unable to create application com.myapp.android.App: com.getkeepsafe.relinker.MissingLibraryException: librealm-jni.so
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: 	at android.app.ActivityThread.handleBindApplication(ActivityThread.java:6764)
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: 	at android.app.ActivityThread.-$$Nest$mhandleBindApplication(Unknown Source:0)
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: 	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2133)
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: 	at android.os.Handler.dispatchMessage(Handler.java:106)
	02-14 16:20:34.060  3753  3753 E AndroidRuntime: 	at android.os.Looper.loopOnce(Looper.java:201)
	…
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: FATAL EXCEPTION: main
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: Process: com.myapp.android, PID: 10818
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: java.lang.RuntimeException: Unable to create application com.myapp.android.App: com.getkeepsafe.relinker.MissingLibraryException: librealm-jni.so
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: 	at android.app.ActivityThread.handleBindApplication(ActivityThread.java:6764)
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: 	at android.app.ActivityThread.-$$Nest$mhandleBindApplication(Unknown Source:0)
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: 	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2133)
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: 	at android.os.Handler.dispatchMessage(Handler.java:106)
	…
	02-17 13:07:45.047 10818 10818 E AndroidRuntime: 	... 9 more
	^C
	 ~

Above captioned is a partial log from my Pixel_6_Pro_API_33 emulator. Below is the output from my other emulator Pixel_6_API_33 with a Samsung Galaxy S23 skin:
	
<img width="2190" alt="Screenshot 2023-02-17 at 2 40 06 PM" src="https://user-images.githubusercontent.com/70295997/219816762-ea7ad37e-26c7-4c65-b9a4-7fdadc1ddf30.png">

I redirect the logcat output into the <code>logcat.txt</code> file. 
	
	% adb logcat -b crash > logcat.txt
	^C
	 ~
	% pwd
	/Users/lanabegunova
	 ~
	% ls
	...
	Desktop		Postman  	logcat.txt
	...
	 ~
	% open logcat.txt
	 ~

<img width="1313" alt="Screenshot 2023-02-17 at 4 21 59 PM" src="https://user-images.githubusercontent.com/70295997/219820379-63503d1c-de63-4d8f-b2bd-2c1b8477046b.png">
	
I attach this file along with other supporting documentation to my bug report. Then, I clear the buffer with the <code>adb logcat -c</code> command. New logs start accumulating in the logcat buffer from this point on, until I clear them again.
	
Optionally, I can use the <code>adb logcat -b crash | tee logfile.txt</code> to concurrently print the output into both the terminal and the file.
	
Command <code>adb logcat | grep ‘myapp’</code> filters the log for a particular app. Command <code>adb logcat | grep -E ‘(myapp1|myapp2)’</code> helps to grep/find more than one word/string.

### Important Information to Check for in Android Logs → <code>adb logcat</code> command, output, filtering
	
When examining a log file from an Android device, there are several pieces of information I look for to help identify the cause of an issue or crash, or understand the behavior of the system:
1. **Timestamps** - Help to understand the sequence of events that occurred and identify patterns or trends. Indicate when the message was generated.
2. **Log Levels** - Different log levels (such as ‘debug’, ‘info’, ‘warning’, and ‘error’) indicate the severity of of an issue or the importance of the message. Examining log messages with higher log levels (e.g., ‘warning’ and ‘error’) can be particularly helpful for identifying issues.
3. **Log Tags** - Help to filter the logs and focus on messages that are relevant to a particular component or subsystem.
4. **Error Messages** - Log message themselves can provide detailed info avow the state of the system or the actions that are being taken. Pay particular attention to log messages that contain the word ‘error’ or ‘exception’, as these may indicate issues or problems.
5. **Stack Traces** - Log messages that includes stack traces can be helpful for identifying the sequence of function calls that led to an issue or crash. Stack traces can be used to trace the issue back to a specific line of code or function.

Overall, examining log files carefully and looking for timestamps, log levels, error messages, and stack traces are an important part of troubleshooting and debugging issues with an Android device. I gain insight into the underlying cause of an issue and take appropriate actions to get it fixed.
	
<img width="1712" alt="Screenshot 2023-02-17 at 7 08 04 PM" src="https://user-images.githubusercontent.com/70295997/219828634-3cceca8d-1924-4418-b02d-33b7446d4cbd.png">
<img width="1712" alt="Screenshot 2023-02-17 at 9 42 50 PM" src="https://user-images.githubusercontent.com/70295997/219843408-3fbfe4ae-431b-4952-a576-15c2bd9de3ee.png">
<img width="1712" alt="Screenshot 2023-02-21 at 5 07 51 PM" src="https://user-images.githubusercontent.com/70295997/220494612-c8c8089a-c9bd-4fb7-9239-079e613588f6.png">
