﻿# Озвучивание

Чтобы предоставить голосовые клипы движку, сохраните их в папке `Resources/Naninovel/Voice` (можно изменить в конфигурации звука в папке `Loader`). Вы можете дополнительно организовать их с помощью подпапок, если хотите; в этом случае используйте косую черту (`/`) при ссылке на них в сценариях Naninovel. Например, голосовой аудиоклип, хранимый как `Resources/Naninovel/Voice/Intro/Day/25.wav`, может использоваться в сценариях как `Voice/Intro/Day/25`.

Кроме того, можно использовать [адресируемую систему ассетов](/ru/guide/resource-providers.md#адресация ) для ручного предоставления ресурсов. Чтобы предоставить доступ к ассету, назначьте адрес, равный пути, который вы использовали бы для его предоставления с помощью описанного выше метода, за исключением опущенной части "Resources/". Например, чтобы предоставить клип Hello.wav", назначьте ассету клипа следующий адрес: `Naninovel/Voice/Hello`. Имейте в виду, что адресируемый провайдер по умолчанию не используется в редакторе; вы можете разрешить его, включив свойство `Enable Addressable In Editor` в меню конфигурации провайдера ресурсов.

Вы можете использовать любые [поддерживаемые Unity](https://docs.unity3d.com/Manual/AudioFiles.html) аудиоформаты для голосовых клипов.

Поведение воспроизведения озвучивания можно настроить с помощью контекстного меню `Naninovel -> Configuration -> Audio`; доступные параметры см. в [руководстве по конфигурации](/ru/guide/configuration.md#аудио).

Используйте команду [@voice], за которой следует имя клипа (путь), чтобы воспроизводить голос в сценариях Naninovel.

## Автоматическое озвучивание

В полностью озвученных играх назначение команды [@voice] для каждой озвученной линии может стать утомительной задачей. Функция автоматического озвучивания позволяет автоматически воспроизводить голосовой клип, имя которого равно номеру строки текущей команды [@print]; таким образом, вам вообще не придется использовать команды [@voice] в сценариях naninovel — голоса будут автоматически воспроизводиться при печати соответствующих строк текста в игре.

Чтобы включить функцию автоматического озвучивания, используйте `Enable Auto Voicing` в меню настроек звука.

Аудиоклипы, используемые для функции автоматического озвучивания, должны быть сгруппированы в папку с именем, равным имени сценария, и иметь следующее имя: *LineNumber*.*CommandIndex*, где *LineNumber* – номер строки соответствующей команды печати, а *CommandIndex* – встроенный или командный индекс команды печати в случаях, когда речь идет об универсальных текстовых строках.

Например, рассмотрим следующий сценарий Naninovel с именем "Script001":

```nani
@print text:"Text from a print command."
Text from a simple generic text line.
Text from first sentence.[i] Text from second sentence.
```

Для того чтобы система автоматического озвучивания воспроизводила соответствующие аудиоклипы при печати этих строк, клипы должны быть помещены в папку `Resources/Naninovel/Voice/Script001` (или зарегистрированы в [адресируемой системе](/ru/guide/resource-providers.md#адресация )) и иметь следующие имена:

Текст | Имя аудиоклипа
--- | ---
Text from a print command. | 1.0
Text from a simple generic text line. | 2.0
Text from first sentence. | 3.0
Text from second sentence. | 3.2

Для упрощения процесса при включенной функции автоматического озвучивания в окне отладки отображается имя голосового клипа для выводимого в данный момент текста:

![auto voicing](https://i.gyazo.com/12772ecc7c14011bcde4a74c81e997b8.png)

Чтобы открыть окно отладки, убедитесь, что в конфигурации движка включена функция `Enable Development Console`, затем нажмите клавишу `~` в режиме воспроизведения, введите `debug` и нажмите `Enter`.

## Документы озвучивания

Вы можете использовать утилиту создания документов озвучивания, доступную через `Naninovel -> Tools -> Voiceover Documents`, для создания документов, содержащих печатный текст из команд [@print] и универсальных текстовых строк. Каждое печатное текстовое сообщение будет связано с именем аудиоклипа, который будет использоваться с функцией автоматического озвучивания.

![](https://i.gyazo.com/69466444d4b8b43d76e7f1566db5ca9a.png)

Свойство `Locale` позволяет выбрать конкретную локаль, для которой будут создаваться документы (локализованные сценарии naninovel для выбранной локали должны существовать в вашем проекте).

Если свойство `Use Markdown Format` включено, то сгенерированные файлы будут иметь формат [Markdown](https://en.wikipedia.org/wiki/Markdown) (расширение.md) с дополнительным форматированием для лучшей читабельности.

![](https://i.gyazo.com/ed6776026a79140de9e9f6a155faffdc.png)

Документы озвучивания предназначены для использования актерами озвучивания при записи озвучивания.