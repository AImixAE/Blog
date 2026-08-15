---
title: 在 Windows 下安装 freetype 库
published: 2026-08-15
description: '安装并使用 freetype'
image: ''
tags: ['库']
category: 'C/C++'
draft: false
lang: 'zh-CN'
slug: zai-windows-xia-an-zhuang-freetype-ku
---

# 在 Windows 下安装并使用 freetype 库

今天在折腾好友的 [**Neko Browser**](https://github.com/xhdndmm/neko-browser) 项目时
，发现需要安装一些库才能正常编译运行，而其中就需要依赖 freetype 库。

而在 Windows 下，如果需要使用 FreeType 库，需要咱先安装 FreeType 库呢~

## 获取 FreeType

先打开 FreeType 的[下载页面](https://www.freetype.org/download.html)

如果你选择 SourceForge 的下载方式，那么你可以直接下载 FreeType 源码
 _(ft2xxx.zip)_

如果你选择 Savannah 的下载方式，里面的文件可能会让你一脸懵 w(ﾟДﾟ)w

不怕不怕，只需要选择 `ft-XXXX.zip` 就好了喵!

当然，这里也有 FreeType 的源码仓库:
 `https://gitlab.freedesktop.org/freetype/freetype.git`

你也可以直接 clone 后编译安装。

## 编译

其中提供的编译方式很多，包括 cmake / Make
，但我们只介绍 cmake 编译方式。

### cmake

进入源码目录后，按照 cmake 的方式即可编译

```bash
cmake -B build -S .
cd build
# 通过你所选择的编译器编译安装
# make / ninja / msbuild / ...
```

如果是 Cmake + MSBuild，你也可以直接按照以下懒人方式:

```sh
cmake -B build -S .
cd build
msbuild ALL_BUILD.vcxproj

sudo msbuild INSTALL.vcxproj   # 安装
```

然后就可以使用啦!

## 使用

这里只提供示例:

```c
#include <stdio.h>
#include <ft2build.h>
#include FT_FREETYPE_H

int main() {
    FT_Library library;
    FT_Face face;
    FT_GlyphSlot slot;
    
    // 1. 初始化
    FT_Init_FreeType(&library);
    
    // 2. 加载字体（改成你系统里有的字体路径）
    FT_New_Face(library, "/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf", 0, &face);
    
    // 3. 设置字体大小
    FT_Set_Pixel_Sizes(face, 0, 48);  // 48像素高
    
    slot = face->glyph;
    
    // 4. 渲染字符 'A'
    FT_Load_Char(face, 'A', FT_LOAD_RENDER);
    
    // 5. 打印字符信息
    printf("字符 'A' 的位图:\n");
    printf("宽度: %d 像素\n", slot->bitmap.width);
    printf("高度: %d 像素\n", slot->bitmap.rows);
    printf("左边距: %d\n", slot->bitmap_left);
    printf("上边距: %d\n", slot->bitmap_top);
    
    // 6. 用字符画显示位图
    printf("\n位图预览:\n");
    for (int y = 0; y < slot->bitmap.rows; y++) {
        for (int x = 0; x < slot->bitmap.width; x++) {
            unsigned char pixel = slot->bitmap.buffer[y * slot->bitmap.width + x];
            // 根据灰度值显示不同字符
            if (pixel > 200) printf("██");
            else if (pixel > 150) printf("▓▓");
            else if (pixel > 100) printf("▒▒");
            else if (pixel > 50) printf("░░");
            else printf("  ");
        }
        printf("\n");
    }
    
    // 7. 清理
    FT_Done_Face(face);
    FT_Done_FreeType(library);
    
    return 0;
}
```
