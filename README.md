# whiteboard

## uv 安装 whisperx

> 更新于 2026-02-10

### 1. 安装

```powershell
uv tool install whisperx --reinstall `
  --with "torch<2.6" `
  --with "requests" `
  --with "pytorch-lightning<2.6" `
  --with "nvidia-cudnn-cu12==8.9.7.29" `
  --with "setuptools<82" `
  --with "huggingface-hub<1.0" `
  --extra-index-url https://download.pytorch.org/whl/cu121 `
  --index-strategy unsafe-best-match
```

### 2. 配置 cuDNN PATH

安装完成后，找到 `cudnn_ops_infer64_8.dll` 所在目录（用 fzf / Everything 均可），将其加入 PATH。以下只是作为参考。

```powershell
# 自动查找 cuDNN 路径
$cudnnPath = Get-ChildItem -Path "C:\Users\Ksfour\AppData\Roaming\uv\tools\whisperx" -Recurse -Filter "cudnn_ops_infer64_8.dll" | Select-Object -First 1 -ExpandProperty DirectoryName

# 若未找到，手动指定
if (-not $cudnnPath) {
    $cudnnPath = "C:\Users\Ksfour\AppData\Local\uv\cache\archive-v0\tryILdzclTENZIT8qlOO8\nvidia\cudnn\bin"
}

$env:PATH = "$cudnnPath;$env:PATH"
```

> **持久化（可选）**：运行一次以下命令，以后打开终端自动生效：
> ```powershell
> Add-Content $PROFILE "`$env:PATH = `"$cudnnPath;`" + `$env:PATH"
> ```

### 3. 运行

```powershell
whisperx "C:\Users\Ksfour\Downloads\audio.wav" --language en --diarize --min_speakers 2 --max_speakers 5 --model large-v3 --hf_token hf_xxxxxxx
```

运行结束后会在当前目录生成 `.json`、`.srt`、`.tsv`、`.txt`、`.vtt` 五种格式的结果。

### 4. 说话人分离（`--diarize`）前置步骤

使用 `--diarize` 需要提供 `--hf_token`，并提前同意模型协议：

1. 前往 [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens) 创建 Token
2. 访问以下页面并**同意用户协议**：
   - [pyannote/segmentation-3.0](https://huggingface.co/pyannote/segmentation-3.0)
   - [pyannote/speaker-diarization-3.1](https://huggingface.co/pyannote/speaker-diarization-3.1)
3. 运行时加上 `--hf_token hf_xxxxxxx`

---

Users care about results. Everything else is noise.\
用户最关心的，事实上，是交付。

Learning deployment strategies or fixing broken environments due to deprecated parameters creates barriers. If you spend hours fighting dependencies just to test a tool, you aren't just wasting time—you are wasting your life.\
用户除交付外的第二个目的才是学习。如果用户还要学习如何部署，并且还要面对脆弱的环境崩坏，那么他就在检验交付结果之前浪费了生命，而不仅仅是时间。

**Your life** is what matters most.\
**用户的生命**是最重要的。
