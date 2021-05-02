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
>注意,以下操作都要在rosrun啟動的情況下執行  
### node的觀念  
使用`roscore`開啟ROS系統的Master node  
再使用`rosrun`執行單個節點
    
    $rosrun turtlesim turtlesim_node (turtlesim是package名稱,turtlesim_node是node名稱)  
啟動後應該會發現小烏龜的畫面,此時可以使用`rosnode`指令查看ROS系統目前執行的node
    
    $rosnode list           #可以查看啟動中的ROS程式  
也可以更改rosnode node的名稱,要先將已經打開的`turtlesim`程式終止掉  

    $rosrun turtlesim turtlesim_node __name:=my_turtle  
    #(__name為特殊關鍵字,為node名稱、後面的:=為remapping語法、my_turtle則是remapping的參數,為最後顯示的名稱)
此時再用`rosnode`看就會顯示/my_turtle了,而此時也會看到前一個已經關掉的`turtlesim`還留在清單上,這是因為rosnode並不會自動斷線,未繼續連接的程式,因此我們需要使用

    $rosnode cleanup
清除當下無法進行連線的node,儘管node正常運作,但並未與master node連接的節點  
再以`rosnode list`查看,`turtlesim`已經消失  

### 資料視覺化  
在`rosrun`啟動的情況下執行以下兩個node

    $rosrun turtlesim turtlesim_node
    $rosrun turtlesim turtle turtle_teleop_key
    #這是利用鍵盤控制烏龜的程式,利用上下左右控制
    $rosrun rqt_graph rqt_graph
rqt graph是可以繪製動態流程關係圖dynamic graph的package,可以透過rosnode list得知目前開啟的ros程式關係,簡易關係如下  
![圖片1](https://user-images.githubusercontent.com/74861204/116804443-73e4b080-ab51-11eb-89ef-80ae8d42a0bf.png)  
可以輕鬆地協助我們了解目前ros系統的前後端關係  
### Topic通訊  
從上面的dinamic graph可以看出目前的topic通道是`/turtle1/cmd_vel`,若想得知topic內交流的資訊是甚麼,可以透過`rostopic`來監控  

    $rostopic echo /turtle1/cmd_vel    #鍵入我們要查看的topic
剛輸入並不會看到任何資訊,這是因為我們尚未操作publisher,也就是dinamic graph的`turtle_teleop_key`  
回到`turtle_teleop_key`的terminal按下任意方向鍵讓小烏龜移動,在`rostopic`的視窗就可以看到`/turtle1/cmd_vel`topic發布的資料了  
此時再用`rqt_graph`可以看出`/turtle1/cmd_vel`同時又連接`rostopic的關係了`  
>rqt_graph 一定要重新啟動才會看到更新後的關係  

1. 這個指令是用來查看ros系統所有的發布者與訂閱者  
    `$rostopic list -v`  
2. 這個則是來查看topic的類型,可以利用得知類型,查看message   
    `$rostopic type /turtle1/cmd_vel`  
    #使用後會發布如下  
    `geometry_msgs/Twist         #資料型態`  
3. 查看資料型態  
    `$rosmsg show geometry_msgs/Twist`   
    #在omni bot中應該會看到linear與angular對應xyz軸的資料型態是float64    
4. 使用rostopic發布topic
    我們從上述操作得知,可以透過`rostopic`指令取出傳送過程中的訊息、信號,同理也可以對node發出訊號與指令,操作如下  
    `$ rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'`  
    此指令為`rostopic pub`,對`turtle1/cmd_vel`發布參數`-1`也就是一次的topic  
    message的名稱是`geometry_msgs/Twist`,也就是資料型態  
    兩個中括號前面是linear的xyz,後面是angular轉向的xyz  
    按照此規則輸入可得規律的圖形,而本指令會得出圓弧裝的路徑,
    * linear越大路徑越長  
    * angular則是影響迴轉半徑  

5. 使用rostopic pub持續發布  
    上述操作發布了一次性的指令,烏龜在執行完指令後即停止,下為範例  
    `rostopic pub /tutle1/cmd_vel geometry_msg/Twist -r 1 -- '[2.0,0.0,0.0]','[0.0,0.0,-1.8]'`
    發布次數從`-1`改為`-r 1`,需要此程式停止才可以停止topic指令輸出  
    此時用`rqt_graph`可以看出`teleop_turtle`和`rostopic`同時輸出給`turtlesim`和`rostopic`,前兩者可互相干涉操作,後兩者可接收同樣資訊  
6. 使topic頻率持續發布
    ros指令許多都有一個特性,僅輸出當下的狀態,不會持續輸出。因此我們希望它能及時發布資訊的操作指令如下  
    `$rostopic hz /turtle1/pose`  
    可以從上簡易看出是通過`turtle1/pose`的topic頻繁的輸出資訊  
7. 觀測動態變化圖  
    首先要先觀測單點資訊,指令如下  
    `$rostopic echo /turtle1/pose`  
    利用topic發布資料當下的scrolling time plot,單點資料  
    我們也可以利用`rqt`package可以繪製圖像的原理,來繪製動態的scrolling time plot圖,這裡使用`rqt_plot`  
    `rosrun rqt_plot rqt_plot`  
    進入GUI介面後選取動態資訊的topic `/turtle1/pose/x`,並對turtle用key輸入移動指令,即可觀測其x向量變化圖  
