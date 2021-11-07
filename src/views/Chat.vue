<template>
  <div class="chat">
    <h1>Chat</h1>
    <react-web-chat
      class="web-chat"
      v-if="directLine && webchatStore"
      :directLine="directLine"
      :store="webchatStore"
      :styleOptions="styleOptions"
    ></react-web-chat>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { Middleware } from "redux";
import { DirectLine } from "botframework-directlinejs";
import ReactWebChat, {
  createDirectLine,
  createStore
} from "botframework-webchat/lib/index";

@Component({
  components: { "react-web-chat": ReactWebChat }
})
export default class Home extends Vue {
  directLine: DirectLine | null = null;
  webchatStore: any | null = null;
  styleOptions: any | null = {
    rootHeight: "100%",
    rootWidth: "100%"
  };

  async mounted() {
    const res = await fetch(
      "https://webchat-mockbot.azurewebsites.net/directline/token",
      { method: "POST" }
    );

    const { token } = await res.json();

    console.log(token);

    this.directLine = createDirectLine({
      token: token.token
    });

    const middleware: Middleware = ({ dispatch }) => next => action => {
      return next(action);
    };

    this.webchatStore = createStore({}, middleware);
  }
}
</script>

<style lang="scss" scoped>
.chat {
  height: 400px;
  width: 100%;
}
.web-chat{
  height: 100%;
  width: 100%;
  border: 1px solid #999;
}
</style>
