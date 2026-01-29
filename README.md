# 🎰 Prominence Poker 简体中文汉化项目
本仓库提供 Steam 平台免费德州扑克游戏 **《Prominence Poker》** 的全文本汉化方案。
项目利用 **Gemini 3 Pro** 大语言模型进行深度语境翻译，并采用“字体硬链接劫持”技术解决了虚幻引擎中文不显示的问题。

---

## 📖 汉化背景

游戏目录内其实预埋了 `NotoSansSC-Regular.ufont` 等中文字体库，但官方迟迟未开启中文支持。由于简单的“重命名替换”无法绕过虚幻引擎的校验，本项目采用 **PowerShell 硬链接脚本**，在系统层面欺骗引擎，实现全 UI 中文化。

---

## 🚀 安装步骤

### 第一步：替换中文文本

1. 找到游戏的本地文件夹。
* *路径参考：* `SteamLibrary\steamapps\common\Prominence Poker\Prominence\Content\Localization\Game`
* *快捷方法：* Steam 库中右键 `Prominence Poker` -> 管理 -> 浏览本地文件。

2. 进入 `en` 文件夹，将本仓库提供的 `Game.locres` 文件覆盖进去。
* *建议：* 替换前请先备份原始文件。

### 第二步：建立字体库映射（关键）
为了让游戏能正确渲染中文字符，我们需要将所有英文字体指向内置的中文字库：

1. 进入字体目录：`Prominence\Content\UI\FONTS`
2. 在该文件夹空白处 **右键 -> 在终端打开** (或打开 PowerShell 并 `cd` 到此目录)。
3. 复制并运行以下代码：

```powershell
# 1. 定义中文母本
$sourceFont = "NotoSansSC-Regular.ufont"

# 2. 自动化全量劫持：将所有 .ufont 指向中文字库
$allUfonts = Get-ChildItem *.ufont | Where-Object { 
    $_.Name -ne $sourceFont -and $_.Name -notlike "*.bak" 
}

foreach ($file in $allUfonts) {
    $targetName = $file.Name
    $bakName = "$targetName.bak"
    if (-not (Test-Path $bakName)) {
        mv $targetName $bakName
        Write-Host "[+] 备份并准备劫持: $targetName" -ForegroundColor Green
    } else {
        rm $targetName -ErrorAction SilentlyContinue
        Write-Host "[!] 覆盖旧链接: $targetName" -ForegroundColor Yellow
    }
    # 建立硬链接
    cmd /c mklink /H "$targetName" "$sourceFont"
}
Write-Host "`n[***] 劫持任务完成！游戏现在具备全 UI 中文显示能力。" -ForegroundColor Cyan

```

---

## 🛠️ 技术原理 Q&A

**Q：为什么直接改文件名不行？**
A：虚幻引擎在加载资源时会校验文件内部头信息。直接改名会导致引擎识别错误。本项目通过 Windows 的硬链接（Hard Link）机制，让引擎在读取 `Anton.ufont`（英文字体）时，实际读取的是 `NotoSansSC`（中文字体）的数据块，从而完美实现“偷梁换柱”。

**Q：为什么有的地方还是英文？**
A：这是因为有些为英文图片而不是文字，无法替换。目前的汉化已经覆盖了 1718 条文本，足以满足正常游戏需求。

---

## 🤝 鸣谢

* **翻译支持**：Gemini 3 Pro (LLM)
* **技术支持**：由 Petrichor001汉化制作。游戏里见到我请让让我！

---

