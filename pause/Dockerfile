FROM mcr.microsoft.com/windows/servercore:ltsc2019

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ARG CONTAINERD_VERSION="1.7.13"
ARG BUILDKIT_VERSION="v0.17.1"
ARG ARCH="amd64"



RUN Invoke-WebRequest -Uri "https://github.com/containerd/containerd/releases/download/v$CONTAINERD_VERSION/containerd-$CONTAINERD_VERSION-windows-$Arch.tar.gz" -OutFile ".\containerd-$CONTAINERD_VERSION-windows-$Arch.tar.gz"
RUN Expand-Archive -Path ".\containerd-$CONTAINERD_VERSION-windows-$Arch.tar.gz" -DestinationPath ".\bin"

RUN Copy-Item -Path .\bin -Destination $Env:ProgramFiles\containerd -Recurse -Force
ENV Path = [Environment]::GetEnvironmentVariable("PATH", "Machine") + [IO.Path]::PathSeparator + "$Env:ProgramFiles\containerd"
RUN [Environment]::SetEnvironmentVariable( "Path", $Path, "Machine")
RUN $Env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
RUN containerd.exe config default | Out-File $Env:ProgramFiles\containerd\config.toml -Encoding ascii
RUN Get-Content $Env:ProgramFiles\containerd\config.toml
RUN containerd.exe --register-service
# RUN Start-Service containerd

## Install buildkitd
RUN Invoke-WebRequest -Uri "https://github.com/moby/buildkit/releases/download/$BUILDKIT_VERSION/buildkit-$BUILDKIT_VERSION.windows-$ARCH.tar.gz" -OutFile "buildkit-$BUILDKIT_VERSION.windows-$ARCH.tar.gz"
RUN Expand-Archive -Path ".\buildkit-$BUILDKIT_VERSION.windows-$ARCH.tar.gz" -DestinationPath ".\bin"

RUN Copy-Item -Path ".\bin" -Destination "$Env:ProgramFiles\buildkit" -Recurse -Force
RUN $Path = [Environment]::GetEnvironmentVariable("PATH", "Machine") + [IO.Path]::PathSeparator + "$Env:ProgramFiles\buildkit"
RUN [Environment]::SetEnvironmentVariable( "Path", $Path, "Machine")
RUN $Env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
RUN Remove-Item .\containerd-$CONTAINERD_VERSION-windows-amd64.tar.gz, .\buildkit-$BUILDKIT_VERSION.windows-$ARCH.tar.gz
# CMD buildkitd.exe