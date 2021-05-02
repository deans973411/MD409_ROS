# TurtleSim與前置動作
#### 110/05/01

## 工作環境
在進行開始之前,建議先安裝`ros-(版本)-full-desktop`以節省操作過程中補充擴充包的時間,若空間不足則建議按照順序安裝。  

    $apt search ros-melodic-turtlesim         #檢查turtle sim是否可以安裝、是否已安裝
    $sudo apt install ros-melodic-turtlesim   #若無則安裝  

├install folder(melodic資料夾)  
├workspace folder(catkin_ws資料夾)  
╟build             #利用catkin建立工作環境與需要到檔案  
╟devel             #msg、srv、head package的程式庫  
╙src               #package、node的存放區  

我們會利用catkin
