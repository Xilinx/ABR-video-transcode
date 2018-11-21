
<table style="width:100%">
  <tr>
    <th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>ABR Video Transcode (UG1311)</h2>
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
    <td align="center"><a href="installation-and-getting-started.md">7. Installation and Getting Started</a></td>
    <td align="center">8. Using FFmpeg with Xilinx Accelerated Video Transcoding</td>
    <td align="center"><a href="known-issues-limitations.md">9. Known Issues and Limitations</a></td>
    <td align="center"><a href="additional-resources.md">10. Additional Resources</td>
  </tr>
</table>

# Using FFmpeg with Xilinx Accelerated Video Transcoding Functionality


This section will walk you through a sampling of FFmpeg command lines that show how to use the Xilinx accelerated video transcoding functionality. Take a look at the following example command lines.  

<details>
<summary><b>Example 1: Running the Xilinx Accelerated H.264 Decoder</b></summary>

## Example 1: Running the Xilinx Accelerated H.264 Decoder

Make sure to configure the device for either VP9 or HEVC encoding. In this case, configure the device for HEVC transcode acceleration with the following command:

`xcdrctl -p HEVC -b U200`

The `xcdrctl` command is a simple Python application, located under `/opt/xilinx/xcdr/bin`. Executing this application writes the configuration file for the HEVC transcoding accelerators in `/var/tmp/xmacfg.yaml`, and subsequently downloads the xclbin to the device.

With the device programmed, you can now decode an elementary H.264 bitstream using the Xilinx accelerated decoder as follows:

`ffmpeg -y -c:v VYUH264 -i input.h264 -vsync 0 output.yuv`

`-c:v VYUH264` preceding the input file indicates that you are using the H.264 Xilinx accelerated decoder to decode the H.264 encoded elementary bitstream. The resulting decoded frames are written as raw video to the output file.

>**:pushpin: NOTE** You can find sample FFmpeg commands in `/opt/xilinx/xcdr/scripts/`.

</details>

<details>
<summary><b>Example 2: Running the Xilinx Accelerated Scaler</b></summary>

## Example 2: Running the Xilinx Accelerated Scaler

The following command line shows how to scale the 1920x1080 uncompressed input frames to 1280x720:

 ```bash
ffmpeg -f rawvideo -pix_fmt yuv420p -s:v 1920x1080 -i input.yuv \
-filter_complex "scale_xma=1: out_1_width=1280:out_1_height=720:[a]" \
-map '[a]' -frames 2000 -f rawvideo -pix_fmt yuv420p -y out.yuv
```

In this evaluation package, the scaler can generate up to four scaled renditions of a single input at a time. This is shown in the command line below:

 ```bash
ffmpeg -f rawvideo -pix_fmt yuv420p -s:v 1920x1080 -i input.yuv  \
-filter_complex "scale_xma=4: \
out_1_width=1280:out_1_height=720: \
out_2_width=848:out_2_height=480: \
out_3_width=640:out_3_height=360: \
out_4_width=424:out_4_height=240[a][b][c][d]" \
-map '[a]' -f rawvideo -pix_fmt yuv420p -y out1.yuv \
-map '[b]' -f rawvideo -pix_fmt yuv420p -y out2.yuv \
-map '[c]' -f rawvideo -pix_fmt yuv420p -y out3.yuv \
-map '[d]' -f rawvideo -pix_fmt yuv420p -y out4.yuv
```

In the above command line, `-filter_complex "scale_xma...[a][b][c][d]"` scales the input frames to an image pyramid of 1280x720, 848x480, 640x360, and 424x240 using the Xilinx ABR scaler. Each of the scaled outputs `[a][b][c][d]` can then, using the `-map '[a]'` command, be referred to individually and written to four output files. `-f rawvideo -pix_fmt yuv420p` indicates that the input frames are raw video, formatted as `yuv420p`, which is a planar YUV 4:2:0 video format. `-s:v 1920x1080` indicates that the resolution of the uncompressed input frames is 1920x1080.
</details>

<details>
<summary><b>Example 3: Running the Xilinx Accelerated HEVC Encoder</b></summary>

## Example 3: Running the Xilinx Accelerated HEVC Encoder

Using the command line below, you can run the Xilinx accelerated HEVC encoder to encode the 1280x720 scaled rendition into an HEVC elementary bitstream.

`ffmpeg -i input.yuv -frames 240 -c:v NGC265 -y out.hevc`

`-c:v NGC265` preceding the output file indicates that you are encoding the raw video using the NGCodec HEVC Xilinx accelerated encoder to an HEVC elementary bitstream file. The standard method for getting the supported options and information about an encoder from FFmpeg is to issue the following command:

`ffmpeg –h encoder=NGC265`

To find the list of available encoders, issue the command:

`ffmpeg --codecs`

In the case of the Xilinx accelerated HEVC encoder, the results are as follows:

```console
Encoder NGC265 [NGCodec H.265 / HEVC]:
    General capabilities: delay threads
    Threading capabilities: auto
    Supported pixel formats: yuv420p
ngc265 AVOptions:
  -aq-mode           <int>        AQ method (from 0 to 1) (default 1)
  -rc-lookahead      <int>        Number of frames to look ahead for frametype and ratecontrol (from 8 to 64) (default 30)
  -idr-period        <int>        IDR Period (from 0 to INT_MAX) (default 0)
  -aq-temp-gain      <int>        Temporal AQ strength. Reduces blocking and blurring in flat and textured areas. (from 50 to 200) (default 100)
  -aq-spat-gain      <int>        Spatial AQ strength. Reduces blocking and blurring in flat and textured areas. (from 50 to 200) (default 100)
  -minQP             <int>        MIN QP for capped VBR (from -12 to 51) (default -12)
```

An overview of all the relevant parameters that control the picture quality of the encoder (including FFmpeg standard controls such as `-b`, and `-g`) is shown in the table below.

| Parameter Name | FFmpeg Command Option | Mininum to Maximum Value Range | Suggested Value |
| :------------------------ |:-------------| :-------| :-------|
| Fixed QP	| -q	| 0-51	| >=15 |
| Min QP        | -minQP | -12-51 | -12 | |
| Bit rate	| -b	| 100K-35M	| Depends on resolution|
| I-Period Interval	| -g	| 0-32767	| 0 |
| AQ Mode	| -aq-mode	| 0-1	| 1|
| Temporal AQ Gain	| -aq-temp-gain | 50-200	| 100|
| Spatial AQ Gain	| -aq-spat-gain	| 50-200	| 100|
| Lookahead Distance	| -rc-lookahead	| 8-30| 	30|
| IDR Period	| -idr-period| 	0-32767| 	0|
</details>

<details>
<summary><b>Example 4: Running Xilinx Accelerated Transcoding from H.264 to HEVC</b></summary>

## Example 4: Running Xilinx Accelerated Transcoding from H.264 to HEVC

As well as running all three accelerators in isolation, you can also put them together in a transcoding pipeline. To transcode a single H.264 encoded elementary bitstream file into an HEVC encoded bitstream file, use the following command line:

`ffmpeg -c:v VYUH264 -r 60 -i input.h264 -frames 100 -c:v NGC265 -g 60 -idr-period 60 -b:v 5000k -r 60 -y output.hevc`

`-c:v VYUH264` preceding the input file indicates that you are using the VYUSync H.264 Xilinx accelerated decoder to decode the H.264 encoded elementary bitstream. `-c:v NGC265` preceding the output file indicates that you are encoding the decoded bitstream using the NGCodec HEVC Xilinx accelerated encoder to an HEVC elementary bitstream file. You are using a bit rate target of 5,000 Kbps (or 5 Mbps) as indicated by `-b:v 5000k`. The IDR period is set to 60 frames with `-idr-period 60`, and the GOP length is set to 60 frames with `-g 60`.
</details>

<details>
<summary><b>Example 5: Running Xilinx Accelerated Transcoding from H.264 to HEVC Using ABR</b></summary>

## Example 5: Running Xilinx Accelerated Transcoding from H.264 to HEVC Using ABR

The following command shows how to transcode a 1920x1080 H.264 encoded elementary bitstream file into four lower resolution HEVC encoded bitstream files.

 ```bash
ffmpeg -c:v VYUH264 -i input.h264 \
-filter_complex "scale_xma=4: \
out_1_width=1280:out_1_height=720: \
out_2_width=848:out_2_height=480: \
out_3_width=640:out_3_height=360: \
out_4_width=424:out_4_height=240[a][b][c][d]" \
-map '[a]' -frames 2000 -c:v NGC265 -g 60 -idr-period 60 -b:v 3000k -r 60 -y out1.hevc \
-map '[b]' -frames 2000 -c:v NGC265 -g 60 -idr-period 60 -b:v 2000k -r 60 -y out2.hevc \
-map '[c]' -frames 2000 -c:v NGC265 -g 60 -idr-period 60 -b:v 1000k -r 60 -y out3.hevc \
-map '[d]' -frames 2000 -c:v NGC265 -g 60 -idr-period 60 -b:v 800k -r 60 -y out4.hevc
```

In the above command line, `-filter_complex "scale_xma...[a][b][c][d]"` is used to scale the decoded frame scales to an image pyramid of 1280x720, 848x480, 640x360, and 424x240 using the Xilinx ABR scaler. Each of the scaled outputs `[a][b][c][d]` can then be referred to individually using the `-map '[a]` command. Each of the outputs is encoded with its own parameters (such as bit rate, GOP length, and so on) to an HEVC elementary bitstream file.
</details>

<details>
<summary><b>Example 6: Running Xilinx Accelerated Transcoding from H.264 to VP9</b></summary>

## Example 6: Running Xilinx Accelerated Transcoding from H.264 to VP9

Now that you are switching from HEVC to VP9 encoding, configure the device for VP9 transcoding with the following command.

`xcdrctl -p VP9 -b U200`

This puts the configuration file for the VP9 transcoding accelerator in the appropriate location, and subsequently downloads and programs the xclbin to the device. If the device is not configured correctly, expect to see the following error:

```console
ERROR: Unable to allocate NGCVP9 encoder session
Error initializing output stream -- Error while opening encoder for output stream - maybe incorrect parameters such as bit_rate, rate, width or height
2018-08-31 13:30:14.729 ERROR    xmares No available kernels of type 'scaler' from vendor NGCodec
2018-08-31 13:30:14.729 ERROR    xmaencoder Failed to allocate free encoder kernel. Return code -3 Conversion failed!
```

With the device correctly configured, you can now transcode a single H.264 encoded elementary bitstream file into a VP9 encoded bitstream file using the following command line:

`ffmpeg -c:v VYUH264 -r 60 -i input.h264 -frames 100 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 5000k -r 60 -y output.vp9`

The above command line is identical to the command line used for HEVC transcoding, with the exception of indicating through `-c:v NGCVP9` that the NGCodec VP9 encoder is used for encoding the decoded frames.

The supported options for the Xilinx accelerated VP9 encoder can be queried with the following command:

`ffmpeg –h encoder=NGCVP9`

This shows the following results:

```console
Encoder NGCVP9 [NGCodec vp9 ]:
    General capabilities: delay threads
    Threading capabilities: auto
    Supported pixel formats: yuv420p
ngcvp9 AVOptions:
  -aq-mode           <int>        AQ method (from 0 to 1) (default 1)
  -rc-lookahead      <int>        Number of frames to look ahead for frametype and ratecontrol (from 8 to 64) (default 30)
  -idr-period        <int>        IDR Period (from 0 to INT_MAX) (default 0)
  -aq-temp-gain      <int>        Temporal AQ strength. Reduces blocking and blurring in flat and textured areas. (from 50 to 200) (default 100)
  -aq-spat-gain      <int>        Spatial AQ strength. Reduces blocking and blurring in flat and textured areas. (from 50 to 200) (default 100)
  -minQP             <int>        MIN QP for capped VBR (from -12 to 51) (default -12)
```
</details>

<details>
<summary><b>Example 7: Running Xilinx Accelerated Transcoding from H.264 to VP9 Using ABR</summary></b>

## Example 7: Running Xilinx Accelerated Transcoding from H.264 to VP9 Using ABR

The following command shows how to transcode a 1920x1080 H.264 encoded elementary bitstream file into four lower resolution VP9 encoded bitstream files.

 ```bash
ffmpeg -c:v VYUH264 -i input.h264 \
-filter_complex "scale_xma=4: \
out_1_width=1280:out_1_height=720: \
out_2_width=848:out_2_height=480: \
out_3_width=640:out_3_height=360: \
out_4_width=424:out_4_height=240[a][b][c][d]" \
-map '[a]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 3000k -r 60 -y out1.vp9 \
-map '[b]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 2000k -r 60 -y out2.vp9 \
-map '[c]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 1000k -r 60 -y out3.vp9 \
-map '[d]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 800k -r 60 -y out4.vp9
```

This command is again almost identical to the HEVC ABR transcoding command line. Again, `-filter_complex "scale_xma...[a][b][c][d]"` is used to scale the decoded frames to an image pyramid of 1280x720, 848x480, 640x360, and 424x240 using the Xilinx ABR scaler. Each of the scaled outputs `[a][b][c][d]` can then be referred to individually using the `-map '[a]` command. Each of the outputs is encoded with its own parameters (such as bit rate, GOP length, and so on) to a VP9 elementary bitstream file.
</details>

<details>
<summary><b>Example 8: Running Xilinx Accelerated Transcoding from H.264 to VP9 Using ABR (Keeping the Original Frame Size)</summary></b>

## Example 8: Running Xilinx Accelerated Transcoding from H.264 to VP9 Using ABR (Keeping the Original Frame Size)

The following command shows how to transcode a 1920x1080 H.264 encoded elementary bitstream file into four lower resolution VP9 encoded bitstream files while also transcoding the 1920x1080 source into a VP9 encoded bitstream.

 ```bash
ffmpeg -c:v VYUH264 -i input.h264 \
-filter_complex "split=2[a][temp]; \
[temp] scale_xma=4: \
out_1_width=1280:out_1_height=720: \
out_2_width=848:out_2_height=480: \
out_3_width=640:out_3_height=360: \
out_4_width=424:out_4_height=240[b][c][d][e]" \
-map '[a]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 5000k -r 30 -y out1.vp9 \
-map '[b]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 3000k -r 30 -y out2.vp9 \
-map '[c]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 2000k -r 30 -y out3.vp9 \
-map '[d]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 1000k -r 30 -y out4.vp9 \
-map '[e]' -frames 2000 -f rawvideo -c:v NGCVP9 -g 60 -idr-period 60 -b:v 800k -r 30 -y out5.vp9
```

This command is almost identical to the VP9 ABR transcoding command line. You are using `-filter_complex "split[a][temp]"` to split the uncompressed frames into two identical 1920x1080 streams. One of the streams goes to to `scale_xma` to be scaled to the image pyramid of 1280x720, 848x480, 640x360, and 424x240 using the Xilinx ABR scaler. Each of the scaled outputs `[b][c][d][e]` and the remaining split output `[a]` can then, using the `-map '[a]'` command, be referred to individually. Each of the outputs is encoded with its own parameters (such as bit rate, GOP length, and so on) to a VP9 elementary bitstream file. This will only work for frame rates up to 30 fps. Otherwise, the sum of resolutions to encode exceeds the equivalent of 1080p60, which is the maximum supported by the encoder. </details>

 <br>


 :arrow_backward:**Previous Topic:**  [7. Installation and Getting Started](installation-and-getting-started.md)

 :arrow_forward:**Next Topic:**  [9. Known Issues and Limitations](known-issues-limitations.md)
