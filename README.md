# updateVirusDefinition
Script ini memeriksa version VDF dan mengupdate

```
Dim urlVersion, urlVDF
Dim fileVersion, fileVDF

urlVersion = "http://localhost/version.txt"
urlVDF = "http://localhost/putty.exe"

fileVersion = "d:\version.txt"
fileVDF = "d:\putty.exe"

function readFile(fname)
	dim fso: Set fso = CreateObject("Scripting.FileSystemObject")
	
	if fso.FileExists(fname) Then
		dim file: Set file = fso.OpenTextFile(fname,1)
		readFile = file.ReadAll()	
		file.Close
		Set file = Nothing		
		
	else
		readFile=0
	end if

end function

function curlFile(url, fname)
	MsgBox url
	dim result: result = false
	dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
	dim bStrm: Set bStrm = createobject("Adodb.Stream")
	xHttp.Open "GET", url, False
	xHttp.Send

	if (xHttp.Status = 200) Then 'site isn't 200
		with bStrm
			.type = 1 '//binary
			.open
			.write xHttp.responseBody
			.savetofile fname, 2 '//overwrite
		end with
		result = true
	end if
	
	curlFile = result
	
end function


function curlString(url)
	dim result: result = false
	dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
	dim bStrm: Set bStrm = createobject("Adodb.Stream")
	xHttp.Open "GET", url, False
	xHttp.Send

	if (xHttp.Status = 200) Then 'site isn't 200
		curlString = xHttp.responseText
	else
		curlString = "error"
	end if
end function

function runFile(fname)
	Set WshShell = WScript.CreateObject("WScript.Shell")
	statusCode = WshShell.Run (fname, 1, true)
end function

dim lastVersion, currVersion
lastVersion = readFile(fileVersion)
currVersion = curlString(urlVersion)
if (currVersion > lastVersion) then
	call curlFile(urlVersion, fileVersion)
	call curlFile(urlVDF, fileVDF)
	call runFile(fileVDF)
else
	MsgBox "Nothing to Update"
end if
```
