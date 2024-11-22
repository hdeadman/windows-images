FROM mcr.microsoft.com/windows/servercore:ltsc2019

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ARG CONTAINERD_VERSION="1.7.13"
ARG BUILDKIT_VERSION="v0.17.1"
ARG ARCH="amd64"

#ENV ARCH=${ARCH}
#ENV CONTAINERD_VERSION=${CONTAINERD_VERSION}
#ENV BUILDKIT_VERSION=${BUILDKIT_VERSION}

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop';"]
RUN Invoke-WebRequest -Uri "https://github.com/containerd/containerd/releases/download/v$($env:CONTAINERD_VERSION)/containerd-$($env:CONTAINERD_VERSION)-windows-$($env:ARCH).tar.gz" -OutFile ".\containerd-$($env:CONTAINERD_VERSION)-windows-$($env:ARCH).tar.gz"
RUN mkdir /bin
RUN tar -xzf ".\containerd-$($env:CONTAINERD_VERSION)-windows-$($env:ARCH).tar.gz" -C ".\bin"

RUN Copy-Item -Path .\bin -Destination $Env:ProgramFiles\containerd -Recurse -Force
RUN $Env:NewPath = [Environment]::GetEnvironmentVariable('PATH', 'Machine') + [IO.Path]::PathSeparator + '$Env:ProgramFiles\containerd'
RUN [Environment]::SetEnvironmentVariable( 'PATH', $Env:NewPath, 'Machine')
#RUN $Env:Path = [Environment]::GetEnvironmentVariable('PATH','Machine')
RUN $Env:ProgramFiles\containerd\containerd.exe config default | Out-File $Env:ProgramFiles\containerd\config.toml -Encoding ascii
RUN Get-Content $Env:ProgramFiles\containerd\config.toml
RUN $Env:ProgramFiles\containerd\containerd.exe --register-service
# RUN Start-Service containerd

## Install buildkitd
RUN Remove-Item -Recurse -Force /bin
RUN mkdir /bin
RUN Invoke-WebRequest -Uri "https://github.com/moby/buildkit/releases/download/$($env:BUILDKIT_VERSION)/buildkit-$($env:BUILDKIT_VERSION).windows-$($env:ARCH).tar.gz" -OutFile "buildkit-$($env:BUILDKIT_VERSION).windows-$($env:ARCH).tar.gz"
RUN tar -xzf ".\buildkit-$($env:BUILDKIT_VERSION).windows-$($env:ARCH).tar.gz" -C ".\bin"

RUN Copy-Item -Path ".\bin" -Destination "$Env:ProgramFiles\buildkit" -Recurse -Force
RUN $Env:NewPath = [Environment]::GetEnvironmentVariable('PATH', 'Machine') + [IO.Path]::PathSeparator + '$Env:ProgramFiles\buildkit'
RUN [Environment]::SetEnvironmentVariable( 'PATH', $Env:NewPath, 'Machine')

RUN Remove-Item .\containerd-$($env:CONTAINERD_VERSION)-windows-amd64.tar.gz, .\buildkit-$($env:BUILDKIT_VERSION).windows-$($env:ARCH).tar.gz
# CMD buildkitd.exe