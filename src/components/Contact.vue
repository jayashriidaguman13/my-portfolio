<script setup>
  import {ref, onMounted, onBeforeUnmount} from 'vue';
  import {Notyf} from 'notyf';
  import 'notyf/notyf.min.css';

  const notyf = new Notyf();
  const WEB3FORMS_ACCESS_KEY = "6d742474-eea2-40f2-88dd-a4c6035337e0";
  const subject = "New message from Portfolio Contact Form";

  const name = ref("");
  const email = ref("")
  const message = ref("")

  const isLoading = ref(false)

  const submitForm = async() => {
    isLoading.value = true;

    if(!recaptchaToken.value) {
      notyf.error('Please verify')
    }

    try {
      const response = await fetch("https://api.web3forms.com/submit", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json"
        },
        body: JSON.stringify({
          access_key: WEB3FORMS_ACCESS_KEY,
          subject: subject,
          name: name.value,
          email: email.value,
          message: message.value
        })
      });

      const result = await response.json();
      if(result.success) {
        console.log(result);

        isLoading.value = false;
        notyf.success("Message sent")
      }
      } catch(error) {
        console.log();

        isLoading.value = false;
        notyf.error("Failed to send message")
    } finally {
      resetRecaptcha();
    }
}

const SITE_KEY = '6Le7xREsAAAAAEm2itC_oTO1RiJxoHpr-rUj6LJk'
const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

function onRecaptchaSuccess(token) {
    recaptchaToken.value = token;
}

function onRecaptchaExpired() {
    recaptchaToken.value = '';
}

function renderRecaptcha() {
    if(!window.grecaptcha) {
        console.error('reCAPTCHA not loaded')
        return;
    }

    recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
    sitekey: SITE_KEY,
    size: 'normal',
    callback: onRecaptchaSuccess,
    'expired-callback': onRecaptchaExpired
    })
}

function resetRecaptcha() {
    if(recaptchaWidgetId.value !== null) {
        window.grecaptcha.reset(recaptchaWidgetId.value);
        recaptchaToken.value = '';
    }
}

onMounted(() => {
    const interval = setInterval(() => {
        if(window.grecaptcha && window.grecaptcha.render) {
            renderRecaptcha();
            clearInterval(interval);
        }
    }, 100)
    onBeforeUnmount(() => {
        clearInterval(interval);
    })
});
</script>

<template>
  <h1 id="contact-title">Contact</h1>

  <form class="w-100 d-flex flex-column align-items-center" @submit.prevent="submitForm">
    <div class="form-field mb-3">
      <label for="fullname" class="form-label"></label>
      <input class="form-control" type="text" v-model="name" name="fullname" placeholder="First Name M.I. Last Name">
    </div>
    <div class="form-field mb-3">
      <label for="email" class="form-label"></label>
      <input class="form-control" type="email" v-model="email" name="email" placeholder="example@email.com">
    </div>
    <div class="form-field mb-3">
      <label for="exampleFormControlTextarea1" class="form-label"></label>
      <textarea class="form-control" v-model="message" id="exampleFormControlTextarea1" rows="5" placeholder="Message"></textarea>
    </div>

    <div class="row form-field mb-3 d-flex flex-column flex-md-row justify-content-between align-items-center pt-3">
      <div class="d-flex justify-content-center justify-content-md-start col-12 col-md-8 mb-3 mb-md-0">
        <a href="https://www.linkedin.com/in/jayashrii-daguman/" target="_blank">
          <img class="social-icon" src="/images/linkedin.png" alt="LinkedIn">
        </a>
        <a href="https://about.gitlab.com/" target="_blank">
          <img class="social-icon" src="/images/gitlab.jpg" alt="GitLab">
        </a>
        <a href="https://git.zuitt.co/jayashrii.daguman13" target="_blank">
          <img class="social-icon" src="/images/github.png" alt="GitHub">
        </a>
      </div>
      <div class="d-flex justify-content-center justify-content-md-end col-12 col-md-4">
        <button type="submit" class="btn" :disabled="isLoading">{{isLoading ? "Sending..." : "Submit"}}</button>
      </div>
    </div>
  </form>
</template>