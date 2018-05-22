---
title: How to Create a Month Calendar Control
description: This topic demonstrates how to dynamically create a month calendar control by using the CreateWindowEx function.
ms.assetid: '35ADDA85-5D7D-46F4-A637-99FEE4592B3B'
---

# How to Create a Month Calendar Control

This topic demonstrates how to dynamically create a month calendar control by using the [**CreateWindowEx**](https://msdn.microsoft.com/library/windows/desktop/ms632680) function.

## What you need to know

### Technologies

-   [Windows Controls](window-controls.md)

### Prerequisites

-   C/C++
-   Windows User Interface Programming

## Instructions

### 

To create a month calendar control, use the [**CreateWindowEx**](https://msdn.microsoft.com/library/windows/desktop/ms632680) function, specifying [**MONTHCAL\_CLASS**](common-control-window-classes.md#monthcal-class) as the window class. You must first register the window class by calling the [**InitCommonControlsEx**](initcommoncontrolsex.md) function, specifying the **ICC\_DATE\_CLASSES** bit in the accompanying [**INITCOMMONCONTROLSEX**](initcommoncontrolsex-4vvx.md) structure.

The following example demonstrates how to create a month calendar control in an existing modeless dialog box. Note that the size values passed to [**CreateWindowEx**](https://msdn.microsoft.com/library/windows/desktop/ms632680) are all zeros. Because the minimum required size depends on the font that the control uses, the example uses the [**MonthCal\_GetMinReqRect**](monthcal-getminreqrect.md) macro to request size information and then resizes the control by calling [**SetWindowPos**](https://msdn.microsoft.com/library/windows/desktop/ms633545). If you subsequently change the font with [**WM\_SETFONT**](https://msdn.microsoft.com/library/windows/desktop/ms632642), the dimensions of the control will not change. You must call **MonthCal\_GetMinReqRect** again and resize the control to fit the new font.

## 


```C++
// Child window identifier of the month calendar.
#define IDC_MONTHCAL 101

// Symbols used by SetWindowPos function (arbitrary values).
#define LEFT 35
#define TOP  40

// Description:
//   Creates a month calendar control in a dialog box.  
// Parameters:
//   hwndOwner - handle of the owner window.
// Nonlocal variables:
//   MonthCalDlgProc - window procedure of the dialog box that 
//     contains the month calendar.
//   g_hInst - global instance handle.
//
HRESULT CreateMonthCalDialog(HWND hwndOwner)
{
    RECT rc;
    INITCOMMONCONTROLSEX icex;
    HWND hwndDlg = NULL;
    HWND hwndMonthCal = NULL;

    // Return an error code if the owner handle is invalid.
    if (hwndOwner == NULL)
        return E_INVALIDARG;

    // Load the window class.
    icex.dwSize = sizeof(icex);
    icex.dwICC  = ICC_DATE_CLASSES;
    InitCommonControlsEx(&amp;icex);

    // Create a modeless dialog box to hold the control.
    hwndDlg = CreateDialog(g_hInst,
                    MAKEINTRESOURCE(IDD_DATE_PICKER),
                    hwndOwner,
                    MonthCalDlgProc);
   
    // Return if creating the dialog box failed. 
    if (hwndDlg == NULL)
        return HRESULT_FROM_WIN32(GetLastError()); 
                        
    // Create the month calendar.
    hwndMonthCal  = CreateWindowEx(0,
                    MONTHCAL_CLASS,
                    L"",
                    WS_BORDER | WS_CHILD | WS_VISIBLE | MCS_DAYSTATE,
                    0,0,0,0, // resize it later
                    hwndDlg,
                    (HMENU) IDC_MONTHCAL,
                    g_hInst,
                    NULL);

    // Return if creating the month calendar failed. 
    if (hwndMonthCal == NULL)
        return HRESULT_FROM_WIN32(GetLastError()); 
                     
    // Get the size required to show an entire month.
    MonthCal_GetMinReqRect(hwndMonthCal, &amp;rc);

    // Resize the control now that the size values have been obtained.
    SetWindowPos(hwndMonthCal, NULL, LEFT, TOP, 
        rc.right, rc.bottom, SWP_NOZORDER);

    // Set the calendar to the annual view.
    MonthCal_SetCurrentView(hwndMonthCal, MCMV_YEAR);

    // Make the window visible.
    ShowWindow(hwndDlg, SW_SHOW);

    return S_OK;
}
```



## Related topics

<dl> <dt>

[Month Calendar Control Reference](bumper-month-calendar-month-calendar-control-reference.md)
</dt> <dt>

[About Month Calendar Controls](month-calendar-controls.md)
</dt> <dt>

[Using Month Calendar Controls](using-month-calendar-controls.md)
</dt> </dl>

 

 



