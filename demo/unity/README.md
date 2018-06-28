```
/Applications/Unity/Unity.app/Contents/MacOS/Unity -quit -batchmode -nographics -logFile .unity-build.log -projectPath $(pwd)/unity-fastpacedmultiplayer -executeMethod BuildTools.QuickBuildMenuLinux
```

```
docker build . -t mofirouz/unity-fastpacedmultiplayer:0.0.1
docker push mofirouz/unity-fastpacedmultiplayer:0.0.1
```
