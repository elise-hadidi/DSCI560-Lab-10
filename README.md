# DSCI560-Lab-10


## Setting up the Web Application
The purpose of this web application is to create an interface for interacting with a ChatBot.

### MySQL: Install & Database Setup
1. Install MySQL Server (consult your OS package manager or installer).
2. Start MySQL and secure it (set root password).
3. Create database and user (or run sql/schema.sql):
Execute:
`CREATE DATABASE groupchat CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'chatuser'@'localhost' IDENTIFIED BY 'chatpass';
GRANT ALL PRIVILEGES ON groupchat.* TO 'chatuser'@'localhost';
FLUSH PRIVILEGES;`

### Backend (FastAPI) Setup
Execute in groupchat_app_src:
`cd backend
python -m venv .venv
source .venv/bin/activate (Windows: .venv\Scripts\activate)
pip install -r requirements.txt`
1. Copy .env.example to ../.env and edit values (DB DSN, JWT secret, LLM endpoint)
2. Run the server: uvicorn app:app --host 0.0.0.0 --port 8000
3. Open http://localhost:8000 in a browser.
4. Sign up, then log in. Messages with a “?” trigger the LLM bot.

### Configuration (.env)
1. Edit .env using an editor like gedit/nano/vim.
2. Then copy paste the bellow lines and save the file.
`DATABASE_URL=mysql+asyncmy://chatuser:chatpass@localhost:3306/groupchat
JWT_SECRET=<replace with a long random string>
JWT_EXPIRE_MINUTES=43200
LLM_API_BASE=http://localhost:8001/v1 
LLM_MODEL=llama-3-8b-instruct
LLM_API_KEY=
APP_HOST=0.0.0.0
APP_PORT=8000`

### Self-Hosted Llama 3 Endpoint
1. Build llama.cpp:
`git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
cmake -B build
cmake --build build --config Release`
2. Obtain Llama 3 weights from Meta/Hugging Face. Accept the license. Download the
model. Place the model in the models folder of llama.cpp.
3. Start the HTTP server (OpenAI-compatible):
`cd llama.cpp/build/bin
./llama-server -m /path/to/model.gguf --host 0.0.0.0 --port 8001 --ctx-size
4096`
4. Optionally enforce an API key:
`# ./server -m /path/to/model.gguf --host 0.0.0.0 --port 8001 --api-key MYKEY`
5. In the chat app .env set:
`LLM_API_BASE=http://localhost:8001/v1
LLM_MODEL=<your model name or leave as placeholder>
LLM_API_KEY=MYKEY (only if you set --api-key)`
6. Restart the FastAPI app and test by sending a question in chat. 
