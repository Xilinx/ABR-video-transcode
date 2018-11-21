
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
    <td align="center"><a href="using-ffmpeg-with-xilinx.md">8. Using FFmpeg with Xilinx Accelerated Video Transcoding</a></td>
    <td align="center">9. Known Issues and Limitations</td>
    <td align="center"><a href="additional-resources.md">10. Additional Resources</td>
  </tr>
</table>

# Known Issues
* Bit rates higher than 20 Mbps may hang the VP9 encoder. 

# Limitations
* The Xilinx ABR video transcode package is provided for evaluation purposes only. The encoded video streams are watermarked with the NGCodec logo.
* Although the video transcode solution can scale to cloud scale, with up to 16 cards per server, the evaluation version is limited to a single accelerator card only.


:arrow_backward:**Previous Topic:**  [8. Using FFmpeg with Xilinx Accelerated Video Transcoding](using-ffmpeg-with-xilinx.md)
:arrow_forward:**Next Topic:**  [10. Additional Resources](additional-resources.md)
