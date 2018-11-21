
<table style="width:100%">
  <tr>
    <th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>ABR Video Transcode (UG1311)</h2>
</th>
  </tr>
  <tr>
    <td align="center"><a href="README.md">1. Overview</a></td>
    <td align="center"><a href="vyusync-decoder.md">2. VYUsync Decoder</a></td>
    <td align="center"><a href="ngcodec-hevc-vp9-encoder.md">3. NGCodec HEVC and VP Encoder</a></td>
    <td align="center">4. Xilinx ABR Scaler</td>
    <td align="center"><a href="ffmpeg-integration.md">5. FFmpeg Integration</a></td>
    </tr>
    <tr>
    <td align="center"><a href="system-requirements.md">6. System Requirements</a></td>
    <td align="center"><a href="installation-and-getting-started.md">7. Installation and Getting Started</a></td>
    <td align="center"><a href="using-ffmpeg-with-xilinx.md">8. Using FFmpeg with Xilinx Accelerated Video Transcoding</a></td>
    <td align="center"><a href="known-issues-limitations.md">9. Known Issues and Limitations</a></td>
    <td align="center"><a href="additional-resources.md">10. Additional Resources</td>
  </tr>
</table>

# Xilinx ABR Scaler
![](./images/xilinx-logo-red-black.png)

For streaming applications, video is distributed in different resolutions and bit rates to adapt to varying network bandwidth conditions. All ABR transcoding systems require an ABR scaler that downscales an input video stream to several different smaller resolutions that are then reencoded. These smaller resolutions are referred to as an image pyramid or an ABR ladder.

The Xilinx ABR scaler is an accelerator capable of generating up to eight lower resolution output images from a single input image. The ABR resolution ladder is generated in a sequential fashion: a smaller resolution on the ladder is derived from the next larger resolution on the ladder. The video scaler core resamples the incoming video data stream using a separable polyphase horizontal and vertical filter to preserve hardware resources. The multiple resolution outputs are obtained by feeding previous output as input to the next stage in a cascading manner and applying tailored horizontal and vertical filter coefficients at each stage.

In the ABR ladder below, a 1280x720, 852x480, 640x360, and 416x240 output are created from a 1920x1080 input.

![ABR Ladder](./images/abr-ladder.png)

The scaler takes the 1920x1080 input and scales it down to 1280x720. It then takes the generated 1280x720 output and scales it down to 852x480, which is then scaled down to 640x360, which is finally scaled down to 416x240. The ABR scaler generates all the output resolutions without any host intervention.

## Features

*  	Supports up to 12 taps in both horizontal and vertical direction per stage.
*  	High-quality polyphase scaling with 64 phases, with quality matching the FFmpeg default bicubic setting.
*  	Dynamically configurable filter coefficients.
*  	Supports 8-bit 4:2:0.
*  	Luma and Chroma processed in parallel.
* 	Supports 1080p60 real time or equivalent distributed between up to eight outputs.
*  	Supports even spatial resolutions from 256x144 to 1920x1080.
* 	Although the main use case is downscaling, the ABR scaler also allows for upscaling. The outputs must be configured in descending order.

For more information, write to sean.gardner@xilinx.com.

:arrow_backward:**Previous Topic:**  [3. NGCodec HEVC and VP Encoder](ngcodec-hevc-vp9-encoder.md)

:arrow_forward:**Next Topic:**  [5. FFmpeg Integration](ffmpeg-integration.md)
