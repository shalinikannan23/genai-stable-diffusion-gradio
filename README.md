## Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework

### AIM:
To design and deploy a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation.

### PROBLEM STATEMENT:
Image generation from textual prompts has broad applications, from creative art to design prototyping and content creation. Stable Diffusion is a state-of-the-art model known for generating realistic and creative images. By integrating Stable Diffusion with Gradio, this project aims to provide a simple and accessible tool for generating images based on user input, catering to artists, designers, and casual users.

### DESIGN STEPS:
#### STEP 1:
##### Model Preparation
Use the pre-trained Stable Diffusion model available from Hugging Face's diffusers library.
Ensure all dependencies, including GPU acceleration (if available), are set up for optimal performance.

#### STEP 2:
##### Framework
Use Gradio to create an interface with:
Input: Textbox for entering the prompt.
Output: Display panel for the generated image.

#### STEP 3:
##### Workflow
Load the Stable Diffusion model and tokenizer.
Accept a text prompt from the user via Gradio's input.
Preprocess the text and generate an image using Stable Diffusion.
Display the generated image in the output panel.

#### STEP 4:
##### Testing and Deployment
Test the application with diverse prompts to ensure quality.
Deploy the application on a public platform (e.g., Hugging Face Spaces or local Gradio server).

### PROGRAM:
```PY
import os
import io
import base64
import json
import requests
from dotenv import load_dotenv, find_dotenv
from PIL import Image
import gradio as gr

# Load environment variables
_ = load_dotenv(find_dotenv())  # Reads local .env file
hf_api_key = os.environ['*******']  # Hugging Face API key
ENDPOINT_URL = os.environ['*******']  # Hugging Face Text-to-Image API endpoint
PORT = int(os.environ['PORT1'])  # Port to launch the Gradio app

# Function to interact with Hugging Face API
def get_completion(inputs, parameters=None):
    headers = {
        "Authorization": f"Bearer {hf_api_key}",
        "Content-Type": "application/json",
    }
    data = {"inputs": inputs}
    if parameters:
        data["parameters"] = parameters
    response = requests.post(ENDPOINT_URL, headers=headers, data=json.dumps(data))
    if response.status_code == 200:
        return response.json()
    else:
        raise ValueError(f"Error {response.status_code}: {response.text}")

# Function to decode base64 to a PIL Image
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

# Gradio generation function
def generate(prompt):
    output = get_completion(prompt)
    if 'image' in output:
        return base64_to_pil(output['image'])
    else:
        return "Error: Image could not be generated."

# Create Gradio interface
gr.close_all()  # Close any previously running Gradio instances
demo = gr.Interface(
    fn=generate,
    inputs=[gr.Textbox(label="Your prompt")],
    outputs=[gr.Image(label="Result")],
    title="Image Generation with Stable Diffusion",
    description="Generate any image with Stable Diffusion by entering a text prompt.",
    allow_flagging="never",
    examples=[
        "the spirit of a tamagotchi wandering in the city of Vienna",
        "a mecha robot in a favela",
        "a futuristic cityscape at night",
        "a cozy cabin in a snowy forest",
    ],
)
# Launch Gradio app
demo.launch(share=True, server_port=PORT)
demo.close()
```

### OUTPUT:
Example Input and Output
##### Input:
"A majestic lion standing on a cliff during a golden sunset."

##### Output:
![image](https://github.com/user-attachments/assets/59e4117c-d777-4c63-ad44-c1b84dbeb96a)


### RESULT:
The application successfully generates high-quality images based on user-provided text prompts. The Stable Diffusion model ensures visually appealing results, and the Gradio interface makes it accessible and interactive.
