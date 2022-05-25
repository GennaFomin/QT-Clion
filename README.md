Краткая инструкция как установить QT на MacOs, если вы не любите QT креатор (10 минут)
==========
Скачиваем QT - HomeBrew
----------
Home Brew это инструемнт для загрузки различных пакетов на ваш компьютер под макосью или линуксом. Если у вас его все еще нет, то вы можете установить его с помощью этой команды

        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
        
После того, как вы введете ее в терминал, в течении нескольких минут у вас появится homebrew.
Для установки QT просто введите в терминал (который стоит перезапустить после установки homebrew)

        brew install qt
        
        
P.S. На этой стадии может возникнуть проблема, где будет надпись типа "no formulae", скорее всего надо апдейтнуть xcode
Проверить это можно при помощи 

        brew doctor
        
Если он выдатс, что установлен старый Xcode - просто обновите его.        
Так же через несколько минут у вас уже будет скачанный HomeBrew. Есть еще один вариант загрузки QT, можно, с использованием VPN скачать его с оффициального сайта, но это займет очень много времени, потому что скорость выгрузки с их стороны очень мала.

Настройка CMAKE файла в проекте в CLion
---------
После загрузки QT, нам нужно получить путь к папке, куда был загружен проект, обычно brew загружает файлы в

        /usr/local/
      
если вдруг вы их там не обнаружили, то можете использовать команду

        brew --prefix qt
        
В моем случае файлы находились 

        /opt/homebrew/Cellar/qt
        
Нам нужен путь до папки Cmake, поэтому в далее из директории qt переходим в папку с названием версии qt, на момент написания инструкции это 6.2.3_1, далее в папку lib, далее cmake. Финальный путь:

        /opt/homebrew/Cellar/qt/6.2.3_1/lib/cmake

Этот путь мы будем указывать далее в CLion.

Отсюда у нас есть два пути: Либо во время создания проекта мы выбираем один из двух QT-шных шаблонов, либо создаем свой проект и там уже прописываем все в CMake файле.

Чем-то особо важным эти варианты не отличаются да и смысла в том, чтобы создавть голый проект мало. Поэтому разберем только вариант с шаблонами. Мы просто укажем полученный путь к директории с cmake сюда

![alt text](https://github.com/GennaFomin/QT-Clion-/blob/main/Снимок%20экрана%202022-05-13%20в%2005.14.39.png)
![alt text](https://github.com/GennaFomin/QT-Clion-/blob/main/Снимок%20экрана%202022-05-13%20в%2005.18.48.png)

По сути это все. Наш cmakefile.txt должен выглядеть так:

        cmake_minimum_required(VERSION 3.21)
        project(untitled)
        
        set(CMAKE_CXX_STANDARD 14)
        set(CMAKE_AUTOMOC ON)
        set(CMAKE_AUTORCC ON)
        set(CMAKE_AUTOUIC ON)
        
        set(CMAKE_PREFIX_PATH "/opt/homebrew/Cellar/qt/6.2.3_1/lib/cmake")
        
        find_package(Qt6 COMPONENTS
        Core
        Gui
        Widgets
        REQUIRED)
        
        add_executable(untitled main.cpp)
        target_link_libraries(untitled
        Qt::Core
        Qt::Gui
        Qt::Widgets
        )
        
Так как это мак и почти все, что связано с разработкой тут скачивается вместе с xcode, то нам не надо больше ничего делать, в том числе возиться с тул чейнами. На самом деле для установки qt на виндоус надо сделать почти все так же (разве что скачать его иначе) и повозиться с тулчейнами. Этот процесс хорошо описан тут -> https://www.jetbrains.com/help/clion/qt-tutorial.html <-

Если же все-таки не хочется пользоваться шаблонами, то можно просто внести все то, что есть в данном cmake в свой cmake и все будет работать. Чтобы удостовериться, что все работает вы можете либо использовать этот код, который выводит версию QT

        #include <QApplication>
        #include <QDebug>
        
        using namespace std;
        
        int main() 
        {
            qDebug() << QT_VERSION_STR;
            return 1;
        }

либо версию из шаблона

        #include <QApplication>
        #include <QPushButton>
        
        int main(int argc, char *argv[]) 
        {
            QApplication a(argc, argv);
            QPushButton button("Hello world!", nullptr);
            button.resize(200, 100);
            button.show();
            return QApplication::exec();
        }
        
которая вернет вам такую кнопочку
![alt text](https://github.com/GennaFomin/QT-Clion-/blob/main/Снимок%20экрана%202022-05-13%20в%2005.19.14.png)

В общем все сильно легче чем казалось. Удачи!

Моменты по CLion и QT
==============
1) Чтобы создать QT UI класс, просто идем в таскбар, и идем File -> New -> QT UI Class и получим такое диалоговое окно ( Аналог визарда из QTCreator )

![alt text](https://github.com/GennaFomin/QT-Clion/blob/main/Снимок%20экрана%202022-05-16%20в%2017.48.03.png) 

2) Для того, чтобы открывать и редактировать файлы с расширением .ui найдите в папке

        ../qt/<your_qt_version>/libexec
        /* в моем случае она находится в /opt/homebrew/Cellar/qt/6.2_3.1/libexec
        
программу Designer, ее интерфейс выглядит так

![alt_text](https://github.com/GennaFomin/QT-Clion/blob/main/Снимок%20экрана%202022-05-19%20в%2016.35.27.png)

и в ней вы можете открывать файлы c расширением .ui, которые создаются автоматически, когда вы добавляете QT UI Class в ваш проект, редактировать их и вы заметите, что файл меняется по мере того, как вы добавляете элементы интерфейса!

3) Если при создании QT Ui Class и при открытие .ui файла в QT Designer вы не можете редактировать этот файл (драг-н-дропать виджеты), то вам надо добавить кусок кода, который добавляет центральный виджет. 

        <widget class="QWidget" name="centralwidget">
                <property name="sizePolicy">
                        <sizepolicy hsizetype="Preferred" vsizetype="Preferred">
                            <horstretch>0</horstretch>
                            <verstretch>0</verstretch>
                        </sizepolicy>
                </property>
        </widget>
        
под MainWindow, чтобы в итоге получить это

            <ui version="4.0">
            <author/>
            <comment/>
            <exportmacro/>
            <class>Ui::MainWniasd</class>
            <widget class="QMainWindow" name="Ui::MainWniasd">
                <property name="geometry">
                    <rect>
                        <x>0</x>
                        <y>0</y>
                        <width>400</width>
                        <height>300</height>
                    </rect>
                </property>
                <property name="windowTitle">
                    <string>MainWniasd</string>
                </property>

                <widget class="QWidget" name="centralwidget">
                    <property name="sizePolicy">
                        <sizepolicy hsizetype="Preferred" vsizetype="Preferred">
                            <horstretch>0</horstretch>
                            <verstretch>0</verstretch>
                        </sizepolicy>
                    </property>
                </widget>

            </widget>
            <pixmapfunction/>
            <connections/>
        </ui>
        
Эта проблема была решена при помощи https://stackoverflow.com/questions/67894319/cannot-add-widgets-to-qmainwindow-using-qt-designer-and-clion
