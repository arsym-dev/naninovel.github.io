# Провайдеры ресурсов

Провайдеры ресурсов используются для извлечения ресурсов, связанных с Naninovel: текстовые файлы ".nani" для сценариев, текстуры для спрайтов персонажей, аудиоклипы для музыки и т.д. Каждый провайдер специализируется на извлечении ресурсов из определенного источника: папки проекта "Resources", адресируемая система ассетов Unity, локальное хранилище файлов, аккаунт Google Drive и т.д.

Общее поведение провайдеров может быть настроено через меню `Naninovel -> Configuration -> Resource Provider`.

![](https://i.gyazo.com/466488bf852f0dd54aa680012b072af1.png)

Свойство `Resource Policy` диктует политику загрузки и выгрузки ресурсов во время выполнения скрипта:

- Статическая — все ресурсы, необходимые для выполнения сценария, предварительно загружаются при запуске воспроизведения (маскируются загрузочным экраном) и выгружаются только после завершения воспроизведения сценария. Эта политика используется по умолчанию и рекомендуется для большинства случаев.
- Динамическая — во время выполнения сценария предварительно загружаются только ресурсы, необходимые для выполнения следующих команд количеством, указанным в Dynamic Policy Steps, а все неиспользуемые ресурсы немедленно выгружаются. Используйте этот режим при работе с платформами со строгими ограничениями памяти и невозможностью правильно организовать сценарии Naninovel. При загрузке ресурсов в фоновом режиме во время игры могут наблюдаться сбои.

Когда включена функция `Log Resources Loading`, различные связанные с провайдеров сообщения журнала будут зеркально отображаться в UI загрузочного экрана по умолчанию.

`Enable Build Processing` включает процедуру предварительной обработки сборки, необходимую для введения в сборки ресурсов, назначенных через меню редактора. Отключайте это только в том случае, если вы используете [пользовательскую среду сборки](/ru/guide/custom-build-environment.md) или прикрепляете свои собственные сборочные крючки. Когда [адресируемая система](https://docs.unity3d.com/Packages/com.unity.addressables@latest) установлена, включение `Use Addressables` оптимизирует этап обработки ассетов, ускоряя процесс сборки; включение `Auto Build Bundles` в то же время приведет к автоматической компиляции пакетов ассетов при сборке плеера.

Другие свойства в меню конфигурации зависят от провайдера и описаны ниже.

Поведение провайдеров ресурсов настраивается с помощью свойств `Loader`, доступных в соответствующих меню конфигурации. Например, вот конфигурация загрузчика по умолчанию, используемая для извлечения аудиоресурсов (BGM и SFX):

![](https://i.gyazo.com/e9b59f738c93d0cdee6f0999b797a461.png)

Свойство `Path Prefix` позволяет указать дополнительный путь поверх корневого пути провайдера для конкретного типа ресурсов. Например, если мы собираемся извлечь аудиофайл "Explosion" из папки проекта "Resources", установка префикса пути к "Audio" приведет к следующему запросу ресурсов: `Resources.Load("Audio/Explosion")`.

`Providers List` позволяет указать, какие типы провайдеров следует использовать и в каком порядке. Например, в приведенном выше примере при запросе аудиоресурса сначала будет использоваться "Addressable" провайдер, а в случае, если провайдер не сможет найти запрашиваемый ресурс, будет использоваться "Project" провайдер.

Имейте в виду, что **в то время как в Редакторе всегда в первую очередь используется специальный провайдер ресурсов "Editor"** (независимо от конфигурации загрузчиков). Провайдер имеет доступ ко всем ресурсам, назначенным через меню конфигурации и менеджера ресурсов Naninovel (`Naninovel -> Resources -> ...`). Когда игра собрана, все такие ресурсы автоматически копируются во временную папку "Resources" или (когда [адресируемая система](https://docs.unity3d.com/Packages/com.unity.addressables@latest) установлена и включена) регистрируются в конфигурации адресации и компилируются в пакеты ассетов. Не забывайте **всегда выполнять любые тесты, связанные с провайдерами, в сборках**, а не в редакторе Unity.

## Адресация

[Адресируемая система ассетов](https://docs.unity3d.com/Packages/com.unity.addressables@latest) – это пакет Unity, предоставляющий простой способ загрузки ассетов по "адресу". Он использует асинхронную загрузку для поддержки загрузки из любого места (локальное хранилище, удаленный веб-хостинг и т. д.) с любым набором зависимостей. Обратитесь к документации Unity, чтобы узнать, как настроить, настроить и использовать систему.

Naninovel будет автоматически использовать адресацию, когда пакет будет установлен в проекте. Никаких дополнительных настроек не требуется. Все ассеты, назначенные в меню конфигурации Naninovel (например, сценарии, спрайты персонажей, аудиоклипы и т.д.), будут зарегистрированы в системе (получат "адрес" в группе "Naninovel") при создании плеера.

Адресируемый провайдер используется только в сборках среды выполнения и по умолчанию отключен в Редакторе. Если вы вручную предоставляете ресурсы через адресацию вместо того, чтобы назначать их менеджерам ресурсов Naninovel, вы можете включить его с помощью свойства `Enable Addressable In Editor` в меню конфигурации провайдера ресурсов. Имейте в виду, что включение этого параметра может вызвать проблемы, когда ресурсы назначаются как в диспетчере ресурсов, так и регистрируются при адресации, а затем переименовываются, дублируются или удаляются.

Для того, чтобы адресируемый ассет стал "видимым" для Naninovel, его адрес должен начинаться с "Naninovel/", и ему должна быть присвоена метка "Naninovel". Вы можете указать дополнительные метки для фильтрации ассетов, используемых Naninovel, с помощью свойства `Extra Labels` в меню конфигурации провайдера ресурсов. Имейте в виду, что адресируемая группа "Naninovel" автоматически повторно генерируется при каждой сборке; либо используйте другую группу для указания пользовательских ресурсов, либо отключите свойство `Enable Build Processing` в меню конфигурации провайдера ресурсов и вручную обработайте ассеты при сборке.

Если вы хотите настроить способ обслуживания адресируемых ассетов Naninovel (например, указать удаленный веб-хост), отредактируйте группу "Naninovel" через меню `Window -> Asset Management -> Addressables -> Groups`. Группа создается автоматически при первой сборке игры; если она отсутствует, вы можете создать ее вручную.

![](https://i.gyazo.com/c93fbd9e232ec94468c685c4d6003916.png)

::: warning
Мы не предоставляем никаких туториалов или поддержки для самой адресируемой системы ассетов Unity, будь то настройка удаленного веб-хостинга для ваших ассетов или какое-либо другое развертывание/обслуживание скриптов; обратитесь к [странице поддержки](/ru/support/#unity-support) для получения дополнительной информации.
:::

## Провайдер проекта

Провайдер проекта обслуживает ресурсы, расположенные в папках "Resources" вашего проекта Unity. Обратитесь к руководству Unity для получения дополнительной информации о [API загрузки ресурсов проекта](https://docs.unity3d.com/Manual/LoadingResourcesatRuntime).

Имейте в виду, что в большинстве случаев [использование папок "Resources" не рекомендуется](https://docs.unity3d.com/Manual/BestPracticeUnderstandingPerformanceInUnity6). Предпочтительно вместо этого назначать ресурсы через меню менеджера ресурсов Naninovel, когда это возможно, или использовать адресируемую систему; не забудьте переместить ресурс из папки "Resources" после этого.

## Локальный провайдер

Локальный провайдер позволяет обслуживать простые (сценарии и управляемый текст, спрайты персонажей и фоны, аудио) ассеты из произвольного расположения в локальной файловой системе.

Свойство `Local Path Root` в конфигурации провайдера ресурсов должно указывать на папку, в которой хранятся локальные ресурсы. Вы можете либо использовать абсолютный (например, `C:\Resources`) или относительный путь, начинающийся с одной из следующих форм:

 - `%DATA%` — папка игровых данных на целевом устройстве ([Application.dataPath](https://docs.unity3d.com/ScriptReference/Application-dataPath));
 - `%PDATA%` — постоянный каталог данных на целевом устройстве ([Application.persistentDataPath](https://docs.unity3d.com/ScriptReference/Application-persistentDataPath));
 - `%STREAM%` — папка "StreamingAssets" ([Application.streamingAssetsPath](https://docs.unity3d.com/ScriptReference/Application-streamingAssetsPath));
 - `%SPECIAL{F}%` — специальная папка операционной системы, где `F` - имя [специальной папки](https://docs.microsoft.com/en-us/dotnet/api/system.environment.specialfolder).

Значение `%DATA%/Resources` по умолчанию указывает на папку "Resources" внутри каталога данных игры (которая отличается в зависимости от целевой платформы).

В качестве одного из примеров использования предположим, что вы хотите загрузить сценарии Naninovel из папки `C:/Users/Admin/Dropbox/MyGame/Scripts`, которой вы делитесь с коллегами, чтобы писать сценарий. Хотя можно просто указать абсолютный путь к корневой папке (`C:/Users/Admin/Dropbox/MyGame`), это потребует от всех ваших сотрудников также хранить папку по одному и тому же пути (под одной и той же меткой диска и именем пользователя). Вместо этого используйте следующий относительный путь от специальной папки "UserProfile": `%SPECIAL{UserProfile}%/Dropbox/MyGame`.

![](https://i.gyazo.com/eb435b782cfb9df6c403702e8f6124df.png)

Данный префикс пути в конфигурации сценарии устанавливается в `Scripts`, локальный провайдер добавляется в список, после чего навигатор сценариев (доступный с помощью [консольной команды](/ru/guide/development-console.md) `nav`) теперь может забрать любой текстовый файл .nani", хранящийся в папке.

![](https://i.gyazo.com/df8ad31d30b5c10c9a918e69a4543567.png)

## Google Drive

Реализованы с открытым исходным кодом (лицензия MIT) стороннего пакета [UnityGoogleDrive](https://github.com/Elringus/UnityGoogleDrive) позволяет использовать [Google Drive](https://www.google.com/drive) в качестве провайдера для следующих ресурсов:

* Сценарии Naninovel и управляемые тексты (через Google Documents);
* Персонажи и фоны (только спрайтовая реализация);
* BGM, SFX и голоса.

Вы можете делиться своей папкой ресурсов Google Drive с другими пользователями для совместной работы без необходимости использования систем контроля версий или других сложных инструментов.

Чтобы иметь возможность выбрать Google Drive в качестве провайдера ресурсов, вы должны сначала установить [Unity Google Drive](https://github.com/Elringus/UnityGoogleDrive). Изучите readme проекта GitHub для получения инструкций по установке и настройке.

После установки и настройки пакета Unity Google Drive соответствующие свойства появятся в меню конфигурации провайдера ресурсов.

![](https://i.gyazo.com/57281ae3a47e85690d9141179af768a8.png)

`Google Drive Root Path` – это относительный путь внутри папки вашего Google Drive к каталогу, который следует рассматривать как корневой каталог ресурсов. Например, если вы хотите сохранить сценарии сценариев в разделе  `MyGame/Scripts`, укажите корень как `MyGame`.

С помощью свойства `Google Drive Request Limit` вы можете установить количество максимально допустимых параллельных запросов при обращении в Google Drive API. Это необходимо для предотвращения ошибок связи при использовании личного плана Google Drive, который ограничивает количество разрешенных одновременных запросов.

`Google Drive Cache Policy` диктует поведение кэширования загруженных ресурсов. `Smart` попытается использовать [Changes API](https://developers.google.com/drive/api/v3/reference/changes), чтобы проверить, изменился ли запрошенный (кэшированный) ресурс в удаленной папке перед его загрузкой. `Purge All On Init` будет очищать кэш при инициализации движка и всегда использовать кэшированные версии после первой загрузки. Кэш также может быть очищен вручную в любое время с помощью [консольной команды](/ru/guide/development-console.md) `purge`.

Не забудьте добавить Google Drive в список провайдеров ресурсов, которые вы хотите использовать с ним. Например, следующее заставит менеджер сценариев искать сценарии в Google Drive в дополнение к адресируемым источникам и источникам проекта:

![](https://i.gyazo.com/0ad07f73fe12be7ae6d421c5f4f33384.png)

::: tip EXAMPLE

См. проект [NaninovelSandbox](https://github.com/Naninovel/Sandbox) для примера того, как настроить и использовать провайдер Google Drive. Имейте в виду, что пакет Naninovel не распространяется вместе с проектом, поэтому ошибки компиляции будут возникать после его первого открытия; импортируйте Naninovel из Asset Store, чтобы разрешить эти ошибки.
:::

## Пользовательские провайдеры

Можно добавить пользовательскую реализацию провайдера ресурсов и заставить Naninovel использовать ее со встроенными провайдерами (или вместо них).

Чтобы добавить пользовательский провайдер, создайте класс C# с конструктором без параметров и реализуйте интерфейс `IResourceProvider`. После создания пользовательский тип провайдера появится во всех меню конфигурации загрузчика вместе со встроенными типами.

![](https://i.gyazo.com/7176a9d4a4ea2d9414c5495e2e465baf.png)

Вы можете найти встроенные реализации провайдеров ресурсов в каталоге пакета `Naninovel/Runtime/Common/ResourceProvider`; используйте их в качестве референсов при реализации своих собственных версий.

Ниже приведен пример пользовательского провайдера, который ничего не делает, но регистрирует сообщения при использовании.

```csharp
using Naninovel;
using System;
using System.Collections.Generic;
using UniRx.Async;

public class CustomResourceProvider : IResourceProvider
{
    public event Action<float> OnLoadProgress;
    public event Action<string> OnMessage;

    public bool IsLoading => default;
    public float LoadProgress => default;
    public IEnumerable<Resource> LoadedResources => default;

    public Resource<T> GetLoadedResourceOrNull<T> (string path)
        where T : UnityEngine.Object
    {
        OnMessage?.Invoke($"GetLoadedResourceOrNull: {path}");
        return default;
    }

    public UniTask<Resource<T>> LoadResourceAsync<T> (string path)
        where T : UnityEngine.Object
    {
        OnMessage?.Invoke($"LoadResourceAsync: {path}");
        OnLoadProgress?.Invoke(1f);
        return default;
    }

    public UniTask<IEnumerable<Resource<T>>> LoadResourcesAsync<T> (string path)
        where T : UnityEngine.Object
    {
        OnMessage?.Invoke($"LoadResourcesAsync: {path}");
        OnLoadProgress?.Invoke(1f);
        return default;
    }

    public UniTask<IEnumerable<Folder>> LocateFoldersAsync (string path)
    {
        OnMessage?.Invoke($"LocateFoldersAsync: {path}");
        return default;
    }

    public UniTask<IEnumerable<string>> LocateResourcesAsync<T> (string path)
        where T : UnityEngine.Object
    {
        OnMessage?.Invoke($"LocateResourcesAsync: {path}");
        return default;
    }

    public UniTask<bool> ResourceExistsAsync<T> (string path)
        where T : UnityEngine.Object
    {
        OnMessage?.Invoke($"ResourceExistsAsync: {path}");
        return default;
    }

    public bool ResourceLoaded (string path)
    {
        OnMessage?.Invoke($"ResourceLoaded: {path}");
        return default;
    }

    public bool ResourceLoading (string path)
    {
        OnMessage?.Invoke($"ResourceLoading: {path}");
        return default;
    }

    public bool SupportsType<T> ()
        where T : UnityEngine.Object
    {
        OnMessage?.Invoke($"SupportsType: {typeof(T).Name}");
        return default;
    }

    public void UnloadResource (string path)
    {
        OnMessage?.Invoke($"UnloadResource: {path}");
    }

    public void UnloadResources ()
    {
        OnMessage?.Invoke("UnloadResources");
    }
}
```