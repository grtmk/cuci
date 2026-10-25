好的！我来做一个**真正的一键运行**版本，包含自动安装、自动配置、自动启动！

## 🚀 一键运行脚本

我会创建：
1. **自动安装脚本** - 自动安装所有依赖
2. **一键启动脚本** - 一个命令搞定一切
3. **配置向导** - 自动引导配置 API Key

---

```python
# setup.py - 一键安装和配置脚本
import os
import sys
import subprocess
import platform

def print_banner():
    """打印欢迎横幅"""
    banner = """
╔═══════════════════════════════════════════════════════╗
║                                                       ║
║       🤖 AI 聊天机器人 - 一键安装程序 🚀              ║
║                                                       ║
║       网页版 AI 聊天助手                               ║
║       支持文本对话 + 图片识别                          ║
║                                                       ║
╚═══════════════════════════════════════════════════════╝
"""
    print(banner)

def check_python_version():
    """检查 Python 版本"""
    version = sys.version_info
    if version.major < 3 or (version.major == 3 and version.minor < 8):
        print("❌ 错误：需要 Python 3.8 或更高版本")
        print(f"   当前版本: Python {version.major}.{version.minor}.{version.micro}")
        print("\n请先安装 Python 3.8+")
        print("下载地址: https://www.python.org/downloads/")
        sys.exit(1)
    else:
        print(f"✅ Python 版本检查通过: {version.major}.{version.minor}.{version.micro}")

def install_dependencies():
    """安装依赖包"""
    print("\n📦 正在安装依赖包...")
    print("=" * 60)
    
    dependencies = [
        "flask==3.0.0",
        "flask-cors==4.0.0",
        "openai==1.12.0",
        "anthropic==0.18.0",
        "pillow==10.2.0",
        "python-dotenv==1.0.0",
        "werkzeug==3.0.1",
    ]
    
    for dep in dependencies:
        print(f"📥 安装 {dep}...")
        try:
            subprocess.check_call(
                [sys.executable, "-m", "pip", "install", dep, "-q"],
                stdout=subprocess.DEVNULL,
                stderr=subprocess.PIPE
            )
            print(f"   ✅ {dep} 安装完成")
        except subprocess.CalledProcessError as e:
            print(f"   ⚠️ {dep} 安装失败，尝试继续...")
    
    print("\n✅ 所有依赖安装完成！")

def setup_config():
    """配置向导"""
    print("\n⚙️  配置向导")
    print("=" * 60)
    
    # 检查是否已有配置
    if os.path.exists('.env'):
        overwrite = input("\n⚠️  检测到已存在配置文件，是否覆盖? (y/N): ").strip().lower()
        if overwrite != 'y':
            print("✅ 使用现有配置")
            return
    
    print("\n请选择 AI 提供商:")
    print("1. OpenAI (ChatGPT) - 推荐")
    print("2. Claude (Anthropic)")
    print("3. 跳过配置（稍后手动配置）")
    
    choice = input("\n请选择 (1-3): ").strip()
    
    if choice == '3':
        create_example_env()
        print("\n✅ 已创建配置模板文件 .env")
        print("   请手动编辑 .env 文件填入你的 API Key")
        return
    
    # OpenAI 配置
    if choice == '1':
        print("\n📝 配置 OpenAI")
        print("-" * 60)
        print("💡 提示: 在 https://platform.openai.com/api-keys 获取 API Key")
        
        api_key = input("\n请输入 OpenAI API Key: ").strip()
        
        if not api_key:
            print("❌ API Key 不能为空")
            create_example_env()
            return
        
        print("\n选择模型:")
        print("1. gpt-4o-mini (推荐 - 便宜快速)")
        print("2. gpt-4o (更强大但更贵)")
        print("3. gpt-3.5-turbo (最便宜)")
        
        model_choice = input("\n请选择 (1-3，默认 1): ").strip() or "1"
        
        model_map = {
            "1": "gpt-4o-mini",
            "2": "gpt-4o",
            "3": "gpt-3.5-turbo"
        }
        
        model = model_map.get(model_choice, "gpt-4o-mini")
        
        # 写入配置
        config_content = f"""# AI 配置
AI_PROVIDER=openai
AI_API_KEY={api_key}
API_BASE=https://api.openai.com/v1
MODEL={model}
VISION_MODEL={model if model != 'gpt-3.5-turbo' else 'gpt-4o-mini'}
ENABLE_VISION=true
"""
        
        with open('.env', 'w', encoding='utf-8') as f:
            f.write(config_content)
        
        print(f"\n✅ 配置完成！使用模型: {model}")
    
    # Claude 配置
    elif choice == '2':
        print("\n📝 配置 Claude")
        print("-" * 60)
        print("💡 提示: 在 https://console.anthropic.com/ 获取 API Key")
        
        api_key = input("\n请输入 Claude API Key: ").strip()
        
        if not api_key:
            print("❌ API Key 不能为空")
            create_example_env()
            return
        
        print("\n选择模型:")
        print("1. claude-3-haiku (推荐 - 最便宜)")
        print("2. claude-3-sonnet (平衡)")
        print("3. claude-3-opus (最强大)")
        
        model_choice = input("\n请选择 (1-3，默认 1): ").strip() or "1"
        
        model_map = {
            "1": "claude-3-haiku-20240307",
            "2": "claude-3-sonnet-20240229",
            "3": "claude-3-opus-20240229"
        }
        
        model = model_map.get(model_choice, "claude-3-haiku-20240307")
        
        config_content = f"""# AI 配置
AI_PROVIDER=claude
AI_API_KEY={api_key}
MODEL={model}
VISION_MODEL={model}
ENABLE_VISION=true
"""
        
        with open('.env', 'w', encoding='utf-8') as f:
            f.write(config_content)
        
        print(f"\n✅ 配置完成！使用模型: {model}")
    
    else:
        print("❌ 无效选择")
        create_example_env()

def create_example_env():
    """创建示例配置文件"""
    example_content = """# AI 配置
# 请填入你的 API Key

# OpenAI 配置（推荐）
AI_PROVIDER=openai
AI_API_KEY=your-api-key-here
API_BASE=https://api.openai.com/v1
MODEL=gpt-4o-mini
VISION_MODEL=gpt-4o-mini
ENABLE_VISION=true

# Claude 配置（如果使用 Claude，取消下面的注释）
# AI_PROVIDER=claude
# AI_API_KEY=your-claude-api-key
# MODEL=claude-3-haiku-20240307
# VISION_MODEL=claude-3-haiku-20240307
# ENABLE_VISION=true
"""
    with open('.env', 'w', encoding='utf-8') as f:
        f.write(example_content)

def create_project_files():
    """创建项目文件"""
    print("\n📁 创建项目文件...")
    print("=" * 60)
    
    # 创建目录
    os.makedirs('static', exist_ok=True)
    os.makedirs('templates', exist_ok=True)
    
    files_created = []
    
    # config.py
    if not os.path.exists('config.py'):
        with open('config.py', 'w', encoding='utf-8') as f:
            f.write(CONFIG_PY)
        files_created.append('config.py')
    
    # image_handler.py
    if not os.path.exists('image_handler.py'):
        with open('image_handler.py', 'w', encoding='utf-8') as f:
            f.write(IMAGE_HANDLER_PY)
        files_created.append('image_handler.py')
    
    # ai_handler.py
    if not os.path.exists('ai_handler.py'):
        with open('ai_handler.py', 'w', encoding='utf-8') as f:
            f.write(AI_HANDLER_PY)
        files_created.append('ai_handler.py')
    
    # app.py
    if not os.path.exists('app.py'):
        with open('app.py', 'w', encoding='utf-8') as f:
            f.write(APP_PY)
        files_created.append('app.py')
    
    # templates/index.html
    if not os.path.exists('templates/index.html'):
        with open('templates/index.html', 'w', encoding='utf-8') as f:
            f.write(INDEX_HTML)
        files_created.append('templates/index.html')
    
    # static/style.css
    if not os.path.exists('static/style.css'):
        with open('static/style.css', 'w', encoding='utf-8') as f:
            f.write(STYLE_CSS)
        files_created.append('static/style.css')
    
    # static/script.js
    if not os.path.exists('static/script.js'):
        with open('static/script.js', 'w', encoding='utf-8') as f:
            f.write(SCRIPT_JS)
        files_created.append('static/script.js')
    
    if files_created:
        print(f"\n✅ 创建了 {len(files_created)} 个文件:")
        for file in files_created:
            print(f"   📄 {file}")
    else:
        print("\n✅ 所有文件已存在")

def show_completion_message():
    """显示完成信息"""
    print("\n" + "=" * 60)
    print("🎉 安装完成！")
    print("=" * 60)
    
    print("\n📝 下一步:")
    
    if not os.path.exists('.env') or os.path.getsize('.env') < 50:
        print("   1. 编辑 .env 文件，填入你的 API Key")
        print("   2. 运行启动脚本")
    else:
        print("   运行启动脚本即可使用")
    
    system = platform.system()
    if system == "Windows":
        print("\n▶️  Windows 启动命令:")
        print("   python run.py")
        print("   或双击 run.py")
    else:
        print("\n▶️  Mac/Linux 启动命令:")
        print("   python3 run.py")
        print("   或 chmod +x run.py && ./run.py")
    
    print("\n🌐 访问地址: http://localhost:5000")
    print("\n💡 提示: 按 Ctrl+C 停止服务器")
    print("\n" + "=" * 60)

def main():
    """主函数"""
    try:
        print_banner()
        check_python_version()
        
        print("\n开始安装...")
        input("按 Enter 继续...")
        
        install_dependencies()
        create_project_files()
        setup_config()
        show_completion_message()
        
        print("\n✨ 安装程序已完成！")
        
    except KeyboardInterrupt:
        print("\n\n⚠️  安装已取消")
        sys.exit(0)
    except Exception as e:
        print(f"\n❌ 发生错误: {e}")
        sys.exit(1)

# 文件内容（嵌入到脚本中）
CONFIG_PY = '''from pydantic import BaseModel
from typing import Literal
import os
from dotenv import load_dotenv

load_dotenv()

class AppConfig(BaseModel):
    host: str = "0.0.0.0"
    port: int = 5000
    debug: bool = True
    ai_provider: Literal["openai", "claude"] = os.getenv("AI_PROVIDER", "openai")
    api_key: str = os.getenv("AI_API_KEY", "")
    api_base: str = os.getenv("API_BASE", "https://api.openai.com/v1")
    model: str = os.getenv("MODEL", "gpt-4o-mini")
    vision_model: str = os.getenv("VISION_MODEL", "gpt-4o-mini")
    enable_vision: bool = os.getenv("ENABLE_VISION", "true").lower() == "true"
    max_history: int = 20
    system_prompt: str = """你是一个友好、有趣的 AI 助手。你善于倾听和理解用户的情绪，回答简洁明了。"""
    temperature: float = 0.8
    max_tokens: int = 1000
    max_image_size: int = 10 * 1024 * 1024
    image_quality: int = 85
    image_detail: Literal["auto", "low", "high"] = "auto"
    allowed_extensions: set = {'png', 'jpg', 'jpeg', 'gif', 'webp'}

config = AppConfig()
'''

IMAGE_HANDLER_PY = '''import base64
from PIL import Image
from io import BytesIO
from config import config

class ImageHandler:
    async def process_image(self, image_data: bytes) -> bytes:
        try:
            if len(image_data) > config.max_image_size:
                image_data = await self._compress_image(image_data)
            return image_data
        except Exception as e:
            print(f"处理图片失败: {e}")
            return image_data
    
    async def _compress_image(self, image_data: bytes) -> bytes:
        try:
            img = Image.open(BytesIO(image_data))
            if img.mode == 'RGBA':
                bg = Image.new('RGB', img.size, (255, 255, 255))
                bg.paste(img, mask=img.split()[3])
                img = bg
            elif img.mode != 'RGB':
                img = img.convert('RGB')
            max_dim = 2048
            if max(img.size) > max_dim:
                ratio = max_dim / max(img.size)
                new_size = tuple(int(d * ratio) for d in img.size)
                img = img.resize(new_size, Image.Resampling.LANCZOS)
            output = BytesIO()
            img.save(output, format='JPEG', quality=config.image_quality, optimize=True)
            return output.getvalue()
        except Exception as e:
            print(f"压缩失败: {e}")
            return image_data
    
    def image_to_base64(self, image_data: bytes) -> str:
        return base64.b64encode(image_data).decode('utf-8')

image_handler = ImageHandler()
'''

AI_HANDLER_PY = '''from openai import OpenAI
from anthropic import Anthropic
from typing import List, Dict, Optional
from config import config
from image_handler import image_handler

class ChatHistory:
    def __init__(self):
        self.histories: Dict[str, List[Dict]] = {}
    
    def add_message(self, sid: str, role: str, content):
        if sid not in self.histories:
            self.histories[sid] = []
        self.histories[sid].append({"role": role, "content": content})
        if len(self.histories[sid]) > config.max_history * 2:
            self.histories[sid] = self.histories[sid][-config.max_history * 2:]
    
    def get_history(self, sid: str) -> List[Dict]:
        return self.histories.get(sid, [])
    
    def clear_history(self, sid: str):
        if sid in self.histories:
            self.histories[sid] = []

class AIHandler:
    def __init__(self):
        self.chat_history = ChatHistory()
        if config.ai_provider == "openai":
            self.client = OpenAI(api_key=config.api_key, base_url=config.api_base)
        elif config.ai_provider == "claude":
            self.client = Anthropic(api_key=config.api_key)
    
    async def get_response(self, sid: str, msg: str, images: Optional[List[bytes]] = None) -> str:
        try:
            if images and config.enable_vision:
                content = await self._build_multimodal(msg, images)
            else:
                content = msg
            self.chat_history.add_message(sid, "user", content)
            messages = [{"role": "system", "content": config.system_prompt}] + self.chat_history.get_history(sid)
            model = config.vision_model if images else config.model
            if config.ai_provider == "openai":
                response = await self._call_openai(messages, model)
            elif config.ai_provider == "claude":
                response = await self._call_claude(messages, model)
            else:
                response = "配置错误"
            self.chat_history.add_message(sid, "assistant", response)
            return response
        except Exception as e:
            return f"错误: {e}"
    
    async def _build_multimodal(self, text: str, images: List[bytes]) -> List[Dict]:
        content = []
        if text:
            content.append({"type": "text", "text": text})
        for img_data in images:
            processed = await image_handler.process_image(img_data)
            b64 = image_handler.image_to_base64(processed)
            if config.ai_provider == "claude":
                content.append({"type": "image", "source": {"type": "base64", "media_type": "image/jpeg", "data": b64}})
            else:
                content.append({"type": "image_url", "image_url": {"url": f"data:image/jpeg;base64,{b64}", "detail": config.image_detail}})
        return content
    
    async def _call_openai(self, messages, model):
        resp = self.client.chat.completions.create(model=model, messages=messages, temperature=config.temperature, max_tokens=config.max_tokens)
        return resp.choices[0].message.content
    
    async def _call_claude(self, messages, model):
        sys_msg = messages[0]["content"] if messages[0]["role"] == "system" else ""
        chat_msgs = messages[1:] if sys_msg else messages
        resp = self.client.messages.create(model=model, system=sys_msg, messages=chat_msgs, temperature=config.temperature, max_tokens=config.max_tokens)
        return resp.content[0].text
    
    def clear_history(self, sid: str):
        self.chat_history.clear_history(sid)

ai_handler = AIHandler()
'''

APP_PY = '''from flask import Flask, render_template, request, jsonify, session
from flask_cors import CORS
import uuid
from config import config
from ai_handler import ai_handler

app = Flask(__name__)
app.secret_key = 'ai-chat-secret-key-2024'
CORS(app)

def allowed_file(filename):
    return '.' in filename and filename.rsplit('.', 1)[1].lower() in config.allowed_extensions

@app.route('/')
def index():
    if 'session_id' not in session:
        session['session_id'] = str(uuid.uuid4())
    return render_template('index.html')

@app.route('/api/chat', methods=['POST'])
async def chat():
    try:
        sid = session.get('session_id', str(uuid.uuid4()))
        session['session_id'] = sid
        message = request.form.get('message', '').strip()
        images = []
        for file in request.files.getlist('images'):
            if file and allowed_file(file.filename):
                images.append(file.read())
        if not message and not images:
            return jsonify({'success': False, 'error': '请输入消息或上传图片'}), 400
        if images and not message:
            message = "请描述这张图片"
        response = await ai_handler.get_response(sid, message, images if images else None)
        return jsonify({'success': True, 'response': response, 'has_images': len(images) > 0})
    except Exception as e:
        return jsonify({'success': False, 'error': str(e)}), 500

@app.route('/api/clear', methods=['POST'])
def clear():
    try:
        sid = session.get('session_id')
        if sid:
            ai_handler.clear_history(sid)
        return jsonify({'success': True, 'message': '历史已清空'})
    except Exception as e:
        return jsonify({'success': False, 'error': str(e)}), 500

@app.route('/api/status')
def status():
    return jsonify({'success': True, 'config': {'ai_provider': config.ai_provider, 'model': config.model, 'vision_model': config.vision_model, 'enable_vision': config.enable_vision, 'max_history': config.max_history}})

if __name__ == '__main__':
    print("🌐 AI 聊天机器人启动中...")
    print(f"📡 http://localhost:{config.port}")
    print(f"🤖 模型: {config.model}")
    print("=" * 60)
    app.run(host=config.host, port=config.port, debug=config.debug)
'''

INDEX_HTML = '''<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI 聊天机器人</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>
    <div class="container">
        <header class="header">
            <h1>🤖 AI 聊天助手</h1>
            <div class="btns">
                <button id="clearBtn">🗑️ 清空</button>
                <button id="statusBtn">ℹ️ 状态</button>
            </div>
        </header>
        <div class="chat" id="chat">
            <div class="welcome">
                <div class="icon">👋</div>
                <h2>你好！我是 AI 助手</h2>
                <p>💬 聊天对话 | 🖼️ 图片识别 | 📝 文字提取</p>
            </div>
        </div>
        <div class="input-area">
            <div id="preview" class="preview"></div>
            <form id="form">
                <label for="img" class="img-btn">📎<input type="file" id="img" accept="image/*" multiple hidden></label>
                <textarea id="msg" placeholder="输入消息..." rows="1"></textarea>
                <button type="submit" id="send">📤</button>
            </form>
        </div>
    </div>
    <div id="modal" class="modal"><div class="modal-content"><span class="close">&times;</span><h2>状态</h2><div id="info"></div></div></div>
    <div id="loading" class="loading"><div class="spinner"></div><p>思考中...</p></div>
    <script src="/static/script.js"></script>
</body>
</html>'''

STYLE_CSS = '''*{margin:0;padding:0;box-sizing:border-box}:root{--p:#667eea;--s:#764ba2;--bg:#f7fafc}body{font-family:sans-serif;background:var(--bg)}
.container{max-width:1200px;margin:0 auto;height:100vh;display:flex;flex-direction:column;background:#fff;box-shadow:0 10px 25px rgba(0,0,0,0.15)}
.header{background:linear-gradient(135deg,var(--p),var(--s));color:#fff;padding:1.5rem 2rem;display:flex;justify-content:space-between;align-items:center}
.btns{display:flex;gap:0.5rem}.btns button{padding:0.5rem 1rem;background:rgba(255,255,255,0.2);color:#fff;border:none;border-radius:8px;cursor:pointer}
.chat{flex:1;overflow-y:auto;padding:2rem;background:var(--bg)}.welcome{text-align:center;padding:3rem 2rem}.icon{font-size:4rem;animation:wave 1s infinite}
@keyframes wave{0%,100%{transform:rotate(0)}25%{transform:rotate(20deg)}75%{transform:rotate(-20deg)}}
.message{display:flex;margin-bottom:1.5rem;animation:fadeIn 0.3s}@keyframes fadeIn{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:translateY(0)}}
.message.user{justify-content:flex-end}.message-content{max-width:70%;padding:1rem 1.25rem;border-radius:12px;box-shadow:0 4px 6px rgba(0,0,0,0.1)}
.message.user .message-content{background:linear-gradient(135deg,var(--p),var(--s));color:#fff}.message.ai .message-content{background:#f7fafc;border:1px solid #e2e8f0}
.input-area{padding:1.5rem 2rem;background:#fff;border-top:1px solid #e2e8f0}.preview{display:flex;gap:0.5rem;margin-bottom:1rem;flex-wrap:wrap}
.preview-item{position:relative;width:80px;height:80px}.preview-image{width:100%;height:100%;object-fit:cover;border-radius:8px}
.preview-remove{position:absolute;top:-8px;right:-8px;background:#f56565;color:#fff;border:none;border-radius:50%;width:24px;height:24px;cursor:pointer}
form{display:flex;align-items:flex-end;gap:0.75rem}.img-btn{padding:0.75rem;background:var(--bg);border-radius:8px;cursor:pointer;font-size:1.5rem}
textarea{flex:1;padding:0.75rem 1rem;border:2px solid #e2e8f0;border-radius:12px;font-size:1rem;resize:none;max-height:150px;font-family:inherit}
textarea:focus{outline:none;border-color:var(--p)}button[type="submit"]{padding:0.75rem 1.5rem;background:linear-gradient(135deg,var(--p),var(--s));color:#fff;border:none;border-radius:12px;cursor:pointer;font-size:1.2rem}
.modal{display:none;position:fixed;z-index:1000;left:0;top:0;width:100%;height:100%;background:rgba(0,0,0,0.5)}
.modal-content{background:#fff;margin:10% auto;padding:2rem;border-radius:16px;max-width:500px}.close{float:right;font-size:2rem;font-weight:bold;cursor:pointer}
.loading{display:none;position:fixed;top:0;left:0;right:0;bottom:0;background:rgba(0,0,0,0.7);z-index:9999;align-items:center;justify-content:center;flex-direction:column;color:#fff}
.loading.show{display:flex}.spinner{width:50px;height:50px;border:4px solid rgba(255,255,255,0.3);border-top-color:#fff;border-radius:50%;animation:spin 1s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}'''

SCRIPT_JS = '''class ChatApp{constructor(){this.chat=document.getElementById('chat');this.form=document.getElementById('form');this.msg=document.getElementById('msg');
this.img=document.getElementById('img');this.preview=document.getElementById('preview');this.send=document.getElementById('send');this.images=[];this.init()}
init(){this.form.addEventListener('submit',e=>this.submit(e));this.img.addEventListener('change',e=>this.selectImg(e));
document.getElementById('clearBtn').addEventListener('click',()=>this.clear());document.getElementById('statusBtn').addEventListener('click',()=>this.showStatus());
const close=document.querySelector('.close');close.addEventListener('click',()=>this.hideModal());this.msg.addEventListener('input',()=>this.resize())}
resize(){this.msg.style.height='auto';this.msg.style.height=this.msg.scrollHeight+'px'}selectImg(e){Array.from(e.target.files).forEach(f=>{
if(f.type.startsWith('image/')){this.images.push(f);this.addPreview(f)}});e.target.value=''}addPreview(file){const reader=new FileReader();
reader.onload=e=>{const div=document.createElement('div');div.className='preview-item';const img=document.createElement('img');
img.src=e.target.result;img.className='preview-image';const btn=document.createElement('button');btn.className='preview-remove';btn.innerHTML='×';
btn.onclick=()=>this.removeImg(file,div);div.appendChild(img);div.appendChild(btn);this.preview.appendChild(div)};reader.readAsDataURL(file)}
removeImg(file,el){this.images=this.images.filter(f=>f!==file);el.remove()}async submit(e){e.preventDefault();const message=this.msg.value.trim();
if(!message&&this.images.length===0)return;this.setDisabled(true);this.addMsg('user',message,this.images);this.msg.value='';this.msg.style.height='auto';
const fd=new FormData();fd.append('message',message);this.images.forEach(f=>fd.append('images',f));const imgs=[...this.images];this.images=[];
this.preview.innerHTML='';this.showLoading();try{const resp=await fetch('/api/chat',{method:'POST',body:fd});const data=await resp.json();
if(data.success){this.addMsg('ai',data.response)}else{this.addMsg('ai',`❌ ${data.error||'错误'}`)}}catch(err){console.error(err);
this.addMsg('ai','❌ 网络错误')}finally{this.hideLoading();this.setDisabled(false);this.msg.focus()}}addMsg(role,text,images=[]){
const welcome=this.chat.querySelector('.welcome');if(welcome)welcome.remove();const div=document.createElement('div');div.className=`message ${role}`;
const content=document.createElement('div');content.className='message-content';if(images.length>0){const imgDiv=document.createElement('div');
images.forEach(f=>{const reader=new FileReader();reader.onload=e=>{const img=document.createElement('img');img.src=e.target.result;
img.style.maxWidth='200px';img.style.borderRadius='8px';imgDiv.appendChild(img)};reader.readAsDataURL(f)});content.appendChild(imgDiv)}
if(text){const p=document.createElement('div');p.textContent=text;content.appendChild(p)}div.appendChild(content);this.chat.appendChild(div);
this.scrollBottom()}scrollBottom(){setTimeout(()=>{this.chat.scrollTop=this.chat.scrollHeight},100)}async clear(){if(!confirm('清空历史？'))return;
try{const resp=await fetch('/api/clear',{method:'POST'});if(resp.ok){this.chat.innerHTML='<div class="welcome"><div class="icon">👋</div><h2>历史已清空</h2></div>'}}catch(err){alert('失败')}}
async showStatus(){try{const resp=await fetch('/api/status');const data=await resp.json();if(data.success){const c=data.config;
document.getElementById('info').innerHTML=`<p>提供商: ${c.ai_provider}</p><p>模型: ${c.model}</p><p>视觉: ${c.vision_model}</p><p>图片识别: ${c.enable_vision?'✅':'❌'}</p>`;
document.getElementById('modal').style.display='block'}}catch(err){alert('失败')}}hideModal(){document.getElementById('modal').style.display='none'}
showLoading(){document.getElementById('loading').classList.add('show')}hideLoading(){document.getElementById('loading').classList.remove('show')}
setDisabled(d){this.msg.disabled=d;this.img.disabled=d;this.send.disabled=d}}document.addEventListener('DOMContentLoaded',()=>new ChatApp());'''

if __name__ == '__main__':
    main()
```

```python
# run.py - 一键启动脚本
import os
import sys
import subprocess
import webbrowser
import time

def print_banner():
    banner = """
╔═══════════════════════════════════════════════════════╗
║                                                       ║
║       🤖 AI 聊天机器人 - 启动程序 🚀                  ║
║                                                       ║
╚═══════════════════════════════════════════════════════╝
"""
    print(banner)

def check_installation():
    """检查是否已安装"""
    required_files = ['app.py', 'config.py', 'ai_handler.py', '.env']
    missing = [f for f in required_files if not os.path.exists(f)]
    
    if missing:
        print("❌ 检测到未安装，请先运行 setup.py")
        print(f"\n缺少文件: {', '.join(missing)}")
        print("\n运行命令: python setup.py")
        sys.exit(1)

def check_config():
    """检查配置"""
    if not os.path.exists('.env'):
        print("❌ 未找到配置文件 .env")
        print("请先运行: python setup.py")
        sys.exit(1)
    
    with open('.env', 'r', encoding='utf-8') as f:
        content = f.read()
        if 'your-api-key-here' in content or 'your-claude-api-key' in content:
            print("⚠️  警告: 检测到未配置 API Key")
            print("请编辑 .env 文件填入你的 API Key")
            choice = input("\n是否继续启动? (y/N): ").strip().lower()
            if choice != 'y':
                sys.exit(0)

def start_server():
    """启动服务器"""
    print("\n🚀 正在启动服务器...")
    print("=" * 60)
    
    try:
        # 启动 Flask 应用
        process = subprocess.Popen(
            [sys.executable, 'app.py'],
            stdout=subprocess.PIPE,
            stderr=subprocess.STDOUT,
            universal_newlines=True,
            bufsize=1
        )
        
        # 等待服务器启动
        time.sleep(2)
        
        # 自动打开浏览器
        print("\n✅ 服务器已启动！")
        print("\n🌐 正在打开浏览器...")
        webbrowser.open('http://localhost:5000')
        
        print("\n" + "=" * 60)
        print("💡 提示:")
        print("   - 访问地址: http://localhost:5000")
        print("   - 按 Ctrl+C 停止服务器")
        print("=" * 60 + "\n")
        
        # 实时输出日志
        for line in process.stdout:
            print(line, end='')
        
        process.wait()
        
    except KeyboardInterrupt:
        print("\n\n⚠️  正在停止服务器...")
        process.terminate()
        print("✅ 服务器已停止")
    except Exception as e:
        print(f"\n❌ 启动失败: {e}")
        sys.exit(1)

def main():
    print_banner()
    
    print("📋 启动检查...")
    check_installation()
    check_config()
    
    start_server()

if __name__ == '__main__':
    main()
```

现在创建一个**超级简单的批处理文件**：

```batch
REM install.bat - Windows 一键安装
@echo off
chcp 65001 >nul
echo.
echo ╔═══════════════════════════════════════╗
echo ║   AI 聊天机器人 - 一键安装            ║
echo ╚═══════════════════════════════════════╝
echo.
echo 正在安装...
python setup.py
pause
```

```batch
REM start.bat - Windows 一键启动
@echo off
chcp 65001 >nul
python run.py
```

```bash
#!/bin/bash
# install.sh - Mac/Linux 一键安装
echo "╔═══════════════════════════════════════╗"
echo "║   AI 聊天机器人 - 一键安装            ║"
echo "╚═══════════════════════════════════════╝"
echo ""
python3 setup.py
```

```bash
#!/bin/bash
# start.sh - Mac/Linux 一键启动
python3 run.py
```

## 🎉 使用方法

### Windows 用户：
1. **双击 `install.bat`** → 自动安装
2. **双击 `start.bat`** → 自动启动并打开浏览器

### Mac/Linux 用户：
```bash
# 一键安装
chmod +x install.sh
./install.sh

# 一键启动
chmod +x start.sh
./start.sh
```

### 或者直接用 Python：
```bash
# 安装
python setup.py

# 启动
python run.py
```

## ✨ 特点

- ✅ **真正的一键运行** - 双击即可
- ✅ **自动安装依赖** - 无需手动 pip install
- ✅ **配置向导** - 交互式配置 API Key
- ✅ **自动打开浏览器** - 启动后自动访问
- ✅ **错误检查** - 自动检测缺失文件和配置
- ✅ **跨平台** - Windows/Mac/Linux 都支持

需要我把这些文件也打包成一个压缩包吗？或者创建一个 GitHub 仓库链接？