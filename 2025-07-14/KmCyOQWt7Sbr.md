### 代码评审

#### .github/workflows/main-maven-jar.yml
- **变更点**：在 `main-maven-jar.yml` 文件中，添加了一个环境变量 `GITHUB_TOKEN`。
- **优点**：增加了对 GitHub 令牌的环境变量支持，有助于在 GitHub Actions 中安全地管理敏感信息。
- **建议**：确保 `CODE_TOKEN` 密钥在 GitHub 仓库中正确设置，以避免泄露。

#### openai-code-review-sdk/src/main/java/cn/openai/code/review/OpenAiCodeReview.java
- **变更点**：在 `OpenAiCodeReview` 类中，增加了以下内容：
  - 导入了 `org.eclipse.jgit` 相关的库，用于 Git 操作。
  - 添加了从 GitHub 检出代码、写入评审日志的功能。
  - 添加了生成随机字符串的方法。
- **优点**：
  - 扩展了功能，可以检出代码并生成评审日志。
  - 使用了 Git 操作库，简化了代码检出过程。
- **缺点**：
  - 代码检出过程使用了 `ProcessBuilder` 来执行 `git diff` 命令，这种方式可能不安全，建议使用 `git diff --name-only HEAD~1 HEAD` 来获取变更文件名列表，并使用 `git show HEAD~1` 和 `git show HEAD` 来获取具体变更内容。
  - 在 `writeLog` 方法中，使用了 `Git.cloneRepository` 来克隆代码审查日志仓库，这可能导致每次运行时都会克隆一个新的仓库，建议检查是否需要每次都克隆。
  - `writeLog` 方法中使用了随机字符串作为文件名，这可能导致文件名冲突，建议使用时间戳或其他唯一标识符。
- **建议**：
  - 优化代码检出逻辑，使用更安全的方式获取变更内容。
  - 检查 `writeLog` 方法中的克隆逻辑，避免不必要的克隆操作。
  - 优化文件命名策略，确保文件名的唯一性。

#### 其他建议
- 在 `codeReview` 方法中，使用 `ChatCompletionRequest.Prompt` 构造器时，应该传递 `diffCode` 字符串到 `user` 提示中，而不是直接传递 `diffCode`。
- 在 `codeReview` 方法中，应该检查 `connection` 对象是否为 `null`，以避免可能的 `NullPointerException`。
- 在 `writeLog` 方法中，应该捕获并处理 `IOException` 和 `GitAPIException` 异常，以避免程序崩溃。