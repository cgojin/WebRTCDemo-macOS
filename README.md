# WebRTCDemo-macOS
WebRTC macOS example Xcode project.

## Build WebRTC

[Reference official document](https://webrtc.googlesource.com/src/+/refs/heads/main/docs/native-code/development/index.md)

### Getting the Code

```sh
cd $HOME && mkdir c && cd c
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
export PATH=$PATH:$HOME/c/depot_tools

cd $HOME/c
fetch --nohooks webrtc
gclient sync

cd $HOME/c/src
git checkout main
git new-branch my
```
### Generate ninja project files
```sh
gn gen ./out/ios_arm64 --args='target_os="ios" target_cpu="arm64" ios_deployment_target="10.0" ios_enable_code_signing=false use_xcode_clang=true is_component_build=false rtc_include_tests=false rtc_libvpx_build_vp9=false enable_ios_bitcode=false use_goma=false rtc_enable_symbol_export=true enable_dsyms=true enable_stripping=true is_debug=true'

gn gen ./out/ios_x64 --args='target_os="ios" target_cpu="x64" ios_deployment_target="10.0" ios_enable_code_signing=false use_xcode_clang=true is_component_build=false rtc_include_tests=false rtc_libvpx_build_vp9=false enable_ios_bitcode=false use_goma=false rtc_enable_symbol_export=true enable_dsyms=true enable_stripping=true is_debug=true'

gn gen ./out/mac_x64 --args='target_os="mac" target_cpu="x64" is_component_build=false rtc_include_tests=false rtc_libvpx_build_vp9=false use_goma=false rtc_enable_symbol_export=true enable_dsyms=true enable_stripping=true is_debug=true'
```

If you compile the release version, please replace *is_debug=true* with *is_debug=false*

### Compile framework
```sh
ninja -C out/mac_x64 sdk:mac_framework_objc
ninja -C out/ios_arm64 sdk:framework_objc
ninja -C out/ios_x64 sdk:framework_objc
```

### Create xcframework

```sh
xcodebuild -create-xcframework \
	-framework out/ios_arm64/WebRTC.framework \
	-framework out/ios_x64/WebRTC.framework \
	-framework out/mac_x64/WebRTC.framework \
	-output out/WebRTC.xcframework
```

## Import AppRTCMobile to XCode project

This XCode project has been imported the [AppRTCMobile](https://webrtc.googlesource.com/src/+/refs/heads/main/examples/objc/). If you want to create the Xcode project manually, please refer to the following steps.

* Create a new Xcode project and stored in $HOME/c  
    File -> New -> Project... -> macOS -> App  
    Product Name: WebRTCDemo-macOS  
    Language: Objective-C  

* Delete all files of WebRTCDemo-macOS group

* Add ../webrtc/src/examples/objc/* to WebRTCDemo-macOS group, delete tests, ios of WebRTCDemo-macOS/AppRTCMobile group.

* Configure targets
    * TARGETS -> WebRTCDemo-macOS -> Build Phases -> Copy Bundle Resources  
        Delete the info.plist (Fixed building error: Multiple commands produce '.../Info.plist')

    * TARGETS -> WebRTCDemo-macOS -> Build Settings  
        Info.plist: ../webrtc/src/examples/objc/AppRTCMobile/mac/Info.plist  
        Header Search Paths: "$(SRCROOT)/../webrtc/src" "$(SRCROOT)/../webrtc/src/sdk/objc/base" "$(SRCROOT)/../webrtc/src/examples/objc/AppRTCMobile" 

    * TARGETS -> WebRTCDemo-macOS -> General -> Framework,Librares, and Embedded Content  
        Add WebRTC.xcframework of webrtc/src/out/ and set Embed & Sign.

    * TARGETS -> WebRTCDemo-macOS -> Signing & Capabilities  
        Checked the following options (Fixed runing error: "Operation not permitted"):
        * Incoming Connections (Server)
        * Outgoing Connections (Client)  
        * Camera  
        * Audio Input  

### License
MIT License - see [LICENSE](LICENSE) for full text
