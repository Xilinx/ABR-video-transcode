
<table style="width:100%">
  <tr>
    <th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>ABR Video Transcode</h2>
</th>
  </tr>
  <tr>
    <td align="center"><a href="README.md">1. Overview</a></td>
    <td align="center"><a href="vyusync-decoder.md">2. VYUsync Decoder</a></td>
    <td align="center"><a href="ngcodec-hevc-vp9-encoder.md">3. NGCodec HEVC and VP Encoder</a></td>
    <td align="center"><a href="xilinx-abr-scaler.md">4. Xilinx ABR Scaler</a></td>
    <td align="center"><a href="ffmpeg-integration.md">5. FFmpeg Integration</a></td>
    </tr>
    <tr>
    <td align="center"><a href="system-requirements.md">6. System Requirements</a></td>
    <td align="center">7. Installation and Getting Started</td>
    <td align="center"><a href="using-ffmpeg-with-xilinx.md">8. Using FFmpeg with Xilinx Accelerated Video Transcoding</a></td>
    <td align="center"><a href="known-issues-limitations.md">9. Known Issues and Limitations</a></td>
    <td align="center"><a href="additional-resources.md">10. Additional Resources</td>
  </tr>
</table>

# Installation and Getting Started

The required package files for setting up the ABR video transcoding evaluation and the U200 accelerator card can be found at the link [here](https://www.xilinx.com/products/boards-and-kits/alveo/applications/adaptive-bit-rate-video-transcoding-application.html#gettingStarted). Read the [Getting Started Guide for U200 and U250 Cards](https://www.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf) for the installation procedure for the card as well as the Xilinx Runtime.

After you have successfully installed the Xilinx Runtime and U200 deployment package, and downloaded the ABR video transcoding evaluation package, follow the steps below to install the package on your system.

<details><summary><b>Ubuntu Installation Guide</b></summary>

## Ubuntu Installation Guide

1. Unzip and untar the video transcoding tarball:

    `tar -xvzf xcdr_deb_pkgs.tar.gz`

2. Edit `/etc/apt/sources.list` to add the directory where the packages are located. You need **sudo** access for this:

    `deb file:/home/user/xcdr_pkgs ./`

3.  Run this command after changing `/etc/apt/sources.list`:

      `sudo apt-get update`

4. Install the downloaded packages with a single command line:

    `sudo apt-get install xcdr`

>**:pushpin: NOTE** On Nimbix you will also need to install the following `sudo apt-get install libva-drm1`.
</details>

<details><summary><b>RHEL/CentOS Installation Guide</b></summary>

## RHEL/CentOS Installation Guide

1. Unzip and untar the video transcoding tarball:

    `tar -xvzf xcdr_rpm_pkgs.tar.gz`

2. To add a local yum repository to the list of repositories, the `/etc/yum.repos.d` directory must be updated. Create a file called `localrepo.repo` that contains the following configuration:

	```
	[localrepo]
	name=Xilinx Transcoder Repository
	baseurl=file:///home/user/xcdr_pkgs
	gpgcheck=0
	enabled=1
	```

3. Install the downloaded packages with the following command line:

    `sudo yum install xcdr`
</details>

<br>

The above steps install the ABR video transcoding evaluation package and all its dependencies. The transcoding package has dependencies (among others) on the Xilinx Runtime, the DSA for the U200 card, and FFmpeg. All packages are installed under the `/opt/xilinx/` directory.

```console
.
├── dsa
│   └── xilinx_u200_xdma_201820_1
├── ffmpeg
│   ├── bin
│   ├── etc
│   ├── include
│   ├── lib
│   └── share
├── xcdr
│   ├── bin
│   ├── scripts
│   └── xclbins
├── xma
│   └── plugins
└── xrt
    ├── bin
    ├── include
    ├── lib
    ├── license
    └── share
```

 As a final step of the installation, execute the following command:

`source /opt/xilinx/xcdr/setup.sh`

This includes `/opt/xilinx/ffmpeg/bin` and `/opt/xilinx/xcdr/bin` in your path, and adds `/opt/xilinx/ffmpeg/lib` and `/opt/xilinx/xma/lib` to the `LD_LIBRARY_PATH`.

When the installation is complete, you are almost ready to start using FFmpeg with the Xilinx accelerated video transcoding functionality. The package does not come with any sample H.264 video files, but you can download a copy of **Big Buck Bunny** from the following [link](http://distribution.bbb3d.renderfarming.net/video/mp4/bbb_sunflower_1080p_30fps_normal.mp4).


:arrow_backward:**Previous Topic:**  [6. System Requirements](system-requirements.md)

:arrow_forward:**Next Topic:**  [8. Using FFmpeg with Xilinx Accelerated Video Transcoding](using-ffmpeg-with-xilinx.md)
