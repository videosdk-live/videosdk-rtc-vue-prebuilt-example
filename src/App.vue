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
      try {
        const response = await fetch(
          `${process.env.VUE_APP_VIDEOSDK_API_ENDPOINT}/get-token`,
          {
            method: "GET",
            headers: {
              Accept: "application/json",
              "Content-Type": "application/json",
            },
          }
        );
        const { token } = await response.json();
        return token;
      } catch (e) {
        console.log(e);
      }
    },
    getMeetingId: async function(token) {
      try {
        const VIDEOSDK_API_ENDPOINT = `${process.env.VUE_APP_VIDEOSDK_API_ENDPOINT}/create-meeting`;
        const options = {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({ token }),
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
    console.log(process.env.VUE_APP_VIDEOSDK_API_ENDPOINT); // SOME_KEY_VALUE

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
        recordingEnabledByDefault: false,
        participantCanToggleRecording: true,
        brandingEnabled: true,
        brandLogoURL:
          "https://app.videosdk.live/_next/image?url=%2Fvideosdk_logo_circle.png&w=1920&q=75",
        brandName: "VIDEO SDK LIVE",
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
