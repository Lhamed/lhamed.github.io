---
layout: post
title: 젠킨스 자동화에 쓰는 유니티 빌드 스크립트 정리 
description: 올리는데 너무 시간끌었나. 
modified: 2019-7-3
tags: [ Journal , Unity , Jenkins, CI  ] 
---

# 빌드 스크립트 정리 

젠킨스를 위해서 자동 빌드 스크립트들을 정리해 두었다.

그간 미뤘다가 대강이라도 지금 정리하는 이유는 아는 분이 부탁했기 때문이기도 한다.

이렇게 해둔 이유는 젠킨스 사용 시에 편하게 쓰기 위함이고 .

내가 해온 삽질이 누군가에게 의미가 있었음 좋겠다.

시간 되면 또 다듬어봐야지 . 

이게 선처리 빌드 스크립트다. 

```csharp
    using UnityEditor;
    using UnityEditor.Build; // 꼭 필요함 . 
    using UnityEngine;
    class PreBuilder : IPreprocessBuild {
        public void OnPreprocessBuild (BuildTarget target, string path) 
        {
        // 빌드 전에 꼭 해야할 처리들을 해줍니다. 
        }
    
    }
```
이게 본 빌드 스크립트다 . ( 젠킨스에서 호출하는 ). 
```csharp
    using System;
    using System.Collections;
    using System.Collections.Generic;
    using System.IO;
    using UnityEditor;
    using UnityEditor.Build;
    using UnityEngine;
    public class AutoBuilder : ScriptableObject {
        static string[] SCENES = FindEnabledEditorScenes ();
        static string APP_NAME = "앱 이름";
        static string TARGET_DIR;
        [MenuItem ("CI/Build For Android")]
        public static void PerformBuildAOS () {
            //  PlayerSettings.Android.bundleVersionCode = 동적으로 버전 코드 설정
            // PlayerSettings.bundleVersion = 동적으로 번들 버전 설정 
            APP_NAME = "앱이름" + PlayerSettings.Android.bundleVersionCode + ".apk";
    				// 맘대로 하셔도 별 지장 없지만, 저는 분간을 위해서 이렇게 합니다. 
            TARGET_DIR = ProjectPath + "/" + "Output";
            Directory.CreateDirectory (TARGET_DIR); // 혹시 없을까봐 디렉토리 만들어줌 
            //PlayerSettings.Android.keyaliasName = 환경 변수로 세팅하길 추천합니다. 
            //PlayerSettings.Android.keystoreName = 환경 변수로 세팅하길 추천합니다. 
            // PlayerSettings.Android.keyaliasPass = 환경 변수로 세팅하길 추천합니다. 
    	      // PlayerSettings.Android.keystorePass = 환경 변수로 세팅하길 추천합니다. 
            PlayerSettings.SetScriptingBackend (BuildTargetGroup.Android, ScriptingImplementation.IL2CPP);
            // 대략 설정해줍니다. 본인 필요에 맞춰서요 .
            PlayerSettings.Android.targetArchitectures = AndroidArchitecture.ARM64 | AndroidArchitecture.ARMv7;
    				// 이넘이니 여러개를 중첩할 수 있습니다. 
            EditorUserBuildSettings.androidBuildSystem = AndroidBuildSystem.Gradle; //저는 그래들로 합니다. 인터널로 하실 수도 있어요 
            EditorUserBuildSettings.androidBuildType = AndroidBuildType.Release; // 디벨롭이 필요하시면 디벨롭으로 하시면 됩니다. 
            BuildAndroid (SCENES, TARGET_DIR + "/" + APP_NAME, BuildTargetGroup.Android, BuildTarget.Android,  BuildOptions.CompressWithLz4HC | BuildOptions.Il2CPP );
            // 이것도 필요하시면 커스텀 ! 
        }
    
    
        
        private static void BuildAndroid (string[] scenes, string app_target, BuildTargetGroup build_target_group, BuildTarget build_target, BuildOptions build_options) {
    				if (EditorUserBuildSettings.activeBuildTarget != BuildTarget.Android)
    		       EditorUserBuildSettings.SwitchActiveBuildTarget (BuildTargetGroup.Android, BuildTarget.Android);
            //현 세팅이 안드로이드 아니면 안드로이드로 바꿔줍니다. 만약 다시 원상태로 돌아오고 싶으면 PostBuild 활용하세요
            BuildPlayerOptions buildPlayerOptions = new BuildPlayerOptions ();
            buildPlayerOptions.scenes = scenes;
            buildPlayerOptions.locationPathName = app_target;
            buildPlayerOptions.target = BuildTarget.Android;
            buildPlayerOptions.options = build_options;
            var report = BuildPipeline.BuildPlayer (buildPlayerOptions);
        }
    
    		private static string[] FindEnabledEditorScenes () 
    		{
            List<string> EditorScenes = new List<string> ();
    
            foreach (EditorBuildSettingsScene scene in EditorBuildSettings.scenes) {
                if (!scene.enabled) continue;
                EditorScenes.Add (scene.path);
            }
    
            return EditorScenes.ToArray ();
        }
    
        static string ProjectPath {
            get { return Application.dataPath.Substring (0, Application.dataPath.LastIndexOf ('/')); }
        } // 귀찮고 시간 없어서 대강 만들어서 쓰고 있는 중인 함수 
    
        [MenuItem ("CI/Build iOS")]
        public static void PerformBuildIOS () {
            BuildOptions opt = BuildOptions.Il2CPP; // 기본이 cpp
            PlayerSettings.iOS.sdkVersion = iOSSdkVersion.DeviceSDK; // 시뮬레이터에서 돌리시려면 시뮬레이터 sdk 로 
            //PlayerSettings.bundleVersion = GetArg ("-BUNDLE_VERSION"); //todo
            //PlayerSettings.iOS.buildNumber = (GetArg ("-VERSION_CODE")); //todo
            char sep = Path.DirectorySeparatorChar;
            string BUILD_TARGET_PATH = ProjectPath + "/ios"; //ios 폴더로 뱉습니다. 
            Directory.CreateDirectory (BUILD_TARGET_PATH);
            PlayerSettings.SetScriptingBackend (BuildTargetGroup.iOS, ScriptingImplementation.IL2CPP);
            try {
                BuildIOS (SCENES, BUILD_TARGET_PATH, BuildTarget.iOS, opt);
            } catch (System.Exception e) {
                Debug.Log (e.Message);
            }
        }
    
        static void BuildIOS (string[] scenes, string target_path,
            BuildTarget build_target, BuildOptions build_options) {
            EditorUserBuildSettings.SwitchActiveBuildTarget (BuildTargetGroup.iOS, build_target);
            string res = BuildPipeline.BuildPlayer (scenes, target_path, build_target, build_options);
            if (res.Length > 0) { throw new Exception ("BuildPlayer failure: " + res); }
        }
    
    }
```

이게 후처리 빌드 스크립트이다. 
```csharp
    #if UNITY_IOS
    using System.IO;
    using UnityEditor;
    using UnityEditor.Build;
    using UnityEditor.iOS.Xcode;
    using UnityEngine;
    // ios 유니티 빌드는 먼저 엑스코드용으로 빼준다음에 다시 시작되니. 빌드후 이렇게 후처리 해주고 엑스코드 에서 아카이빙 해주면 끝이납니다. 이부분은 젠킨스 파트에서 더 설명할게요 . 
    class PostBuilder : IPostprocessBuild
    {
        public int callbackOrder { get { return 0; } }
    
        static string ProjectPath
        {
            get { return Application.dataPath.Substring(0, Application.dataPath.LastIndexOf('/')); }
        }
        public void OnPostprocessBuild(BuildTarget buildTarget, string pathToBuiltProject)
        {
            // Stop processing if targe is NOT iOS 
            if (buildTarget != BuildTarget.iOS) return;
            // Initialize PbxProject 
            var projectPath = pathToBuiltProject + "/Unity-iPhone.xcodeproj/project.pbxproj";
            PBXProject pbxProject = new PBXProject();
            pbxProject.ReadFromFile(projectPath);
            string targetGuid = pbxProject.TargetGuidByName("Unity-iPhone");
            pbxProject.AddCapability(targetGuid, PBXCapabilityType.InAppPurchase);
            pbxProject.AddCapability(targetGuid, PBXCapabilityType.iCloud);
            pbxProject.AddCapability(targetGuid, PBXCapabilityType.GameCenter);
            pbxProject.AddCapability(targetGuid, PBXCapabilityType.PushNotifications);
    //이런 식으로 권한 추가해주면 되는데, 특이하게 푸시나 아이클라우드는 추가로 더 해줘야합니다.  (다음에 나옴 ) 
    
            pbxProject.SetBuildProperty(targetGuid, "DEVELOPMENT_TEAM", "애플 개발자 팀 아이디 ");
            var guid = pbxProject.FindFileGuidByProjectPath("Classes/UI/Keyboard.mm");
            var flags = pbxProject.GetCompileFlagsForFile(targetGuid, guid);
            flags.Add("-fno-objc-arc");
            pbxProject.SetCompileFlagsForFile(targetGuid, guid, flags);
            pbxProject.AddFrameworkToProject(targetGuid, "CloudKit.framework", false);
    
            // Apply settings 
            File.WriteAllText(projectPath, pbxProject.WriteToString());
            // Samlpe of editing Info.plist 
            var plistPath = Path.Combine(pathToBuiltProject, "Info.plist");
            var plist = new PlistDocument();
            plist.ReadFromFile(plistPath);
            // Add string setting 
            plist.root.SetBoolean("ITSAppUsesNonExemptEncryption", false); // 앱이 암호화를 쓰는지 제출하는 건데, 안하면 귀찮게 바로 테스트플라이트가 안올라가고 의미없는 설문을 더 해야하니 . 암호화를 특별나게 쓰고 있지만 않다면 이렇게 합시다. 
            // Add URL Scheme\
            // Apply editing settings to Info.plist
            var cap = plist.root.CreateArray("UIRequiredDeviceCapabilities");
            cap.AddString("gamekit");
            plist.WriteToFile(plistPath);
    
    
            //-----
    
            var file_name = "unity.entitlements"; // 이거없으면 푸시 안됩니다 
            var proj_path = projectPath;
            var proj = new PBXProject();
            proj.ReadFromFile(proj_path);
    
    
            // target_name = "Unity-iPhone"
            var target_name = PBXProject.GetUnityTargetName();
            var target_guid = proj.TargetGuidByName(target_name);
            var dst = pathToBuiltProject + "/" + target_name + "/" + file_name;
            try
            {
                File.WriteAllText(dst, entitlements);
                proj.AddFile(target_name + "/" + file_name, file_name);
                proj.AddBuildProperty(target_guid, "CODE_SIGN_ENTITLEMENTS", target_name + "/" + file_name);
                proj.WriteToFile(proj_path);
            }
            catch (IOException e)
            {
                Debug.Log("Could not copy entitlements. Probably already exists. " + e);
            }
    
            UnityEditor.iOS.Xcode.ProjectCapabilityManager pcm = new UnityEditor.iOS.Xcode.ProjectCapabilityManager(proj_path, dst, target_name);
            pcm.AddPushNotifications(false);
    
        }
    // 슬픈 하드코딩 타자가 길어서 슬픈 짐승이여 
    
        private const string entitlements = @"
         <?xml version=""1.0"" encoding=""UTF-8\""?>
         <!DOCTYPE plist PUBLIC ""-//Apple//DTD PLIST 1.0//EN"" ""http://www.apple.com/DTDs/PropertyList-1.0.dtd"">
         <plist version=""1.0"">
             <dict>
                 <key>aps-environment</key>
                 <string>production</string>
             </dict>
         </plist>";
    
    }
    #endif
```