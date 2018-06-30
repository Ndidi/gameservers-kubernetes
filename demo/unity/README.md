Clone this repo:

```
git clone https://github.com/JoaoBorks/unity-fastpacedmultiplayer
```

Add the following code to:

`Assets/_Project/Editor/Tools`

```cs
public static void QuickBuildMenuLinux()
{
    BuildPlayerOptions opts = new BuildPlayerOptions
    {
        options = BuildOptions.Development & BuildOptions.EnableHeadlessMode,
        locationPathName = "/path/to/binary/server",
        target = BuildTarget.StandaloneLinux64
    };

    BuildPipeline.BuildPlayer(opts);
}
```

Then execute to build binary:

```
/Applications/Unity/Unity.app/Contents/MacOS/Unity -quit -batchmode -nographics -logFile .unity-build.log -projectPath $(pwd)/unity-fastpacedmultiplayer -executeMethod BuildTools.QuickBuildMenuLinux
```

Build Docker image:

```
docker build . -t mofirouz/unity-fastpacedmultiplayer:0.0.1
docker push mofirouz/unity-fastpacedmultiplayer:0.0.1
```
