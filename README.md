# Anonymous_Pipe
Anonymous Pipe Demo Codes
# Author 
Charles Chai
# Key words
`Interprocess Communication` `Anonymous Pipe` `Windows Program` `C++`
# Abstract
Demo cods for interprocess communication by anonymous pipe on windows platform. <br>
C++ project which can be debugged by visual stdio 2015 is provided.
# Detial Description
* A interprocess communication demo is provided. Anonymous pipe is applied by the project. 
* Anonymous pipe only can be used by parent process and its child process. In my project, both parent and child has the same application name. The main difference between the parent and child is that the child process has arguments.
* Parent process create the 'Anonymous Pipe` and child process. After that, parent process write a string to the pipe.
* The only task of the child process is read string form the pipe and output it.
# Codes
```cpp
#include "stdafx.h"
#include <windows.h>
#include <iostream>

using namespace std;

int main(int argc, char* argv[])
{
	if (argc > 1){
		/* * * Child Process * * */
		cout << "Child Process" << endl;
		/* * Read data from the anonymous pipe * */
		CHAR szBuffer[16]{ 0 };
		ReadFile(GetStdHandle(STD_INPUT_HANDLE), szBuffer, sizeof(szBuffer), nullptr, nullptr);
		// Output the data
		cout << szBuffer << endl;
	}
	else{
		/* * * Parent Process * * */
		cout << "Parent Process" << endl;
		HANDLE hPipeW = NULL, hPipeR = NULL; //Pipe handle for read and write
		// Fill the security attributes  struct
		SECURITY_ATTRIBUTES sa{ 0 };
		sa.nLength = sizeof(sa);
		sa.bInheritHandle = TRUE; 
		// Create the anonymous pipe
		if (!CreatePipe(&hPipeR, &hPipeW, &sa, 0)){
			cout << "Create pipe failed! Processs return!" << endl;
			return 1;
		}
		// Initial the process information struct
		PROCESS_INFORMATION pi{ 0 };
		// Fill the process startup information struct
		STARTUPINFOA si{ 0 };
		si.cb = sizeof(si);
		si.hStdInput = hPipeR;			
		si.dwFlags = STARTF_USESTDHANDLES;
		// Prepare the command line for child process
		char param[1024];
		sprintf_s(param, "%s %s", argv[0], "test"); 
		// Create child process
		if (!CreateProcessA(nullptr, param, nullptr, nullptr, TRUE, CREATE_NEW_CONSOLE, nullptr, nullptr, &si, &pi)){
			CloseHandle(hPipeR);
			CloseHandle(hPipeW);
			cout << "Create child process failed! Process return!" << endl;
			return 2;
		}
		// Write data to the pipe
		WriteFile(hPipeW, "ezhchai", 7, nullptr, nullptr);
	}
	system("pause");
	return 0;
}
```
