安装必要的工具
首先，需要安装PyInstaller：

pip install pyinstaller
打包应用程序
基本打包方法
打开命令提示符，导航到包含main.py文件的目录
执行打包命令：

pyinstaller --name "工具" --windowed --icon=app_icon.ico main.py
参数解释：

–name - 指定生成的exe文件名称
–windowed - 创建没有控制台窗口的GUI应用
–icon - 应用程序图标
高级打包方法（推荐）
为了确保所有依赖关系都被正确打包，我建议创建一个规范文件：

1.首先生成规范文件：

pyi-makespec --name "工具" --windowed --icon=app_icon.ico main.py
2. 编辑生成的.spec文件，添加必要的数据文件（如果有样式文件等）：

# 在datas列表中添加所需的额外文件
a = Analysis(
    ['main.py'],
    pathex=[],
    binaries=[],
    datas=[('style.qss', '.')],  # 添加样式文件
    hiddenimports=[],
    hookspath=[],
    hooksconfig={},
    runtime_hooks=[],
    excludes=[],
    win_no_prefer_redirects=False,
    win_private_assemblies=False,
    cipher=None,
    noarchive=False,
)
如果在当前环境下，直接运行第三个步骤，忽略第二步。

3. 使用规范文件构建应用：

pyinstaller "工具.spec"
单文件模式
如果您希望打包成单个exe文件（而不是文件夹），可以使用–onefile选项：

pyinstaller --onefile --name "工具" --windowed --icon=app_icon.ico main.py
单文件模式会使启动速度变慢，因为它需要在临时目录解压所有文件。

对于复杂的应用，PyInstaller有时可能会遗漏某些隐式导入的模块。您可以在.spec文件中添加这些模块：

hiddenimports=['rasterio', 'PIL', 'numpy', 'matplotlib'],
处理数据文件
如果您的应用使用外部数据文件（如图标、样式表等），确保它们被正确包含。在.spec文件的datas列表中添加：

datas=[
    ('style.qss', '.'),
    ('icons/', 'icons/')  # 如果有图标文件夹
],
替代方案
除了PyInstaller，还有其他选择：

cx_Freeze
cx_Freeze是另一个打包Python程序的工具：
pip install cx_Freeze
使用setup.py：

import sys
from cx_Freeze import setup, Executable

build_exe_options = {
    "packages": ["os", "PyQt5", "numpy", "PIL", "rasterio", "matplotlib"],
    "include_files": ["style.qss"]
}

setup(
    name="工具",
    version="0.1",
    description="工具",
    options={"build_exe": build_exe_options},
    executables=[Executable("main.py", base="Win32GUI", icon="app_icon.ico")]
)
执行：

python setup.py build
Nuitka
Nuitka将Python代码编译成C语言代码，然后编译为可执行文件，性能更好：
pip install nuitka
打包命令：

python -m nuitka --follow-imports --windows-disable-console --enable-plugin=qt-plugins --include-data-files=style.qss=style.qss main.py
PyInstaller适合大多数情况

如果追求更好的性能，可以考虑Nuitka