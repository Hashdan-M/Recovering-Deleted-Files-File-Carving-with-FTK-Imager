<img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/4aa73bedd7db10391b518c13c82f52d892b55e87/FTK%20Imager/logo.jpg"/></a>
<h1>Recovering Deleted Files: File Carving with FTK Imager</h1>
File carving is both an art and a science, especially when dealing with corrupt Master File Tables (MFT) or File Allocation Tables (FAT). In such cases, it may be necessary to manually extract files from allocated space. Additionally, remnants of files can often be found in unallocated or slack space, which may contain valuable information relevant to an investigation. These fragments can sometimes be analyzed for readable content or hashed for further comparison.
<br />
<br />
Several techniques exist for file carving. Investigators typically search for file headers and footers, then use a hex editor to carve out the blocks of data between these markers. This method relies on specific file signatures that indicate the start of a file and end-of-file (EOF) markers, as outlined in the table below:
<br />
<br />

| File Type                | File Signature          | EOF Marker           |
|-------------------------|-------------------------|----------------------|
| JPEG                    | FF D8 FF E0 or FF D8 FF E1 | FF D9              |
| PNG              | 89	50	4E	47	0D	0A	1A	0A              | 49	45	4E	44	AE	42	60	82     |
| PDF                     | 25 50 44 46             | 25 25 45 4F 46       |
| Microsoft Word       | EC	A5 C1	00 | None                |
| Microsoft Excel      | FD	FF FF	FF nn or 09	08	10	00	00	06	05	00	| None                |
| Microsoft Office Open XML       | 50 4B 03 04 14 00 06 00 50 | 50	4B 05	06                 |
| GIF                     | 47 49 46 38 37 61       | 00 3B                |
| Windows Executable      | 4D 5A                   | None                 |
| ZIP     | 50	4B	03	04                  | 50	4B	17	characters	00	00	00                 |


While this method is effective, it has limitations, particularly when headers or footers are missing or if the files are fragmented. Other carving techniques include block carving, which analyzes data on a block-by-block basis, and semantic carving, which examines data using linguistic analysis.

In addition to carving complete files, examiners can also recover valuable file fragments. This is often applicable for non-compressed and non-encrypted files, such as text files, HTML documents, or data from a swap/page file.

## Activity: Manual File Carving Using FTK Imager

### Instructions:

1. Download and Install **FTK Imager**.
2. Download the **"raw_image2.dd"** file from the Digital Forensics Workbook website and save it to your desktop.
3. Launch **FTK Imager**.
4. From the main menu, select **“File”** and then **“Add Evidence Item…”**.
 
  <img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/8b188872f44272f8a96486a22f7deba9df429583/FTK%20Imager/3-1.png"/></a>
   
5. In the **“Select Source”** dialog box, choose the radio button next to **“Image File”** and click **“Next”**.

 <img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/8b188872f44272f8a96486a22f7deba9df429583/FTK%20Imager/3-2.PNG"/></a>

6. Browse to the **“raw_image2.dd”** file and open it. 

 <img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/8b188872f44272f8a96486a22f7deba9df429583/FTK%20Imager/3-3.PNG"/></a>

7. FTK Imager will display the file. Upon inspecting the mounted image, you’ll notice the file system is FAT32, and no files are stored on it. In the Evidence Tree pane, select the **“unallocated space”** item. In the File List pane, click on the first unallocated item labeled **000003**.
After clicking **000003**, the contents of the unallocated space will appear in both hex and text views, revealing useful information and a file signature at the start of the cluster.
Identify the file signature present. In this case, it is **FF D8 FF E1**, associated with JPEG files.

 <img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/8b188872f44272f8a96486a22f7deba9df429583/FTK%20Imager/3-4.PNG"/></a>

8. Refer to the table to find the EOF marker for JPEG files: it is **FF D9**.
 
| File Type                | File Signature          | EOF Marker           |
|-------------------------|-------------------------|----------------------|
| JPEG                    | FF D8 FF E0 or FF D8 FF E1 | FF D9              |

9. Note the offset at the start of the sector, which is **0000000** in this example.
In the hexadecimal view, right-click on the start of the file signature and select **“Find…”**.

<img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/8b188872f44272f8a96486a22f7deba9df429583/FTK%20Imager/3-5.png"/></a>
    
10. In the Find dialog, enter **FFD9** (without spaces) and set the search type to **Binary (hex)**, then click **“Find”**.

<img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/8b188872f44272f8a96486a22f7deba9df429583/FTK%20Imager/3-6.PNG"/></a>

23. FTK Imager will locate the next instance of **FFD9** in the unallocated space.

<img src="https://github.com/Hashdan-M/Recovering-Deleted-Files-File-Carving-with-FTK-Imager/blob/8b188872f44272f8a96486a22f7deba9df429583/FTK%20Imager/3-7.PNG"/></a>

25. To confirm whether this is the actual EOF marker, consider the following:
    - Is the EOF marker adjacent to the start of the next sector? (In this case, it is not.)
    - Are the characters between **FFD9** and the next sector indicative of slack space or actual data? (Here, there appears to be data.)
    - Does the start of the next cluster have a file signature or unallocated space? (No recognizable signature is present.)
    
    Based on these answers, it’s likely that this occurrence of **FFD9** is not the EOF marker.
26. Search for the next occurrence of **FFD9** by pressing **F3** or right-clicking and selecting **“Find Next”**.
27. The next occurrence will appear at offset **00400e2**. Evaluate its status using the same questions as before.
28. If the answers suggest that it is the actual EOF marker, the file runs from **0000000** to **00400e3**.
29. In FTK Imager, highlight the data from just after **FFD9** up to the file signature at **0000000**.
30. Right-click and select **“Save Selection…”**.
31. Save the file with a **“.jpg”** extension on your desktop.
32. Open the saved file to verify that it displays correctly.
33. Search for the hexadecimal string **25504446** in FTK Imager.
34. Confirm if this string appears at the start of a sector (it does at **offset 012a000**).
35. Identify the type of file associated with this signature (it is for PDFs).
36. The EOF for this file type is **25 25 45 4F 46**.
37. Using **offset 012a000**, search for the next occurrence of **2525454F46**.
38. This string appears at **offset 012a440**.
39. Determine if this is the actual EOF marker (it is likely not, based on context).
40. Continue searching for the next occurrence of **2525454F46**.
41. The next instance occurs at **offset 013b00a**.
42. Highlight from just before the EOF marker to the start of the file at **offset 012a000**.
43. Hold down **SHIFT** and click on the first byte of the file’s signature.
44. Right-click and select **“Save Selection…”**.
45. Save the file with a **“.pdf”** extension.
46. Open the saved PDF file to ensure it displays as expected.

By following these steps, you can effectively perform manual file carving using FTK Imager, enabling the recovery of deleted files even when traditional methods fail.
