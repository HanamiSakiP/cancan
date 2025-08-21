以下是 fcitx5 配置整理成的 Markdown 文档：


#### 下载fcitx5及中文拼音包
```bash
sudo apt install fcitx5 fcitx5-chinese-addons
```

### 主题下载安装
* [关注塔菲喵](https://github.com/thep0y/fcitx5-themes-candlelight)
```bash
git clone https://github.com/thep0y/fcitx5-themes.git
cd fcitx5-themes
cp spring ~/.local/share/fcitx5/themes -r
vim ~/.config/fcitx5/conf/classicui.conf
```

### 主题配置（classicui.conf）
```bash
# 垂直候选列表
Vertical Candidate List=False

# 按屏幕 DPI 使用
PerScreenDPI=True

# Font (设置成你喜欢的字体)
Font="Smartisan Compact CNS 22"

# 主题(这里要改成你想要使用的主题名，主题名就在下面)
Theme=spring
```
