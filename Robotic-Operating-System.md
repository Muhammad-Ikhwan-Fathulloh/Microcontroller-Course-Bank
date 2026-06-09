# Materi Robotic Operating System (ROS)

## Bagian 1: Pendahuluan ROS

### 1.1 Apa itu ROS?
ROS (Robot Operating System) bukanlah sistem operasi tradisional, melainkan **middleware** berbasis framework yang menyediakan:
- Komunikasi antar proses (node)
- Manajemen paket
- Alat debugging dan visualisasi
- Simulasi (Gazebo, RViz)

### 1.2 Versi ROS
| Versi | Distribusi | Platform | Tahun |
|-------|------------|----------|-------|
| ROS 1 | Noetic Ninjemys | Ubuntu 20.04 | 2020 |
| ROS 2 | Humble / Iron | Ubuntu 22.04+ | 2022+ |

> **Catatan:** Materi ini fokus pada **ROS Noetic** (ROS 1) yang stabil untuk belajar.

---

## Bagian 2: Konsep Fundamental

### 2.1 Arsitektur ROS
```
[Node A] --publish--> [Topic] --subscribe--> [Node B]
    |                       |
    |--service call--> [Service Server] --response--> |
    |                       |
    |--parameter--> [Parameter Server] <--get/set-- |
```

### 2.2 Komponen Utama

| Komponen | Fungsi | Contoh |
|----------|--------|--------|
| **Master** | Mendaftarkan nama node, topic, service | `roscore` |
| **Node** | Program eksekusi tunggal | `teleop_key`, `gazebo` |
| **Topic** | Komunikasi asinkron (stream data) | `/cmd_vel` (topic velositas) |
| **Message** | Struktur data untuk topic | `Twist`, `LaserScan` |
| **Service** | Komunikasi sinkron (request/reply) | `/add_two_ints` |
| **Parameter** | Variabel global di Master | `robot_name: "burger"` |
| **Bag** | Rekaman data ROS | `rosbag record -a` |

### 2.3 Command Line Essential

```bash
# Melihat semua node aktif
rosnode list

# Informasi detail node
rosnode info /gazebo

# Melihat topic aktif
rostopic list

# Monitor data topic secara real-time
rostopic echo /cmd_vel

# Melihat tipe message topic
rostopic type /cmd_vel

# Memanggil service
rosservice call /spawn "x: 1.0 y: 2.0"

# Menjalankan launch file
roslaunch package_name file.launch
```

---

## Bagian 3: File Penting dalam ROS

### 3.1 Package Structure
```
my_robot_package/
├── CMakeLists.txt          # Build configuration
├── package.xml             # Metadata & dependencies
├── launch/
│   └── my_launch.launch    # Launch multiple nodes
├── src/
│   └── my_node.py / .cpp   # Source code
├── urdf/
│   └── robot.urdf          # Robot model
├── config/
│   └── params.yaml         # Parameter values
└── worlds/
    └── my_world.world      # Gazebo environment
```

### 3.2 URDF (Unified Robot Description Format)
Contoh sederhana robot dengan satu roda:
```xml
<robot name="my_robot">
  <link name="base_link">
    <visual>
      <geometry>
        <box size="0.5 0.5 0.2"/>
      </geometry>
    </visual>
  </link>
  
  <joint name="wheel_joint" type="continuous">
    <parent link="base_link"/>
    <child link="wheel_link"/>
    <axis xyz="0 1 0"/>
  </joint>
</robot>
```

### 3.3 Launch File (XML)
```xml
<launch>
  <!-- Set parameter -->
  <param name="robot_description" command="cat $(find my_pkg)/urdf/robot.urdf"/>
  
  <!-- Start Gazebo with empty world -->
  <include file="$(find gazebo_ros)/launch/empty_world.launch"/>
  
  <!-- Spawn robot -->
  <node name="spawn_robot" pkg="gazebo_ros" type="spawn_model" 
        args="-param robot_description -urdf -model my_robot"/>
  
  <!-- Start teleop node -->
  <node name="teleop" pkg="turtlebot3_teleop" type="teleop_keyboard.py"/>
</launch>
```

---

## Bagian 4: Pemrograman Node di ROS

### 4.1 Contoh Publisher (Python)
```python
#!/usr/bin/env python3
import rospy
from geometry_msgs.msg import Twist

def publisher_node():
    rospy.init_node('my_publisher', anonymous=True)
    pub = rospy.Publisher('/cmd_vel', Twist, queue_size=10)
    rate = rospy.Rate(10)  # 10 Hz
    
    while not rospy.is_shutdown():
        msg = Twist()
        msg.linear.x = 0.2   # maju 0.2 m/s
        msg.angular.z = 0.5  # belok 0.5 rad/s
        pub.publish(msg)
        rate.sleep()

if __name__ == '__main__':
    try:
        publisher_node()
    except rospy.ROSInterruptException:
        pass
```

### 4.2 Contoh Subscriber (Python)
```python
#!/usr/bin/env python3
import rospy
from sensor_msgs.msg import LaserScan

def callback_laser(data):
    # data.ranges adalah list jarak (meter)
    jarak_depan = data.ranges[360]  # index tengah untuk sensor 720 titik
    print(f"Jarak depan: {jarak_depan:.2f} meter")

def subscriber_node():
    rospy.init_node('laser_listener', anonymous=True)
    rospy.Subscriber('/scan', LaserScan, callback_laser)
    rospy.spin()  # Menjaga node tetap berjalan
```

---

## Bagian 5: Simulasi Lengkap (Studi Kasus TurtleBot3)

### 5.1 Membuat Robot Bergerak Otomatis (Python Script)

Buat file `auto_drive.py` di dalam package Anda:

```python
#!/usr/bin/env python3
import rospy
from geometry_msgs.msg import Twist
from sensor_msgs.msg import LaserScan

class AutoDriver:
    def __init__(self):
        rospy.init_node('auto_driver', anonymous=True)
        self.pub = rospy.Publisher('/cmd_vel', Twist, queue_size=10)
        rospy.Subscriber('/scan', LaserScan, self.scan_callback)
        self.rate = rospy.Rate(10)
        self.front_distance = float('inf')
        
    def scan_callback(self, data):
        # Ambil 3 sampel depan (indeks sekitar 330-390 untuk sensor 720 titik)
        self.front_distance = min(data.ranges[330:390])
        
    def drive(self):
        while not rospy.is_shutdown():
            msg = Twist()
            if self.front_distance > 0.5:  # Jika jarak > 0.5 meter
                msg.linear.x = 0.2   # Maju
                msg.angular.z = 0.0
            else:  # Ada halangan
                msg.linear.x = 0.0
                msg.angular.z = 0.5   # Belok kanan
                
            self.pub.publish(msg)
            self.rate.sleep()

if __name__ == '__main__':
    driver = AutoDriver()
    driver.drive()
```

### 5.2 Jalankan Simulasi Otomatis

Dalam container Docker yang sama (setelah Gazebo berjalan):

```bash
# Beri izin eksekusi script
chmod +x auto_drive.py

# Jalankan node autopilot
rosrun your_package auto_drive.py
```

Robot akan bergerak maju secara otomatis dan menghindari dinding ketika terlalu dekat.

---

## Bagian 6: Tools Visualisasi dan Debugging

| Tool | Fungsi | Perintah |
|------|--------|----------|
| **rqt_graph** | Lihat grafik komunikasi node | `rqt_graph` |
| **rqt_plot** | Plot data numeric secara real-time | `rqt_plot /scan[360]` |
| **rviz** | Visualisasi 3D robot dan sensor | `rviz` |
| **rqt_console** | Monitor log messages | `rqt_console` |
| **rosbag** | Rekam & replay data | `rosbag record -O data.bag /cmd_vel /scan` |

---

## Bagian 7: Tugas Praktik (Hands-On Assignment)

### Tujuan:
Membuat robot yang dapat mengikuti dinding (wall follower).

### Langkah:
1. Gunakan Docker dan TurtleBot3 seperti panduan sebelumnya.
2. Buat node Python yang subscribe ke topic `/scan`.
3. Hitung jarak ke dinding kiri, kanan, dan depan.
4. Implementasi logika:
   - Jika jarak kiri < 0.4m → belok kanan
   - Jika jarak kanan < 0.4m → belok kiri
   - Jika jarak depan < 0.5m → mundur
   - Lainnya → maju lurus

### Output yang Diharapkan:
Robot dapat menyusuri koridor tanpa menabrak dinding.

---

## Ringkasan Alur Kerja ROS End-to-End

```
1. [Write Code] → 2. [Build Package] → 3. [Launch Simulasi] → 4. [Run Node]
       ↓                   ↓                     ↓                    ↓
  auto_drive.py      catkin_make         roslaunch gazebo      rosrun auto_drive
                        atau                 world.launch            .py
                    source devel/setup.bash
```

---

## Referensi Lanjutan

| Topik | Sumber |
|-------|--------|
| Official ROS Wiki | http://wiki.ros.org |
| TurtleBot3 Tutorial | https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/ |
| ROS Python API | http://docs.ros.org/en/noetic/api/rospy/html/ |
| Gazebo Tutorial | http://gazebosim.org/tutorials |
