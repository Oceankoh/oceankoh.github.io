---
layout: project
title: Smarter Word Counter
excerpt: Microsoft Word VBA Script to exclude certain words from word count
categories: Macros, Scripting, IB
---

Introduction
------

Was trying to count the number of words in my Extended Essay, and realised I had a lot of tables and close to 40+ figures. Since IB excludes words in tables and captions, I had to subtract all of those words from my total word count. With so many tables and captions, manually counting them all up was a pain. There must be a better way to do this... and indeed there is...

### Solution

After a quick google, this [blog post](https://www.datanumen.com/blogs/2-methods-exclude-table-texts-word-count-statistics/) reminded me that macros exist. However, for some reason the script attach in the link did not work for me. So I had to rewrite certain parts of it to make it work. This was my first time writing a macro, my script is attached below. 

```vb
Sub EEWordCount()
Dim Tbl As Table, d As Long, t As Long
Dim p As Paragraph, w As Long

With ActiveDocument
  For Each Tbl In .Tables
    With Tbl
      t = t + .Range.ComputeStatistics(wdStatisticWords)
    End With
  Next
  
  For Each p In .Paragraphs
    With p
      If .Style = "Caption" Then
        w = w + .Range.ComputeStatistics(wdStatisticWords)
      End If
    End With
  Next
  
  d = .Range.ComputeStatistics(wdStatisticWords)
End With



  
MsgBox "There are:" & vbCr & _
  d & " words in the document body, including" & vbCr & _
  t & " words in tables." & vbCr & _
  w & " words as Captions." & vbCr & vbCr & "There are" & vbCr & _
  d - t - w & " words in the document body, excluding tables, captions and footnotes."
End Sub
```

### Takeaways

* Don't blindly assume and trust random scripts online. They can have errors too.  

I suspect that the VBA script provided in the blog post I referenced didn't work due to the different versions of MS word. I managed to fix this by referring to the VBA documentation. 

* Trust your ability to learn. 

Despite being my first time reading let alone coding in VBA, it wasn't as difficult as I initially thought it was. VBA looks super ugly and scary compared to more common programming languages like python and Java. I was tempted to give up after the copy-pasted script didn't work. However, I also really didn't want to manually count all my captions, so I decided to try it out. Probably a combination of my subpar CTF Reversing knowledge and some experience using Jekyll `Liquid` which I found VBA really similar to, I was able to fix the script and even modify it such that it better suited my purposes. 