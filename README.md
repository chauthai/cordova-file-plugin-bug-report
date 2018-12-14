# cordova-file-plugin Bug Report
## Abstract
The `readAsText` function of the cordova-file-plugin causes an encoding error when reading an UTF-8 file larger than 2 megabytes on platforms iOS, Android and Windows.

## Description
The error is caused by the native implementation of the [`readAsText`](https://github.com/apache/cordova-plugin-file/blob/d135cd0e006736ea60c9dec46590de0927d57b6b/src/ios/CDVFile.m#L866) function of the `cordova-file-plugin`. Files are cut into chunks at a predefined [size](https://github.com/apache/cordova-plugin-file/blob/d135cd0e006736ea60c9dec46590de0927d57b6b/www/FileReader.js#L52) and are immediately converted to UTF-8. UTF-8 encoded characters use 4 bytes and if the cut is exactly at one character with 4 bytes, an encoding error is thrown.
You can see the UTF-8 leading byte `e2` at the end of [data-chunk.dump file](./data-chunk.dump).

## Workaround
We implemented a workaround using the `readAsArrayBuffer` function of the `cordova-file-plugin` and converting the typed array to UTF-8 in JavaScript with [TextDecoder.decode()](https://developer.mozilla.org/en-US/docs/Web/API/TextDecoder/decode).

## How to reproduce the `encoding error`
1. Install dependencies first
`npm i`
2. Initialize Cordova
`npm run init`
3. Deploy on platform
`npm run ios/android/windows`
4. Press `Fire Parser` in Demo App
5. Watch the logs
6. Set a breakpoint at the throw of [encoding error](https://github.com/apache/cordova-plugin-file/blob/d135cd0e006736ea60c9dec46590de0927d57b6b/src/ios/CDVFile.m#L872) in iOS
7. Look at the `data` variable
