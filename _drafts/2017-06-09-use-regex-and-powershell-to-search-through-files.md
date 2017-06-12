---
layout: post
title: Search through multiple files using regex and Powershell
comments: True
blog: Tech
tags: [Powershell, Windows]
description: 
---

# Find Not Matching File Class Name

```
$Time = [System.Diagnostics.Stopwatch]::StartNew()

$expr = "((public|static|private) (class|interface))"

$files = Get-ChildItem -Path .. -Filter *.cs -Recurse

write-host $("The following files contains a class or interface that doesn't match the name of the file:")

ForEach ($file in $files)
{
    Get-Content $file.FullName -Raw | Select-String -Pattern $expr -AllMatches | ForEach-Object {
        Get-Content $file.FullName |
            foreach{ $_.Trim()} |
            Select-String -Pattern $expr |
            Select-String -Pattern "^(?!.*$($file.BaseName)).*$" |
            Select-Object @{Name='Path';Expression={$file.FullName}}, LineNumber, Line
    }
}

write-host $([string]::Format("`rDone in: {0} seconds", $Time.Elapsed.TotalSeconds))
```

# Find Multiple Classes In One File

```
$Time = [System.Diagnostics.Stopwatch]::StartNew()

$expr = "((public|static|private) (class|interface))"

$files = Get-ChildItem -Path .. -Filter *.cs -Recurse

$count = 0

write-host $("The following files contains multiple classes or interfaces:")

ForEach ($file in $files)
{
    $content = Get-Content $file.FullName -Raw |
        Select-String -Pattern $expr -AllMatches

    If ($content.Matches.Count -gt 1)
    {
        $count++
        Get-Content $file.FullName |
            foreach{ $_.Trim()} |
            Select-String -Pattern $expr |
            Select-Object @{Name='Path';Expression={$file.FullName}}, LineNumber, Line
    }
}

If ($count -gt 0) 
{
    write-host $([string]::Format("`rFound {0} files in: {1} seconds", $count, $Time.Elapsed.TotalSeconds))
}
Else
{
    write-host $([string]::Format("`rDone in: {0} seconds", $Time.Elapsed.TotalSeconds))
}
```

# Find One Line If Statements


```
$Time = [System.Diagnostics.Stopwatch]::StartNew()

$expr = "^ *(if|else|else if)((?![a-z])).*(?!\{)$"

$files = Get-ChildItem -Path .. -Filter *.cs -Recurse

$count = 0

write-host $("The following files one line if-statements:")

ForEach ($file in $files)
{
    Get-Content $file.FullName |
        Select-String -Pattern $expr -AllMatches -Context 0, 1 |
        ForEach-Object {
            $oneLine = $_ |
                Select-Object -ExpandProperty Context |
                Select-Object -ExpandProperty PostContext |
                foreach { $_.Trim()}  |
                Select-String -Pattern "^[^\{]+$" -AllMatches -Context 1, 0

            If($oneLine)
            {
                $count++
                $_ | Select-Object @{Name='Path';Expression={$file.FullName}}, LineNumber, @{Name='Line';Expression={$_.Line.Trim()}}
            }
        }
}

If ($count -gt 0) 
{
    write-host $([string]::Format("`rFound {0} occurrences in: {1} seconds", $count, $Time.Elapsed.TotalSeconds))
}
Else
{
    write-host $([string]::Format("`rDone in: {0} seconds", $Time.Elapsed.TotalSeconds))
}
```