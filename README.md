# Development of a Named Entity Recognition (NER) Prototype Using a Fine-Tuned BART Model and Gradio Framework

### AIM:
To design and develop a prototype application for Named Entity Recognition (NER) by leveraging a fine-tuned BART model and deploying the application using the Gradio framework for user interaction and evaluation.

# Register No: 212224040371

### PROBLEM STATEMENT:
Text data, especially from the web or internal documents, is vast and unstructured. Manually extracting specific pieces of information, such as names of people (PER), organizations (ORG), locations (LOC), or other miscellaneous entities (MISC), is time-consuming, prone to error, and inefficient.

The problem is to develop an automated system that can process any given text, accurately identify these named entities, and classify them into their predefined categories. This system must also be wrapped in a simple, accessible web interface, allowing non-technical users to easily input text and visualize the model's predictions for testing and validation.

### DESIGN STEPS:
### Step1 - Install Required Libraries:
Install the necessary Python libraries such as transformers, torch, and gradio.

### Step2 - Load the NER Model:
Use the transformers library to load a fine-tuned BART model for NER, for example, dslim/bart-large-ner.

### Step3 - Prepare the Output Format:
Organize the model output so that entities and their labels can be clearly highlighted in the interface.

### Step4 - Build the Gradio Interface:
Create a user-friendly interface with a text input box and a highlighted output display using the Gradio framework.

### Step5 - Customize the Interface:
Add colors for each entity type (like red for people, blue for organizations, green for locations) and include a title and description.

### PROGRAM:
```
import os
import io
from IPython.display import Image, display, HTML
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']

# Helper function
import requests, json

#Summarization endpoint
def get_completion(inputs, parameters=None,ENDPOINT_URL=os.environ['HF_API_SUMMARY_BASE']): 
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL, headers=headers,
                                data=json.dumps(data)
                               )
    return json.loads(response.content.decode("utf-8"))

def ner(input):
    output = get_completion(input, parameters=None, ENDPOINT_URL=API_URL)
    return {"text": input, "entities": output}

gr.close_all()
demo = gr.Interface(fn=ner,
                    inputs=[gr.Textbox(label="Text to find entities", lines=2)],
                    outputs=[gr.HighlightedText(label="Text with entities")],
                    title="NER with dslim/bert-base-NER",
                    description="Find entities using the `dslim/bert-base-NER` model under the hood!",
                    allow_flagging="never",
                    #Here we introduce a new tag, examples, easy to use examples for your application
                    examples=["My name is Andrew and I live in California", "My name is Poli and work at HuggingFace"])
demo.launch(share=True, server_port=int(os.environ['PORT3']))

def merge_tokens(tokens):
    merged_tokens = []
    for token in tokens:
        if merged_tokens and token['entity'].startswith('I-') and merged_tokens[-1]['entity'].endswith(token['entity'][2:]):
            # If current token continues the entity of the last one, merge them
            last_token = merged_tokens[-1]
            last_token['word'] += token['word'].replace('##', '')
            last_token['end'] = token['end']
            last_token['score'] = (last_token['score'] + token['score']) / 2
        else:
            # Otherwise, add the token to the list
            merged_tokens.append(token)

    return merged_tokens

def ner(input):
    output = get_completion(input, parameters=None, ENDPOINT_URL=API_URL)
    merged_tokens = merge_tokens(output)
    return {"text": input, "entities": merged_tokens}

gr.close_all()
demo = gr.Interface(fn=ner,
                    inputs=[gr.Textbox(label="Text to find entities", lines=2)],
                    outputs=[gr.HighlightedText(label="Text with entities")],
                    title="NER with dslim/bert-base-NER",
                    description="Find entities using the `dslim/bert-base-NER` model under the hood!",
                    allow_flagging="never",
                    examples=["My name is Andrew, I'm building DeeplearningAI and I live in California", "My name is Poli, I live in Vienna and work at HuggingFace"])

demo.launch(share=True, server_port=int(os.environ['PORT4']))
```
# OUTPUT:

![image alt](https://github.com/Yogesh1380/Genai-ner-bart-gradio/blob/e823a94b712f72b5b0bcb4e6fd3160146c43ce27/Screenshot%202026-03-13%20144536.png)


### RESULT:
To develop a prototype application for Named Entity Recognition (NER) by leveraging a fine-tuned BART model and deploying the application using the Gradio framework for user interaction and evaluation excuted successfully.
