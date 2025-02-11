<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>科技工具箱 | 大僧版</title>
    <style>
        :root {
            --primary-color: #00ff9d;
            --bg-color: #0a0a0a;
            --card-bg: #1a1a1a;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', sans-serif;
            background: var(--bg-color);
            color: #fff;
            line-height: 1.6;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        header {
            text-align: center;
            padding: 60px 0;
            border-bottom: 2px solid var(--primary-color);
        }

        h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
            color: var(--primary-color);
        }

        .search-box {
            margin: 30px auto;
            max-width: 500px;
        }

        #searchInput {
            width: 100%;
            padding: 12px;
            background: var(--card-bg);
            border: 1px solid #333;
            color: #fff;
            border-radius: 5px;
        }

        .category-filter {
            display: flex;
            gap: 15px;
            justify-content: center;
            margin: 30px 0;
            flex-wrap: wrap;
        }

        .filter-btn {
            padding: 8px 20px;
            background: #333;
            border: none;
            color: #fff;
            border-radius: 20px;
            cursor: pointer;
            transition: 0.3s;
        }

        .filter-btn.active {
            background: var(--primary-color);
            color: #000;
        }

        .tools-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 25px;
            margin-top: 40px;
        }

        .tool-card {
            background: var(--card-bg);
            padding: 20px;
            border-radius: 10px;
            border: 1px solid #333;
            transition: transform 0.3s;
        }

        .tool-card:hover {
            transform: translateY(-5px);
        }

        .tool-icon {
            font-size: 2em;
            color: var(--primary-color);
            margin-bottom: 15px;
        }

        .tool-status {
            display: inline-block;
            padding: 4px 10px;
            font-size: 0.8em;
            border-radius: 3px;
            margin-bottom: 10px;
        }

        .status-dev { background: #ff9100; color: #000; }
        .status-beta { background: #00b7ff; color: #000; }
        .status-live { background: #00ff55; color: #000; }

        .tool-link {
            display: inline-block;
            padding: 8px 20px;
            background: var(--primary-color);
            color: #000;
            text-decoration: none;
            border-radius: 5px;
            margin-top: 15px;
            font-weight: bold;
            transition: 0.3s;
        }

        .tool-link:hover {
            opacity: 0.8;
        }

        @media (max-width: 768px) {
            .tools-grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <header>
        <div class="container">
            <h1>⚡ 科技工具箱</h1>
            <p>这里汇集了视频中推荐的所有工具和项目</p>
            <div class="search-box">
                <input type="text" id="searchInput" placeholder="搜索工具名称或功能...">
            </div>
        </div>
    </header>

    <div class="container">
        <div class="category-filter" id="filterButtons">
            <!-- 分类按钮将通过JavaScript生成 -->
        </div>

        <div class="tools-grid" id="toolsContainer">
            <!-- 工具卡片将通过JavaScript动态加载 -->
        </div>
    </div>

    <script>
        // 工具数据（按需修改）
        const toolsData = [
            {
                name: "AI视频增强器",
                icon: "🤖",
                desc: "使用AI算法提升视频画质的神器",
                category: "视频工具",
                status: "live",
                link: "#"
            },
            {
                name: "智能去背景工具",
                icon: "🎭",
                desc: "一键去除视频/图片背景",
                category: "图像处理",
                status: "beta",
                link: "#"
            },
            {
                name: "3D模型生成器",
                icon: "🕶️",
                desc: "文本转3D模型的创新工具",
                category: "开发工具",
                status: "dev",
                link: "#"
            }
            // 在此处继续添加新工具...
        ];

        // 初始化页面
        function init() {
            // 生成分类按钮
            const categories = [...new Set(toolsData.map(tool => tool.category))];
            const filterButtons = document.getElementById('filterButtons');
            
            // 添加「全部」按钮
            filterButtons.innerHTML = `<button class="filter-btn active" onclick="filterTools('all')">全部</button>`;
            
            categories.forEach(category => {
                filterButtons.innerHTML += `
                    <button class="filter-btn" onclick="filterTools('${category}')">
                        ${category}
                    </button>
                `;
            });

            // 首次加载工具
            filterTools('all');
        }

        // 工具筛选函数
        function filterTools(category) {
            const filteredTools = category === 'all' 
                ? toolsData 
                : toolsData.filter(tool => tool.category === category);

            // 更新按钮状态
            document.querySelectorAll('.filter-btn').forEach(btn => {
                btn.classList.remove('active');
                if (btn.textContent.trim() === category || 
                   (category === 'all' && btn.textContent.trim() === '全部')) {
                    btn.classList.add('active');
                }
            });

            renderTools(filteredTools);
        }

        // 渲染工具卡片
        function renderTools(tools) {
            const container = document.getElementById('toolsContainer');
            container.innerHTML = tools.map(tool => `
                <div class="tool-card">
                    <div class="tool-icon">${tool.icon}</div>
                    ${getStatusBadge(tool.status)}
                    <h3>${tool.name}</h3>
                    <p>${tool.desc}</p>
                    <a href="${tool.link}" class="tool-link" target="_blank">立即体验 →</a>
                </div>
            `).join('');

            // 添加搜索功能
            document.getElementById('searchInput').addEventListener('input', (e) => {
                const searchTerm = e.target.value.toLowerCase();
                const filtered = toolsData.filter(tool => 
                    tool.name.toLowerCase().includes(searchTerm) || 
                    tool.desc.toLowerCase().includes(searchTerm)
                );
                renderTools(filtered);
            });
        }

        // 状态标签生成
        function getStatusBadge(status) {
            const statusMap = {
                dev: { text: '开发中', class: 'status-dev' },
                beta: { text: '测试版', class: 'status-beta' },
                live: { text: '已上线', class: 'status-live' }
            };
            return `<span class="tool-status ${statusMap[status].class}">${statusMap[status].text}</span>`;
        }

        // 页面初始化
        init();
    </script>
</body>
</html>