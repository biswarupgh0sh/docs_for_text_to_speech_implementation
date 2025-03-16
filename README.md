# Employing Text-to-Speech (TTS) Using JavaScript and Python

### 1. Introduction: 
* Text-to-Speech (TTS) technology converts written text into spoken words. It is widely used in accessibility applications, voice assistants, and automation. This document provides a roadmap for integrating TTS using JavaScript (Frontend) and Python (Backend) and highlights potential challenges.

### 2. Roadmap for Implementing TTS

#### Step 1: Define Use Case

* Accessibility feature (for visually impaired users)

* Voice assistant

* Automated audio content generation

#### Step 2: Choose a TTS Library or API

* JavaScript (Frontend)

* Web Speech API (Built-in browser API)

* Google Cloud TTS API

* Python (Backend)

* pyttsx3 (Offline TTS)

* gTTS (Google Text-to-Speech)

* Amazon Polly API(AWS)

* Google Cloud TTS API(GCP)

#### Step 3: Implementation

* Frontend (JavaScript)

* Implementing tts using react in frontend(React)

```bash
import React, { useState, useEffect } from 'react';

const TextToSpeechWithControls = () => {
  const [text, setText] = useState("");
  const [voices, setVoices] = useState([]);
  const [selectedVoice, setSelectedVoice] = useState(null);
  const [rate, setRate] = useState(1);
  const [pitch, setPitch] = useState(1);

  // Load available voices once the component mounts
  useEffect(() => {
    const loadVoices = () => {
      const availableVoices = window.speechSynthesis.getVoices();
      setVoices(availableVoices);
      setSelectedVoice(availableVoices[0]); // Set default voice
    };

    loadVoices();
    window.speechSynthesis.onvoiceschanged = loadVoices;
  }, []);

  const handleTextChange = (e) => {
    setText(e.target.value);
  };

  const handleSpeakClick = () => {
    if (text.trim() !== "") {
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.voice = selectedVoice;
      utterance.rate = rate;
      utterance.pitch = pitch;
      
      window.speechSynthesis.speak(utterance);
    } else {
      alert("Please enter some text.");
    }
  };

  return (
    <div>
      <h2>Text-to-Speech with Voice Selection</h2>
      <textarea
        value={text}
        onChange={handleTextChange}
        rows="6"
        cols="40"
        placeholder="Enter text here"
      />
      <br />
      <label>Choose Voice:</label>
      <select
        value={selectedVoice ? selectedVoice.name : ""}
        onChange={(e) => {
          const selected = voices.find(voice => voice.name === e.target.value);
          setSelectedVoice(selected);
        }}
      >
        {voices.map((voice, index) => (
          <option key={index} value={voice.name}>
            {voice.name} ({voice.lang})
          </option>
        ))}
      </select>
      <br />
      <label>Speech Rate:</label>
      <input
        type="range"
        min="0.1"
        max="2"
        step="0.1"
        value={rate}
        onChange={(e) => setRate(e.target.value)}
      />
      <br />
      <label>Speech Pitch:</label>
      <input
        type="range"
        min="0"
        max="2"
        step="0.1"
        value={pitch}
        onChange={(e) => setPitch(e.target.value)}
      />
      <br />
      <button onClick={handleSpeakClick}>Speak</button>
    </div>
  );
};

export default TextToSpeechWithControls;
```

* Using Web Speech API:

```bash
const synth = window.speechSynthesis;
const speakText = (text) => {
    let utterance = new SpeechSynthesisUtterance(text);
    synth.speak(utterance);
};

speakText("Hello, this is a text-to-speech example.");
```

* It is not a good idea to implement tts using gcloud from frotnend without needing an backend api because of:
    * Security Concerns: Exposing API Keys: To directly call the Google Cloud API from the frontend, you would need to expose your Google Cloud API key. This could lead to security vulnerabilities because anyone with access to the frontend code can extract the key and abuse your quota or incur unnecessary charges.
    Unauthorized Access: If you expose your credentials in the frontend, anyone can call your Google Cloud API with your key, potentially leading to misuse.

    * Authentication and Authorization: Google Cloud services often require authentication (typically via service accounts and API keys). Directly integrating this into the frontend means you'd need to handle sensitive credentials securely, which is very hard in a frontend environment.

    * CORS Issues: When trying to access Google Cloud APIs directly from a frontend, you'll likely encounter CORS (Cross-Origin Resource Sharing) issues, as Google Cloud APIs are not configured to accept requests directly from all origins for security reasons.

    * Best Practice: It’s a best practice to implement a backend API for interacting with Google Cloud services. The backend can handle the API key securely, act as an intermediary between your frontend and Google Cloud, and return the results to the frontend without exposing sensitive data.


* High quality tts: 
    * Google Cloud Text-to-Speech: Similar to AWS Polly with neural voice options and multi-language support. However, it might be pricier for larger-scale use.
    * Microsoft Azure Cognitive Services: Another powerful TTS API that offers lifelike voices, with competitive pricing and flexibility.
    * IBM Watson Text-to-Speech: Another alternative with high-quality voices, although it might not be as widely used as AWS or Google services.
    * ResponsiveVoice: A lightweight, client-side TTS solution if you're looking for simpler integration.


* This is another way of using tts, here we can handle the speech rate, speech pitch and language without employing an translation api.

```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Text-to-Speech with SpeechSynthesis</title>
</head>
<body>
    <h2>Text-to-Speech Example</h2>
    <textarea id="text-to-speak" placeholder="Enter text here" rows="5" cols="30"></textarea><br>
    <button onclick="speakText()">Speak</button>

    <script>
        function speakText() {
            const text = document.getElementById('text-to-speak').value;
            const speech = new SpeechSynthesisUtterance(text);
            speech.lang = "en-US";  // Set language to English
            speech.rate = 1;        // Set speed (1 is normal, 0.5 is slow, 2 is fast)
            speech.pitch = 1;       // Set pitch (1 is normal, 0 is low, 2 is high)

            // Speak the text
            window.speechSynthesis.speak(speech);
        }
    </script>
</body>
</html>
```

* Backend (Python)

* Using gTTS:

```bash
from gtts import gTTS
import os

def text_to_speech(text):
    tts = gTTS(text=text, lang='en')
    tts.save("output.mp3")
    os.system("mpg321 output.mp3")

text_to_speech("Hello, this is a text-to-speech example.");

```

#### Step 4: Integrate Frontend with Backend

* Use an API endpoint to send text from the frontend to the backend.

* Backend processes the text and generates an audio file.

* dependencies
```bash
npm install @google-cloud/text-to-speech fs util express
```
* implementation
```bash
const textToSpeech = require('@google-cloud/text-to-speech');
const fs = require('fs');
const util = require('util');
const express = require('express');

const app = express();
app.use(express.json());

const client = new textToSpeech.TextToSpeechClient();

app.post('/tts', async (req, res) => {
    const text = req.body.text || "Hello, this is a text-to-speech example.";
    
    const request = {
        input: { text: text },
        voice: { languageCode: 'en-US', ssmlGender: 'NEUTRAL' },
        audioConfig: { audioEncoding: 'MP3' },
    };

    const [response] = await client.synthesizeSpeech(request);
    const fileName = 'output.mp3';
    await util.promisify(fs.writeFile)(fileName, response.audioContent, 'binary');

    res.download(fileName);
});

app.listen(3000, () => console.log('Server running on port 3000'));

```
* There is js library providing offline tts feature
* dependencies
```bash
npm install say express
```
* implementation
```bash
const say = require('say');
const express = require('express');

const app = express();
app.use(express.json());

app.post('/tts', (req, res) => {
    const text = req.body.text || "Hello, this is an offline text-to-speech example.";

    say.export(text, 'Alex', 1.0, 'output.wav', (err) => {
        if (err) return res.status(500).send(err);
        res.download('output.wav');
    });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

* tts using AWS

* dependencies
```bash
npm install aws-sdk express fs util
```

* implementation
```bash
const AWS = require('aws-sdk');
const fs = require('fs');
const util = require('util');
const express = require('express');

const app = express();
app.use(express.json());

// Configure AWS Polly
AWS.config.update({ region: 'us-east-1' });
const polly = new AWS.Polly();
const s3 = new AWS.S3();

app.post('/tts', async (req, res) => {
    const text = req.body.text || "Hello, this is an AWS Polly text-to-speech example.";

    const params = {
        Text: text,
        OutputFormat: 'mp3',
        VoiceId: 'Joanna', // Choose from: Matthew, Joanna, Amy, etc.
    };

    try {
        // Generate speech using Polly
        const data = await polly.synthesizeSpeech(params).promise();

        if (!data.AudioStream) {
            return res.status(500).send("Error generating speech.");
        }

        // Save the MP3 file
        const fileName = 'output.mp3';
        await util.promisify(fs.writeFile)(fileName, data.AudioStream);

        res.download(fileName);
    } catch (error) {
        console.error(error);
        res.status(500).send("Error generating speech.");
    }
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

* uploading to s3
```bash
const bucketName = "your-s3-bucket-name";

app.post('/tts', async (req, res) => {
    const text = req.body.text || "Hello, AWS Polly with S3!";
    const fileName = `tts-${Date.now()}.mp3`;

    const params = {
        Text: text,
        OutputFormat: 'mp3',
        VoiceId: 'Joanna',
    };

    try {
        const data = await polly.synthesizeSpeech(params).promise();

        if (!data.AudioStream) {
            return res.status(500).send("Error generating speech.");
        }

        // Upload to S3
        const s3Params = {
            Bucket: bucketName,
            Key: fileName,
            Body: data.AudioStream,
            ContentType: 'audio/mpeg',
        };

        await s3.upload(s3Params).promise();
        const fileUrl = `https://${bucketName}.s3.amazonaws.com/${fileName}`;

        res.json({ message: "TTS generated!", url: fileUrl });
    } catch (error) {
        console.error(error);
        res.status(500).send("Error generating speech.");
    }
});
```
* Frontend fetches and plays the generated audio file.

* Example API Request (Frontend):

```bash

fetch('/api/tts', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({text: "Hello, world!"})
})
.then(response => response.blob())
.then(blob => {
    const audio = new Audio(URL.createObjectURL(blob));
    audio.play();
});
```

##### Example Flask Backend:

```bash
from flask import Flask, request, send_file
from gtts import gTTS
import os

app = Flask(__name__)

@app.route('/api/tts', methods=['POST'])
def tts():
    text = request.json.get("text", "")
    tts = gTTS(text=text, lang='en')
    filename = "output.mp3"
    tts.save(filename)
    return send_file(filename, as_attachment=True)

if __name__ == '__main__':
    app.run(debug=True)
```
* employing AWS polyy with django 
```bash
import boto3
import os
from django.http import JsonResponse, FileResponse
from django.views.decorators.csrf import csrf_exempt
import json

#to configure AWS Polly
polly = boto3.client('polly', region_name='us-east-1')

@csrf_exempt
def text_to_speech(request):
    if request.method == 'POST':
        try:
            data = json.loads(request.body)
            text = data.get("text", "Hello, this is AWS Polly with Django!")

            #polly TTS request
            response = polly.synthesize_speech(
                Text=text,
                OutputFormat='mp3',
                VoiceId='Joanna'  #choose a voice (e.g., Matthew, Amy, etc.)
            )

            #save audio file
            file_path = "tts_output.mp3"
            with open(file_path, 'wb') as file:
                file.write(response['AudioStream'].read())

            return FileResponse(open(file_path, 'rb'), as_attachment=True, content_type='audio/mpeg')

        except Exception as e:
            return JsonResponse({"error": str(e)}, status=500)

    return JsonResponse({"message": "Send a POST request with text data."})
```
* to save the audio in s3 employing django

```bash
s3 = boto3.client('s3')
bucket_name = "your-s3-bucket"

@csrf_exempt
def text_to_speech_s3(request):
    if request.method == 'POST':
        try:
            data = json.loads(request.body)
            text = data.get("text", "Hello from Django and AWS Polly!")

            response = polly.synthesize_speech(
                Text=text,
                OutputFormat='mp3',
                VoiceId='Joanna'
            )

            file_name = f"tts-{int(time.time())}.mp3"
            file_path = f"/tmp/{file_name}"

            with open(file_path, 'wb') as file:
                file.write(response['AudioStream'].read())

            #upload to S3
            s3.upload_file(file_path, bucket_name, file_name, ExtraArgs={'ContentType': 'audio/mpeg'})
            file_url = f"https://{bucket_name}.s3.amazonaws.com/{file_name}"

            return JsonResponse({"message": "TTS generated!", "url": file_url})

        except Exception as e:
            return JsonResponse({"error": str(e)}, status=500)
```

###### Here is the link of using tts(web speech api): [Goto](https://biswarupgh0sh.github.io/text-to-speech-with-js/)


#### 3. Challenges in TTS Integration

##### Frontend Challenges

* Browser Compatibility: Web Speech API is not fully supported in all browsers.

* Latency Issues: Fetching TTS-generated audio from a backend can introduce lag.

* Customization: Limited control over voice, pitch, and pronunciation.

Backend Challenges

* API Limits & Costs: Google Cloud, AWS Polly, and other APIs have usage limits.

* Performance Optimization: Generating and serving audio quickly.

* File Management: Handling temporary audio files efficiently.

* General Integration Challenges

* Handling Multiple Languages: Not all services support all languages equally.

* Security: Ensuring that TTS API calls and file storage do not expose sensitive data.

* Scaling: Handling a high number of requests efficiently.

#### 4. Conclusion

* Integrating TTS using JavaScript (Frontend) and Python (Backend) is feasible with APIs like Web Speech API, gTTS, and AWS Polly. However, performance, compatibility, and cost considerations should be addressed. Future enhancements could include caching mechanisms, real-time streaming, and AI-driven voice modulation.
