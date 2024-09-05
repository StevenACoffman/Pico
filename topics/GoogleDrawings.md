# Converting vector to Google Drawings
1. [CloudConvert](https://cloudconvert.com/) can convert a variety of Vector formats like SVG into EMF and save it into your Google Drive.
2. You need to make a Google Apps Script (GAS) to convert these to the correct Mime type:

```
function betterConvertMIMETypeOfEMFs() {
  var oneWeekAgo = new Date();
  oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);

  var targetMimeType = 'image/emf';
  var desiredMimeType = 'application/x-msmetafile';
  var query = 'mimeType != "' + desiredMimeType + '" and fileExtension = "emf" and modifiedDate > "' + oneWeekAgo.toISOString() + '"';
  var files = DriveApp.searchFiles(query);
 
  var count = 0;
  var maxFilesToConvert = 1;

  while (files.hasNext() && count < maxFilesToConvert) {
    var file = files.next();

    // Check if file has parents before attempting to retrieve
    var parents = file.getParents();
    if (parents.hasNext()) {
      var folder = parents.next();
     
      // Try to get the blob and change MIME type; if error, skip this file
      try {
        var blob = file.getBlob();
        blob.setContentType(desiredMimeType);
        folder.createFile(blob);
        count++;
      } catch (e) {
        Logger.log("Error processing file " + file.getName() + ": " + e.toString());
        continue;  // Skip this file and move to the next one
      }
    }
  }

  if (count === 0) {
    Logger.log('No recent EMF files needing conversion were found.');
  } else {
    Logger.log(count + ' recent EMF files converted to desired MIME type.');
  }
}
```
