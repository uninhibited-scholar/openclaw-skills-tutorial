# OpenClaw Skills 开发教程

> 从零开始创建你的第一个 OpenClaw 技能 | 成为 OpenClaw Contributor 的完整指南

**作者**: [@uninhibited-scholar](https://github.com/uninhibited-scholar)  
**最后更新**: 2026-03-10  
**适用版本**: OpenClaw v2026.3+

---

## 📖 教程目录

1. [为什么创建 Skills？](#为什么创建-skills)
2. [Skill 基础结构](#skill-基础结构)
3. [实战：创建第一个 Skill](#实战创建第一个-skill)
4. [测试与调试](#测试与调试)
5. [发布到 ClawHub](#发布到-clawhub)
6. [贡献到 OpenClaw 官方](#贡献到-openclaw-官方)

---

## 为什么创建 Skills？

### 🎯 技能的价值

1. **展示技术能力** - GitHub 上的技能仓库是最好的能力证明
2. **解决实际问题** - 自动化重复工作，提高效率
3. **贡献开源社区** - 让更多人使用你的工具
4. **学习 OpenClaw 架构** - 深入理解 Agent 系统

### 📈 成功案例

- **rss-digest**: RSS 摘要技能，被广泛使用
- **weather**: 天气查询，新手入门首选
- **github**: GitHub 集成，展示 API 调用能力

---

## Skill 基础结构

### 标准目录结构

```
my-skill/
├── SKILL.md          # 必需：技能描述和触发规则
├── index.js          # 必需：主逻辑文件
├── utils/            # 可选：工具函数
├── config.json       # 可选：配置文件
└── README.md         # 可选：使用说明
```

### SKILL.md 模板

```markdown
## 技能名称
<skill-name>

## 触发规则
**激活条件**（满足任一即可）:
- 用户提到 <关键词 1>、<关键词 2>
- 用户发送命令：`/<command>`
- 用户 <具体行为描述>

**优先级**: 高/中/低

## 功能描述
简要说明技能功能，包括：
1. 核心功能 1
2. 核心功能 2
3. 核心功能 3

## 使用示例

### 示例 1: 基础用法
```
用户：<用户输入>
助手：[技能响应]
```

### 示例 2: 高级用法
```
用户：<用户输入>
助手：[技能响应]
```

## 配置项

```json
{
  "key": "value"
}
```

## 依赖工具
- `tool_name` - 用途说明

## 注意事项
- 注意点 1
- 注意点 2
```

### index.js 模板

```javascript
/**
 * <Skill Name> - <Brief Description>
 * 
 * Features:
 * - Feature 1
 * - Feature 2
 * - Feature 3
 */

const fs = require('fs');
const path = require('path');

// Configuration
const CONFIG = {
  // Add your config here
};

// Storage path
const DATA_PATH = path.join(
  process.env.HOME || '~',
  `.openclaw/workspace/skills/<skill-name>/data.json`
);

/**
 * Generate unique ID
 */
function generateId() {
  return `<prefix>_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
}

/**
 * Load data
 */
function loadData() {
  try {
    if (fs.existsSync(DATA_PATH)) {
      const data = fs.readFileSync(DATA_PATH, 'utf8');
      return JSON.parse(data);
    }
  } catch (e) {
    console.error('Load failed:', e.message);
  }
  return { items: [], lastUpdate: null };
}

/**
 * Save data
 */
function saveData(data) {
  try {
    const dir = path.dirname(DATA_PATH);
    if (!fs.existsSync(dir)) {
      fs.mkdirSync(dir, { recursive: true });
    }
    data.lastUpdate = new Date().toISOString();
    fs.writeFileSync(DATA_PATH, JSON.stringify(data, null, 2));
    return true;
  } catch (e) {
    console.error('Save failed:', e.message);
    return false;
  }
}

/**
 * Main function
 */
async function main(command, args) {
  switch (command) {
    case 'create':
      // Implementation
      return 'Created';
    
    case 'list':
      // Implementation
      return 'List';
    
    default:
      return 'Usage: create | list';
  }
}

// Exports
module.exports = {
  main,
  CONFIG,
  loadData,
  saveData
};
```

---

## 实战：创建第一个 Skill

### 项目：待办事项管理器 (todo-manager)

#### 步骤 1: 创建目录

```bash
mkdir -p ~/.openclaw/workspace/skills/todo-manager
cd ~/.openclaw/workspace/skills/todo-manager
```

#### 步骤 2: 编写 SKILL.md

```markdown
## 技能名称
todo-manager

## 触发规则
**激活条件**（满足任一即可）:
- 用户提到 "待办"、"TODO"、"任务"、"todo"
- 用户发送命令：`/todo`、`/add`、`/done`
- 用户说 "提醒我..."、"记得..."

**优先级**: 高

## 功能描述
简单的待办事项管理工具：
1. 添加新任务
2. 标记任务完成
3. 列出所有任务
4. 删除任务

## 使用示例

### 示例 1: 添加任务
```
用户：添加一个待办：明天买机票
助手：✅ 任务已添加：明天买机票
```

### 示例 2: 列出任务
```
用户：我有什么待办？
助手：📋 待办清单：
1. [ ] 明天买机票
2. [ ] 回复邮件
```

### 示例 3: 完成任务
```
用户：标记买机票为完成
助手：✅ 任务已完成：明天买机票
```

## 配置项

```json
{
  "storageFile": "todos.json",
  "maxTodos": 100
}
```

## 依赖工具
- `memory_search` - 搜索历史任务
- `feishu_doc` - 创建任务文档（可选）

## 注意事项
- 任务数据存储在本地
- 支持导入/导出 JSON
```

#### 步骤 3: 编写 index.js

```javascript
/**
 * Todo Manager - 待办事项管理工具
 * 
 * Features:
 * - Add todos
 * - Mark as done
 * - List all todos
 * - Delete todos
 */

const fs = require('fs');
const path = require('path');

// Configuration
const CONFIG = {
  storageFile: 'todos.json',
  maxTodos: 100
};

// Storage path
const TODOS_PATH = path.join(
  process.env.HOME || '~',
  '.openclaw/workspace/skills/todo-manager/todos.json'
);

/**
 * Generate unique ID
 */
function generateId() {
  return `todo_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
}

/**
 * Load todos
 */
function loadTodos() {
  try {
    if (fs.existsSync(TODOS_PATH)) {
      const data = fs.readFileSync(TODOS_PATH, 'utf8');
      return JSON.parse(data);
    }
  } catch (e) {
    console.error('Load todos failed:', e.message);
  }
  return { todos: [], lastUpdate: null };
}

/**
 * Save todos
 */
function saveTodos(data) {
  try {
    const dir = path.dirname(TODOS_PATH);
    if (!fs.existsSync(dir)) {
      fs.mkdirSync(dir, { recursive: true });
    }
    data.lastUpdate = new Date().toISOString();
    fs.writeFileSync(TODOS_PATH, JSON.stringify(data, null, 2));
    return true;
  } catch (e) {
    console.error('Save todos failed:', e.message);
    return false;
  }
}

/**
 * Add a new todo
 */
function addTodo(text) {
  const todosData = loadTodos();
  
  if (todosData.todos.length >= CONFIG.maxTodos) {
    throw new Error('待办事项已达上限');
  }
  
  const todo = {
    id: generateId(),
    text,
    done: false,
    createdAt: new Date().toISOString()
  };
  
  todosData.todos.push(todo);
  saveTodos(todosData);
  
  return todo;
}

/**
 * Mark todo as done
 */
function markDone(todoId) {
  const todosData = loadTodos();
  const todo = todosData.todos.find(t => t.id === todoId);
  
  if (!todo) {
    throw new Error('任务不存在');
  }
  
  todo.done = true;
  todo.completedAt = new Date().toISOString();
  
  saveTodos(todosData);
  return todo;
}

/**
 * List all todos
 */
function listTodos(options = {}) {
  const todosData = loadTodos();
  let todos = todosData.todos;
  
  // Filter by status
  if (options.status === 'done') {
    todos = todos.filter(t => t.done);
  } else if (options.status === 'pending') {
    todos = todos.filter(t => !t.done);
  }
  
  // Sort by creation date
  todos.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt));
  
  return todos;
}

/**
 * Delete a todo
 */
function deleteTodo(todoId) {
  const todosData = loadTodos();
  const index = todosData.todos.findIndex(t => t.id === todoId);
  
  if (index === -1) {
    throw new Error('任务不存在');
  }
  
  todosData.todos.splice(index, 1);
  saveTodos(todosData);
  
  return true;
}

/**
 * Format todo list for display
 */
function formatTodoList(todos) {
  if (todos.length === 0) {
    return '暂无待办事项';
  }
  
  let output = '📋 **待办清单**\n\n';
  
  todos.forEach((todo, index) => {
    const checkbox = todo.done ? '✅' : '⬜';
    output += `${index + 1}. ${checkbox} ${todo.text}\n`;
    
    if (todo.done && todo.completedAt) {
      const completedDate = new Date(todo.completedAt).toLocaleDateString();
      output += `   完成于：${completedDate}\n`;
    }
  });
  
  return output;
}

/**
 * Main function
 */
async function main(command, args) {
  switch (command) {
    case 'add':
      const added = addTodo(args.text);
      return `✅ 任务已添加：${added.text}\nID: ${added.id}`;
    
    case 'done':
      const completed = markDone(args.id);
      return `✅ 任务已完成：${completed.text}`;
    
    case 'list':
      const todos = listTodos(args.options);
      return formatTodoList(todos);
    
    case 'delete':
      deleteTodo(args.id);
      return '✅ 任务已删除';
    
    default:
      return '用法：add | done | list | delete';
  }
}

// Exports
module.exports = {
  main,
  CONFIG,
  addTodo,
  markDone,
  listTodos,
  deleteTodo,
  loadTodos,
  saveTodos
};
```

---

## 测试与调试

### 本地测试

```bash
# 1. 将 skill 放入 skills 目录
cp -r todo-manager ~/.openclaw/workspace/skills/

# 2. 重启 OpenClaw Gateway
openclaw gateway restart

# 3. 在聊天中测试
# 发送：添加一个待办：测试任务
```

### 调试技巧

1. **查看日志**: `openclaw logs --follow | grep todo`
2. **检查文件**: `cat ~/.openclaw/workspace/skills/todo-manager/todos.json`
3. **热重载**: 修改代码后 `openclaw gateway restart`

---

## 发布到 ClawHub

### 步骤 1: 准备发布

```bash
# 确保有以下文件
cd ~/.openclaw/workspace/skills/todo-manager
ls -la
# SKILL.md, index.js, README.md
```

### 步骤 2: 发布

```bash
npx clawhub publish todo-manager
```

### 步骤 3: 验证

```bash
# 搜索已发布的技能
npx clawhub search todo-manager

# 安装测试
npx clawhub install todo-manager
```

---

## 贡献到 OpenClaw 官方

### 找到合适的 Issue

1. 访问 https://github.com/openclaw/openclaw/issues
2. 搜索 `good first issue` 或 `skill` 标签
3. 评论认领 Issue

### 提交流程

```bash
# 1. Fork 仓库
gh repo fork openclaw/openclaw

# 2. Clone 到本地
git clone https://github.com/your-username/openclaw.git

# 3. 创建分支
git checkout -b feature/add-todo-skill

# 4. 添加技能
cp -r todo-manager openclaw/skills/

# 5. 提交
git add skills/todo-manager
git commit -m "feat: add todo-manager skill

- Add todo management functionality
- Support add, complete, list, delete
- Local storage with JSON file

Closes #123"

# 6. 推送并创建 PR
git push origin feature/add-todo-skill
gh pr create --title "feat: add todo-manager skill" --body "Closes #123"
```

---

## 🎓 进阶学习

### 推荐资源

- [OpenClaw 官方文档](https://docs.openclaw.ai)
- [ClawHub 技能市场](https://clawhub.com)
- [示例技能仓库](https://github.com/uninhibited-scholar/openclaw-skills)

### 下一步

1. ⭐ Star 本教程仓库
2. 🍴 Fork 并创建你的第一个 skill
3. 📝 分享你的使用经验
4. 🤝 参与 OpenClaw 社区

---

**Happy Coding! 🦞**
