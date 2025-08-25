![](https://github.com/tyrchen/ydl-rs/workflows/build/badge.svg)

# ydl-rs

一个快速、可靠的 YouTube 字幕下载器库和命令行工具，使用 Rust 编写。该项目提供了用于编程访问的库（`ydl`）和用于下载各种格式 YouTube 字幕的命令行界面（`ydl-cli`）。

## 功能特性

- 🚀 快速高效的字幕下载
- 📝 支持多种字幕格式（SRT、VTT、TXT、JSON、原始 XML）
- 🌐 支持选择字幕语言
- 📊 视频元数据提取
- 🎯 自动字幕轨道选择
- 🔄 可靠的指数退避重试逻辑
- 📖 从视频转录生成博客文章（使用 OpenAI）
- 🛠️ 同时提供库和命令行界面

## 安装

### 作为库使用

在您的 `Cargo.toml` 中添加 `ydl`：

```toml
[dependencies]
ydl = "0.1.0"
```

### 作为命令行工具

使用 cargo 安装命令行工具：

```bash
cargo install ydl-cli
```

## 使用方法

### 命令行使用

#### 下载字幕

```bash
# 下载 SRT 格式的字幕（默认）
ydl https://www.youtube.com/watch?v=VIDEO_ID

# 下载 VTT 格式的字幕
ydl https://www.youtube.com/watch?v=VIDEO_ID --format vtt

# 下载特定语言的字幕
ydl https://www.youtube.com/watch?v=VIDEO_ID --language en

# 保存到指定文件
ydl https://www.youtube.com/watch?v=VIDEO_ID --output my_subtitles.srt

# 保存到指定目录
ydl https://www.youtube.com/watch?v=VIDEO_ID --output-dir ./subtitles/
```

#### 其他操作

```bash
# 列出可用的字幕轨道
ydl https://www.youtube.com/watch?v=VIDEO_ID --list

# 显示视频元数据
ydl https://www.youtube.com/watch?v=VIDEO_ID --info

# 从视频转录生成博客文章（需要 OpenAI API 密钥）
ydl https://www.youtube.com/watch?v=VIDEO_ID --generate-blog

# 启用详细日志记录
ydl https://www.youtube.com/watch?v=VIDEO_ID -v
```

### 库使用

```rust
use ydl::{Ydl, YdlOptions, SubtitleType};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // 创建选项
    let options = YdlOptions::builder()
        .language("en")
        .subtitle_type(SubtitleType::Srt)
        .build();

    // 创建下载器
    let downloader = Ydl::new("https://www.youtube.com/watch?v=VIDEO_ID", options)?;

    // 下载字幕
    let subtitles = downloader.download().await?;

    // 处理字幕
    println!("下载了 {} 条字幕", subtitles.entries.len());
    for entry in &subtitles.entries {
        println!("{} --> {}: {}", entry.start, entry.end, entry.text);
    }

    // 保存到文件
    downloader.download_to_file("output.srt").await?;

    Ok(())
}
```

## 支持的格式

- **SRT** - SubRip 字幕格式
- **VTT** - WebVTT 格式
- **TXT** - 纯文本格式
- **JSON** - 结构化 JSON 格式
- **Raw** - YouTube 原始 XML 格式

## 环境变量

该工具支持 OpenAI 兼容的 API 用于博客生成。您可以通过设置以下环境变量来配置不同的模型：

### 博客生成所需
- `OPENAI_API_KEY` - 您的 AI 服务 API 密钥
- `OPENAI_BASE_URL` - AI 服务 API 的基础 URL
- `OPENAI_MODEL` - 要使用的模型名称

### 当前支持的模型

#### ✅ GLM 模型（正常工作）
```bash
OPENAI_BASE_URL=https://open.bigmodel.cn/api/paas/v4/
OPENAI_API_KEY=your_glm_api_key_here
OPENAI_MODEL=glm-4.5
```

#### ❌ Deepseek（当前失败）
注意：由于 API 兼容性问题，Deepseek 配置当前无法正常工作。

更多配置详情请参考 `.env.example` 文件。

## 项目结构

- `ydl/` - 用于 YouTube 字幕下载的核心库
- `ydl-cli/` - 命令行界面
- `examples/` - 示例使用代码

## 许可证

本项目基于 MIT 许可证分发。

详情请参见 [LICENSE](LICENSE.md)。

版权所有 2025 Tyr Chen
