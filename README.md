Краткая инструкция как установить QT на MacOs, если вы не любите QT креатор (10 минут)
==========
Скачиваем QT - HomeBrew
----------
Home Brew это инструемнт для загрузки различных пакетов на ваш компьютер под макосью или линуксом. Если у вас его все еще нет, то вы можете установить его с помощью этой команды

        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
        
После того, как вы введете ее в терминал, в течении нескольких минут у вас появится homebrew.
Для установки QT просто введите в терминал (который стоит перезапустить после установки homebrew)

        brew install qt
        
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

        /opt/homebrew/Cellar/qt/6.2.3_1/cmake

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
