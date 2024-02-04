# 📃 Chat Your PDF! [Deploy to Google Cloud Run]

This example Streamlit app demonstrates how to build a simple chatbot powered by Redis, LangChain, and Google's Vertex AI. It contains the following elements:

- ⚙️ [LangChain](https://python.langchain.com/docs/get_started/introduction.html) for app orchestration, agent construction, and tools
- 🖥️ [Streamlit](https://docs.streamlit.io/knowledge-base/tutorials/build-conversational-apps) for the front end and conversational interface
- ☁️ [GCP Vertex AI Palm 2](https://cloud.google.com/vertex-ai/docs/generative-ai/start/quickstarts/api-quickstart) models for embedding creation and chat completion
- 💥 [Redis](https://redis.io) and [RedisVL client](https://redisvl.com) for Retrieval-Augmented Generation (RAG), LLM Semantic Caching, and chat history persistence

![Chat Your PDF screenshot](https://github.com/RedisVentures/gcp-redis-llm-stack/assets/20304844/fdf24716-965a-4a66-88b2-761427ff61c0)

## About
Redis is well-versed to power chatbots thanks to its flexible data models, query engine, and high performance. This enables users to leverage redis for a variety of gen AI needs:
- **RAG** -- ensures that relevant context is retrieved from Redis as a [Vector Database](https://redis.com/solutions/use-cases/vector-database), given a users question
- **Semantic Caching** -- ensures that duplicate requests for identical or very *similar* information are not exhuastive. Ex:
    ```bash
    streamlit    | Full Response Time (secs) 1.6435627937316895
    streamlit    | Cache Response Time (secs) 0.11130380630493164
    ```
- **Chat History** -- ensures distributed & low latency access to conversation history in Redis [Lists](https://redis.io/docs/data-types/lists/)

    
1) Build the chatbot container
```
export PROJECT_ID=<your GCP project ID>
gcloud builds submit --tag gcr.io/$PROJECT_ID/glau-chat-your-pdfs-streamlit-app

For example,
export PROJECT_ID=central-beach-194106
gcloud builds submit --tag gcr.io/$PROJECT_ID/glau-chat-your-pdfs-streamlit-app
```
     
2) Deploy to Cloud Run    
Note:    
GCP_PROJECT_ID: Google Cloud project for Vertex AI APIs    
GCP_LOCATION: Region for the Vertex AI APIs    
```
gcloud run deploy glau-chat-your-pdfs-streamlit-app \
--image gcr.io/$PROJECT_ID/glau-chat-your-pdfs-streamlit-app \
--platform managed \
--allow-unauthenticated --region us-central1 \
--set-env-vars=DOCS_FOLDER="pdfs/",REDIS_URL="redis://default:<Redis DB password>@<Redis DB endpoint>:<Redis DB port>",GCP_PROJECT_ID=${PROJECT_ID},GCP_LOCATION=<GCP Cloud Region> \
--memory 4Gi \
--port 8080 

For example,
gcloud run deploy glau-chat-your-pdfs-streamlit-app \
--image gcr.io/$PROJECT_ID/glau-chat-your-pdfs-streamlit-app \
--platform managed \
--allow-unauthenticated --region us-central1 \
--set-env-vars=DOCS_FOLDER="pdfs/",REDIS_URL="redis://default:xnurcS28JREs9S8HHemx2cKc1jLFi3ua@redis-10996.c279.us-central1-1.gce.cloud.redislabs.com:10996",GCP_PROJECT_ID="central-beach-194106",GCP_LOCATION="us-central1" \
--memory 4Gi \
--port 8080 
```
    
3) Hit your brower at the Service URL from the output above 
```
Example output:
Service URL: https://glau-chat-your-pdfs-streamlit-app-t5a5vybtpq-uc.a.run.app
```
   
4) To view the full response time and cache response time in Cloud Run's LOGS window    
Apply the filter **Response  Time (secs)**:
![Cloud Run LOGS](./img/cloudRunLogs.png)

    
