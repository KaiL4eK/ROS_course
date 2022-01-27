# Собираем телегу в Gazebo. Плагины.

Пришло время собрать свою собственную модель робота. 
Сделать это можно несколькими способами: с помощью URDF или SDF.
URDF или SDF это два разных формата описания робота. Оба этих формата для создания моделей используют язык XML. Отличаются форматы своим функционалом, 
несколько отличающимися способами описания различных элементов, но в целом довольно схожи. 

> Здесь мы более подробно остановимся на формате SDF (Simulation Description Format). Данный формат специально предназначем для моделирования в симуляторе Gazebo.

> Что такое Gazebo? Gazebo - это среда для моделирования роботов. Он позволяет быстро и точно создавать роботов, воссоздавать окружающую среду 
(как уличную, так и помещения) и безопасно и эффективно отлаживать алгоритмы управления роботом. Одной из ключевых возможностей Gazebo - это возможность 
имитировать работу различных датчиков таких, как лидар, радар, камера, инерциальный датчик. Для работы с датчиками и управления роботом пользователю предосталяются 
встроенные API.

> Робот созданный в формате SDF, как правило, состоит из следующих основных частей:
- Link (звено) - основные видимые элеметны конструкции: мобильные базы, колеса, звенья манипуляторов и тд
- Joint (соединение) - место сочленения двух звеньев.
- Sensor (датчик)

> Файл модели имеет расширение *.sdf. Макет описания робота можно представитьв следующем виде:
```
<?xml version='1.0'?>
<sdf version='1.4'>
  <model name="mobot" xmlns:xacro="http://www.ros.org/wiki/xacro">
    <link name='base_link'> <!--создание базы мобильного робота-->
    </link>
    <joint name="$wheel_steer_joint" type="revolute">  <!--создание места сочленения базы и колеса-->
      <parent>base_link</parent>
      <child>$wheel_steer_link</child>
      <pose relative_to="base_link">0.5 0.2 0.1 1.5 0 0</pose> <!--место сочленения позиционируется относительно центра мобильной базы-->
    </joint>
    <link name="$wheel_steer_link">  <!--создание колеса мобильного робота-->
      <pose relative_to="$wheel_steer_joint">0 0 0 0 0 0</pose>  <!--центр колеса позиционируется относительно места сочленения-->
    </link>
  </model>
</sdf>
```

### Рассмотрим более подробно некоторые основные элементы и особенности моделирования с использованием SDF
Первый элемент это link. Он включает в себя следующие параметры:
1) pose - положение звена. Желательно указывать относительно чего выбрано положение. Положение указывается в координатах "x y z roll pith yaw".
2) collision - опеределяет область взаимодействия с другими объектами. Как правило форма этой области значительно упрощена и представляет собой куб, 
цилиндр или сферу.
3) visual - определяет внешний вид звена. Может быть задан простой фигурой. Существует фозможность загрузки сторонних моделей в формате STL.
4) inertial - в этом параметре задаются масса и момент инерции звена.

Второй элемент это joint. Он включает в себя следующие параметры:
1) тип сочленения: фиксированное, вращающееся, призматическое и другие.
2) pose - положение сочленения.
3) parent - обычно более старшее звено в сочленении.
4) child - более младшее звено, которое крепится к старшему.
5) axis - определяет оси, в которых сочленение может двигаться (вращаться). Этот параметр также может включать в себя ограничения на движение сочленения.


Третьим элементом является sensor. Его создание происходит в 3 этапа:
1) Создание звена, являющегося телом датчика (вместе с сочленением с остальным роботом, если это необхожимо)
2) Создание непосредственно самого датчика и задание его параметров. В SDF присутствует большое количество датчиков и все они требуют индивидуальной настройки.
3) Подключение плагина для имитации работы датчика и получения в ROS данных от него.

> Здесь приведены лишь краткие сведения по созданию модели. Полный перечень всех элементов модели, их описание и параметры доступны на официальном сайте 
> SDFormat: http://sdformat.org/spec?ver=1.8&elem=sdf

### Как заставить робот двигаться?
Это действительно не тривиальная задача. В открытом доступе приведено можество различных инструкций. Однако работают они далеко на со всеми версиями ROS и Gazebo.
Это еще одна причина выбора SDF как основного инструмента моделирования роботов в ROS.