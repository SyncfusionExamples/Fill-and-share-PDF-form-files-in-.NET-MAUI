# Fill-and-share-PDF-form-files-in-.NET-MAUI
This repository contains the example which demonstrates how to fill a PDF form with proper validations and share it externally using Syncfusion&reg; .NET MAUI PDF Viewer.

## How to Fill and Share PDF Form Files in .NET MAUI
This guide explains how to load a PDF form, use a SfDatePicker for a better date-filling experience, and share the completed form using the Syncfusion PDF Viewer in a .NET MAUI application.

The primary files involved are:

MainPage.xaml
: Defines the UI, including the PDF viewer, share button, and the date picker.
MainPage.xaml.cs
: Contains the logic for loading the PDF, handling date selection, and sharing the file.

### Step 1: Add Dependencies
Ensure your project file PdfFormFillingExample.csproj includes the necessary Syncfusion NuGet packages for the PDF viewer, buttons, and the date picker.

```
<!-- File: PdfFormFillingExample.csproj -->
<ItemGroup>
    <PackageReference Include="Syncfusion.Maui.PdfViewer" Version="*" />
    <PackageReference Include="Syncfusion.Maui.Picker" Version="*" />
    <PackageReference Include="Syncfusion.Maui.Buttons" Version="*" />
    <!-- Other packages... -->
</ItemGroup>
```

### Step 2: Define the User Interface
In MainPage.xaml, add the SfPdfViewer, a SfButton for sharing, and a hidden Grid containing the SfDatePicker. The date picker will be shown programmatically.
```
 <Grid>
        <!-- 1. PDF Viewer to display the form -->
        <viewer:SfPdfViewer x:Name="pdfViewer" FormFieldFocusChanged="pdfViewer_FormFieldFocusChanged" />

        <!-- 2. Share button to export the form -->
        <button:SfButton x:Name="shareButton" Text="Share" Clicked="shareButton_Clicked" ... />

        <!-- 3. Hidden grid with a Date Picker for date fields -->
        <Grid x:Name="datePickerGrid" IsVisible="False">
            <Grid BackgroundColor="Black" Opacity="0.5" />
            <picker:SfDatePicker x:Name="datePicker" 
                                 OkButtonClicked="datePicker_OkButtonClicked" 
                                 CancelButtonClicked="datePicker_CancelButtonClicked" ... >
                <picker:SfDatePicker.FooterView>
                    <picker:PickerFooterView ShowOkButton="True" />
                </picker:SfDatePicker.FooterView>
            </picker:SfDatePicker>
        </Grid>
    </Grid>
```

### Step 3: Trigger the Date Picker on Field Focus
To provide a better UX than typing a date, use the FormFieldFocusChanged event to detect when the user selects the "Date of Birth" field (named "dob"). When this happens, show the datePickerGrid and hide the soft keyboard.

```
// File: MainPage.xaml.cs
private void pdfViewer_FormFieldFocusChanged(object sender, FormFieldFocusChangedEvenArgs e)
{
    // Check if the focused field is the date of birth field
    if (e.FormField.Name == "dob")
    {
        if (e.HasFocus)
        {
            // Make the date picker visible
            datePickerGrid.IsVisible = true;

            // Hide the soft keyboard on Android and iOS
            #if ANDROID
                // ... platform-specific code to hide keyboard
            #elif IOS && !MACCATALYST
                // ... platform-specific code to hide keyboard
            #endif
        }
    }
}
```

### Step 4: Handle Date Selection
When the user confirms a date in the SfDatePicker, the OkButtonClicked event is fired. The code then formats the selected date and updates the PDF form field's text.

```
// File: MainPage.xaml.cs
private void datePicker_OkButtonClicked(object sender, EventArgs e)
{
    // Find the 'dob' form field
    FormField field = pdfViewer.FormFields.First(f => f.Name == "dob");
    if (field is TextFormField dateOfBirthField)
    {
        // Format the date and set it as the field's text
        string date = datePicker.SelectedDate.ToString("dd/MM/yyyy");
        dateOfBirthField.Text = date;
    }
    // Hide the date picker
    datePickerGrid.IsVisible = false;
}

private void datePicker_CancelButtonClicked(object sender, EventArgs e)
{
    // If cancelled, just hide the date picker
    datePickerGrid.IsVisible = false;
}
```
### Step 5: Share the Filled PDF
Finally, the shareButton_Clicked event handler in MainPage.xaml.cs saves the filled form and uses the .NET MAUI Share API to open the native sharing dialog.
```
// File: MainPage.xaml.cs
private async void shareButton_Clicked(object sender, EventArgs e)
{
    bool isFormDataValid = await ValidateFormData();
    if (isFormDataValid)
    {
        // Save the filled document to a memory stream
        MemoryStream outputStream = new MemoryStream();
        await pdfViewer.SaveDocumentAsync(outputStream);

        // Write the stream to a local file
        string filePath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "workshop_registration.pdf");
        File.WriteAllBytes(filePath, outputStream.ToArray());

        // Open the native share dialog with the file
        await Share.Default.RequestAsync(new ShareFileRequest
        {
            Title = "Share filled form",
            File = new ShareFile(filePath)
        });
    }
}
```

### Conclusion

We hope you enjoyed learning how to fill and share pdf form files .NET-MAUI PDF Viewer

Refer to our [.NET MAUI PDF Viewer’s feature tour](https://www.syncfusion.com/maui-controls/maui-pdf-viewer) page to learn about its other groundbreaking feature representations. You can also explore our [.NET MAUI PDF Viewer Documentation](https://help.syncfusion.com/maui/pdf-viewer/getting-started) to understand how to present and manipulate data.

For current customers, check out our .NET MAUI components on the [License and Downloads](https://www.syncfusion.com/sales/teamlicense) page. If you are new to Syncfusion, try our 30-day [free trial](https://www.syncfusion.com/downloads/maui) to explore our .NET MAUI PDF Viewer and other .NET MAUI components.

Please let us know in the following comments if you have any queries or require clarifications. You can also contact us through our [support forums](https://www.syncfusion.com/downloads/maui), [support ticket](https://support.syncfusion.com/create) or [feedback portal](https://www.syncfusion.com/feedback/maui). We are always happy to assist you!
