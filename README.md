# Appstore-upload-issue
Submit to App Store issues: Unsupported Architecture x86_64,i386 (IBMMobileFirstPlatformFoundation.framework) +IONIC 2 + IBM MFP 8.0

I was getting these error when upload to appstore.

**Unsupported Architecture x86_64,i386 for the framework**

I had added the IBMMobileFirstPlatformFoundation.framework in Targets => General => Embedded Binaries. (By default it is there in Linked Framework and Libraries)

If I remove IBMMobileFirstPlatformFoundation.framework from Embedded Libraries (By default it is there in Linked Framework and Libraries), Build will success, but App will crash.

I could take adhoc build,

I have used Ionic version 2 and IBM MFP 8.0. and

cordova-plugin-mfp 8.0.2017060910 "IBM MobileFirst Platform Foundation"

**HOW TO FIX THIS**

**Step 1.** 
Add The Embedded framwork (Xcode => Targets => Build phases => Embedded Binaries).

**Step 2.**
Add The Run Script (Xcode => Targets => Build phases => Use + button to add Run Script Phases).
        Set it to use /bin/sh and enter the following script:
        ```
        APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"

        # This script loops through the frameworks embedded in the application and
        # removes unused architectures.
        find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
        do
            FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
            FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
            echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"

            EXTRACTED_ARCHS=()

            for ARCH in $ARCHS
            do
                echo "Extracting $ARCH from $FRAMEWORK_EXECUTABLE_NAME"
                lipo -extract "$ARCH" "$FRAMEWORK_EXECUTABLE_PATH" -o "$FRAMEWORK_EXECUTABLE_PATH-$ARCH"
                EXTRACTED_ARCHS+=("$FRAMEWORK_EXECUTABLE_PATH-$ARCH")
            done

            echo "Merging extracted architectures: ${ARCHS}"
            lipo -o "$FRAMEWORK_EXECUTABLE_PATH-merged" -create "${EXTRACTED_ARCHS[@]}"
            rm "${EXTRACTED_ARCHS[@]}"

            echo "Replacing original executable with thinned version"
            rm "$FRAMEWORK_EXECUTABLE_PATH"
            mv "$FRAMEWORK_EXECUTABLE_PATH-merged" "$FRAMEWORK_EXECUTABLE_PATH"

        done
        ```
Thats all

Thank you
