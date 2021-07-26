# Simple Low Latency Streaming Platform

## Features
* Sub-second end-to-end latency via WebRTC
* Configurable latency (down to 1 second) via LL-DASH
* Royalty free solution - based on open-source [OvenMediaEngine](https://www.ovenmediaengine.com/)
* RTMP ingest - broadcast with any capable software (i.e. OBS) or hardware (i.e. LiveU)
* WebRTC egress - plays in any [WebRTC capable browser](https://caniuse.com/?search=webrtc)
* MPEG-DASH (Chunked CMAF) egress - plays in a [variety](https://bitmovin.com/mpeg-dash-browser-support-device-compatibility) of browsers

## Setup

### Deploying the architecture
1. Sign in to the [AWS Management Console](https://aws.amazon.com/console)
2. Switch to the [AWS region](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) that is closest to your broadcast location
3. Click the button below to launch the CloudFormation template. Alternatively you can [download](template.yaml) the template and adjust it to your needs.

[![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home#/stacks/create/review?stackName=low-latency-streaming-platform&templateURL=https://lostshadow.s3.amazonaws.com/low-latency-streaming-platform/template.yaml)

4. Choose the instance type for your server; make your choice depending on how much you will need to stream (use figures [here](https://cloudonaut.io/ec2-network-performance-cheat-sheet/) as reference); you will be able to change the instance size later, or just discard the stack and create a new one with a different setup
5. Leave all other settings to default
6. Hit the `Create Stack` button. 
7. Wait for the `Status` to become `CREATE_COMPLETE`. Note that this may take **1-2 minutes** or more.
8. Under `Outputs`, notice the keys named `IngressEndpoint`, `LLHLSDemoPlayerUrl` and `WebRTCDemoPlayerUrl`; write these down for using later


### Testing your setup
1. Point your RTMP broadcaster (any of [these](https://support.google.com/youtube/answer/2907883) will work) to the RTMP `IngressEndpoint` output by CloudFormation above

	Note that, while some RTMP broadcasters require a simple URI, others (like [OBS Studio](https://obsproject.com)) require a **Server** and **Stream key**. In this case, split the URI above at the last *slash* character, as following:
	
	**Server**: `rtmp://[HOST]/app`  
	**Stream key**: `stream001`

	Also note that **the enpoint may not be ready** as soon as CloudFormation stack is complete; it may take a couple minutes more for the server software to be compiled, installed and started on the virtual server so be patient
2. Open the LL-HLS test player URL output by CloudFormation above as `LLHLSDemoPlayerUrl` to view the LL-HLS stream; you may experiment with different delay settings (2 seconds by default) by adjusting the `LL-DASH Live Delay` value
3. Open the WebRTC test player URL output by CloudFormation above as `WebRTCDemoPlayerUrl` to view the WebRTC stream


### Integration

For WebRTC playback, you'll have to set up and integrate the free OvenPlayer [here](https://github.com/AirenSoft/OvenPlayer). The same OvenPlayer can be used for LL-DASH, or any other compatible player.

## Notes
* POC only uses a single stream, namely `stream1`; you can freely use another name, also run multiple streams simultaneously
* The CloudFormation template creates a VPC with the internal IP range `10.132.0.0/16`; be sure to change this if it overlaps with other VPCs