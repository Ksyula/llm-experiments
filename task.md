## Homework: Introduction

In this homework, we'll learn more about search and use Elastic Search for practice. 

> It's possible that your answers won't match exactly. If it's the case, select the closest one.

## Q1. Running Elastic 

Run Elastic Search 8.4.3, and get the cluster information. If you run it on localhost, this is how you do it:

```bash
curl localhost:9200
```

What's the `version.build_hash` value?

**42f05b9372a9a4a470db3b52817899b99a76ee73**


## Getting the data

Now let's get the FAQ data. You can run this snippet:

```python
import requests 

docs_url = 'https://github.com/DataTalksClub/llm-zoomcamp/blob/main/01-intro/documents.json?raw=1'
docs_response = requests.get(docs_url)
documents_raw = docs_response.json()

documents = []

for course in documents_raw:
    course_name = course['course']

    for doc in course['documents']:
        doc['course'] = course_name
        documents.append(doc)
```

Note that you need to have the `requests` library:

```bash
pip install requests
```

## Q2. Indexing the data

Index the data in the same way as was shown in the course videos. Make the `course` field a keyword and the rest should be text. 

Don't forget to install the ElasticSearch client for Python:

```bash
pip install elasticsearch
```

Which function do you use for adding your data to elastic?

* `insert`
* **`index`**
* `put`
* `add`

## Q3. Searching

Now let's search in our index. 

We will execute a query "How do I execute a command in a running docker container?". 

Use only `question` and `text` fields and give `question` a boost of 4, and use `"type": "best_fields"`.

What's the score for the top ranking result?

* 94.05
* 84.05
* **74.05**
* 64.05

Look at the `_score` field.

## Q4. Filtering

Now let's only limit the questions to `machine-learning-zoomcamp`.

Return 3 results. What's the 3rd question returned by the search engine?

* How do I debug a docker container?
* How do I copy files from a different folder into docker container’s working directory?
* How do Lambda container images work?
* How can I annotate a graph?

```
[{'text': 'I wanted to understand how lambda container images work in depth and how lambda functions are initialized, for this reason, I found the following documentation\nhttps://docs.aws.amazon.com/lambda/latest/dg/images-create.html\nhttps://docs.aws.amazon.com/lambda/latest/dg/runtimes-api.html\nAdded by Alejandro aponte',
  'section': '9. Serverless Deep Learning',
  'question': 'How do Lambda container images work?',
  'course': 'machine-learning-zoomcamp'},
 {'text': 'Tim from BentoML has prepared a dedicated video tutorial wrt this use case here:\nhttps://www.youtube.com/watch?v=7gI1UH31xb4&list=PL3MmuxUbc_hIhxl5Ji8t4O6lPAOpHaCLR&index=97\nKonrad Muehlberg',
  'section': 'Miscellaneous',
  'question': 'How to pass BentoML content / docker container to Amazon Lambda',
  'course': 'machine-learning-zoomcamp'},
 {'text': 'Launch the container image in interactive mode and overriding the entrypoint, so that it starts a bash command.\ndocker run -it --entrypoint bash <image>\nIf the container is already running, execute a command in the specific container:\ndocker ps (find the container-id)\ndocker exec -it <container-id> bash\n(Marcos MJD)',
  'section': '5. Deploying Machine Learning Models',
  'question': 'How do I debug a docker container?',
  'course': 'machine-learning-zoomcamp'}]
  ```

## Q5. Building a prompt

Now we're ready to build a prompt to send to an LLM. 

Take the records returned from Elasticsearch in Q4 and use this template to build the context. Separate context entries by two linebreaks (`\n\n`)
```python
context_template = """
Q: {question}
A: {text}
""".strip()
```

Now use the context you just created along with the "How do I execute a command in a running docker container?" question 
to construct a prompt using the template below:

```
prompt_template = """
You're a course teaching assistant. Answer the QUESTION based on the CONTEXT from the FAQ database.
Use only the facts from the CONTEXT when answering the QUESTION.

QUESTION: {question}

CONTEXT:
{context}
""".strip()
```

What's the length of the resulting prompt? (use the `len` function)

* 962
* 1462
* **1962**
* 2462

## Q6. Tokens

When we use the OpenAI Platform, we're charged by the number of 
tokens we send in our prompt and receive in the response.

The OpenAI python package uses `tiktoken` for tokenization:

```bash
pip install tiktoken
```

Let's calculate the number of tokens in our query: 

```python
encoding = tiktoken.encoding_for_model("gpt-4o")
```

Use the `encode` function. How many tokens does our prompt have?

* 122
* 222
* 322
* **422**

Note: to decode back a token into a word, you can use the `decode_single_token_bytes` function:

```python
encoding.decode_single_token_bytes(63842)
```

## Bonus: generating the answer (ungraded)

Let's send the prompt to OpenAI. What's the response?  

```
Choice(finish_reason='stop', index=0, logprobs=None, message=ChatCompletionMessage(content="The provided context does not include specific instructions on how to execute a command in a running Docker container. However, based on general Docker knowledge, you can execute a command in a running Docker container using the following command:\n\n```sh\ndocker exec -it <container_id_or_name> <command>\n```\n\nFor example, if your Docker container ID is `abcd1234` and you want to execute the `bash` shell inside it, you would use:\n\n```sh\ndocker exec -it abcd1234 bash\n```\n\nReplace `<container_id_or_name>` with your container's ID or name and `<command>` with the command you wish to execute inside the container.", role='assistant', function_call=None, tool_calls=None))
```

Note: you can replace OpenAI with Ollama. See module 2.

## Bonus: calculating the costs (ungraded)

Suppose that on average per request we send 150 tokens and receive back 250 tokens.

How much will it cost to run 1000 requests?

**150*0.005 + 250*0.015 = $4.5**

You can see the prices [here](https://openai.com/api/pricing/)

On June 17, the prices for gpt4o are:

* Input: $0.005 / 1K tokens
* Output: $0.015 / 1K tokens

You can redo the calculations with the values you got in Q6 and Q7.


## Submit the results

* Submit your results here: https://courses.datatalks.club/llm-zoomcamp-2024/homework/hw1
* It's possible that your answers won't match exactly. If it's the case, select the closest one.