
FROM python:3.13.15-slim

WORKDIR /app
COPY . .

# Configure pip with higher timeouts and retries to prevent connection failures in Codespaces
RUN pip install --no-cache-dir --upgrade pip --default-timeout=100 --retries 5

RUN pip install --no-cache-dir \
    torch \
    --index-url https://download.pytorch.org/whl/cpu \
    --default-timeout=100 --retries 5

RUN pip install --no-cache-dir -r requirements.txt --default-timeout=100 --retries 5

ENV HF_HOME=/app/huggingface

# Download the model at build time
RUN python -c "\
from langchain_huggingface import HuggingFaceEmbeddings; \
HuggingFaceEmbeddings( \
    model_name='HarishMaths/Hotel-Policy-Embedding', \
    model_kwargs={'device': 'cpu'}, \
    encode_kwargs={'normalize_embeddings': True} \
)"

# Force offline mode at runtime so it uses the cached model
ENV TRANSFORMERS_OFFLINE=1
ENV HF_HUB_OFFLINE=1

CMD ["gunicorn", "-w", "1", "--timeout", "120", "-b", "0.0.0.0:7860", "app:rag_api"]
