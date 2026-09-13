# ludus_vs_build_tools

Ansible role that performs a silent installation of Visual Studio 2022 Build Tools and the .NET SDK on Windows Server. Provides MSVC and .NET compilation capability for the MAAS Windows build runner.

## What it does

1. Downloads the VS Build Tools installer from Microsoft
2. Runs a silent install with the specified workloads and components
3. Installs the .NET SDK via Chocolatey
4. Adds MSBuild and MSVC tools to the system PATH
5. Cleans up installer files

## What gets installed

- **Visual Studio 2022 Build Tools** (MSVC compiler, linker, Windows SDK)
- **C/C++ desktop build workload** (VCTools)
- **.NET managed desktop build workload**
- **Windows 11 SDK (22621)**
- **.NET 8.0 SDK**

## Requirements

- Windows Server 2022
- Chocolatey package manager available
- Internet access during installation
- At least 10 GB free disk space

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_vs_build_tools_version` | `2022` | VS Build Tools version |
| `ludus_vs_build_tools_channel_url` | `https://aka.ms/vs/17/release/channel` | VS installer channel URL |
| `ludus_vs_build_tools_installer_url` | `https://aka.ms/vs/17/release/vs_buildtools.exe` | VS installer download URL |
| `ludus_vs_build_tools_install_dir` | `C:\BuildTools` | Installation directory |
| `ludus_vs_build_tools_workloads` | `[VCTools, ManagedDesktopBuildTools]` | VS workloads to install |
| `ludus_vs_build_tools_components` | `[Windows11SDK.22621, VC.Tools.x86.x64]` | Additional VS components |
| `ludus_dotnet_sdk_version` | `8.0` | .NET SDK version |
| `ludus_vs_build_tools_timeout` | `3600` | Install timeout in seconds |

## Required Ansible Collections

- `ansible.windows`
- `chocolatey.chocolatey`

## Example (Ludus range config)

```yaml
- vm_name: '{{ range_id }}-runner-win01'
  hostname: '{{ range_id }}-runner-win01'
  template: win2022-server-x64-template
  vlan: 99
  ip_last_octet: 7
  ram_gb: 8
  cpus: 4
  windows: {}
  roles:
    - whispergate.ludus_jenkins_agent_windows
    - whispergate.ludus_vs_build_tools
```

## Notes

- The VS Build Tools install takes 10-20 minutes depending on network speed
- Exit code 3010 (reboot required) is treated as success; the role does not reboot automatically
- The async timeout defaults to 1 hour to accommodate slow downloads

## License

BSD-2-Clause
