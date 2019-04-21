# centos7 基本命令

## screen

### 安装

`yum install screen`

### 基本操作

**创建 screen(名称为 nodeServer)**

`screen -S nodeServer`

**进入某个 screen**

`screen -r nodeServer`

或者

`screen -r 进程编号`

**列出所有的 screen**

`screen -ls`

**清除 dead 会话**

如果由于某种原因其中一个会话死掉了（例如人为杀掉该会话），这时 screen -list 会显示该会话为 dead 状态。使用 screen -wipe 命令清除该会话

`screen -wipe`

**关闭或杀死窗口**

杀死当前的窗口，同时也将杀死这个窗口中正在运行的进程。

`C-a k`

或者

`C-a:` 输入 quit

**推出某个 screen(变成 detached 状态)**

在每个 screen session 下，所有命令都以 ctrl+a(C-a) 开始

`C-a d`

暂时离开当前 session，将目前的 screen session (可能含有多个 windows) 丢到后台执行，并会回到还没进 screen 时的状态，此时在 screen session 里，每个 window 内运行的 process (无论是前台/后台)都在继续执行，即使 logout 也不影响。

## 文档编辑

| 操作 | 作用                           |
| ---- | ------------------------------ |
| \$y  | 是全部复制，不是全选           |
| gg   | 跳到行首                       |
| V    | 是进入 Visual(可视）模式       | 、 |
| G    | 光标移到最后一行               |
| d    | 删除选中内容                   |
| ggdG | esc 后 ggdG 删除全部           |
| ggVG | esc 后 ggVG 全选！=> 删除 按 d |
| ggyG | 全选复制！                     |
