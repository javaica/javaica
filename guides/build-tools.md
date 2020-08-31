# Системы сборки

## Гайды

1. [Maven](maven.md)
2. Gradle (coming soon...)

## Краткая история

Давным-давно для того, чтобы собрать проект, необходимо было
добыть где-то нужные библиотеки, добавить их в проект,
собрать это все компилятором и сделать много других, не особо приятных вещей.
Поэтому умные люди придумали автоматизировать все это. Так и появились
системы сборки, которые автоматизировали компиляцию, сборку бинарника,
деплой на сервер и тому подобное. Изначально системы сборки появились
для C++ (потому что это было еще в те времена, когда не так уж много
чего было). Первая система сборки для Java - это Apache Ant, идея
которого была взята у плюсового make. Ant был императивным, то есть
сборка выражалась в конкретных действиях (скомпилируй это, собери то).
Эти скрипты составлялись в XML (да, звучит не очень красиво).

Наследником Ant стал Apache Maven, который уже был декларативным
(т.е. в конфигурационном файле определяются свойства, а не действия).
Также, Maven научился автоматически управлять зависимостями и делать много
других полезных вещей, которые уже не нужно было прописывать вручную. Конфиг
все так же делается через XML, но в данном случае XML подходит гораздо лучше.

Но и Maven через какое-то время перестал удовлетворять все желания
разработчиков именно из-за своей декларативности, т.к. для кастомной логики
сборки приходилось подыскивать нужный плагин или писать его самим.
И появился Gradle, использующий лучшее из предыдущих двух систем.
Gradle позволяет определять сложную логику сборки прямо в конфигурационном
файле, причем на удобном языке (Groovy/Kotlin DSL) (ну и всем надоело
лепить громоздкий XML).

На данный момент Ant уже практически не используется (разве что
в совсем уж страшном легаси), Maven и Gradle разделяют рынок. Для учебных
проектов рекомендую начать с Maven, т.к. вам точно не нужна сложная логика
сборки, а знать нужно обе системы. Даже если вы не собираетесь осуществлять
сборку с помощью Maven, все равно достаточно часто нужно генерировать
описание проекта в виде POM (Project Object Model).

Плюс Maven также в том, что, благодаря декларативности описания проекта,
IDE умеет подсказывать зависимости благодаря существующим поисковым машинам
для репозиториев Maven. Т.е. вы можете ввести groupId, тыкнуть на подсказку, и
IDE подставит groupId и version. У Gradle такого, к сожалению, нет.