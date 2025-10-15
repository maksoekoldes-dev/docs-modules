# Panduan Lengkap Gemini  API

## Daftar Isi
1. [Informasi Dasar](#informasi-dasar)
2. [Endpoint Lengkap](#endpoint-lengkap)
3. [Fitur Lanjutan](#fitur-lanjutan)
4. [Implementasi Lengkap](#implementasi-lengkap)
5. [Best Practices](#best-practices)
6. [Troubleshooting](#troubleshooting)

---

## Informasi Dasar

### Base URL
```
https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/
```

**Cara Mendapatkan Base URL:**
1. Base URL ini adalah endpoint resmi dari google yang telah diverifikasi
2. URL ini dapat digunakan langsung tanpa modifikasi
3. Pastikan menggunakan HTTPS untuk keamanan

### Format Autentikasi
```http
Authorization: Bearer ganti-api-anda
Content-Type: application/json
```

**API Key:**
```
ganti-api-anda
```

**Catatan Penting:**
- API key harus disertakan di setiap request
- Format menggunakan Bearer token
- Jangan share API key dengan pihak lain

### Model yang Tersedia

#### Gemini 2.5 Flash
- **Nama Model:** `gemini-2.5-flash`
- **Deskripsi:** Model cepat dan serbaguna untuk tugas volume tinggi
- **Limit Input Token:** 1,048,576 tokens
- **Limit Output Token:** 8,192 tokens
- **Metode yang Didukung:** `generateContent`, `countTokens`
- **Keunggulan:** Response cepat, biaya lebih rendah

#### Gemini 2.5 Pro
- **Nama Model:** `gemini-2.5-pro`
- **Deskripsi:** Model terbaik untuk berbagai tugas kompleks
- **Limit Input Token:** 1,048,576 tokens
- **Limit Output Token:** 8,192 tokens
- **Metode yang Didukung:** `generateContent`, `countTokens`
- **Keunggulan:** Kualitas response lebih tinggi, pemahaman konteks lebih baik

#### Text Embedding Model
- **Nama Model:** `text-embedding-004`
- **Deskripsi:** Model untuk generating text embeddings
- **Dimensi:** 768 dimensions
- **Metode yang Didukung:** `embedContent`

---

## Endpoint Lengkap

### 1. Model Listing

**Endpoint:** `GET /proxy/gemini/v1beta/models`

**Request:**
```bash
curl -X GET "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json"
```

**Response Format:**
```json
{
  "models": [
    {
      "name": "models/gemini-2.5-flash",
      "version": "001",
      "displayName": "Gemini 2.5 Flash",
      "description": "Fast and versatile model for high-volume tasks",
      "inputTokenLimit": 1048576,
      "outputTokenLimit": 8192,
      "supportedGenerationMethods": ["generateContent", "countTokens"],
      "temperature": 0.9,
      "topP": 1.0,
      "topK": 1
    },
    {
      "name": "models/gemini-2.5-pro",
      "version": "001",
      "displayName": "Gemini 2.5 Pro",
      "description": "Best overall model for a wide range of tasks",
      "inputTokenLimit": 2097152,
      "outputTokenLimit": 8192,
      "supportedGenerationMethods": ["generateContent", "countTokens"],
      "temperature": 0.9,
      "topP": 1.0,
      "topK": 1
    },
    {
      "name": "models/text-embedding-004",
      "version": "001",
      "displayName": "Text Embedding 004",
      "description": "Text embedding model for semantic search",
      "inputTokenLimit": 2048,
      "outputTokenLimit": 1,
      "supportedGenerationMethods": ["embedContent"]
    }
  ]
}
```

### 2. Generate Content (Chat)

**Endpoint:** `POST /proxy/gemini/v1beta/models/{model}:generateContent`

**Basic Request:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-flash:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Halo! Siapa kamu?"
          }
        ],
        "role": "user"
      }
    ]
  }'
```

**Advanced Request with Configuration:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Jelaskan tentang kecerdasan buatan dalam bahasa Indonesia"
          }
        ],
        "role": "user"
      }
    ],
    "generationConfig": {
      "temperature": 0.9,
      "topK": 40,
      "topP": 0.95,
      "maxOutputTokens": 2048,
      "stopSequences": ["\n\n", "###"],
      "candidateCount": 1
    },
    "safetySettings": [
      {
        "category": "HARM_CATEGORY_HARASSMENT",
        "threshold": "BLOCK_MEDIUM_AND_ABOVE"
      },
      {
        "category": "HARM_CATEGORY_HATE_SPEECH",
        "threshold": "BLOCK_MEDIUM_AND_ABOVE"
      }
    ]
  }'
```

**Response Format:**
```json
{
  "candidates": [
    {
      "content": {
        "parts": [
          {
            "text": "Kecerdasan buatan (AI) adalah cabang ilmu komputer yang berfokus pada pembuatan mesin yang dapat melakukan tugas yang biasanya memerlukan kecerdasan manusia..."
          }
        ],
        "role": "model"
      },
      "finishReason": "STOP",
      "index": 0,
      "safetyRatings": [
        {
          "category": "HARM_CATEGORY_HARASSMENT",
          "probability": "NEGLIGIBLE"
        },
        {
          "category": "HARM_CATEGORY_HATE_SPEECH",
          "probability": "NEGLIGIBLE"
        },
        {
          "category": "HARM_CATEGORY_SEXUALLY_EXPLICIT",
          "probability": "NEGLIGIBLE"
        },
        {
          "category": "HARM_CATEGORY_DANGEROUS_CONTENT",
          "probability": "NEGLIGIBLE"
        }
      ]
    }
  ],
  "usageMetadata": {
    "promptTokenCount": 15,
    "candidatesTokenCount": 250,
    "totalTokenCount": 265
  },
  "modelVersion": "gemini-2.5-flash-001"
}
```

### 3. Embeddings

**Endpoint:** `POST /proxy/gemini/v1beta/models/text-embedding-004:embedContent`

**Request:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/text-embedding-004:embedContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "content": {
      "parts": [
        {
          "text": "Ini adalah contoh teks untuk embedding dalam bahasa Indonesia"
        }
      ]
    },
    "taskType": "RETRIEVAL_DOCUMENT"
  }'
```

**Response Format:**
```json
{
  "embedding": {
    "values": [0.1234, -0.5678, 0.9012, 0.3456, -0.7890, ...]
  }
}
```

**Task Types yang Tersedia:**
- `RETRIEVAL_QUERY`: Untuk query pencarian
- `RETRIEVAL_DOCUMENT`: Untuk dokumen
- `SEMANTIC_SIMILARITY`: Untuk similarity
- `CLASSIFICATION`: Untuk klasifikasi
- `CLUSTERING`: Untuk clustering

### 4. Token Counting

**Endpoint:** `POST /proxy/gemini/v1beta/models/{model}:countTokens`

**Request:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-flash:countTokens" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Ini adalah teks panjang yang akan saya hitung tokennya untuk mengetahui berapa banyak token yang digunakan dalam permintaan ini."
          }
        ]
      }
    ]
  }'
```

**Response Format:**
```json
{
  "totalTokens": 35
}
```

### 5. Streaming

**Endpoint:** `POST /proxy/gemini/v1beta/models/{model}:streamGenerateContent`

**Request:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-flash:streamGenerateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Tulis sebuah cerita pendek tentang petualangan di hutan"
          }
        ],
        "role": "user"
      }
    ],
    "generationConfig": {
      "temperature": 0.8,
      "maxOutputTokens": 500
    }
  }'
```

**Streaming Response Format:**
```json
{
  "candidates": [
    {
      "content": {
        "parts": [
          {
            "text": "Pada suatu hari"
          }
        ],
        "role": "model"
      },
      "finishReason": "STOP",
      "index": 0
    }
  ]
}
```

---

## Fitur Lanjutan

### 1. Multimodal (Image, Audio, Video)

#### Image Processing
**Request dengan Image:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Deskripsikan gambar ini dalam bahasa Indonesia"
          },
          {
            "inline_data": {
              "mime_type": "image/jpeg",
              "data": "base64_encoded_image_data_here"
            }
          }
        ],
        "role": "user"
      }
    ]
  }'
```

**Format yang Didukung:**
- `image/jpeg` - JPEG images
- `image/png` - PNG images
- `image/webp` - WebP images
- `image/heic` - HEIC images
- `image/heif` - HEIF images

#### Audio Processing
**Request dengan Audio:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Transkripsikan audio ini"
          },
          {
            "inline_data": {
              "mime_type": "audio/wav",
              "data": "base64_encoded_audio_data_here"
            }
          }
        ],
        "role": "user"
      }
    ]
  }'
```

**Format Audio yang Didukung:**
- `audio/wav` - WAV audio
- `audio/mp3` - MP3 audio
- `audio/aac` - AAC audio
- `audio/ogg` - OGG audio
- `audio/flac` - FLAC audio

#### Video Processing
**Request dengan Video:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Analisis video ini dan berikan ringkasan"
          },
          {
            "inline_data": {
              "mime_type": "video/mp4",
              "data": "base64_encoded_video_data_here"
            }
          }
        ],
        "role": "user"
      }
    ]
  }'
```

**Format Video yang Didukung:**
- `video/mp4` - MP4 video
- `video/webm` - WebM video
- `video/mov` - QuickTime video
- `video/avi` - AVI video

### 2. Function Calling

**Request dengan Function Calling:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Berapa cuaca di Jakarta hari ini?"
          }
        ],
        "role": "user"
      }
    ],
    "tools": [
      {
        "functionDeclarations": [
          {
            "name": "get_weather",
            "description": "Get current weather information for a location",
            "parameters": {
              "type": "object",
              "properties": {
                "location": {
                  "type": "string",
                  "description": "The city name"
                },
                "unit": {
                  "type": "string",
                  "enum": ["celsius", "fahrenheit"],
                  "description": "Temperature unit"
                }
              },
              "required": ["location"]
            }
          }
        ]
      }
    ]
  }'
```

**Response dengan Function Call:**
```json
{
  "candidates": [
    {
      "content": {
        "parts": [
          {
            "functionCall": {
              "name": "get_weather",
              "args": {
                "location": "Jakarta",
                "unit": "celsius"
              }
            }
          }
        ],
        "role": "model"
      }
    }
  ]
}
```

**Function Response:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Berapa cuaca di Jakarta hari ini?"
          }
        ],
        "role": "user"
      },
      {
        "parts": [
          {
            "functionCall": {
              "name": "get_weather",
              "args": {
                "location": "Jakarta",
                "unit": "celsius"
              }
            }
          }
        ],
        "role": "model"
      },
      {
        "parts": [
          {
            "functionResponse": {
              "name": "get_weather",
              "response": {
                "temperature": 28,
                "condition": "Cerah berawan",
                "humidity": 75
              }
            }
          }
        ],
        "role": "function"
      }
    ]
  }'
```

### 3. System Instructions

**Request dengan System Instructions:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "systemInstruction": {
      "parts": [
        {
          "text": "Kamu adalah asisten AI yang ahli dalam bahasa Indonesia. Selalu jawab dengan bahasa Indonesia yang formal dan sopan. Berikan informasi yang akurat dan bermanfaat."
        }
      ],
      "role": "user"
    },
    "contents": [
      {
        "parts": [
          {
            "text": "Jelaskan tentang sejarah Indonesia"
          }
        ],
        "role": "user"
      }
    ]
  }'
```

### 4. Batch Processing

**Multiple Content in Single Request:**
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-flash:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {
            "text": "Apa ibu kota Indonesia?"
          }
        ],
        "role": "user"
      },
      {
        "parts": [
          {
            "text": "Ibu kota Indonesia adalah Jakarta."
          }
        ],
        "role": "model"
      },
      {
        "parts": [
          {
            "text": "Berapa populasi Jakarta?"
          }
        ],
        "role": "user"
      }
    ]
  }'
```

### 5. Safety Settings

**Konfigurasi Safety Lengkap:**
```json
{
  "safetySettings": [
    {
      "category": "HARM_CATEGORY_HARASSMENT",
      "threshold": "BLOCK_MEDIUM_AND_ABOVE"
    },
    {
      "category": "HARM_CATEGORY_HATE_SPEECH",
      "threshold": "BLOCK_MEDIUM_AND_ABOVE"
    },
    {
      "category": "HARM_CATEGORY_SEXUALLY_EXPLICIT",
      "threshold": "BLOCK_MEDIUM_AND_ABOVE"
    },
    {
      "category": "HARM_CATEGORY_DANGEROUS_CONTENT",
      "threshold": "BLOCK_MEDIUM_AND_ABOVE"
    }
  ]
}
```

**Threshold Options:**
- `BLOCK_NONE`: Tidak blocking sama sekali
- `BLOCK_ONLY_HIGH`: Hanya block konten berbahaya tinggi
- `BLOCK_MEDIUM_AND_ABOVE`: Block konten medium dan tinggi
- `BLOCK_LOW_AND_ABOVE`: Block konten low, medium, dan tinggi

---

## Implementasi Lengkap

### 1. cURL Examples untuk Semua Fitur

#### Basic Chat
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-flash:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [{"text": "Halo!"}],
        "role": "user"
      }
    ]
  }'
```


#### Image Analysis
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-pro:generateContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {"text": "Apa yang ada di gambar ini?"},
          {
            "inline_data": {
              "mime_type": "image/jpeg",
              "data": "/9j/4AAQSkZJRgABAQAAAQ..."
            }
          }
        ],
        "role": "user"
      }
    ]
  }'
```

#### Embeddings
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/text-embedding-004:embedContent" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "content": {
      "parts": [{"text": "Teks untuk embedding"}]
    }
  }'
```

#### Token Counting
```bash
curl -X POST "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models/gemini-2.5-flash:countTokens" \
  -H "Authorization: Bearer ganti-api-anda" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [{"text": "Hitung token untuk teks ini"}]
      }
    ]
  }'
```

### 2. Python Implementation

#### Basic Python Client
```python
import requests
import json
import base64
import time
from typing import Dict, List, Optional, Union

class GeminiProxyClient:
    def __init__(self, api_key: str = "ganti-api-anda"):
        self.api_key = api_key
        self.base_url = "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta"
        self.headers = {
            "Authorization": f"Bearer {self.api_key}",
            "Content-Type": "application/json"
        }
    
    def generate_content(
        self,
        prompt: str,
        model: str = "gemini-2.5-flash",
        temperature: float = 0.9,
        max_output_tokens: int = 1024,
        system_instruction: Optional[str] = None
    ) -> Dict:
        """Generate content using Gemini model"""
        url = f"{self.base_url}/models/{model}:generateContent"
        
        data = {
            "contents": [
                {
                    "parts": [{"text": prompt}],
                    "role": "user"
                }
            ],
            "generationConfig": {
                "temperature": temperature,
                "maxOutputTokens": max_output_tokens
            }
        }
        
        if system_instruction:
            data["systemInstruction"] = {
                "parts": [{"text": system_instruction}],
                "role": "user"
            }
        
        response = requests.post(url, headers=self.headers, json=data)
        response.raise_for_status()
        return response.json()
    
    def generate_content_stream(
        self,
        prompt: str,
        model: str = "gemini-2.5-flash",
        temperature: float = 0.9,
        max_output_tokens: int = 1024
    ):
        """Generate content with streaming"""
        url = f"{self.base_url}/models/{model}:streamGenerateContent"
        
        data = {
            "contents": [
                {
                    "parts": [{"text": prompt}],
                    "role": "user"
                }
            ],
            "generationConfig": {
                "temperature": temperature,
                "maxOutputTokens": max_output_tokens
            }
        }
        
        response = requests.post(url, headers=self.headers, json=data, stream=True)
        response.raise_for_status()
        
        for line in response.iter_lines():
            if line:
                try:
                    yield json.loads(line.decode('utf-8'))
                except json.JSONDecodeError:
                    continue
    
    def embed_content(
        self,
        text: str,
        task_type: str = "RETRIEVAL_DOCUMENT"
    ) -> Dict:
        """Generate embeddings for text"""
        url = f"{self.base_url}/models/text-embedding-004:embedContent"
        
        data = {
            "content": {
                "parts": [{"text": text}]
            },
            "taskType": task_type
        }
        
        response = requests.post(url, headers=self.headers, json=data)
        response.raise_for_status()
        return response.json()
    
    def count_tokens(
        self,
        text: str,
        model: str = "gemini-2.5-flash"
    ) -> Dict:
        """Count tokens in text"""
        url = f"{self.base_url}/models/{model}:countTokens"
        
        data = {
            "contents": [
                {
                    "parts": [{"text": text}]
                }
            ]
        }
        
        response = requests.post(url, headers=self.headers, json=data)
        response.raise_for_status()
        return response.json()
    
    def analyze_image(
        self,
        image_path: str,
        prompt: str,
        model: str = "gemini-2.5-pro"
    ) -> Dict:
        """Analyze image with text prompt"""
        url = f"{self.base_url}/models/{model}:generateContent"
        
        # Read and encode image
        with open(image_path, "rb") as image_file:
            image_data = base64.b64encode(image_file.read()).decode('utf-8')
        
        # Determine MIME type
        mime_type = "image/jpeg"
        if image_path.lower().endswith('.png'):
            mime_type = "image/png"
        elif image_path.lower().endswith('.webp'):
            mime_type = "image/webp"
        
        data = {
            "contents": [
                {
                    "parts": [
                        {"text": prompt},
                        {
                            "inline_data": {
                                "mime_type": mime_type,
                                "data": image_data
                            }
                        }
                    ],
                    "role": "user"
                }
            ]
        }
        
        response = requests.post(url, headers=self.headers, json=data)
        response.raise_for_status()
        return response.json()
    
    def function_calling(
        self,
        prompt: str,
        functions: List[Dict],
        model: str = "gemini-2.5-pro"
    ) -> Dict:
        """Make request with function calling"""
        url = f"{self.base_url}/models/{model}:generateContent"
        
        data = {
            "contents": [
                {
                    "parts": [{"text": prompt}],
                    "role": "user"
                }
            ],
            "tools": [
                {
                    "functionDeclarations": functions
                }
            ]
        }
        
        response = requests.post(url, headers=self.headers, json=data)
        response.raise_for_status()
        return response.json()
    
    def list_models(self) -> Dict:
        """List available models"""
        url = f"{self.base_url}/models"
        
        response = requests.get(url, headers=self.headers)
        response.raise_for_status()
        return response.json()

# Contoh penggunaan
if __name__ == "__main__":
    client = GeminiProxyClient()
    
    # Basic chat
    print("=== Basic Chat ===")
    response = client.generate_content("Halo! Siapa kamu?")
    print(response["candidates"][0]["content"]["parts"][0]["text"])
    
    # Streaming
    print("\n=== Streaming ===")
    for chunk in client.generate_content_stream("Tulis cerita pendek"):
        if "candidates" in chunk and chunk["candidates"]:
            text = chunk["candidates"][0]["content"]["parts"][0]["text"]
            print(text, end="", flush=True)
    print()
    
    # Embeddings
    print("\n=== Embeddings ===")
    embed_response = client.embed_content("Ini adalah contoh teks")
    print(f"Embedding dimensions: {len(embed_response['embedding']['values'])}")
    
    # Token counting
    print("\n=== Token Counting ===")
    token_response = client.count_tokens("Hitung token untuk teks ini")
    print(f"Total tokens: {token_response['totalTokens']}")
    
    # List models
    print("\n=== Available Models ===")
    models = client.list_models()
    for model in models["models"]:
        print(f"- {model['name']}: {model['displayName']}")
```

#### Advanced Python Implementation with Error Handling
```python
import requests
import json
import time
import logging
from typing import Dict, List, Optional, Union, Any
from requests.exceptions import RequestException, Timeout, ConnectionError

# Setup logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class GeminiProxyError(Exception):
    """Custom exception for Gemini Proxy errors"""
    pass

class RateLimiter:
    """Simple rate limiter for API requests"""
    def __init__(self, requests_per_minute: int = 60):
        self.requests_per_minute = requests_per_minute
        self.requests = []
    
    def wait_if_needed(self):
        """Wait if rate limit would be exceeded"""
        now = time.time()
        # Remove requests older than 1 minute
        self.requests = [req_time for req_time in self.requests if now - req_time < 60]
        
        if len(self.requests) >= self.requests_per_minute:
            sleep_time = 60 - (now - self.requests[0])
            if sleep_time > 0:
                logger.info(f"Rate limit reached, sleeping for {sleep_time:.2f} seconds")
                time.sleep(sleep_time)
        
        self.requests.append(now)

class AdvancedGeminiProxyClient:
    def __init__(
        self,
        api_key: str = "ganti-api-anda",
        base_url: str = "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta",
        timeout: int = 30,
        max_retries: int = 3,
        requests_per_minute: int = 60
    ):
        self.api_key = api_key
        self.base_url = base_url
        self.timeout = timeout
        self.max_retries = max_retries
        self.rate_limiter = RateLimiter(requests_per_minute)
        
        self.headers = {
            "Authorization": f"Bearer {self.api_key}",
            "Content-Type": "application/json",
            "User-Agent": "GeminiProxyClient/1.0"
        }
    
    def _make_request(
        self,
        method: str,
        endpoint: str,
        data: Optional[Dict] = None,
        stream: bool = False
    ) -> Union[Dict, Any]:
        """Make HTTP request with retry logic"""
        url = f"{self.base_url}/{endpoint}"
        
        for attempt in range(self.max_retries):
            try:
                self.rate_limiter.wait_if_needed()
                
                if method.upper() == "GET":
                    response = requests.get(
                        url,
                        headers=self.headers,
                        timeout=self.timeout
                    )
                elif method.upper() == "POST":
                    response = requests.post(
                        url,
                        headers=self.headers,
                        json=data,
                        timeout=self.timeout,
                        stream=stream
                    )
                else:
                    raise ValueError(f"Unsupported method: {method}")
                
                # Handle different status codes
                if response.status_code == 200:
                    if stream:
                        return response
                    return response.json()
                elif response.status_code == 400:
                    error_data = response.json()
                    raise GeminiProxyError(f"Bad request: {error_data.get('error', {}).get('message', 'Unknown error')}")
                elif response.status_code == 401:
                    raise GeminiProxyError("Invalid API key")
                elif response.status_code == 429:
                    retry_after = response.headers.get('Retry-After', 60)
                    logger.warning(f"Rate limited, retrying after {retry_after} seconds")
                    time.sleep(int(retry_after))
                    continue
                elif response.status_code >= 500:
                    logger.warning(f"Server error (attempt {attempt + 1}/{self.max_retries})")
                    if attempt < self.max_retries - 1:
                        time.sleep(2 ** attempt)  # Exponential backoff
                        continue
                    else:
                        raise GeminiProxyError(f"Server error: {response.status_code}")
                else:
                    raise GeminiProxyError(f"Unexpected status code: {response.status_code}")
            
            except Timeout:
                if attempt < self.max_retries - 1:
                    logger.warning(f"Timeout (attempt {attempt + 1}/{self.max_retries})")
                    time.sleep(2 ** attempt)
                    continue
                else:
                    raise GeminiProxyError("Request timeout")
            except ConnectionError:
                if attempt < self.max_retries - 1:
                    logger.warning(f"Connection error (attempt {attempt + 1}/{self.max_retries})")
                    time.sleep(2 ** attempt)
                    continue
                else:
                    raise GeminiProxyError("Connection error")
            except RequestException as e:
                raise GeminiProxyError(f"Request failed: {str(e)}")
    
    def generate_content(
        self,
        prompt: str,
        model: str = "gemini-2.5-flash",
        temperature: float = 0.9,
        max_output_tokens: int = 1024,
        system_instruction: Optional[str] = None,
        safety_settings: Optional[List[Dict]] = None
    ) -> Dict:
        """Generate content with comprehensive error handling"""
        try:
            data = {
                "contents": [
                    {
                        "parts": [{"text": prompt}],
                        "role": "user"
                    }
                ],
                "generationConfig": {
                    "temperature": temperature,
                    "maxOutputTokens": max_output_tokens
                }
            }
            
            if system_instruction:
                data["systemInstruction"] = {
                    "parts": [{"text": system_instruction}],
                    "role": "user"
                }
            
            if safety_settings:
                data["safetySettings"] = safety_settings
            
            response = self._make_request("POST", f"models/{model}:generateContent", data)
            
            # Validate response structure
            if "candidates" not in response or not response["candidates"]:
                raise GeminiProxyError("No candidates in response")
            
            return response
        
        except Exception as e:
            logger.error(f"Error generating content: {str(e)}")
            raise
    
    def generate_content_stream(
        self,
        prompt: str,
        model: str = "gemini-2.5-flash",
        temperature: float = 0.9,
        max_output_tokens: int = 1024
    ):
        """Generate content with streaming"""
        try:
            data = {
                "contents": [
                    {
                        "parts": [{"text": prompt}],
                        "role": "user"
                    }
                ],
                "generationConfig": {
                    "temperature": temperature,
                    "maxOutputTokens": max_output_tokens
                }
            }
            
            response = self._make_request(
                "POST",
                f"models/{model}:streamGenerateContent",
                data,
                stream=True
            )
            
            for line in response.iter_lines():
                if line:
                    try:
                        yield json.loads(line.decode('utf-8'))
                    except json.JSONDecodeError:
                        continue
        
        except Exception as e:
            logger.error(f"Error in streaming: {str(e)}")
            raise
    
    def batch_generate(
        self,
        prompts: List[str],
        model: str = "gemini-2.5-flash",
        temperature: float = 0.9,
        max_output_tokens: int = 1024,
        delay_between_requests: float = 1.0
    ) -> List[Dict]:
        """Generate content for multiple prompts with rate limiting"""
        results = []
        
        for i, prompt in enumerate(prompts):
            try:
                logger.info(f"Processing prompt {i + 1}/{len(prompts)}")
                result = self.generate_content(
                    prompt=prompt,
                    model=model,
                    temperature=temperature,
                    max_output_tokens=max_output_tokens
                )
                results.append(result)
                
                # Add delay between requests to avoid rate limiting
                if i < len(prompts) - 1:
                    time.sleep(delay_between_requests)
            
            except Exception as e:
                logger.error(f"Error processing prompt {i + 1}: {str(e)}")
                results.append({"error": str(e)})
        
        return results

# Contoh penggunaan advanced client
if __name__ == "__main__":
    try:
        client = AdvancedGeminiProxyClient()
        
        # Basic usage with error handling
        print("=== Basic Chat ===")
        response = client.generate_content("Halo! Siapa kamu?")
        print(response["candidates"][0]["content"]["parts"][0]["text"])
        
        # Batch processing
        print("\n=== Batch Processing ===")
        prompts = [
            "Apa ibu kota Indonesia?",
            "Berapa populasi Indonesia?",
            "Apa mata uang Indonesia?"
        ]
        
        results = client.batch_generate(prompts)
        for i, result in enumerate(results):
            if "error" in result:
                print(f"Prompt {i + 1}: Error - {result['error']}")
            else:
                text = result["candidates"][0]["content"]["parts"][0]["text"]
                print(f"Prompt {i + 1}: {text[:100]}...")
    
    except GeminiProxyError as e:
        logger.error(f"Gemini Proxy Error: {str(e)}")
    except Exception as e:
        logger.error(f"Unexpected error: {str(e)}")
```

### 3. Error Handling Komprehensif

#### Error Types and Solutions
```python
class GeminiErrorHandler:
    """Comprehensive error handling for Gemini Proxy"""
    
    @staticmethod
    def handle_error(error_response: Dict) -> str:
        """Handle different types of errors"""
        if "error" not in error_response:
            return "Unknown error occurred"
        
        error = error_response["error"]
        code = error.get("code", 0)
        message = error.get("message", "Unknown error")
        status = error.get("status", "UNKNOWN")
        
        error_handlers = {
            400: GeminiErrorHandler._handle_bad_request,
            401: GeminiErrorHandler._handle_unauthorized,
            403: GeminiErrorHandler._handle_forbidden,
            404: GeminiErrorHandler._handle_not_found,
            429: GeminiErrorHandler._handle_rate_limit,
            500: GeminiErrorHandler._handle_server_error,
            503: GeminiErrorHandler._handle_service_unavailable
        }
        
        handler = error_handlers.get(code, GeminiErrorHandler._handle_generic_error)
        return handler(message, status)
    
    @staticmethod
    def _handle_bad_request(message: str, status: str) -> str:
        """Handle 400 Bad Request errors"""
        suggestions = [
            "Check your JSON format",
            "Verify required fields are present",
            "Ensure model name is correct",
            "Check content is not empty"
        ]
        return f"Bad Request: {message}. Suggestions: {', '.join(suggestions)}"
    
    @staticmethod
    def _handle_unauthorized(message: str, status: str) -> str:
        """Handle 401 Unauthorized errors"""
        return f"Unauthorized: {message}. Please check your API key."
    
    @staticmethod
    def _handle_forbidden(message: str, status: str) -> str:
        """Handle 403 Forbidden errors"""
        return f"Forbidden: {message}. You don't have permission to access this resource."
    
    @staticmethod
    def _handle_not_found(message: str, status: str) -> str:
        """Handle 404 Not Found errors"""
        return f"Not Found: {message}. Check the endpoint URL and model name."
    
    @staticmethod
    def _handle_rate_limit(message: str, status: str) -> str:
        """Handle 429 Rate Limit errors"""
        return f"Rate Limit Exceeded: {message}. Please wait before making another request."
    
    @staticmethod
    def _handle_server_error(message: str, status: str) -> str:
        """Handle 500 Server Error"""
        return f"Server Error: {message}. Please try again later."
    
    @staticmethod
    def _handle_service_unavailable(message: str, status: str) -> str:
        """Handle 503 Service Unavailable"""
        return f"Service Unavailable: {message}. The service is temporarily down."
    
    @staticmethod
    def _handle_generic_error(message: str, status: str) -> str:
        """Handle generic errors"""
        return f"Error ({status}): {message}"
```

### 4. Rate Limiting Implementation

#### Advanced Rate Limiter
```python
import time
import threading
from collections import deque
from typing import Deque

class AdvancedRateLimiter:
    """Advanced rate limiter with multiple strategies"""
    
    def __init__(
        self,
        requests_per_minute: int = 60,
        requests_per_hour: int = 3600,
        burst_limit: int = 10
    ):
        self.requests_per_minute = requests_per_minute
        self.requests_per_hour = requests_per_hour
        self.burst_limit = burst_limit
        
        self.minute_requests: Deque[float] = deque()
        self.hour_requests: Deque[float] = deque()
        self.burst_requests: Deque[float] = deque()
        
        self.lock = threading.Lock()
    
    def _cleanup_old_requests(self, requests: Deque[float], window: float) -> Deque[float]:
        """Remove requests older than the time window"""
        now = time.time()
        while requests and now - requests[0] > window:
            requests.popleft()
        return requests
    
    def wait_if_needed(self):
        """Wait if any rate limit would be exceeded"""
        with self.lock:
            now = time.time()
            
            # Clean up old requests
            self.minute_requests = self._cleanup_old_requests(self.minute_requests, 60)
            self.hour_requests = self._cleanup_old_requests(self.hour_requests, 3600)
            self.burst_requests = self._cleanup_old_requests(self.burst_requests, 1)
            
            # Check limits
            sleep_time = 0
            
            # Check burst limit (requests per second)
            if len(self.burst_requests) >= self.burst_limit:
                sleep_time = max(sleep_time, 1.0)
            
            # Check minute limit
            if len(self.minute_requests) >= self.requests_per_minute:
                oldest_request = self.minute_requests[0]
                sleep_time = max(sleep_time, 60 - (now - oldest_request))
            
            # Check hour limit
            if len(self.hour_requests) >= self.requests_per_hour:
                oldest_request = self.hour_requests[0]
                sleep_time = max(sleep_time, 3600 - (now - oldest_request))
            
            if sleep_time > 0:
                logger.info(f"Rate limit reached, sleeping for {sleep_time:.2f} seconds")
                time.sleep(sleep_time)
            
            # Record this request
            self.minute_requests.append(now)
            self.hour_requests.append(now)
            self.burst_requests.append(now)
```

---

## Best Practices

### 1. Performance Optimization

#### Request Optimization
```python
# Use appropriate model for task
def choose_model(task_type: str, complexity: str) -> str:
    """Choose the best model based on task requirements"""
    if task_type == "simple_chat" and complexity == "low":
        return "gemini-2.5-flash"
    elif task_type == "complex_analysis" or complexity == "high":
        return "gemini-2.5-pro"
    else:
        return "gemini-2.5-flash"  # Default to faster model

# Optimize token usage
def optimize_prompt(prompt: str) -> str:
    """Optimize prompt to reduce token usage"""
    # Remove redundant words
    # Use concise language
    # Remove unnecessary formatting
    return prompt.strip()

# Batch similar requests
def batch_requests(requests: List[Dict], batch_size: int = 5) -> List[List[Dict]]:
    """Group similar requests for batch processing"""
    batches = []
    for i in range(0, len(requests), batch_size):
        batches.append(requests[i:i + batch_size])
    return batches
```

#### Caching Strategy
```python
import hashlib
import json
from typing import Dict, Optional
import pickle
import os

class ResponseCache:
    """Simple file-based caching for API responses"""
    
    def __init__(self, cache_dir: str = "gemini_cache"):
        self.cache_dir = cache_dir
        os.makedirs(cache_dir, exist_ok=True)
    
    def _get_cache_key(self, prompt: str, model: str, **kwargs) -> str:
        """Generate cache key from request parameters"""
        cache_data = {
            "prompt": prompt,
            "model": model,
            **kwargs
        }
        cache_string = json.dumps(cache_data, sort_keys=True)
        return hashlib.md5(cache_string.encode()).hexdigest()
    
    def get(self, prompt: str, model: str, **kwargs) -> Optional[Dict]:
        """Get cached response"""
        cache_key = self._get_cache_key(prompt, model, **kwargs)
        cache_file = os.path.join(self.cache_dir, f"{cache_key}.pkl")
        
        if os.path.exists(cache_file):
            try:
                with open(cache_file, 'rb') as f:
                    return pickle.load(f)
            except Exception:
                return None
        return None
    
    def set(self, prompt: str, model: str, response: Dict, **kwargs):
        """Cache response"""
        cache_key = self._get_cache_key(prompt, model, **kwargs)
        cache_file = os.path.join(self.cache_dir, f"{cache_key}.pkl")
        
        try:
            with open(cache_file, 'wb') as f:
                pickle.dump(response, f)
        except Exception:
            pass  # Fail silently if caching fails
```

### 2. Security Considerations

#### API Key Management
```python
import os
from cryptography.fernet import Fernet

class SecureAPIKeyManager:
    """Secure API key management"""
    
    def __init__(self, key_file: str = "api_key.enc"):
        self.key_file = key_file
        self.cipher_suite = Fernet(self._get_or_create_key())
    
    def _get_or_create_key(self) -> bytes:
        """Get or create encryption key"""
        key_file = "encryption.key"
        if os.path.exists(key_file):
            with open(key_file, 'rb') as f:
                return f.read()
        else:
            key = Fernet.generate_key()
            with open(key_file, 'wb') as f:
                f.write(key)
            return key
    
    def store_api_key(self, api_key: str):
        """Encrypt and store API key"""
        encrypted_key = self.cipher_suite.encrypt(api_key.encode())
        with open(self.key_file, 'wb') as f:
            f.write(encrypted_key)
    
    def get_api_key(self) -> str:
        """Decrypt and retrieve API key"""
        if os.path.exists(self.key_file):
            with open(self.key_file, 'rb') as f:
                encrypted_key = f.read()
            return self.cipher_suite.decrypt(encrypted_key).decode()
        return ""
```

#### Input Validation
```python
import re
from typing import List

class InputValidator:
    """Validate inputs for API requests"""
    
    @staticmethod
    def validate_prompt(prompt: str) -> bool:
        """Validate prompt input"""
        if not prompt or not prompt.strip():
            return False
        
        # Check for potentially harmful content
        harmful_patterns = [
            r'<script[^>]*>.*?</script>',
            r'javascript:',
            r'data:text/html',
        ]
        
        for pattern in harmful_patterns:
            if re.search(pattern, prompt, re.IGNORECASE):
                return False
        
        return len(prompt.strip()) <= 100000  # Reasonable length limit
    
    @staticmethod
    def validate_model(model: str, available_models: List[str]) -> bool:
        """Validate model name"""
        return model in available_models
    
    @staticmethod
    def validate_temperature(temp: float) -> bool:
        """Validate temperature parameter"""
        return 0.0 <= temp <= 2.0
    
    @staticmethod
    def validate_max_tokens(tokens: int) -> bool:
        """Validate max tokens parameter"""
        return 1 <= tokens <= 8192
```

### 3. Cost Management

#### Token Usage Tracking
```python
class TokenTracker:
    """Track token usage for cost management"""
    
    def __init__(self):
        self.usage_history = []
        self.daily_limit = 100000  # Example daily limit
        self.cost_per_token = {
            "gemini-2.5-flash": 0.0001,  # Example costs
            "gemini-2.5-pro": 0.0005
        }
    
    def track_usage(self, model: str, prompt_tokens: int, completion_tokens: int):
        """Track token usage"""
        total_tokens = prompt_tokens + completion_tokens
        cost = total_tokens * self.cost_per_token.get(model, 0.0001)
        
        usage_record = {
            "timestamp": time.time(),
            "model": model,
            "prompt_tokens": prompt_tokens,
            "completion_tokens": completion_tokens,
            "total_tokens": total_tokens,
            "cost": cost
        }
        
        self.usage_history.append(usage_record)
    
    def get_daily_usage(self) -> Dict:
        """Get daily usage statistics"""
        today = time.time() // 86400  # Unix day
        today_usage = [
            record for record in self.usage_history
            if record["timestamp"] // 86400 == today
        ]
        
        total_tokens = sum(record["total_tokens"] for record in today_usage)
        total_cost = sum(record["cost"] for record in today_usage)
        
        return {
            "total_tokens": total_tokens,
            "total_cost": total_cost,
            "limit_remaining": max(0, self.daily_limit - total_tokens)
        }
    
    def is_within_limit(self, estimated_tokens: int) -> bool:
        """Check if estimated usage is within daily limit"""
        daily_usage = self.get_daily_usage()
        return (daily_usage["total_tokens"] + estimated_tokens) <= self.daily_limit
```

#### Cost Optimization Strategies
```python
class CostOptimizer:
    """Optimize API usage for cost efficiency"""
    
    @staticmethod
    def optimize_model_selection(task_complexity: str, budget_constraint: float) -> str:
        """Select model based on task complexity and budget"""
        if budget_constraint < 0.01:  # Low budget
            return "gemini-2.5-flash"
        elif task_complexity == "high" and budget_constraint > 0.05:
            return "gemini-2.5-pro"
        else:
            return "gemini-2.5-flash"
    
    @staticmethod
    def optimize_response_length(context_length: int, max_response: int) -> int:
        """Optimize response length based on context"""
        # Shorter responses for simple queries
        if context_length < 100:
            return min(max_response, 256)
        elif context_length < 500:
            return min(max_response, 512)
        else:
            return max_response
    
    @staticmethod
    def batch_optimize(requests: List[Dict]) -> List[Dict]:
        """Optimize batch requests"""
        # Group similar requests
        # Remove duplicates
        # Prioritize important requests
        unique_requests = []
        seen = set()
        
        for req in requests:
            req_key = (req.get("prompt", ""), req.get("model", ""))
            if req_key not in seen:
                seen.add(req_key)
                unique_requests.append(req)
        
        return unique_requests
```

### 4. Monitoring and Analytics

#### Performance Monitoring
```python
import time
import statistics
from typing import List, Dict

class PerformanceMonitor:
    """Monitor API performance"""
    
    def __init__(self):
        self.response_times = []
        self.error_count = 0
        self.success_count = 0
    
    def record_request(self, start_time: float, success: bool, error: str = None):
        """Record request performance"""
        response_time = time.time() - start_time
        
        self.response_times.append(response_time)
        
        if success:
            self.success_count += 1
        else:
            self.error_count += 1
    
    def get_stats(self) -> Dict:
        """Get performance statistics"""
        if not self.response_times:
            return {}
        
        total_requests = self.success_count + self.error_count
        success_rate = (self.success_count / total_requests) * 100 if total_requests > 0 else 0
        
        return {
            "avg_response_time": statistics.mean(self.response_times),
            "median_response_time": statistics.median(self.response_times),
            "min_response_time": min(self.response_times),
            "max_response_time": max(self.response_times),
            "success_rate": success_rate,
            "total_requests": total_requests,
            "error_count": self.error_count
        }
```

---

## Troubleshooting

### Common Issues and Solutions

#### 1. Authentication Issues
**Problem:** 401 Unauthorized error
**Solutions:**
- Verify API key is correct
- Check if API key has expired
- Ensure Bearer token format is correct
- Check for extra spaces in API key

```bash
# Test API key
curl -X GET "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models" \
  -H "Authorization: Bearer ganti-api-anda"
```

#### 2. Rate Limiting
**Problem:** 429 Too Many Requests
**Solutions:**
- Implement rate limiting in client
- Use exponential backoff
- Reduce request frequency
- Consider batch processing

```python
# Exponential backoff implementation
import time

def exponential_backoff_request(request_func, max_retries=5):
    for attempt in range(max_retries):
        try:
            return request_func()
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            
            wait_time = 2 ** attempt
            time.sleep(wait_time)
```

#### 3. Model Not Available
**Problem:** 404 Model not found
**Solutions:**
- Check available models with `/models` endpoint
- Verify model name spelling
- Ensure model is supported by the proxy

```bash
# List available models
curl -X GET "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models" \
  -H "Authorization: Bearer ganti-api-anda"
```

#### 4. Invalid Request Format
**Problem:** 400 Bad Request
**Solutions:**
- Validate JSON format
- Check required fields
- Verify content structure
- Use proper data types

```python
# Request validation
def validate_request(request_data):
    required_fields = ["contents"]
    for field in required_fields:
        if field not in request_data:
            raise ValueError(f"Missing required field: {field}")
    
    if not request_data["contents"]:
        raise ValueError("Contents cannot be empty")
```

#### 5. Timeout Issues
**Problem:** Request timeout
**Solutions:**
- Increase timeout value
- Reduce request size
- Use streaming for long responses
- Implement retry logic

```python
# Timeout configuration
import requests

session = requests.Session()
session.timeout = 30  # 30 seconds timeout
```

### Debugging Tools

#### Request/Response Logger
```python
import logging
import json

class APILogger:
    """Log API requests and responses for debugging"""
    
    def __init__(self, log_file: str = "gemini_api.log"):
        logging.basicConfig(
            filename=log_file,
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s'
        )
        self.logger = logging.getLogger(__name__)
    
    def log_request(self, method: str, url: str, data: Dict):
        """Log API request"""
        self.logger.info(f"REQUEST: {method} {url}")
        self.logger.info(f"DATA: {json.dumps(data, indent=2)}")
    
    def log_response(self, response: Dict, status_code: int):
        """Log API response"""
        self.logger.info(f"RESPONSE STATUS: {status_code}")
        self.logger.info(f"RESPONSE DATA: {json.dumps(response, indent=2)}")
```

#### Health Check
```python
def health_check():
    """Check if API is accessible"""
    try:
        response = requests.get(
            "https://krsbeknjypkg.sg-members-1.clawcloudrun.com/proxy/gemini/v1beta/models",
            headers={
                "Authorization": "Bearer ganti-api-anda",
                "Content-Type": "application/json"
            },
            timeout=10
        )
        
        if response.status_code == 200:
            print("✅ API is accessible")
            return True
        else:
            print(f"❌ API returned status code: {response.status_code}")
            return False
    
    except Exception as e:
        print(f"❌ API health check failed: {str(e)}")
        return False
```

### Performance Issues

#### Slow Response Times
**Causes and Solutions:**
1. **Large requests** - Reduce input size
2. **Complex prompts** - Simplify prompts
3. **Network latency** - Use appropriate timeout
4. **Server load** - Implement retry logic

#### Memory Issues
**Solutions:**
- Use streaming for large responses
- Clear cache periodically
- Process responses incrementally
- Monitor memory usage

```python
# Memory-efficient streaming
def process_streaming_response(response_stream):
    """Process streaming response efficiently"""
    for chunk in response_stream:
        # Process chunk immediately
        yield chunk
        
        # Clear from memory if needed
        del chunk
```

---

## Kesimpulan

Panduan ini menyediakan implementasi lengkap untuk Gemini API dengan:

1. **Informasi dasar** yang akurat dan terverifikasi
2. **Endpoint lengkap** dengan contoh penggunaan
3. **Fitur lanjutan** termasuk multimodal dan function calling
4. **Implementasi Python** dengan error handling komprehensif
5. **Best practices** untuk optimasi dan keamanan
6. **Troubleshooting** untuk masalah umum

Pastikan untuk:
- Menggunakan API key dengan benar
- Memilih model yang sesuai untuk task
- Implementasi rate limiting untuk menghindari error
- Monitor penggunaan token untuk cost management
- Menggunakan error handling yang tepat

Untuk informasi lebih lanjut atau update terbaru, selalu merujuk pada dokumentasi resmi gemini api.
