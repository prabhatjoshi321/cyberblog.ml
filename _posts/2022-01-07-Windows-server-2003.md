---
title: Windows Server 2003 (NT 5.2 / 3790) guide
author:
  name: Wrathdemon
  link: https://github.com/prabhatjoshi321
date: 2022-01-07 02:40:00 +0800
categories: [Tutorial, Instructions, Notes]
tags: [writing]    # TAG names should always be lowercase
---


# Windows Server 2003 (NT 5.2 / 3790) guide

## Preparing Environment
---

- Extract source tree, in this guide we will assume `C:\NT` (if you wish for your binaries to match RTM as closely as possible, use `D:\srv03rtm`)
- Unset Read Only on extracted directory (including subfolders and files)
- Copy over files from [this ZIP](https://anonfiles.com/l0S54cc1pe/toolset_v3_zip) into source tree, overwriting existing files as necessary
- ***Manually*** import `\tools\driver.pfx` certificate and private key (if asked for password, simply continue without typing anything)
- Create desktop shortcut for `%windir%\system32\cmd.exe /k C:\NT\tools\razzle.cmd free offline` (see below for explanation) and change `Start in` to `C:\NT`
- Open razzle window using shortcut you created

## Building
---

### Clean build

Performs clean rebuild of all components:

  - `build /cZP`

### "Dirty" build

Builds only components that have changed since last clean build:

  - `build /ZP`

### Post-build

  - Mount RTM CD and execute `tools\missing.cmd` (optionally, perform this step for every SKU)
  - `tools\postbuild.cmd` (use `-sku:{sku}` if you want to process only specific one, expect `filechk` errors if you ignore this and have skipped on `missing.cmd` optional step)

## Getting ISO files

  - Execute `tools\oscdimg.cmd {sku}` where `{sku}` is one of:
    - `srv` - Windows Server 2003 Standard Edition
    - `sbs` - Windows Server 2003 Small Business Edition
    - `ads` - Windows Server 2003 Enterprise Edition
    - `dtc` - Windows Server 2003 Datacenter Edition
    - `bla` - Windows Server 2003 Web Edition
    - `per` - Windows XP Home Edition
    - `pro` - Windows XP Professional

## Additions
---

### Timebomb

- Can be enabled or disabled by commenting or uncommenting `timebomb.cmd` entry in `\tools\postbuildscripts\pbuild.dat`
- If enabled, time can be adjusted by editing `\tools\postbuildscripts\timebomb.cmd`

### Different build options

You can modify your razzle shortcut (or execute command manually inside `C:\NT`) to include (or remove) additional argument(s):

- `free` - build 'free' bits (production, ommiting it will generated checked bits)
- `CkhKernel` - build 'checked' (testing) kernel/hal/ntdll when building 'free' bits
- `no_opts` - disable binary optimization (useful for debugging, but will most likely fail a full build, some code can't be built without optimization)
- `verbose` - enable verbose execution of the build process
- `binaries_dir <basepath>` - specifies custom output directory (default is `binaries`, the suffix added after `.` is non-customizable)

Other options are not described here, see `razzle.cmd /?` for details.

### Creating fresh postbuild

- `tools\postbuild.cmd -full`
- `tools\missing.cmd`
- `tools\postbuild.cmd`

Use `-sku:{sku}` if you want to process only specific one

### Building specific components

Most components can be built seperately. For example, if you wish to rebuild `ntos` component, perform these steps:
 - `cd base\ntos` (you can also use `ntos` alias that razzle has set up for you)
 - `bcz`

Generally `postbuild.cmd` is clever enough to include your changes properly without needing fresh build as it uses `bindiff` to find differences.

### Generating new build number/name

Version information is stored in `\public\sdk\inc\ntverp.h`

You can also use `nmake set_builddate set_buildnum set_buildname -f makefil0` to generate new build name quickly.

### Original CD filenames

- `5.2.3790.0.srv03_rtm.030324-2048_x86fre_server-standard_retail_en-us-NRMSFPP_EN.iso` (SHA1: A600409482A5678EF6AF2B26D3576D6D9894178D)
- `5.2.3790.0.srv03_rtm.030324-2048_x86fre_server-datacenter_retail_en-us-NRMDOEM_EN.iso` (SHA1: E2B47A7CE45C6C6305594CEE4C1B64894805AAF4)
- `5.2.3790.0.srv03_rtm.030324-2048_x86fre_server-enterpriseserver_retail_en-us-NRMEFPP_EN.iso` (SHA1: 0309FFB4181BA5122C692A6E1079E9FC1D53FCE4)
- `5.2.3790.0.srv03_rtm.030324-2048_x86fre_server-webserver_retail_en-us-NRMWFPP_EN.iso` (SHA1: 46C1CCB2CFC96803E304A35BEF50CD71B2C1DE38)
- `5.1.2600.0.xpclient.010817-1148_x86fre_client-home_retail_en-us-WXHFPP_EN.iso` (SHA1: B273C8D41E3844E3E46722F52F5A4CF9F206C8D0)
- `5.1.2600.0.xpclient.010817-1148_x86fre_client-professional_retail_en-us-WXPFPP_EN.iso` (SHA1: 1400DED4402D50F3864ED3D8DCF5CC52BA79A04A)
- `sbs.iso` (coverted from mdf; SHA1: CDB30C80FDE314C16CA11F5CD31650ECBEC7A214)

### Product keys

- Standard Edition: M6RJ9-TBJH3-9DDXM-4VX9Q-K8M8M
