# MediaStreaming - LiveVideoStreaming

Repository for the LiveVideoStreaming of Media Streaming Module (T3.3).

### Live Video Streaming:
This functionality allows the user to stream a live video from the device camera to the Personal Storage via RTMP (Real Time Messaging Protocol). To use this capability, an RTMP server is needed. 
To start the video streaming (e.g. using the MediaStreaming App to call this extension) it is necessary to introduce the RTMP url like this: `rtmp://$IP_of_server:1935/$App/$stream_name`.

MediaStreaming App: https://github.com/helios-h2020/h.app-MediaStreaming

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-LiveVideoStreaming/master/doc/mediastreaming_rtmp.png" alt="Launch streaming from the App">

After, we can start the streaming

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-LiveVideoStreaming/master/doc/start_streaming.png" alt="Starting Streaming from the App">

And while is running we can stop or switch the camera.

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-LiveVideoStreaming/master/doc/stop_streaming.png" alt="Manage streaming">

To show the stream generated, we can use the VideoPlayer extension included in the MediaStreaming App
https://github.com/helios-h2020/h.extension-MediaStreaming-VideoPlayer

For this is needed to use an url like `http://$IP_of_server:800/hls/stream_name.m3u8` 

### How to use Live Video Streaming:

This module generates a .aar file to be included in your applications as a dependency. See more details at Multiproject dependencies chapter.

To call the extension from your application, include in your activity the stream URL.
```        
        Intent liveVideoStreamingIntent = new Intent(MainActivity.this, LiveVideoStreamingActivity.class);
        liveVideoStreamingIntent.putExtra("stream_url", "rtmp://1.1.1.1:1935/helios/stream_name");
        MainActivity.this.startActivity(liveVideoStreamingIntent);
```

### Request permissions
Before start activity of the live video streaming intent from the activity of your application:
```
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED
                || ContextCompat.checkSelfPermission(this, Manifest.permission.RECORD_AUDIO) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CAMERA, Manifest.permission.RECORD_AUDIO}, ALL_PERMISSIONS_CODE);
        }
```

### How to Develop

- Install Git in your computer: https://github.com/git-guides/install-git

- Choose a directory from your computer and download with Git the code using the link provided in this page:

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-LiveVideoStreaming/master/doc/github.PNG" alt="LiveStream github">

`git clone https://github.com/helios-h2020/h.extension-MediaStreaming-LiveVideoStreaming.git`

- Open Android Studio and open an existing project from the directory of your code downloaded. To install Android Studio follow the next link: https://developer.android.com/studio/install)

- To generate an aar file from the code, select the Build option in the Menu Bar, select ReBuild project or choose Make Project icon as you can see in the picture. Once generated, you can find the file in app/build/options/aar (you can rename the file as you like):

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-LiveVideoStreaming/master/doc/build.PNG" alt="Build aar">


## Multiproject dependencies ##

HELIOS software components are organized into different repositories
so that these components can be developed separately avoiding many
conflicts in code integration. However, the modules also depend on
each other.

### How to configure the dependencies ###

To manage project dependencies developed by the consortium, the approach proposed is to use a private Maven repository with Nexus.

To avoid clone all dependencies projects in local, to compile the "father" project. Otherwise, a developer should have all the projects locally to
be able to compile. Using Nexus, the dependencies are located in a remote repository, available to compile, as described in the next section.
Also to improve the automation for deploy, versioning and distribution of the project.

### How to use the HELIOS Nexus ###

Similar to other dependencies available in Maven Central, Google or others repositories. In this case we specify the Nexus
repository provided by Atos: `https://registry.helios.ari-imet.eu/repository/helios-repository/`

This URL makes the project dependencies available.

To access, we simply need credentials, that we will define locally in the variables `heliosUser` and `heliosPassword`.

The `build.gradle` of the project define the Nexus repository and the credential variables in this way:

```
repositories {
        ...
        maven { url 'https://jitpack.io' } // for the import of needed modules
        maven {
            url "https://registry.helios.ari-imet.eu/repository/helios-repository/"
            credentials {
                username = heliosUser
                password = heliosPassword
            }
        }
    }
```

And the variables of Nexus's credentials are stored locally at `~/.gradle/gradle.properties`:

```
heliosUser=username
heliosPassword=password
```

To request Nexus username and password, contact with: `francesco.dandria@atos.net`

### How to deploy a new version of the dependencies ###

Let's say that we want to deploy a new version of the videocall project. This project is a dependency of MediaStreaming.
For Continuous Integration we use Jenkins. It deploys the configured projects (e.g., videocall) in different jobs,
and the results are libraries packaged like AAR (Android ARchive). These packaged libraries are upload to Nexus and in this way,
they are available to build the projects that depend on them (e.g., MediaStreaming).
In the videocall example, Jenkins jobs generate automatically and aar library and store it at the Nexus repository to make it available.

Jenkins is the tool deployed by Atos (WP6 leader) in HELIOS to automate the generation of APKs, joining all the project modules.
Due to the need of managing the dependencies, Atos has selected additional tools, as explained in this document.

After pushing a change to the `master` branch, the maintainer can builds the module by means of the job in the Jenkins interface. GitLab repositories are set to protect
the `master` branch push and merge for the partner in charge of its module/project (maintainer).

To request Jenkins username and password, contact with: `francesco.dandria@atos.net`

### How to use the dependencies ###

To use the dependency in `build.gradle` of the "father" project, you should specify the last version available in Nexus, related to the last Jenkins's deploy.
For example, to declare the dependency on the videocall module and the respective version:

`implementation 'eu.h2020.helios_social.modules.livevideostreaming:livevideostreaming:1.0.21'`

For more info review: `https://scm.atosresearch.eu/ari/helios_group/generic-issues/blob/master/multiprojectDependencies.md`

### How to use the dependencies locally ###

If you want to include the .aar file generated as a dependency in the application whitout use Nexus dependencies:

- Go to your application code and create libs folder inside app folder:

<img src="https://raw.githubusercontent.com/helios-h2020/h.app-MediaStreaming/master/doc/libs.PNG" alt="libs folder">

- Open build.gradle at Project level and add flatDir{dirs 'libs'} :

<img src="https://raw.githubusercontent.com/helios-h2020/h.app-MediaStreaming/master/doc/libs_gradle.PNG" alt="Project build.gradle">

```
allprojects {
   repositories {
      jcenter()
      flatDir {
        dirs 'libs'
      }
   }
}
```

- Open build.gradle at app level and add .aar file:

<img src="https://raw.githubusercontent.com/helios-h2020/h.app-MediaStreaming/master/doc/gradle_app.PNG" alt="app build.gradle">

```
dependencies {
     compile(name:'file_name', ext:'aar')
}
``` 

### LiveStreaming module storage

The module implements a local storage system to generate and consume the live stream.

See more info at: https://github.com/helios-h2020/h.core-PersonalStorageElements

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-LiveVideoStreaming/master/doc/livestreaming_storage.png" alt="LiveStreaming local storage implementation">
