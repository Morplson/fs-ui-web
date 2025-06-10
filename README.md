# fs-ui-web
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pretty File Tree UI</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .file-tree-container {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(20px);
            border-radius: 20px;
            box-shadow: 0 25px 50px rgba(0, 0, 0, 0.25);
            padding: 30px;
            width: 100%;
            max-width: 450px;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .header {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-bottom: 25px;
            padding-bottom: 20px;
            border-bottom: 2px solid rgba(102, 126, 234, 0.1);
        }

        .header-icon {
            width: 32px;
            height: 32px;
            background: linear-gradient(45deg, #667eea, #764ba2);
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 16px;
        }

        .header-title {
            font-size: 24px;
            font-weight: 700;
            background: linear-gradient(45deg, #667eea, #764ba2);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .file-tree {
            list-style: none;
        }

        .tree-item {
            margin: 2px 0;
            position: relative;
        }

        .tree-item-content {
            display: flex;
            align-items: center;
            padding: 8px 12px;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
        }

        .tree-item-content::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
            transition: left 0.6s;
        }

        .tree-item-content:hover {
            background: linear-gradient(45deg, rgba(102, 126, 234, 0.1), rgba(118, 75, 162, 0.1));
            transform: translateX(5px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.2);
        }

        .tree-item-content:hover::before {
            left: 100%;
        }

        .tree-item-content:active {
            transform: translateX(3px) scale(0.98);
        }

        .tree-icon {
            width: 20px;
            height: 20px;
            margin-right: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 14px;
            transition: transform 0.3s ease;
        }

        .folder-icon {
            color: #ffd700;
            filter: drop-shadow(0 2px 4px rgba(255, 215, 0, 0.3));
        }

        .file-icon {
            color: #667eea;
        }

        .expand-icon {
            color: #999;
            transition: transform 0.3s ease;
            margin-right: 5px;
        }

        .tree-item.expanded .expand-icon {
            transform: rotate(90deg);
        }

        .tree-item-name {
            font-size: 14px;
            font-weight: 500;
            color: #2d3748;
            flex: 1;
        }

        .tree-item-size {
            font-size: 12px;
            color: #718096;
            margin-left: auto;
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .tree-item-content:hover .tree-item-size {
            opacity: 1;
        }

        .tree-children {
            margin-left: 20px;
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .tree-item.expanded .tree-children {
            max-height: 500px;
        }

        .tree-children::before {
            content: '';
            position: absolute;
            left: 10px;
            top: 0;
            bottom: 0;
            width: 1px;
            background: linear-gradient(to bottom, rgba(102, 126, 234, 0.3), transparent);
        }

        .pulse {
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }

        .breadcrumb {
            font-size: 12px;
            color: #718096;
            margin-bottom: 15px;
            padding: 8px 12px;
            background: rgba(102, 126, 234, 0.05);
            border-radius: 8px;
            border-left: 3px solid #667eea;
        }

        /* File type specific colors */
        .js-file { color: #f7df1e; }
        .html-file { color: #e34c26; }
        .css-file { color: #1572b6; }
        .json-file { color: #000; }
        .md-file { color: #083fa1; }
        .img-file { color: #ff6b6b; }
    </style>
</head>
<body>
    <div class="file-tree-container">
        <div class="header">
            <div class="header-icon">📁</div>
            <div class="header-title">Project Explorer</div>
        </div>
        
        <div class="breadcrumb">
            📍 /home/user/my-awesome-project
        </div>

        <ul class="file-tree" id="fileTree">
            <!-- Tree structure will be populated by JavaScript -->
        </ul>
    </div>

    <script>
        const fileTreeData = {
            name: "my-awesome-project",
            type: "folder",
            children: [
                {
                    name: "src",
                    type: "folder",
                    children: [
                        {
                            name: "components",
                            type: "folder",
                            children: [
                                { name: "Header.jsx", type: "file", size: "2.4 KB", ext: "js" },
                                { name: "Sidebar.jsx", type: "file", size: "1.8 KB", ext: "js" },
                                { name: "Footer.jsx", type: "file", size: "1.2 KB", ext: "js" }
                            ]
                        },
                        {
                            name: "pages",
                            type: "folder",
                            children: [
                                { name: "Home.jsx", type: "file", size: "3.6 KB", ext: "js" },
                                { name: "About.jsx", type: "file", size: "2.1 KB", ext: "js" },
                                { name: "Contact.jsx", type: "file", size: "2.8 KB", ext: "js" }
                            ]
                        },
                        { name: "App.jsx", type: "file", size: "1.9 KB", ext: "js" },
                        { name: "index.js", type: "file", size: "0.5 KB", ext: "js" },
                        { name: "styles.css", type: "file", size: "4.2 KB", ext: "css" }
                    ]
                },
                {
                    name: "public",
                    type: "folder",
                    children: [
                        { name: "index.html", type: "file", size: "1.1 KB", ext: "html" },
                        { name: "favicon.ico", type: "file", size: "15 KB", ext: "img" },
                        {
                            name: "assets",
                            type: "folder",
                            children: [
                                { name: "logo.png", type: "file", size: "25 KB", ext: "img" },
                                { name: "banner.jpg", type: "file", size: "120 KB", ext: "img" }
                            ]
                        }
                    ]
                },
                { name: "package.json", type: "file", size: "1.3 KB", ext: "json" },
                { name: "README.md", type: "file", size: "2.7 KB", ext: "md" },
                { name: ".gitignore", type: "file", size: "0.8 KB", ext: "file" }
            ]
        };

        function getFileIcon(item) {
            if (item.type === 'folder') {
                return '📁';
            }
            
            const iconMap = {
                'js': '⚡',
                'html': '🌐',
                'css': '🎨',
                'json': '📋',
                'md': '📝',
                'img': '🖼️'
            };
            
            return iconMap[item.ext] || '📄';
        }

        function getFileClass(item) {
            if (item.type === 'folder') return '';
            return `${item.ext}-file`;
        }

        function createTreeItem(item, level = 0) {
            const li = document.createElement('li');
            li.className = 'tree-item';
            
            const content = document.createElement('div');
            content.className = 'tree-item-content';
            
            let expandIcon = '';
            if (item.type === 'folder' && item.children && item.children.length > 0) {
                expandIcon = '<span class="expand-icon">▶</span>';
            }
            
            const icon = getFileIcon(item);
            const fileClass = getFileClass(item);
            const size = item.size ? `<span class="tree-item-size">${item.size}</span>` : '';
            
            content.innerHTML = `
                ${expandIcon}
                <span class="tree-icon ${item.type === 'folder' ? 'folder-icon' : 'file-icon'} ${fileClass}">${icon}</span>
                <span class="tree-item-name">${item.name}</span>
                ${size}
            `;
            
            li.appendChild(content);
            
            if (item.type === 'folder' && item.children && item.children.length > 0) {
                const childrenContainer = document.createElement('ul');
                childrenContainer.className = 'tree-children';
                
                item.children.forEach(child => {
                    childrenContainer.appendChild(createTreeItem(child, level + 1));
                });
                
                li.appendChild(childrenContainer);
                
                content.addEventListener('click', () => {
                    li.classList.toggle('expanded');
                    
                    // Add a subtle animation to the folder icon
                    const folderIcon = content.querySelector('.folder-icon');
                    if (folderIcon) {
                        folderIcon.style.transform = 'scale(1.2)';
                        setTimeout(() => {
                            folderIcon.style.transform = 'scale(1)';
                        }, 150);
                    }
                });
            } else {
                content.addEventListener('click', () => {
                    // File click animation
                    content.style.transform = 'scale(0.95)';
                    setTimeout(() => {
                        content.style.transform = '';
                    }, 100);
                    
                    console.log(`Opening file: ${item.name}`);
                });
            }
            
            return li;
        }

        function renderFileTree() {
            const treeContainer = document.getElementById('fileTree');
            fileTreeData.children.forEach(item => {
                treeContainer.appendChild(createTreeItem(item));
            });
            
            // Auto-expand the src folder for better initial view
            setTimeout(() => {
                const srcFolder = Array.from(document.querySelectorAll('.tree-item-name'))
                    .find(el => el.textContent === 'src')?.closest('.tree-item');
                if (srcFolder) {
                    srcFolder.classList.add('expanded');
                }
            }, 500);
        }

        // Initialize the file tree
        renderFileTree();

        // Add some dynamic effects
        document.addEventListener('DOMContentLoaded', () => {
            // Stagger animation for initial load
            const items = document.querySelectorAll('.tree-item-content');
            items.forEach((item, index) => {
                item.style.opacity = '0';
                item.style.transform = 'translateX(-20px)';
                
                setTimeout(() => {
                    item.style.transition = 'all 0.5s cubic-bezier(0.4, 0, 0.2, 1)';
                    item.style.opacity = '1';
                    item.style.transform = 'translateX(0)';
                }, index * 50);
            });
        });
    </script>
