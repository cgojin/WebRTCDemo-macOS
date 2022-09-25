# WebRTCDemo-macOS

WebRTC macOS example Xcode project.

## Build WebRTC.xcframework

```sh
# Make a work directory
cd $HOME
mkdir work/webrtc
cd work/webrtc

# Building Official Document: https://webrtc.googlesource.com/src/+/refs/heads/main/docs/native-code/development/index.md
# Building script of stasel/WebRTC: https://github.com/stasel/WebRTC/blob/latest/scripts/build.sh
# Download building script of stasel/WebRTC
curl -fsSL -o build.sh https://raw.github.com/stasel/WebRTC/latest/scripts/build.sh
chmod +x build.sh

# Build the WebRTC.xcframework
MACOS=true IOS=true BUILD_VP9=true ./build.sh

# Build WebRTC.xcframework for debugging
MACOS=true IOS=true BUILD_VP9=true DEBUG=true ./build.sh
```

## Import AppRTCMobile to XCode project

This XCode project has been imported the [AppRTCMobile](https://webrtc.googlesource.com/src/+/refs/heads/main/examples/objc/). If you want to create the Xcode project manually, please refer to the following steps.

- Create a new Xcode project and stored in $HOME/work/
    - File -> New -> Project... -> macOS -> App  
        - Product Name: WebRTCDemo-macOS  
        - Language: Objective-C  

- Delete all files of WebRTCDemo-macOS group, except *.entitlements.

- Add ../webrtc/src/examples/objc/* to WebRTCDemo-macOS group, delete tests, ios of WebRTCDemo-macOS/AppRTCMobile group.

- Configure targets
    - TARGETS -> WebRTCDemo-macOS -> Build Phases -> Copy Bundle Resources  
        Delete the info.plist (Fixed building error: Multiple commands produce '.../Info.plist')

    - TARGETS -> WebRTCDemo-macOS -> Build Settings  
        - Info.plist File: ../webrtc/src/examples/objc/AppRTCMobile/mac/Info.plist  
        - Header Search Paths: "$(SRCROOT)/../webrtc/src" "$(SRCROOT)/../webrtc/src/sdk/objc/base" "$(SRCROOT)/../webrtc/src/examples/objc/AppRTCMobile" 

    - TARGETS -> WebRTCDemo-macOS -> General -> Framework,Librares, and Embedded Content  
        Add WebRTC.xcframework of webrtc/src/out/ and set Embed & Sign.

    - TARGETS -> WebRTCDemo-macOS -> Signing & Capabilities  
        Checked the following options (Fixed runing error: "Operation not permitted"):
        - Incoming Connections (Server)
        - Outgoing Connections (Client)  
        - Camera  
        - Audio Input  

### License

MIT License - see [LICENSE](LICENSE) for full text
