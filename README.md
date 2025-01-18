# Drunk-AI

### An Integration of OpenAI ChatGPT in the MagicMirror

---

## Demo

Watch the full demo [here](https://youtu.be/eDcc7zqAFwE).

![Demo Video](https://user-images.githubusercontent.com/65818001/224538652-0ed34179-5164-43a6-882a-8c199d5897d1.mov)

---

## About

This is the initial version of Drunk-AI. As a non-technical individual, I acknowledge that the code and scripts may not be perfect. If you find areas for improvement, feel free to submit a PR. While the setup process may seem lengthy and a bit messy, the final result is worth it!

---

## Getting Started

### 1. Download the Required Shortcuts

Download the shortcuts linked below and configure them with the API key from MMM-RemoteControl.
- More info about MMM-RemoteControl: [GitHub Repository](https://github.com/Jopyth/MMM-Remote-Control.git)
- iOS Shortcuts Link: [Download Shortcut](https://www.icloud.com/shortcuts/8a0e7600808d45eb9616dae8105653ef)

### 2. Configure the Shortcut
Edit the downloaded shortcut with:
1. Your MagicMirror's IP address.
2. Replace `apikey` in the text with your API key from MMM-RemoteControl.

---

## Usage

1. Run the shortcut each time you want to interact with ChatGPT.
2. Alternatively, use the default Telegram commands (without voice input).
3. Future updates will include hotword detection.

### Interaction Steps
- When you run the shortcut, the mirror will display a Jarvis animation and the text: **"Say something"**.
- Speak into the microphone after seeing the prompt.
- If the audio is captured successfully, the spoken text will appear on the mirror.
- Listening duration is set to 4 seconds by default. Adjust this in `transcript.py` in the MMM-Chat module.

---

## Module Setup

### Install and Configure Required Modules

1. **[MMM-Chat](https://github.com/magicmirror-sdmy/MMM-Chat)**
2. **[MMM-NotificationTrigger](https://github.com/MMRIZE/MMM-NotificationTrigger.git)**

   Add the following to your `config.js`:

   ```json
   {
     module: 'MMM-NotificationTrigger',
     config: {
       triggers: [
         {
           trigger: 'SHOW_ALERT',
           fires: [
             {
               fire: 'MY_COMMAND',
               exec: (payload) => `python3 /home/pi/MagicMirror/modules/MMM-11-TTS/main.py "${payload.message}"`
             }
           ]
         }
       ]
     }
   }
   ```

3. **[MMM-OpenAI](https://github.com/MMRIZE/MMM-OpenAI.git)**

   Add the following to your `config.js`:

   ```json
   {
     module: "MMM-OpenAI",
     position: 'top_right',
     config: {
       defaultChatInstruction: "Your name is Marvin and you are a paranoid android that reluctantly answers questions with sarcastic responses.",
       stealth: true,
       postProcessing: function (handler, responseObj) {
         if (responseObj.error) return;
         let method = responseObj.options.method;
         let alertPayload = {
           title: responseObj.request.prompt,
           imageUrl: (method === 'IMAGE') ? responseObj.response.data[0].url : null,
           message: (method === 'TEXT') ? responseObj.response.choices[0].text : ((method === 'CHAT') ? responseObj.response.choices[0].message.content : null),
           timer: 2 * 1000
         };
         handler.sendNotification('SHOW_ALERT', alertPayload);
       }
     }
   }
   ```

4. Clone the following repository: [MMM-11-TTS](https://github.com/magicmirror-sdmy/MMM-11-TTS).
   - Follow the installation instructions.
   - Edit `main.py` to add your API key and voice ID. A default voice ID is included.
   - Refer to the ElevenLabs API documentation for voice ID details.

---

## Notes and Disclaimers

### Voice Quality
While ElevenLabs TTS offers superior voice quality compared to Google TTS, its free account character limit is only 10,000 characters per month. Paid accounts offer 30,000 characters and voice cloning features. To enable cloning, update the voice ID in `main.py`.

### Efficiency
The current implementation involves downloading audio from API requests, converting it using `ffmpeg`, and playing it as output. This may not be the most efficient approach. Install `ffmpeg` using `apt` if it is not already installed.

---

## Future Enhancements
A streamlined, single-module implementation would simplify this process. Contributions are welcome!

