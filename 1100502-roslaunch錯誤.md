# 關於RLException警示
### 本文著重於roslaunch部分
#### **2021/05/02**  

* 輸入 ` roslaunch neuronbot2_gazebo neutonbot2_world.launch ` 出現

 主要錯誤碼為 ` is neither a launch file in package ` 
 及 ` a launch file name The excpetion was written to the log file `

**此狀況應為系統未進入環境**，根本解決辦法為修改程式
   
   
    $gedit  ~/ .bash
    $source ~/catkin_ws(工作資料夾位置)/devel/setup.bash(於文末添加)
