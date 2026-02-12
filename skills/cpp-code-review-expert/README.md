# C++ Code Review Expert - LLM Skill Guide

**专业级C/C++代码审查技能** - 为LLM设计，用于在与用户对话中自动触发专业级代码审查。

## 📋 目录

- [技能概述](#技能概述)
- [LLM触发机制](#llm触发机制)
- [对话触发示例](#对话触发示例)
- [执行条件与上下文](#执行条件与上下文)
- [技能输出格式](#技能输出格式)
- [对话最佳实践](#对话最佳实践)
- [常见问题](#常见问题)

## 🎯 技能概述

这个技能专门为LLM设计，当用户在对话中提出C++代码相关需求时，LLM可以自动调用此技能进行专业级代码审查。

### 核心能力
- **内存安全检测** - 发现缓冲区溢出、内存泄漏、use-after-free等
- **性能问题识别** - 识别低效算法、不必要的拷贝、优化机会
- **现代化建议** - 推荐C++11/14/17/20现代特性和最佳实践
- **安全性评估** - 检查整数溢出、输入验证、并发安全等
- **架构质量评估** - 系统架构、模块化、可扩展性、可维护性审查
- **设计模式应用** - 评估组件设计、接口设计、依赖关系合理性

### 适用场景
- ✅ **代码审查请求** - 用户明确要求审查C++代码
- ✅ **代码质量检查** - 用户询问代码质量或改进建议
- ✅ **安全问题排查** - 用户担心代码安全性
- ✅ **性能优化咨询** - 用户寻求性能改进建议
- ✅ **现代化升级** - 用户希望升级旧C++代码
- ✅ **Pull Request分析** - 分析代码变更的潜在问题

## 🤖 LLM触发机制

### 自动触发关键词

当LLM识别到以下关键词组合时，应自动调用此技能：

#### **直接审查请求**
```
"审查这段C++代码"
"帮我看看这段代码有什么问题"
"代码审查"
"请检查代码质量"
"这段代码安全吗"
"有什么改进建议"
```

#### **安全相关询问**
```
"内存泄漏"
"缓冲区溢出"
"安全漏洞"
"这段代码有风险吗"
"use-after-free"
"空指针"
```

#### **性能相关咨询**
```
"性能优化"
"这段代码效率如何"
"可以优化吗"
"算法复杂度"
"性能瓶颈"
"内存使用"
```

#### **现代化需求**
```
"现代化"
"modern C++"
"C++11"
"升级代码"
"现代C++写法"
"智能指针"
```

#### **架构设计相关询问**
```
"系统架构"
"模块设计"
"可扩展性"
"组件设计"
"接口设计"
"依赖关系"
"设计模式"
```

### 触发条件矩阵

| 用户意图 | 关键词示例 | 触发优先级 | 建议响应 |
|---------|-----------|-----------|-----------|
| **代码审查** | "审查"、"检查"、"看看" | 🔴 高 | 立即触发技能 |
| **安全担忧** | "安全"、"漏洞"、"泄漏" | 🔴 高 | 立即触发技能 |
| **性能咨询** | "性能"、"优化"、"效率" | 🟡 中 | 先询问后触发 |
| **现代化** | "现代"、"升级"、"C++11" | 🟡 中 | 先询问后触发 |
| **架构设计** | "架构"、"模块"、"扩展"、"设计模式" | 🟡 中 | 先询问后触发 |
| **一般询问** | "代码"、"C++" | 🟢 低 | 需要更多上下文 |

## 💬 对话触发示例

### 场景1: 直接代码审查

**用户输入**:
```
请帮我审查这段C++代码：

```cpp
#include <iostream>
#include <cstring>

void process_user_input(const char* input) {
    char buffer[100];
    strcpy(buffer, input);
    std::cout << "Processed: " << buffer << std::endl;
}
```
```

**LLM响应**:
```
我来帮您进行专业的C++代码审查。
::call-skill{name="cpp-code-review-expert", code="..."}::

根据C++代码审查结果：

## C++ Code Review Summary

**Files reviewed**: 1 files, 8 lines
**Overall assessment**: REQUEST_CHANGES

---

## Findings

### P0 - Critical (Memory Safety & Security)
- **[code:6]** Buffer overflow risk
  - Using `strcpy` with user-controlled input without bounds checking
  - 输入长度超过100个字符会导致缓冲区溢出

### P1 - High (Resource Management & Performance)
- **[code:3]** Missing bounds checking
  - Function doesn't validate input pointer is null
  - 可能导致空指针解引用

**建议修复方案**：
```cpp
#include <iostream>
#include <string>
#include <algorithm>

void process_user_input(const char* input) {
    if (!input) return;  // 空指针检查
    
    // 使用std::string避免缓冲区溢出
    std::string buffer(input);
    if (buffer.length() >= 100) {
        std::cout << "Input too long" << std::endl;
        return;
    }
    
    std::cout << "Processed: " << buffer << std::endl;
}
```
```

### 场景2: 性能优化咨询

**用户输入**:
```
这段C++代码的性能怎么样？有什么可以优化的地方吗？

```cpp
std::vector<int> find_duplicates(const std::vector<int>& data) {
    std::vector<int> duplicates;
    for (size_t i = 0; i < data.size(); ++i) {
        for (size_t j = i + 1; j < data.size(); ++j) {
            if (data[i] == data[j]) {
                if (std::find(duplicates.begin(), duplicates.end(), data[i]) == duplicates.end()) {
                    duplicates.push_back(data[i]);
                }
            }
        }
    }
    return duplicates;
}
```
```

**LLM响应**:
```
我来为您进行性能分析和优化建议。
::call-skill{name="cpp-code-review-expert", code="...", focus="performance"}::

## 性能分析结果

### P2 - Medium (Performance Issues)
- **算法复杂度**: 当前为O(n²)，对于大数据集性能较差
- **不必要的线性搜索**: 使用`std::find`增加额外复杂度

**优化建议**：
```cpp
#include <vector>
#include <unordered_set>

std::vector<int> find_duplicates(const std::vector<int>& data) {
    std::unordered_set<int> seen;
    std::unordered_set<int> duplicates_set;
    
    for (const auto& item : data) {
        if (seen.count(item)) {
            duplicates_set.insert(item);
        } else {
            seen.insert(item);
        }
    }
    
    return std::vector<int>(duplicates_set.begin(), duplicates_set.end());
}
```

**性能提升**: O(n²) → O(n)，大数据集性能显著改善
```

### 场景3: 架构设计评审

**用户输入**:
```
我设计了一个C++系统，想评审一下架构设计是否合理：

```cpp
// 数据访问层
class UserRepository {
public:
    User findById(int id) {
        // 直接数据库查询
        DatabaseConnection db;
        return db.query("SELECT * FROM users WHERE id = " + std::to_string(id));
    }
    void save(User user) {
        DatabaseConnection db;
        db.execute("INSERT INTO users VALUES (" + user.toSql() + ")");
    }
};

// 业务逻辑层
class UserService {
private:
    UserRepository repo;
public:
    User getUser(int id) {
        return repo.findById(id);
    }
    void updateUserProfile(int userId, ProfileData profile) {
        User user = repo.findById(userId);
        // 业务逻辑混在数据访问中
        if (user.isValid()) {
            user.setProfile(profile);
            repo.save(user);
        }
    }
};
```
```

**LLM响应**:
```
我来帮您进行架构设计和代码结构评审。
::call-skill{name="cpp-code-review-expert", code="...", focus="architecture"}::

## 架构评审结果

### P1 - High (Architecture Issues)
- **[UserRepository:5]** 紧耦合架构
  - 业务逻辑与数据访问混合，违反单一职责原则
  - 难以单元测试和替换数据存储实现

### P2 - Medium (Design Pattern Issues)
- **[UserService:8]** 缺少依赖注入和抽象
  - 硬编码依赖关系，降低了系统灵活性
  - 业务逻辑与数据访问逻辑耦合

**架构改进建议**：
```cpp
// 数据访问抽象接口
class IUserRepository {
public:
    virtual ~IUserRepository() = default;
    virtual User findById(int id) = 0;
    virtual void save(const User& user) = 0;
};

// 具体实现
class SqlUserRepository : public IUserRepository {
private:
    DatabaseConnection db;
public:
    User findById(int id) override {
        return db.query("SELECT * FROM users WHERE id = ?", id);
    }
    void save(const User& user) override {
        db.execute("INSERT INTO users VALUES (?, ?)", user.id(), user.name());
    }
};

// 业务逻辑服务
class UserService {
private:
    std::unique_ptr<IUserRepository> repo;
public:
    UserService(std::unique_ptr<IUserRepository> repository) 
        : repo(std::move(repository)) {}
    
    User getUser(int id) {
        return repo->findById(id);
    }
    
    void updateUserProfile(int userId, ProfileData profile) {
        User user = getUser(userId);
        if (user.isValid()) {
            user.setProfile(profile);
            repo->save(user);
        }
    }
};
```

**架构改进点**:
- ✅ 分离关注点，提高可测试性
- ✅ 使用依赖注入，提高灵活性
- ✅ 接口抽象，便于实现替换
- ✅ 符合SOLID原则，提高可维护性
```

### 场景3: 现代化升级

**用户输入**:
```
我有一段老的C++代码，想用现代C++重写：

```cpp
class StringHolder {
private:
    char* data;
public:
    StringHolder(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
    }
    ~StringHolder() {
        delete[] data;
    }
    StringHolder(const StringHolder& other) {
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
    }
};
```
```

**LLM响应**:
```
我来帮您将这段代码现代化，使用现代C++最佳实践。
::call-skill{name="cpp-code-review-expert", code="...", focus="modernization"}::

## 现代化建议

### P1 - High (Resource Management)
- **违反RAII原则**: 手动内存管理容易出错
- **缺少移动语义**: 拷贝构造函数效率低

**现代C++版本**：
```cpp
#include <string>

class StringHolder {
private:
    std::string data;
public:
    // 默认构造函数即可
    StringHolder() = default;
    
    // 直接构造
    StringHolder(const char* str) : data(str) {}
    
    // 默认的拷贝/移动构造函数就足够了
    StringHolder(const StringHolder&) = default;
    StringHolder(StringHolder&&) = default;
    
    // 默认的赋值运算符
    StringHolder& operator=(const StringHolder&) = default;
    StringHolder& operator=(StringHolder&&) = default;
    
    // 访问器
    const char* c_str() const { return data.c_str(); }
    const std::string& get() const { return data; }
};
```

**改进点**:
- ✅ 使用std::string自动管理内存
- ✅ 默认的移动语义提高性能
- ✅ 异常安全
- ✅ 代码更简洁
```

## ⚙️ 执行条件与上下文

### 必要条件

在调用技能之前，LLM需要确保：

1. **代码完整性**
   - 用户提供完整的代码片段
   - 包含必要的头文件和上下文
   - 代码能够理解其意图

2. **语言确认**
   - 确认是C/C++代码（不是其他语言）
   - 识别C++版本特征（C++11/14/17/20）

3. **问题理解**
   - 明确用户的具体需求（审查、优化、安全等）
   - 有足够上下文理解代码用途

### 上下文收集策略

当上下文不足时，LLM应该：

```cpp
// 当代码不完整时
"我看到您提到了C++代码，但代码片段似乎不完整。
能否提供完整的函数或类定义，包括：
- 相关的头文件
- 函数的完整实现
- 使用这个函数的上下文"

// 当需求不明确时
"您希望我重点检查哪些方面？
- 🔒 内存安全和缓冲区问题
- ⚡ 性能优化机会  
- 🛡️ 安全漏洞
- 📈 代码现代化
- 🎯 通用代码质量"
```

### 触发决策流程

```
用户输入 → 语言检测 → 意图识别 → 上下文检查 → 技能调用
     ↓              ↓           ↓           ↓           ↓
  是否C++？    用户想什么？  代码完整吗？  是否满足条件？
     ↓              ↓           ↓           ↓
   ✅/❌        审查/优化/   ✅/❌      ✅/❌ → 调用技能
               安全/现代化
```

## 📋 技能输出格式

### 标准审查输出

技能会返回结构化的代码审查结果：

```markdown
## C++ Code Review Summary

**Files reviewed**: X files, Y lines changed
**Overall assessment**: [APPROVE / REQUEST_CHANGES / COMMENT]

---

## Findings

### P0 - Critical (Memory Safety & Security)
- **[file:line]** Brief title
  - Description of issue
  - Suggested fix with code examples

### P1 - High (Resource Management & Performance)
- **[file:line]** Brief title
  - Description of issue
  - Suggested fix with code examples

### P2 - Medium (Code Quality & Modernization)
- **[file:line]** Brief title
  - Description of issue
  - Suggested fix with code examples

### P3 - Low (Style & Minor Issues)
- **[file:line]** Brief title
  - Description of issue
  - Suggested fix with code examples

---

## Memory/Resource Analysis
Summary of memory safety improvements

## Additional Suggestions
Optional improvements not blocking merge
```

### LLM后续处理

技能返回结果后，LLM应该：

1. **总结关键问题**
   ```cpp
   "发现1个P0级别的缓冲区溢出风险，建议立即修复"
   ```

2. **提供修复示例**
   ```cpp
   "这是安全的实现方式..."
   ```

3. **解释技术原理**
   ```cpp
   "使用std::vector可以避免手动内存管理和缓冲区溢出"
   ```

4. **询问下一步**
   ```cpp
   "您希望我帮您实现修复方案吗？还是需要进一步解释某个问题？"
   ```

## 💡 对话最佳实践

### 🎯 主动识别机会

即使用户没有明确要求，LLM也应该在以下情况主动触发技能：

#### 代码片段分享时
```
用户: "我写了这样一个函数..."
LLM: 检测到C++代码，我来帮您快速检查一下潜在问题：
```

#### 技术问题咨询时
```
用户: "我的程序总是崩溃"
LLM: 让我检查一下可能的安全问题和内存管理问题
```

#### 性能抱怨时
```
用户: "这段代码运行很慢"
LLM: 我来分析可能的性能瓶颈和优化机会
```

### 📚 渐进式审查

对于长代码，采用分阶段策略：

```cpp
1. "我来快速检查一下主要的内存安全问题"
2. "现在让我分析性能相关的问题"
3. "最后看看有没有现代化改进机会"
```

### 🏗️ 渐进式架构评审

对于复杂系统，采用分层审查策略：

```cpp
1. "我先检查系统架构的模块化设计"
2. "然后分析组件间的依赖关系和接口设计"
3. "接着评估数据流和控制流的合理性"
4. "最后看看设计模式的应用和扩展性"
```

### 🎯 混合评审策略

对于既有架构问题又有代码质量问题的场景：

```cpp
1. "我先从架构层面分析整体设计合理性"
2. "然后深入检查具体实现的技术问题"
3. "最后提供架构重构和代码改进的综合方案"
```

### 🔍 上下文扩展

当用户提供部分代码时，主动收集相关信息：

```cpp
"我看到这段代码使用了文件操作。为了全面审查，我需要了解：
- 这个函数的调用频率
- 处理的文件大小范围
- 是否在多线程环境中使用"
```

### 📊 价值强调

在展示审查结果时，强调发现的问题的重要性：

```cpp
"⚠️  **P0级别问题** - 这可能导致程序崩溃或安全漏洞"
"💡  **P1级别建议** - 修复后可以显著提升性能"
"📈  **P2级别改进** - 让代码更现代、更易维护"
```

## ❓ 常见问题

### **Q1: 如何判断是否应该触发技能？**

**A**: 遵循以下决策树：
1. 是否包含C++代码？ ✅ 继续
2. 用户是否询问质量、安全、性能？ ✅ 继续  
3. 代码是否完整且可理解？ ✅ 触发技能
4. 如果任一条件为❌，先询问用户澄清

### **Q2: 用户提供其他语言代码怎么办？**

**A**: 识别语言特征并提示：
```cpp
"我注意到您提供的代码看起来是Python/Java/JavaScript。
如果您有C++代码需要审查，我很乐意帮助！"
```

### **Q3: 代码太长如何处理？**

**A**: 分段处理：
```cpp
"代码较长，我先检查前50行的关键问题，然后继续..."
```

### **Q4: 用户不同意审查结果怎么办？**

**A**: 尊重用户并进一步沟通：
```cpp
"理解您的考虑。能否告诉我您对这个评估的看法？
也许有额外的上下文我需要了解..."
```

### **Q5: 如何处理复杂的架构问题？**

**A**: 聚焦可审查的部分：
```cpp
"从提供的代码片段看，我发现以下具体问题...
关于整体架构设计，可能需要更多上下文才能给出完整建议。"
```

---

## 🎯 技能使用总结

这个C++代码审查技能是LLM的强大工具，通过智能触发和专业分析，为用户提供：

- **即时代码质量反馈**
- **具体可行的修复建议** 
- **现代C++最佳实践指导**
- **安全漏洞预防**

**记住**: 好的代码审查不仅发现问题，更帮助用户成长为更好的C++开发者！ 🚀