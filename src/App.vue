<template>
  <div></div>
</template>

<script>
import { VideoSDKMeeting } from "@videosdk.live/js-prebuilt";
export default {
  name: "App",
  data() {
    return {
      name: "Flavio",
    };
  },
  methods: {
    getToken: async function() {
      const ENDPOINT = "http://192.168.0.81:9000/"; // localhopst:9000
      try {
        const response = await fetch(`${ENDPOINT}get-token`, {
          method: "GET",
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
          },
        });
        const { token } = await response.json();
        return token;
      } catch (e) {
        console.log(e);
      }
    },
    getMeetingId: async function(token) {
      try {
        const VIDEOSDK_API_ENDPOINT = `https://api.zujonow.com/v1/meetings`;
        const options = {
          method: "POST",
          headers: {
            Authorization: token,
          },
        };
        const response = await fetch(VIDEOSDK_API_ENDPOINT, options)
          .then(async (result) => {
            const { meetingId } = await result.json();
            return meetingId;
          })
          .catch((error) => console.log("error", error));
        return response;
      } catch (e) {
        console.log(e);
      }
    },
  },
  mounted: async function() {
    const token = await this.getToken();
    const meetingId = await this.getMeetingId(token);
    if (meetingId) {
      let name = "Demo User";
      const videoMeetingSpecs = {
        micEnabled: true,
        webcamEnabled: true,
        name,
        meetingId,
        redirectOnLeave: window.location.href,
        chatEnabled: true,
        screenShareEnabled: true,
        pollEnabled: true,
        whiteBoardEnabled: true,
        participantCanToggleSelfWebcam: true,
        participantCanToggleSelfMic: true,
        raiseHandEnabled: true,
        token: token,
        containerId: null,
        recordingEnabled: true,
        recordingWebhookUrl: "https://www.videosdk.live/callback",
      };
      const videoMeeting = new VideoSDKMeeting();

      await videoMeeting.init(videoMeetingSpecs);
    }
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
