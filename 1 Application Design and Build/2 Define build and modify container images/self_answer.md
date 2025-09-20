Task 1:
```bash
docker build -f Buildfiles/Containerfile1 -t ckad:pluralsight ./App
```

Task 2:
```bash
docker save ckad:pluralsight | gzip > ckad-image.zip
 ```

Task 3:
```bash
docker tag ckad:pluralsight nigelpoulton/ckad:prod
docker rmi ckad:pluralsight
```

Task 4:
```bash
docker build -f Buildfiles/Dockerfile.dev -t ckad:ps1 ./App2
```

Task 5:
```bash
docker build -f Buildfiles/Dockerfile -t internal-reg.io/dev-fe/ckad:ps2 ./App2
```

Task 6:
```bash
docker pull alpine:3.15.4
```

Task 7:
```bash
docker save internal-reg.io/dev-fe/ckad:ps2 -o ckad-ps2.tar
```

Task 8:
```bash
docker rmi alpine:3.15.4
```

Task 9:
```bash
docker build -f Buildfiles/buildfile.ckad -t internal-reg.io/ckad-dev/ckad:ps3  ./dev/ckad
```