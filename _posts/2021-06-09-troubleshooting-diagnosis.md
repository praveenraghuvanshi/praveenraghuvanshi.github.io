---

layout: post
comments: true
title: "Troubleshooting and Diagnosing Issues"
category: Troubleshooting

---

# Troubleshooting and Diagnosing Issues

<div align="center">
  <img src="/images/troubleshooting-diagnosis/troubleshooting.png" alt="Troubleshooting" style="zoom:80%;" />
</div>

## Issue 1

#### Problem: Tool 'microsoft.dotnet-interactive' failed to install.

I was trying to install dotnet-interactive on my Windows Machine by following steps in [Using .NET notebooks on your machine](https://github.com/dotnet/interactive/blob/main/docs/NotebooksLocalExperience.md) and below error reported

<div align="center">
  <img src="/images/troubleshooting-diagnosis/dotnet-interactive-failed-to-install.png" alt=".Net Interactive failed to install" style="zoom:80%;" />
</div>



#### Solution

Delete 'Nuget.Config' file located at 'C:\Users\<YOUR_USERNAME>\' and retry the installation, it should work.

<div align="center">
  <img src="/images/troubleshooting-diagnosis/dotnet-interactive-success.png" alt=".Net Interactive Successful installation" style="zoom:80%;" />
</div>


---

## Issue 2

#### Problem: EntryPointNotFoundException: Unable to find an entry point named 'OrtGetApiBase' in DLL 'onnxruntime'.

<div align="center">
  <img src="/images/troubleshooting-diagnosis/onnxruntime-error.png" alt="Onnx runtime exception installation" style="zoom:80%;" />
</div>

#### Solution

Add a reference to Microsoft.ML.OnnxRuntime nuget package.

https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/

---

## Issue 3

### Problem:  0% [Connecting to archive.ubuntu.com] 

<div align="center">
  <img src="/images/troubleshooting-diagnosis/wsl-connecting-to-internet.png" alt="WSL Internet connection issue" style="zoom:80%;" />
</div>

Internet not working in WSL

### Solution

Run command ```echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null```

