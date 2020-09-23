<div align="center">

## AutoResize version 2


</div>

### Description

This code resizes a form and it's controls (and fonts) according to the users resolution.
 
### More Info
 
designwidth, designheight, designfontsize

For example if you designed your app at 800x600 and Small Fonts then you would set the above variables to 800, 600, 96 (or 120 for Large fonts). Then just sit back and let the code do the rest.

THIS CODE ALSO ALLOWS BASIC POSITIONING OF CONTROLS.

For example L120 would add 120 pixels onto the left coordinate.

T200 would add 200 pixels onto the top coordinate.

W150 would add 150 pixels onto the width coordinate.

H70 would add 70 pixels onto the height coordinate.

To achieve this simply enter the letter followed by the amount in the controls 'Tag' property.

No custom controls are supported at the moment. If you need any help with this then e-mail me at: mparter@hotmail.com


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Mark Parter](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/mark-parter.md)
**Level**          |Unknown
**User Rating**    |4.3 (81 globes from 19 users)
**Compatibility**  |VB 5\.0, VB 6\.0
**Category**       |[Custom Controls/ Forms/  Menus](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/custom-controls-forms-menus__1-4.md)
**World**          |[Visual Basic](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/visual-basic.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/mark-parter-autoresize-version-2__1-3546/archive/master.zip)

### API Declarations

```
Public Declare Function GetDesktopWindow Lib "user32" () As Long
Public Declare Function GetDeviceCaps Lib "gdi32" (ByVal hdc As Long, ByVal nIndex As Long) As Long
Public Declare Function GetDC Lib "user32" (ByVal hWnd As Long) As Long
Public Declare Function ReleaseDC Lib "user32" (ByVal hWnd As Long, ByVal hdc As Long) As Long
Public Const LOGPIXELSX = 88
Public Const LOGPIXELSY = 90
```


### Source Code

```
PLACE THE FOLLOWING CODE INTO A MODULE:
Public Function IsScreenFontSmall() As Boolean
Dim hWndDesk As Long
Dim hDCDesk As Long
Dim logPix As Long
Dim r As Long
hWndDesk = GetDesktopWindow()
hDCDesk = GetDC(hWndDesk)
logPix = GetDeviceCaps(hDCDesk, LOGPIXELSX)
r = ReleaseDC(hWndDesk, hDCDesk)
If logPix = 96 Then IsScreenFontSmall = True
Exit Function
End Function
--------------------------------------------------------
Sub ResizeControls(frmName As Form, winstate As Integer)
On Error Resume Next
Dim designwidth As Integer, designheight As Integer, designfontsize As Integer, currentfontsize As Integer
Dim numofcontrols As Integer, a As Integer
Dim movetype As String, moveamount As Integer
'Change the designwidth and the designheight according to the resolution that the form was designed at
designwidth = 1024
designheight = 768
designfontsize = 96
GetResolutionX = Screen.Width / Screen.TwipsPerPixelX
GetResolutionY = Screen.Height / Screen.TwipsPerPixelY
'Work out the ratio for resizing the controls
ratiox = GetResolutionX / designwidth
ratioy = GetResolutionY / designheight
'check to see what size of fonts are being used
If IsScreenFontSmall Then
  currentfontsize = 96
Else
  currentfontsize = 120
End If
'work out the ratio for the fontsize
fontratio = designfontsize / currentfontsize
If ratiox = 1 And ratioy = 1 And fontratio = 1 Then Exit Sub
numofcontrols = frmName.Controls.Count - 1 'count the number of controls on the form
If winstate = 0 Then 'if the form isn't fullscreen then
  frmName.Height = frmName.Height * ratioy
  frmName.Width = frmName.Width * ratiox
  If frmName.Tag <> "" Then
    movetype = Left(frmName.Tag, 1)
    moveamount = Mid(frmName.Tag, 2, Len(frmName.Tag))
    Select Case movetype
      Case "L"
        frmName.Left = frmName.Left + moveamount
      Case "T"
        frmName.Top = frmName.Top + moveamount
      Case "H"
        frmName.Height = frmName.Height + moveamount
      Case "W"
        frmName.Width = frmName.Width + moveamount
    End Select
  End If
ElseIf winstate = 2 Then 'otherwise if it is fullscreen then
  frmName.Width = Screen.Width
  frmName.Height = Screen.Height
  frmName.Top = 0
  frmName.Left = 0
End If
For a = 0 To numofcontrols 'loop through each control
  If frmName.Controls(a).Font.Size <= 8 And ratiox < 1 Then
    frmName.Controls(a).Font.Name = "Small Fonts"
    frmName.Controls(a).Font.Size = frmName.Controls(a).Font.Size - 0.5
  Else
    frmName.Controls(a).Font.Size = frmName.Controls(a).Font.Size * ratiox
  End If
  If TypeOf frmName.Controls(a) Is Line Then
    frmName.Controls(a).X1 = frmName.Controls(a).X1 * ratiox
    frmName.Controls(a).Y1 = frmName.Controls(a).Y1 * ratioy
    frmName.Controls(a).X2 = frmName.Controls(a).X2 * ratiox
    frmName.Controls(a).Y2 = frmName.Controls(a).Y2 * ratioy
  ElseIf TypeOf frmName.Controls(a) Is PictureBox Then
    frmName.Controls(a).Width = frmName.Controls(a).Width * ratiox
    frmName.Controls(a).Height = frmName.Controls(a).Height * ratioy
    frmName.Controls(a).Top = frmName.Controls(a).Top * ratioy
    frmName.Controls(a).Left = frmName.Controls(a).Left * ratiox
    frmName.Controls(a).ScaleHeight = frmName.Controls(a).ScaleHeight * ratioy
    frmName.Controls(a).ScaleWidth = frmName.Controls(a).ScaleWidth * ratiox
  ElseIf TypeOf frmName.Controls(a) Is Toolbar Then
    frmName.Controls(a).ButtonHeight = frmName.Controls(a).ButtonHeight * ratioy
    frmName.Controls(a).ButtonWidth = frmName.Controls(a).ButtonWidth * ratiox
    frmName.Controls(a).Width = frmName.Controls(a).Width * ratiox
    frmName.Controls(a).Height = frmName.Controls(a).Height * ratioy
    frmName.Controls(a).Top = frmName.Controls(a).Top * ratioy
    frmName.Controls(a).Left = frmName.Controls(a).Left * ratiox
  ElseIf TypeOf frmName.Controls(a) Is MSFlexGrid Then
    frmName.Controls(a).ColWidth = frmName.Controls(a).ColWidth * ratiox
    frmName.Controls(a).RowHeight = frmName.Controls(a).RowHeight * ratioy
    frmName.Controls(a).Width = frmName.Controls(a).Width * ratiox
    frmName.Controls(a).Height = frmName.Controls(a).Height * ratioy
    frmName.Controls(a).Top = frmName.Controls(a).Top * ratioy
    frmName.Controls(a).Left = frmName.Controls(a).Left * ratiox
  Else
    frmName.Controls(a).Width = frmName.Controls(a).Width * ratiox
    frmName.Controls(a).Height = frmName.Controls(a).Height * ratioy
    frmName.Controls(a).Top = frmName.Controls(a).Top * ratioy
    frmName.Controls(a).Left = frmName.Controls(a).Left * ratiox
  End If
  If frmName.Controls(a).Tag <> "" Then
    movetype = Left(frmName.Controls(a).Tag, 1)
    moveamount = Mid(frmName.Controls(a).Tag, 2, Len(frmName.Controls(a).Tag))
    Select Case movetype
      Case "L"
        frmName.Controls(a).Left = frmName.Controls(a).Left + moveamount
      Case "T"
        frmName.Controls(a).Top = frmName.Controls(a).Top + moveamount
      Case "H"
        frmName.Controls(a).Height = frmName.Controls(a).Height + moveamount
      Case "W"
        frmName.Controls(a).Width = frmName.Controls(a).Width + moveamount
    End Select
  End If
Next a
If fontratio <> 1 Then
  If winstate = 0 Then
    frmName.Height = frmName.Height * fontratio
    frmName.Width = frmName.Width * fontratio
    If frmName.Tag <> "" Then
      movetype = Left(frmName.Tag, 1)
      moveamount = Mid(frmName.Tag, 2, Len(frmName.Tag))
      Select Case movetype
        Case "L"
          frmName.Left = frmName.Left + moveamount
        Case "T"
          frmName.Top = frmName.Top + moveamount
        Case "H"
          frmName.Height = frmName.Height + moveamount
        Case "W"
          frmName.Width = frmName.Width + moveamount
      End Select
    End If
  ElseIf winstate = 2 Then
    frmName.Width = Screen.Width
    frmName.Height = Screen.Height
    frmName.Top = 0
    frmName.Left = 0
  End If
  For a = 0 To numofcontrols
    If frmName.Controls(a).Font.Size <= 8 And fontratio < 1 Then
      frmName.Controls(a).Font.Name = "Small Fonts"
      frmName.Controls(a).Font.Size = frmName.Controls(a).Font.Size - 0.5
    Else
      frmName.Controls(a).Font.Size = frmName.Controls(a).Font.Size * fontratio
    End If
  If TypeOf frmName.Controls(a) Is Line Then
    frmName.Controls(a).X1 = frmName.Controls(a).X1 * fontratio
    frmName.Controls(a).Y1 = frmName.Controls(a).Y1 * fontratio
    frmName.Controls(a).X2 = frmName.Controls(a).X2 * fontratio
    frmName.Controls(a).Y2 = frmName.Controls(a).Y2 * fontratio
  ElseIf TypeOf frmName.Controls(a) Is PictureBox Then
    frmName.Controls(a).Width = frmName.Controls(a).Width * fontratio
    frmName.Controls(a).Height = frmName.Controls(a).Height * fontratio
    frmName.Controls(a).Top = frmName.Controls(a).Top * fontratio
    frmName.Controls(a).Left = frmName.Controls(a).Left * fontratio
    frmName.Controls(a).ScaleHeight = frmName.Controls(a).ScaleHeight * fontratio
    frmName.Controls(a).ScaleWidth = frmName.Controls(a).ScaleWidth * fontratio
  ElseIf TypeOf frmName.Controls(a) Is Toolbar Then
    frmName.Controls(a).ButtonHeight = frmName.Controls(a).ButtonHeight * fontratio
    frmName.Controls(a).ButtonWidth = frmName.Controls(a).ButtonWidth * fontratio
    frmName.Controls(a).Width = frmName.Controls(a).Width * fontratio
    frmName.Controls(a).Height = frmName.Controls(a).Height * fontratio
    frmName.Controls(a).Top = frmName.Controls(a).Top * fontratio
    frmName.Controls(a).Left = frmName.Controls(a).Left * fontratio
  ElseIf TypeOf frmName.Controls(a) Is MSFlexGrid Then
    frmName.Controls(a).ColWidth = frmName.Controls(a).ColWidth * fontratio
    frmName.Controls(a).RowHeight = frmName.Controls(a).RowHeight * fontratio
    frmName.Controls(a).Width = frmName.Controls(a).Width * fontratio
    frmName.Controls(a).Height = frmName.Controls(a).Height * fontratio
    frmName.Controls(a).Top = frmName.Controls(a).Top * fontratio
    frmName.Controls(a).Left = frmName.Controls(a).Left * fontratio
  Else
    frmName.Controls(a).Width = frmName.Controls(a).Width * fontratio
    frmName.Controls(a).Height = frmName.Controls(a).Height * fontratio
    frmName.Controls(a).Top = frmName.Controls(a).Top * fontratio
    frmName.Controls(a).Left = frmName.Controls(a).Left * fontratio
  End If
  Next a
End If
End Sub
PLACE THE FOLLOWING CODE INTO THE FORM_LOAD EVENT OF THE FORM:
ResizeControls Me, x (replace the x with a 2 for a fullscreen form or a 0 for any other size of form.)
```

