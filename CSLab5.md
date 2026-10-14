# Program 5: File Comparison for Forensic Investigation Using Compare It!

## Objective

To compare two versions of a text file, identify added, deleted, or
modified content, and observe how file-comparison tools can support
digital forensic investigations.

## Software Required

-   Windows Operating System
-   Notepad
-   Compare It! Tool

## Step 1: Create the First Text File

1.  Open Notepad.
2.  Type the following content:

``` text
Student Name: Rahul
USN: 1DS22CS001
Department: CSE
Semester: 7
College: DSCE
```

3.  Click **File → Save As**.
4.  Save the file as `File1.txt` in an easy-to-find location, such as
    the Desktop.

## Step 2: Create the Second Text File

1.  Open Notepad again.
2.  Type the following content, which contains slight differences:

``` text
Student Name: Rahul Kumar
USN: 1DS22CS001
Department: Computer Science
Semester: 7
College: DSCE Bangalore
```

3.  Save the file as `File2.txt` in the same folder as `File1.txt`.

## Step 3: Install Compare It!

1.  Open the official website: https://www.grigsoft.com/wincmp3.htm
2.  Download the Compare It! software.
3.  Run the installer.
4.  Follow the installation wizard.
5.  Click **Finish** after installation.

## Step 4: Launch Compare It!

Open **Compare It!** from the Start Menu or Desktop shortcut. The
application opens with two file-selection panels.

## Step 5: Load the First File

1.  Click the **Browse/Open** button on the left side.
2.  Navigate to the folder where `File1.txt` is saved.
3.  Select `File1.txt`.
4.  Click **Open**.

## Step 6: Load the Second File

1.  Click the **Browse/Open** button on the right side.
2.  Select `File2.txt`.
3.  Click **Open**.

## Step 7: Compare the Files

The software compares the files and displays them side by side. It
highlights differences using colors.

Typical color indications in the manual:

-   **Yellow:** Modified text
-   **Green:** Newly added text
-   **Red:** Deleted text

The exact colors may vary depending on the software theme and settings.

## Step 8: Analyze the Differences

Observe the highlighted differences, including:

-   Changed student name: `Rahul` → `Rahul Kumar`
-   Modified department: `CSE` → `Computer Science`
-   Additional text in the college name: `DSCE` → `DSCE Bangalore`

These changes show which information differs between the two files.

## Step 9: Navigate Between Differences

Use the toolbar buttons or keyboard shortcuts to:

-   Move to the next difference
-   Move to the previous difference

Review each highlighted change.

## Step 10: Synchronize or Copy Changes (Optional)

If required:

1.  Select a highlighted difference.
2.  Click **Copy Left → Right** or **Copy Right → Left**.
3.  The selected change is copied to the other file.

This step is optional.

## Step 11: Generate a Difference Report

1.  Click **File → Print** or **Print Preview**.
2.  Review the report showing the detected differences.
3.  Save or print the report for documentation, if required.

The report can document differences observed during a forensic
investigation.

## Step 12: Close the Application

1.  Review the comparison results.
2.  Save any required reports.
3.  Close the Compare It! application.

## Result

The two text files were compared using Compare It!, and the changed
student name, department, and college name were identified.

## Conclusion

Compare It! displays differences between two versions of a file, helping
an investigator identify modified, added, or deleted content during a
digital forensic investigation.
