# ADB <code>logcat</code>: Options, Filters & More

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
<img width="1712" alt="Screenshot 2023-02-18 at 10 48 54 PM" src="https://user-images.githubusercontent.com/70295997/219933488-fc7380c1-9160-409e-bad7-7c25b148d7c6.png">
