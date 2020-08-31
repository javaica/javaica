# Системы сборки

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

## Maven

Конфиг Maven состоит в единственном файле - **pom.xml**. В нем определяются:
1. Основная информация проекта (важное - (*))
   * groupId - идентификатор разработчика (*)
   * artifactId - название проекта (*)
   * version (*)
   * name - если хотите определить удобное для чтения человеком имя
   * description
   * packaging (jar, war, ear) - что вы хотите получить после сборки (чаще всего jar)
   * *Всякая другая ерунда*
2. Зависимости (и управление зависимостями, если нужно)
3. Плагины

Все действия в Maven описываются плагинами.
Так что хочешь пукнуть при сборке - ищи соответствующий плагин.

Шаблон pom.xml выглядит примерно так:

```xml
<project>
  <modelVersion>4.0.0</modelVersion> <!-- Стандартное для Maven -->

  <parent>
    <!-- Если есть родительский проект,
    например, если это один модуль из нескольких в одном проекте -->
  </parent>

  <groupId>com.github.kuzznya</groupId>
  <artifactId>my-awesome-project</artifactId>
  <version>0.0.1</version>
  <description>My awesome project with Maven</description>

  <dependencies>
    <dependency>
      <groupId>com.dep</groupId>
      <artifactId>dependency-project</artifactId>
      <version>1.0.0</version>
    </dependency>

    <!-- ... -->
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <!-- Определение версии Java -->
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <executions>
          <execution> <!-- Определение фазы жизненного цикла Maven и цели (goal), которую выполняет плагин -->
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

</project>
```

Жизненный цикл Maven есть на [Вики](https://ru.wikipedia.org/wiki/Apache_Maven)

### Требования

Файл pom.xml должен находиться в корневом каталоге проекта.
Исходный код проекта должен находиться в `src/main/java`,
исходный код тестов - в `src/test/java`

Для Web-проектов, использующих web.xml, он должен находиться в `src/main/webapp/WEB-INF`

### Сборка JAR

Для сборки есть 3 различных плагина, разница между ними хорошо
описана [тут (англ)](https://medium.com/@randilfernando/when-to-use-maven-jar-maven-assembly-or-maven-shade-ffc3f76ba7a6#:~:text=maven%2Djar%2Dplugin%20%3A%20This,src%2Fmain%2Fresources%2F.&text=maven%2Dshade%2Dplugin%20%3A%20It,by%20specifying%20the%20main%20class.)

Проще всего использовать `maven-shade-plugin`:

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-shade-plugin</artifactId>
  <executions>
    <execution>
      <phase>package</phase>
      <goals>
        <goal>shade</goal>
      </goals>
      <configuration>
        <transformers>
          <transformer implementation=”org.apache.maven.plugins.shade.resource.ManifestResourceTransformer”>
            <mainClass>
              path.to.MainClass
            </mainClass>
          </transformer>
        </transformers>
        <createDependencyReducedPom>
          false
        </createDependencyReducedPom>
      </configuration>
    </execution>
  </executions>
</plugin>
```

### Тонкости при сборке проекта с JavaFX

OpenJFX 11+, наследник убранного из JDK JavaFX начиная с Java 9,
использует Java Modules для того, чтобы не тащить в JAR реализации JavaFX для
различных ОС. Но для небольших, не production проектов, это доставляет только
дополнительную головную боль. Поэтому рекомендую использовать такую конфигурацию:

```xml
<dependencies>
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-graphics</artifactId>
    <version>12</version>
    <classifier>win</classifier>
  </dependency>
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-graphics</artifactId>
    <version>12</version>
    <classifier>linux</classifier>
  </dependency>
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-graphics</artifactId>
    <version>12</version>
    <classifier>mac</classifier>
  </dependency>

  <!-- ... -->
</dependencies>

<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-shaded-plugin</artifactId>
      <executions>
        <execution>
          <phase>package</phase>
          <goals>
            <goal>shade</goal>
          </goals>
          <configuration>
            <transformers>
              <transformer implementation=”org.apache.maven.plugins.shade.resource.ManifestResourceTransformer”>
                <mainClass>
                  path.to.MainClass
                </mainClass>
              </transformer> 
            </transformers>
            <createDependencyReducedPom>
              false
            </createDependencyReducedPom>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```
