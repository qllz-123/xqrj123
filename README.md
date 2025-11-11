# xqrj123[mood-diary.html](https://github.com/user-attachments/files/23477907/mood-diary.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>心情日记</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
            color: #333;
        }
        
        .container {
            max-width: 400px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            overflow: hidden;
        }
        
        .header {
            background: #5b6bf0;
            color: white;
            padding: 20px;
            text-align: center;
        }
        
        .header h1 {
            font-size: 1.5rem;
            font-weight: 600;
        }
        
        .mood-selector {
            display: flex;
            justify-content: space-around;
            padding: 20px;
            background: #f8f9fa;
        }
        
        .mood-btn {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            transition: all 0.3s ease;
            background: white;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        .mood-btn:hover {
            transform: scale(1.1);
        }
        
        .mood-btn.selected {
            transform: scale(1.2);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
        
        .text-area {
            padding: 20px;
        }
        
        textarea {
            width: 100%;
            height: 120px;
            border: 1px solid #e0e0e0;
            border-radius: 10px;
            padding: 15px;
            font-size: 1rem;
            resize: none;
            outline: none;
            transition: border-color 0.3s;
        }
        
        textarea:focus {
            border-color: #5b6bf0;
        }
        
        .actions {
            padding: 0 20px 20px;
            display: flex;
            gap: 10px;
        }
        
        .btn {
            flex: 1;
            padding: 12px;
            border: none;
            border-radius: 10px;
            font-size: 1rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .btn-primary {
            background: #5b6bf0;
            color: white;
        }
        
        .btn-secondary {
            background: #f8f9fa;
            color: #666;
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        .diary-list {
            padding: 20px;
            max-height: 300px;
            overflow-y: auto;
        }
        
        .diary-item {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 10px;
            border-left: 4px solid #5b6bf0;
        }
        
        .diary-date {
            font-size: 0.8rem;
            color: #666;
            margin-bottom: 5px;
        }
        
        .diary-mood {
            font-size: 1.2rem;
            margin-right: 5px;
        }
        
        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: #666;
        }
        
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>今天心情如何？</h1>
        </div>
        
        <div class="mood-selector">
            <button class="mood-btn" data-mood="😊">😊</button>
            <button class="mood-btn" data-mood="😐">😐</button>
            <button class="mood-btn" data-mood="😢">😢</button>
            <button class="mood-btn" data-mood="😡">😡</button>
            <button class="mood-btn" data-mood="🤩">🤩</button>
        </div>
        
        <div class="text-area">
            <textarea placeholder="记录今天的心情和想法..."></textarea>
        </div>
        
        <div class="actions">
            <button class="btn btn-secondary" id="viewHistory">查看记录</button>
            <button class="btn btn-primary" id="saveEntry">保存心情</button>
        </div>
        
        <div class="diary-list hidden" id="diaryList">
            <!-- 日记列表会动态生成 -->
        </div>
    </div>

    <script>
        class MoodDiary {
            constructor() {
                this.currentMood = null;
                this.entries = this.loadEntries();
                this.init();
            }
            
            init() {
                // 绑定心情选择事件
                document.querySelectorAll('.mood-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        this.selectMood(e.target.dataset.mood);
                    });
                });
                
                // 绑定保存事件
                document.getElementById('saveEntry').addEventListener('click', () => {
                    this.saveEntry();
                });
                
                // 绑定查看记录事件
                document.getElementById('viewHistory').addEventListener('click', () => {
                    this.toggleHistory();
                });
                
                // 初始化显示
                this.updateHistoryView();
            }
            
            selectMood(mood) {
                this.currentMood = mood;
                document.querySelectorAll('.mood-btn').forEach(btn => {
                    btn.classList.remove('selected');
                });
                event.target.classList.add('selected');
            }
            
            saveEntry() {
                const text = document.querySelector('textarea').value.trim();
                const mood = this.currentMood;
                
                if (!mood) {
                    alert('请选择一个心情');
                    return;
                }
                
                const entry = {
                    id: Date.now(),
                    mood: mood,
                    text: text,
                    date: new Date().toLocaleString('zh-CN')
                };
                
                this.entries.unshift(entry);
                this.saveEntries();
                this.clearForm();
                this.showMessage('心情记录已保存！');
            }
            
            clearForm() {
                document.querySelector('textarea').value = '';
                document.querySelectorAll('.mood-btn').forEach(btn => {
                    btn.classList.remove('selected');
                });
                this.currentMood = null;
            }
            
            toggleHistory() {
                const diaryList = document.getElementById('diaryList');
                const viewHistoryBtn = document.getElementById('viewHistory');
                
                if (diaryList.classList.contains('hidden')) {
                    diaryList.classList.remove('hidden');
                    viewHistoryBtn.textContent = '返回记录';
                    this.updateHistoryView();
                } else {
                    diaryList.classList.add('hidden');
                    viewHistoryBtn.textContent = '查看记录';
                }
            }
            
            updateHistoryView() {
                const diaryList = document.getElementById('diaryList');
                
                if (this.entries.length === 0) {
                    diaryList.innerHTML = `
                        <div class="empty-state">
                            <p>还没有任何记录</p>
                            <p>开始记录你的心情吧！</p>
                        </div>
                    `;
                    return;
                }
                
                diaryList.innerHTML = this.entries.map(entry => `
                    <div class="diary-item">
                        <div class="diary-date">${entry.date}</div>
                        <div>
                            <span class="diary-mood">${entry.mood}</span>
                            ${entry.text || '<span style="color: #999">无文字内容</span>'}
                        </div>
                    </div>
                `).join('');
            }
            
            loadEntries() {
                const saved = localStorage.getItem('moodDiary');
                return saved ? JSON.parse(saved) : [];
            }
            
            saveEntries() {
                localStorage.setItem('moodDiary', JSON.stringify(this.entries));
            }
            
            showMessage(message) {
                // 简单的消息提示
                alert(message);
            }
        }
        
        // 初始化应用
        document.addEventListener('DOMContentLoaded', () => {
            new MoodDiary();
        });
    </script>
</body>
</html>
