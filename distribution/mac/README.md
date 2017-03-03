# Mac App Store Submission


### Reference

[Official Site](https://electron.atom.io/docs/tutorial/mac-app-store-submission-guide/)

[nw.js related](https://github.com/nwjs/nw.js/wiki/Mac-App-Store-%28MAS%29-Submission-Guideline#first-steps)


### Procedure

+ Install the latest `Xcode` (on `mac`)

+ An active developer account (on apple's website)
  
  -  [Apple Developer Program](https://developer.apple.com/programs/)

+ Requesting certificates (on `mac`)

  - [better than mine](https://github.com/nwjs/nw.js/wiki/MAS%3A-Requesting-certificates)
  
+ Get `Team ID` (on apple's website)
  
+ Registering a new app on the Mac App Store (on apple's website)

  - [better than mine](https://github.com/nwjs/nw.js/wiki/MAS%3A-Registering-a-new-app-on-the-MAS)
  
+ Download project from `github` & build (on `mac`)

  ```
    git clone https://github.com/JiangWeiGitHub/fruitmix-desktop.git
    cd fruitmix-desktop
    git checkout transimission
    npm install electron-prebuilt --save-dev
    npm install electron-builder webpack
    npm install
    webpack --> pack frontend
    npm run build --> pack backend

    ./node_modules/.bin/build --config ./electron-builder.yml --mac
  ```
  
+ Sign This App (on `mac`)

  - Go into project's root
  
  - Modify `Info.plist` inside `?.app` file, which path just like: `dist/mac/?.app/Contents/Info.plist`, add `ElectronTeamID` key 

    ```
      <plist version="1.0">
      <dict>
        ...
        <key>ElectronTeamID</key>
        <string>TEAM_ID</string>
      </dict>
      </plist>
    ```
  
  - Create two files

  `child.plist`

    ```
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
      <plist version="1.0">
        <dict>
          <key>com.apple.security.app-sandbox</key>
          <true/>
          <key>com.apple.security.inherit</key>
          <true/>
        </dict>
      </plist>
    ```

  `parent.plist`
  
    ```
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
      <plist version="1.0">
        <dict>
          <key>com.apple.security.app-sandbox</key>
          <true/>
          <key>com.apple.security.application-groups</key>
          <string>TEAM_ID.your.bundle.id</string>
        </dict>
      </plist>
    ```
    
    ps: replace `TEAM_ID` & `your.bundle.id`
    
+ Create a new package (on `mac`)

  - Create a shell script `sh.sh`
  
    ```
      #!/bin/bash

      # Name of your app.
      APP="YourApp"
      # The path of your app to sign.
      APP_PATH="/path/to/YourApp.app"
      # The path to the location you want to put the signed package.
      RESULT_PATH="~/Desktop/$APP.pkg"
      # The name of certificates you requested.
      APP_KEY="3rd Party Mac Developer Application: Company Name (APPIDENTITY)"
      INSTALLER_KEY="3rd Party Mac Developer Installer: Company Name (APPIDENTITY)"
      # The path of your plist files.
      CHILD_PLIST="/path/to/child.plist"
      PARENT_PLIST="/path/to/parent.plist"

      FRAMEWORKS_PATH="$APP_PATH/Contents/Frameworks"

      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Electron Framework"

      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Resources/crashpad_handler"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Squirrel.framework/Versions/A/Resources/ShipIt"

      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Libraries/libffmpeg.dylib"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Libraries/libnode.dylib"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper.app/Contents/MacOS/$APP Helper"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper.app/"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper EH.app/Contents/MacOS/$APP Helper EH"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper EH.app/"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper NP.app/Contents/MacOS/$APP Helper NP"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper NP.app/"
      codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$APP_PATH/Contents/MacOS/$APP"
      codesign -s "$APP_KEY" -f --entitlements "$PARENT_PLIST" "$APP_PATH"

      productbuild --component "$APP_PATH" /Applications --sign "$INSTALLER_KEY" "$RESULT_PATH"
    ```

  - Run this script
    - `./sh.sh`
    
+ Submit this new package using `Xcode` (on `mac`)

+ Done
