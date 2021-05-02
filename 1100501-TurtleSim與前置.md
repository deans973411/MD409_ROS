# TurtleSim與前置動作
#### 110/05/01

## 工作環境
在進行開始之前,建議先安裝`ros-(版本)-full-desktop`以節省操作過程中補充擴充包的時間,若空間不足則建議按照順序安裝。  
運行系統為Ubuntu18.04、ROS melodic  

    $apt search ros-melodic-turtlesim         #檢查turtle sim是否可以安裝、是否已安裝
    $sudo apt install ros-melodic-turtlesim   #若無則安裝  
(資料樹狀圖)  
├(melodic資料夾)install folder  
├(catkin_ws資料夾)workspace folder  
╟(build)             #利用catkin建立工作環境與需要到檔案  
╟(devel)             #msg、srv、head package的程式庫  
╙(src)               #package、node的存放區  

我們會在src的目錄下,利用catkin建構package,生成cmake系統所需的cmakelist.txt和package.xml兩個文件  
 >目錄底下一定要有src才能camke
   
    $mkdir -p ~/practice/catkin_ws/src              (-p是允許創建子目錄 -m創建指定目錄的權限)  
    $cd ~/practice/catkin_ws/
    $catkin_make                        
>成功會建構build devel src三個主要資料夾  

    $source ~/practice/catkin_ws/devel/setup.bash   (設定termal的資料來源、工作環境)
    $sudo gedit ~/.bashrc                           (修改termal啟動參數)
    #gedit是gui介面,沒安裝desktop的系統則使用nano編輯
在/.bashrc裡面新增如下
>##set ROS Network##  
>export ROS_HOSTNAME=localhost  
>export ROS_MASTER_URI=http://localhost.11311  
>此設定是用於同機器上使用ROS系統,若為SSH則要另外設定IP  

>##practice##  
>source /opt/ros/melodic/setup.bash  
>source ~/practice/catkin_ws/devel/setup.bash  
>此設定使termal每次都可以載入ROS與practice progect的工作環境  

完成後儲存

## 建立package  
將目錄索引到`src`後,用`catkin_create_pkg`建立package的描述檔案  
`$cd ~/practice/catkin_ws/src`  
`$catkin_create-pkg first_pkg rospy std_msg`
first是package的資料夾名稱,rospy和stdmsg是需要的依賴選項  
建構後src下應該會有cmakelist.txt和package.xml  

## 試用模擬小烏龜
使用`roscore`開啟ROS系統的Master node  
再使用`rosrun`執行單個節點
    
    $rosrun turtlesim turtlesim_node (turtlesim是package名稱,turtlesim_node是node名稱)  
啟動後應該會發現小烏龜的畫面,此時可以使用`rosnode`指令查看ROS系統目前執行的node
    
    $rosnode list           #可以查看啟動中的ROS程式  
也可以更改rosnode node的名稱

    $rosrun turtlesim turtlesim_node __name:=my_turtle  
    #(__name為特殊關鍵字,為node名稱、後面的:=為remapping語法、my_turtle則是remapping的參數,為最後顯示的名稱)
此時再用`rosnode`看就會顯示/my_turtle了,而


    
