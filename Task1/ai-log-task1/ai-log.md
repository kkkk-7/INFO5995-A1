# AI Interaction Log – Task 1

During the APK decompilation analysis, an AI tool was used to assist with understanding key concepts and selecting appropriate decompilation tools.

First, we asked the AI about the structure of Android APK files and why decompilation is necessary for security auditing. The AI explained the main components of an APK and how decompilation allows analysts to inspect the internal logic of an application.

We also asked the AI about commonly used APK decompilation tools. The AI mentioned several tools, including **jadx**, **apktool**, and **dex2jar**. JADX can convert Dalvik bytecode into readable Java code and provides a graphical interface for browsing the source code. Apktool is mainly used for analyzing application resources and configuration files, while dex2jar is often used together with Java decompilers.

Based on this comparison, we chose **JADX** to decompile the provided APK file and successfully examined the application's source code and login logic.

## Example Prompts

Examples of prompts used during the interaction with the AI tool include:

- "What is an APK file and what components does it contain?"
- "Why is APK decompilation necessary for security auditing?"
- "What tools can be used to decompile Android APK files?"
- "How can JADX help inspect the login logic of a decompiled Android application?"