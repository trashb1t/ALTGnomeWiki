# NVIDIA

Эта статья описывает процесс установки и настройки драйверов для видеочипов NVIDIA.

На данный момент существует 2 драйвера:
### 1. Проприетарный драйвер от NVIDIA

[NVIDIA](https://www.nvidia.com/), предлагает собственный драйвер для своих продуктов. Этот драйвер являются [закрытым программным обеспечением](https://en.wikipedia.org/wiki/Proprietary_software) и поставляются отдельно от ядра Linux, требуя индивидуальной установки.

Важно отметить, что драйвер NVIDIA постоянно развивается и обновляется, и различные версии этого драйвера могут иметь свои особенности и ограничения. [Поэтому критически важно знать версию установленного драйвера и информацию, связанную с ними.](nvidia.md#настроика-драиверов)

При установке основного пакета [nvidia_glx_common](nvidia.md#способ-2-вручную), необходимая версия драйверов устанавливается автоматически, и не требуется никаких дополнительных действий со стороны пользователя. Также существует скрипт установки через [EPM](nvidia.md#способ-1-через-epm), предлагающий собственные дополнительные настройки.

### 2. Драйвер Nouveau

Nouveau - это [open-source](https://en.wikipedia.org/wiki/Open-source_software) проект, созданный независимыми разработчиками с использованием методов реверс-инжиниринга. В настоящее время он разрабатывается под эгидой [X.Org Foundation](https://x.org/) и размещается на [freedesktop.org](https://nouveau.freedesktop.org/). Nouveau является частью проекта [MESA 3D](https://www.mesa3d.org/), и систем GNU/Linux.

Особенности разработки драйверов Nouveau приводят к тому, что уровень поддержки различных архитектур GPU может варьироваться. [Поэтому важно знать уровень поддержки для конкретной архитектуры вашей видеокарты](https://nouveau.freedesktop.org/FeatureMatrix.html).

Драйверы Nouveau изначально присутствуют в системе, и их установка не требуется. Это делает их удобными для пользователей, которые не хотят или не могут установить дополнительное программное обеспечение.

::: info
У Nouveau есть страница с навзаниями видеокарт, их кодовыми наименованиями и их архитектурой.

См. страницу [Code Names](https://nouveau.freedesktop.org/CodeNames.html)
:::

## Выбор правильного драйвера:

При выборе драйвера для графического чипа, следует учесть три основных аспекта:
1.	**Развитие драйверов:** Как Nouveau, так и NVIDIA постоянно обновляются и развиваются. Невозможно точно предсказать, какие функции будут добавлены или изменены в каждом из них в будущем.
2.	**Архитектура графического чипа:** Важно учитывать, что старые модели графических чипов могут лучше работать с драйвером Nouveau, чем с последними поддерживаемым драйвером NVIDIA. Всё индивидуально и необходимо проверять для каждой архитектуры видеочипов отдельно. Для проверки уровня поддержки различных архитектур можно обратиться к [FeatureMatrix](https://nouveau.freedesktop.org/FeatureMatrix.html) для Nouveau и к этой странице в случае с NVIDIA (См. блоки «Для драйверов XXX»).
3.	**Происхождение драйверов:** Проприетарные драйверы NVIDIA разрабатываются самой компанией NVIDIA, в то время как Nouveau является частью проекта Mesa 3D и разрабатывается с помощью реверс-инжиниринга. Это означает, что проприетарные драйверы обычно обеспечивают более высокий уровень поддержки видеокарт NVIDIA, в то время как Nouveau обеспечивает более глубокую интеграцию с системами GNU/Linux.

### На данный момент, драйвера имеют следующие основные различия:
Проприетарный драйвер NVIDIA обеспечивает управление питанием большего числа видеокарт, поддерживают работу CUDA, а также обеспечивают лучшую поддержку игр и графических программ. Это способствует более комфортной работе с операционной системой.

С другой стороны, Nouveau встроен в ядро Linux и обычно не требует дополнительных настроек. Он также поддерживает управление питанием, но на меньшем количестве видеокарт, не поддерживает CUDA и имеют плохую поддержку игр.

::: info
Дополнительную информацию о поддержке управления питанием с использованием Nouveau можно найти здесь: [PowerManagement](https://nouveau.freedesktop.org/FeatureMatrix.html)
:::

### Рекомендации по выбору:
Если вам необходимы игры, CUDA и другие технологии NVIDIA, и при использовании Nouveau возникают проблемы с производительностью, рекомендуется установить драйверы NVIDIA.

Если вы не используете вышеупомянутые технологии, и ваша видеокарта работает стабильно на Nouveau, следует следует остаться на нём.

## Проприетарный драйвер NVIDIA:

## Установка (Смена Nouveau драйверов на проприетарные)

### Способ 1. Через EPM

Для упрощения процесса установки проприетарного драйвера графических карт NVIDIA возможно использование [интерфейса EPM](/epm).  Данный скрипт включает в себя **наиболее полную первоначальную настройку**, включая [активацию wayland](./nvidia.md#сессия-wayland) и решения проблемы с [неизвестным монитором](./nvidia.md#«неизвестныи-монитор»-в-настроиках-дисплеев-в-сессии-wayland).

**Если необходима наиболее простая установка, используйте этот способ.**

Выполним команду:

```shell
su -
epm play switch-to-nvidia
```

::: tip
Для корректной установки во время выполнения скрипта следует читать сообщения из консоли и следовать указанным там шагам. Возможно, потребуется несколько перезагрузок. Например, для автоматического обновления ядра.
:::

После успешного завершения работы скрипта перезагружаем операционную систему.

### Способ 2. Вручную

Для перехода с Nouveau на NVIDIA рекомендуется сначала обновить ядро:

```shell
su -
update-kernel
```
Для дальнейшей корректной установки проприетарных драйверов NVIDIA, **необходимо перезагрузить операционную систему**.

Установим проприетарный драйвер NVIDIA:

```shell
su -
rpm -e $(rpm -qf `modinfo -F filename nouveau`)
apt-get install nvidia_glx_common
nvidia-install-driver
make-initrd
```

::: danger
Не следует запускать фирменный инсталлятор драйвера NVIDIA из .run-файла!
:::

## Настройка драйверов

::: info
Настройки описаны, в первую очередь, для актуальных драйверов.

Перед началом настройки, сначала узнайте версию драйверов и ознакомьтесь с информацией, связанной с ними (См. блоки «Для драйверов XXX», где XXX это версия установленных у вас драйверов).
:::

Чтобы узнать версию драйверов, необходимо ввести:
```shell
inxi -G
```

## Настройка и информация для драйверов (550.54.14 и новее)

Активируем **Wayland** сессию в **ALT Regular Gnome** для видеокарт NVIDIA с установленными проприетарными драйверами.

::: info
При установке драйверов через [EPM](./nvidia.md#способ-1-через-epm) настройка под wayland уже будет произведена. Если Вы установили таким способом, дальнейшая настройка не требуется.
:::

::: warning
У драйверов NVIDIA существуют [известные проблемы и несовместимости](./nvidia.md#проблемы-c-wayland-протоколом), связанные с wayland. Вы можете ознакомится с ними для лучшего понимания дел на данный момент.
:::

### Для работы wayland необходим [Kernel Mode Setting](https://www.kernel.org/doc/html/latest/gpu/drm-kms.html).
[Начиная с октября 2023 года](https://git.altlinux.org/tasks/archive/done/_324/332535/gears/100/git?p=git;a=commit;h=3bb30586e8c78e167a59f680370bad04fe50fadc), в ручной активации KMS нет необходимости. Правильная опция будет прописана вместе с установкой драйверов и будет находиться в /etc/modprobe.d/nvidia_common.conf.

::: tip
Чтобы проверить, работает ли KMS, напишите в консоли от имени root (Администратора):

'cat /sys/module/nvidia_drm/parameters/modeset'

Если консоль выводит «Y» значит работает.

Если не работает( показывает «N»), можете попробовать включить его через [параметры ядра](./nvidia.md#не-работает-kms)
:::

### Для корректно работы сна или гибернизации в режиме wayland, необходимо правильное сохранение выделенных ресурсов видеопамяти.
Существует 2 способа как это сделать.[(см. источник)](https://download.nvidia.com/XFree86/Linux-x86_64/550.40.07/README/powermanagement.html):

### 1 способ. Через сохранение выделенных ресурсов в безымянных временных файлах.

Такой способ позволяет использовать глубокий уровень сна (S4 и S5), и не требует дополнительной поддержки S0ix.
::: warning
Существуют проблемы на некоторых устройствах с PRIME. Этот способ больше направлен на **настольные ПК**
:::

**Как включить:**

 Активируем интерфейсы управления питания NVIDIA:

```shell
su -
systemctl enable nvidia-suspend.service nvidia-resume.service nvidia-hibernate.service
```
::: tip
Эти интерфейсы заменяют стандартные sleep hibernate и resume, давая возможность записать ресурсы видеопамяти перед отключением питания у видеокарты.
:::

В опциях драйверов NVIDIA указываем о смене способа сохранения ресурсов видеопамяти и указываем куда:

```shell
su -
cat << _EOF_ > /etc/modprobe.d/nvidia_videomemory_allocation.conf
options nvidia NVreg_PreserveVideoMemoryAllocations=1
options nvidia NVreg_TemporaryFilePath=/var/tmp
_EOF_
make-initrd
```

::: info
Для сохранения ресурсов видеопамяти важно, чтобы файловая система имела поддержку безымянных временных файлов и имела достаточный объём для сохранения видеопамяти. Объём, равный сумме всей видеопамяти + 5% от неё, будет вполне достаточно для её сохранения.

Узнать количество видеопамяти можно командой: `nvidia-smi -q -d MEMORY |grep 'FB Memory Usage' -A1`

Также, во избежании нехватки места, следует избегать директорий с tmpfs в опции `NVreg_TemporaryFilePath`. (Таких как /tmp или /run)
:::

### 2 способ. Чере через режим сохранения питания [S0ix](https://ru.msi.com/blog/why-modern-standby-matters-for-business-laptop#:~:text=%D0%A4%D1%83%D0%BD%D0%BA%D1%86%D0%B8%D1%8F%20Modern%20Standby%20%E2%80%93%20%D1%8D%D1%82%D0%BE,%D0%BE%D0%BD%D0%B0%20%D0%BF%D0%BE%D0%B7%D0%B2%D0%BE%D0%BB%D1%8F%D0%B5%D1%82%20%D1%83%D1%81%D1%82%D1%80%D0%BE%D0%B9%D1%81%D1%82%D0%B2%D1%83%20%C2%AB%D0%BF%D1%80%D0%BE%D0%B1%D1%83%D0%B6%D0%B4%D0%B0%D1%82%D1%8C%D1%81%D1%8F%C2%BB%20%D0%BC%D0%BE%D0%BC%D0%B5%D0%BD%D1%82%D0%B0%D0%BB%D1%8C%D0%BD%D0%BE).

Этот способ позволяет не выгружать видеопамять. Сам по себе режим S0ix, а следовательно и данный способ направлены **на переносные устройства**.

**Как включить:**

Проверяем, поддерживает ли система s2idle. Для этого выполните команду:

```shell
cat /sys/power/mem_sleep
```
::: tip
Если в выводе есть s2idle, значит s2idle поддерживается и проверяем видеочип. Если в выводе нет s2idle, тогда используйте 1 способ.
:::

Проверяем, поддерживает ли s0ix видеочип. Для этого выполните команду, перед этим подставив расположение вашего устройства(Посмотрите по каталогам после `/proc/driver/nvidia/gpus`. У каждого устройства своё значение).
:

```shell
grep 'Video Memory Self Refresh' /proc/driver/nvidia/gpus/<Domain>:<Bus>:<Device>.0/power
```
::: tip
Если ответ `Video Memory Self Refresh: is supported` значит s0ix поддерживает и можно проверять систему дальше.
:::

Если система и видеочип поддерживают указанные режимы, добавляем параметр  `NVreg_EnableS0ixPowerManagement=1`:

```shell
su -
modprobe nvidia NVreg_EnableS0ixPowerManagement=1
```

Если при выводе `cat /sys/power/mem_sleep` **s2idle** не был в скобках (Вот так: **\[s2idle\]**), выполняем в консоли:

```shell
su -
echo "s2idle" > /sys/power/mem_sleep
```
После указания опции и режима , тестируем сон и проверяем, всё ли корректно загрузилось. Если всё хорошо, добавляем настройки на постоянную загрузку:

```shell
su -
cat << _EOF_ > /etc/modprobe.d/enable_S0ix_power_management.conf
options nvidia NVreg_EnableS0ixPowerManagement=1
_EOF_
make-initrd
```
Если ранее приходилось вручную указывать s2idle, прописываем в параметр `GRUB_CMDLINE_LINUX_DEFAULT` значение `mem_sleep_default=s2idle` и генерируем новых grub.cfg:
```shell
su -
mcedit /etc/sysconfig/grub2
grub-mkconfig -o /boot/grub/grub.cfg
```

::: info
При переходе видеокарты в спящий режим S0ix, будет выполнятся один из 2-х сценариев:
- Если использование памяти было меньше определённого порога, видеопамять скопируется в системную память и полностью отключится вместе с графическим процессором
- Если использование памяти было больше определённого порога, видеопамять будет в режиме самообновления, в то время как остальная часть графического процессора будет отключена

По умолчанию, этот порог составляет 256 МБ, и его можно изменить с помощью параметра `NVreg_S0ixPowerManagementVideoMemoryThreshold`. Имейте ввиду, чем больше указан размер, тем выше вероятность, что часть видеопамяти не будет сохранена.
:::

---

**Перезагружаем операционную систему, в интерфейсе выбора сессий появится дополнительные пункты для входа в Xorg, выберите в списке сессию GNOME.**

## Настройка и информация для драйверов < 470.223.02

### Проблема с Intel:
Драйвер 470.223.02 и более ранние версии, начиная с Linux 5.18 могут работать неправильно в системах с процессорами Intel 11-го поколения и новее, из-за несовместимости с [Indirect Branch Tracking](https://edc.intel.com/content/www/us/en/design/ipla/software-development-platforms/client/platforms/alder-lake-desktop/12th-generation-intel-core-processors-datasheet-volume-1-of-2/007/indirect-branch-tracking/).
Можно отключить его, добавив значение `ibt=off` в параметр `GRUB_CMDLINE_LINUX_DEFAULT`:

```shell
su -
mcedit /etc/sysconfig/grub2
grub-mkconfig -o /boot/grub/grub.cfg
```

::: warning
Имейте в виду, что эта функция безопасности отвечает за [защиту от ряда методов эксплойта](https://lwn.net/Articles/889475/) .
:::

### Сессия Wayland

Kernel Mode Setting необходимо указать вручную в параметрах ядра. Для этого необходимо прописать в параметр `GRUB_CMDLINE_LINUX_DEFAULT` значение `nvidia-drm.modeset=1` и cгенерировать новых grub.cfg:

```shell
su -
mcedit /etc/sysconfig/grub2
grub-mkconfig -o /boot/grub/grub.cfg
```

::: info
Драйвера до версии 495 имеют поддержку только [EGLStreams](https://www.phoronix.com/news/GNOME-Mutter-Mainline-EGLStream).

Начиная с 495 NVIDIA представила поддержку [GBM](https://en.wikipedia.org/wiki/Generic_Buffer_Management).
GBM считается более лучшим и более широко поддерживаемым buffer API, в отличии от EGLStreams, которую продвигала только одна NVIDIA.
GNOME, один из немногих, кто ещё поддерживает EGLStreams, но на сегодняшний день эта поддержка постепенно уходит на второй план.
:::

В остальном, информация актуальна как для [драйверов < 550.54.14](./nvidia.md#настроика-и-информация-для-драиверов-550-54-14-и-новее)

## Настройка и информация для драйверов < 390.157

### Сессия Wayland
::: info
Драйверы NVIDIA до версии 470 не поддерживают аппаратное ускорение Xwayland, из-за чего приложения, не относящиеся к Wayland, страдают от низкой производительности в сеансах Wayland.

Для драйверов до версии  ~400 NVDEC недоступен.
:::


В остальном, информация актуальна как для [драйверов < 470.223.02](./nvidia.md#настроика-и-информация-для-драиверов-470-223-02)

## Настройка и информация для драйверов < 340.108
::: info
Драйверы до версии 364.12 **не имеют поддержку Kernel Mode Settings**.

**Wayland** сессия с таким драйвером **недоступна**.

Для драйверов до версии ~400 ускоренное декодирование видео NVDEC **недоступен**.
:::

В остальном, информация актуальна как для [драйверов < 390.157](./nvidia.md#настроика-и-информация-для-драиверов-390-157)

## Настройка и информация для драйверов < 304.137

::: info
Для драйверов до версии 340.108 ускоренное декодирование видео VDPAU **недоступен**.
:::

В остальном, информация актуальна как для [драйверов < 340.108](./nvidia.md#настроика-и-информация-для-драиверов-340-108)

## Полезные программы:
::: info
При установке драйверов через **EPM**, многие программы, скорее всего уже будут установлены.

Группа устанавливаемых пакетов, может постоянно менятся, поэтому для уточнения рекомендованно проверить сам [EPM скрипт](https://gitlab.eterfund.ru/etersoft/eepm/blob/master/prescription.d/switch-to-nvidia.sh)
:::

### NVIDIA Settings

Утилита для настройки и оптимизации графических параметров на компьютерах с видеокартами Nvidia. Она позволяет пользователям настраивать качество изображения, разрешение экрана, а также различные параметры, связанные с производительностью видеокарты. С помощью Nvidia Settings можно также включить или отключить определенные функции, такие как вертикальная синхронизация или сглаживание.

В зависимости от сессии Xorg или Wayland возможны различаться набор настроек в утилите NVIDIA Settings. Например, в Wayland нельзя настроить вертикальную синхронизацию и тройную буферизацию, так как они работают по-другому в этом окружении. Также в Wayland нет поддержки G-Sync, так как эта технология работает только с NVIDIA и требует специального оборудования. В целом, основные функции NVIDIA Settings доступны в обоих окружениях, но некоторые дополнительные возможности могут быть ограничены.

#### Установка из репозитория

**NVIDIA Settings** можно установить любым привычным и удобным способом:

**Установка через терминал**
::: code-group

```shell[apt-get]
su -
apt-get update
apt-get install nvidia-settings
```
```shell[epm]
epm -i nvidia-settings
```
:::

### switcheroo-control

Утилита для выбора графического устройства при запуске приложения с типом устройств dual-GPU

Для пользовательского выбора графического устройства при запуске приложения в рабочем окруженнии GNOME, необходимо установить утилиту `switcheroo-control`

:::info
Для систем, имеющих как встроенный, так и выделенный графический процессор, switcheroo-control по умолчанию принудительно использует встроенный графический процессор для экономии энергии.
:::

**Установка через терминал**
::: code-group

```shell[apt-get]
su -
apt-get update
apt-get install switcheroo-control
systemctl enable --now switcheroo-control.service
```
```shell[epm]
epm -i switcheroo-control
systemctl enable --now switcheroo-control.service
```
:::

После установки приложения switcheroo-control, в меню появится пункт «Запустить с помощью выделенной видеокарты»

### EnvyControl

EnvyControl это утилита командной строки которая позволяет выбирать режим работы гибридной графики, на выбор представлено три режима

#### Гибридный режим

 - Позволяет драйверу самому решать какой графический процессор использовать, обычно большую часть времени используется интегрированая графика, а дискретная графика работает только если она нужна, например в играх или программах для монтажа
 - Позволяет использовать RTD3, [подробнее](/nvidia#управление-питанием-pci-express-runtime-d3-rtd3)
 - Работает только на видиокартах Turing и выше
 - Возможны проблемы с HDMI

 #### Интегрированный режим

 - Использует только интегрированную графику (Intel или AMD) отключая Nvidia
 - Мониторы подключенные к Nvidia работать не будут

#### Nvidia режим

 - Используется исключительно видеокарта от Nvidia

#### Пара примеров

Установить интегрированный режим:

```shell
su-
envycontrol -s integrated
```

Установить гибридный режим и включить RTD3 (если ничего не указывать то значение будет равно двум):

```shell
su-
envycontrol -s hybrid --rtd3
```

Установить гибридный режим, включить ForceCompositionPipeline, а так же установить coolbits на 28 разрешая разгон видеокарты:
```shell
su-
envycontrol -s nvidia --force-comp --coolbits 24
```

**Установка через терминал**
::: code-group

```shell[apt-get]
su -
apt-get update
apt-get install envycontrol
```
```shell[epm]
epm -i envycontrol
```
:::

После установки рекомедуем ввести комманду ```envycontrol --help``` что бы ознокомится со всеми возможностями утилиты, если вы желаеете использовать графическую утилиту, доступно так же [расширение для GNOME](https://extensions.gnome.org/extension/5009/gpu-profile-selector/)

### vulkan-tools

Программа vulkaninfo отображает информацию о поддерживаемых возможностях Vulkan для пользователей графических устройст NVIDIA. Необходимо установить пакет `vulkan-tools`:

::: code-group

```shell[apt-get]
su -
apt-get update
apt-get install vulkan-tools
```
```shell[epm]
epm -i vulkan-tools
```
:::

Для вывода информации `vulkaninfo` в терминале введите:

```shell
vulkaninfo --summary
```

### nvidia-vaapi-driver
Реализация VA-API, использующая NVDEC в качестве бэкенда. Эта реализация специально разработана для использования в Firefox для ускоренного декодирования веб-содержимого и может работать некорректно в других приложениях. Доп. информацию смотрите на [странице проекта](https://github.com/elFarto/nvidia-vaapi-driver)

```shell[apt-get]
su -
apt-get update
apt-get install nvidia-vaapi-driver
```

### nvidia-modprobe
Утилита nvidia-modprobe используется компонентами драйверов NVIDIA в пользовательском пространстве
для проверки загрузки модуля ядра NVIDIA и наличия
файлов символьных устройств NVIDIA. Эти возможности обычно предоставляются
системами конфигурации дистрибутива GNU/Linux, такими как udev. По возможности
рекомендуется использовать встроенные механизмы вашего дистрибутива GNU/Linux
для управления загрузкой модулей ядра и созданием файлов устройств. Эта утилита
предоставляется в качестве запасного варианта для работы независимо от дистрибутива.

```shell[apt-get]
su -
apt-get update
apt-get install nvidia-modprobe
```

### nvidia-cuda-toolkit
Этот пакет содержит библиотеки и сопутствующие файлы, необходимые для запуска
программ, использующих [CUDA](https://developer.nvidia.com/cuda-toolkit).

```shell[apt-get]
su -
apt-get update
apt-get install nvidia-cuda-toolkit
```

### nvidia-xconfig
nvidia-xconfig - это инструмент, предназначенный для обеспечения базового управления по параметрам конфигурации, доступным в драйвере NVIDIA X11.

```shell[apt-get]
su -
apt-get update
apt-get install nvidia-xconfig
```

## Выбор проприетарного драйвера в ЦУС (Устаревший способ выбора)

::: warning
Данная инструкция рекомендована для использования только на стационарных ПК, в которых графическая карта NVIDIA будет использована как основная. Для ноутбуков с гибридной графикой рекомендуется пропустить этот шаг и воспользоваться [утилитой switcheroo-control](/nvidia#выбор-графического-устроиства-при-запуске-приложения-с-типом-устроиств-dual-gpu), или же [envycontrol](/nvidia#переключение-режимов-гибриднои-графики-с-помощью-envycontrol)
:::

::: warning
Выбор драйвера через ЦУС происходит через изменение /etc/X11/xorg.conf.d/10-monitor.conf, а значит работает только на **Xorg** сессии и только на поздних этапах, игнорируя **KMS**. На данный момент, такой способ изменения драйверов не будет работать с **wayland**, и является устаревшим и [**не рекомендуется**](https://www.altlinux.org/index.php?title=Nvidia&diff=75067&oldid=73752)
:::

::: info
Удостоверьтесь, что у вас стоит пакет 'alterator-x11', а также сами драйвера от nvidia. Если его нет, установите.

Без этого не будет необходимого параметра «Дисплей» и варианта выбора «nvidia - NVIDIA (proprietary)»
:::

После перезагрузки устройства следует зайти в Центр управления системой.

![nvidia-acc-1](/nvidia/nvidia-acc-1.png)

Выбираем параметр **Дисплей** в категории **Графический интерфейс**.

![nvidia-acc-2](/nvidia/nvidia-acc-2.png)

В графе **Драйвер** нажимаем на кнопку **Другой драйвер**.

![nvidia-acc-3](/nvidia/nvidia-acc-3.png)

В открывшемся окне выбираем **nvidia - NVIDIA (proprietary)**.

После выбора соглашаемся, нажимаем **ОК** и **Применить** и снова перезагружаем операционную систему.

## Дополнительные настройки

### Управление питанием PCI-Express Runtime D3 (RTD3)
Драйвер NVIDIA имеют поддержку динамического управления питанием графического процессора NVIDIA ([PCI-Express Runtime D3 (RTD3) Power Management]().

В это управление входит регулирование тактовой частоты, напряжение на разных участках микросхемы, а также, в некоторых случаях, полное отключение тактовой частоты или питания элементов чипа.

И всё это не влияя на функциональность, позволяя работать графическому процессору с меньшей производительностью, но с более низким потреблением энергии.

Для работы RTD3 необходимо следующее:
- Ноутбук
- Процессор из cерия чипсетов Coffeelake или новее
- Видеокарта архитектуры Turing или новее
- Linux ядро версии 4.18 и новее
- Ядро Linux собрано с CONFIG_PM (CONFIG_PM=y). Как правило, если система поддерживает S3 (suspend-to-RAM), то и CONFIG_PM будет определён как надо

::: info
Для Ampere или более поздних версий видеокарт, **RTD3 включено по умолчанию. Настройка не нужна.**

Для видеокарт Turing настройка должна быть включена вручную:
:::

Для автоматизации управления надо добавить правила в '/lib/udev/rules.d':
```shell
su -
cat << _EOF_ > /lib/udev/rules.d/80-nvidia-pm
# Enable runtime PM for NVIDIA VGA/3D controller devices on driver bind
ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="auto"
ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="auto"

# Disable runtime PM for NVIDIA VGA/3D controller devices on driver unbind
ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="on"
ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="on"
_EOF_
make-initrd
```

В /etc/modprobe.d добавляем конфигурационный файл с параметром:
```shell
su -
cat << _EOF_ > /etc/modprobe.d/nvidia_RTD3.conf
# This options activate RTD3
options nvidia "NVreg_DynamicPowerManagement=0x02"
_EOF_
make-initrd
```

::: info
Более подробное описание работы, а также решение возможных проблем смотрите в [документации NVIDIA](https://download.nvidia.com/XFree86/Linux-x86_64/550.40.07/README/dynamicpowermanagement.html).
:::

### PAT

Драйвер NVIDIA позволяет сменить старый атрибут страничной организации памяти [MTRR на PAT](https://wiki.gentoo.org/wiki/MTRR_and_PAT).

[PAT](https://docs.kernel.org/arch/x86/pat.html) более современная технология и является более гибким атрибутом, добавляя новые возможности для организации памяти.

Чтобы её включить, в /etc/modprobe.d добавляем конфигурационный файл с параметром:
```shell
su -
cat << _EOF_ > /etc/modprobe.d/nvidia_PAT.conf
# This options activate PAT
options nvidia NVreg_UsePageAttributeTable=1
_EOF_
make-initrd
```
::: warning
Удостоверьтесть, что ваша система поддерживает PAT, а в противном случае, у вас могут быть проблемы с системой.
:::

Проверка поддержки PAT:

```shell
su -
cat /proc/cpuinfo | grep pat
```

### GSP прошивка

Некоторые видеокарты имеют GSP процессор, который может использоваться для разгрузки задач и управления графическим процессором. По умолчанию, он включён для ограниченного числа видеокарт.
Тем не менее, начиная с архитектуры Turing, этот процессор присутствует во всех видеокартах и его можно принудительно включить(Подробнее см. [GSP firmware](https://download.nvidia.com/XFree86/Linux-x86_64/550.40.07/README/gsp.html))

В /etc/modprobe.d добавляем конфигурационный файл с параметром:

```shell
su -
cat << _EOF_ > /etc/modprobe.d/nvidia_GSP_firmware.conf
# This options force unlock GPU firmware
options nvidia NVreg_EnableGpuFirmware=1
_EOF_
make-initrd
```

### Фреймбуфер от NVIDIA

Начиная с драйверов версии 545.29, можно включить фреймбуффер предусмотренный nvidia-drm. Он заменяет стандартные фреймбуферы, такие как efifb или vesafb.
Для работы, необходимо, чтобы работал [KMS](./nvidia.md#kms)

```shell
su -
cat << _EOF_ > /etc/modprobe.d/nvidia-framebuffer.conf
# This options unlock experimental nvidia framebuffer
options nvidia_drm fbdev=1
_EOF_
```

::: danger
Это экспериментальная функция и работает нестабильно.
:::

## Решение известных проблем

### «Неизвестный монитор» в настройках дисплеев в сессии Wayland
:::info
Данная проблема может привести к некорректной работе устройств ввода: мышка, тачпад. Особенно часто данные проблемы проявляются в играх.
:::
**На данный момент существует 2 способа устранения фантомного неизвестного монитора**
1. Использование собственного фреймбуфера от NVIDIA:
   Необходимо добавить опцию [fbdev=1 в /etc/modprobe.d/](./nvidia.md#фреимбуфер-от-nvidia)

2. Отключение загрузки simpledrm:
Внесем изменение в конфигурацию **GRUB**:

```shell
su -
mcedit /etc/sysconfig/grub2
```

Добавляем в параметр `GRUB_CMDLINE_LINUX_DEFAULT` значение `initcall_blacklist=simpledrm_platform_driver_init` и генерируем grub.cfg.

```shell
su -
grub-mkconfig -o /boot/grub/grub.cfg
```

:::warning
Данное решение приводит к невозможности входа в tty, к отсутствию вывода логов во время загрузки (Если не включён Plymouth с заставкой, закрывающий эту загрузку), и к чёрному экрану, если система, по какой-то причине не запустилась вовсе.

Если у вас после загрузки чёрный экран, либо необходимо проинспектировать запуск, **уберите это значение из параметров ядра на этапе загрузки GRUB**. Также, для решения проблем, Вам, возможно, будет полезно убрать параметр **quiet** для вывода более подробных логов, и **splash** для отключения заставки во время вывода логов.
:::

### После настроек для [сессии wayland](./nvidia.md#сессия-wayland), wayland в GDM не появляется

Если, после настройки, по каким-то причинам сессия с wayland протоколом не появляется(нет явных пунктов с X11), его можно принудительно включить в GDM через маскирование [правил](https://git.altlinux.org/srpms/g/gdm.git?p=gdm.git;a=blob;f=gdm/data/61-gdm.rules.in;h=a4f841bdfe02138f2a4ef00979a8700caeeadcac;hb=45.0.1-alt3.1#l51):

```
su -
ln -s /dev/null /etc/udev/rules.d/61-gdm.rules
```

### Не работает KMS
Если при загрузке GNOME чёрный экран, либо при проверке 'cat /sys/module/nvidia_drm/parameters/modeset' выводит 'N', можно попробовать прописать в параметр `GRUB_CMDLINE_LINUX_DEFAULT` значение `nvidia-drm.modeset=1` и cгенерировать новых grub.cfg:

```shell
su -
mcedit /etc/sysconfig/grub2
grub-mkconfig -o /boot/grub/grub.cfg
```
### Если при установке или первом запуске чёрный экран, артефакты или другие проблемы
Если при первом же включении или установке появляются проблемы с отображением картинки, можно попробовать сменить режим вывода графики.

Для этого необходимо на этапе выбора вариантов загрузки в [GRUB](https://en.wikipedia.org/wiki/GNU_GRUB) нажать кнопку <<**e**>> и дописать одни из следующих параметров:

- **nomodeset** — не загружать видеодрайверы для видеокарты до момента, пока не будет загружена графическая система X. В результате система загружается в текстовом виде, можно видеть сообщения процесса загрузки, проблема «черного экрана загрузки» устраняется;
- **xdriver** — графический установщик предпринимает попытку автоматического подбора драйвера видеокарты, но иногда это ему не удаётся. Данным параметром можно отключить «искусственный интеллект» и явно указать нужный вариант драйвера;
- **instdebug** — если будет присутствовать этот параметр, то перед запуском и после завершения работы графического установщика будет запущена командная оболочка (shell). Это очень полезное средство для выявления причин отсутствия запуска графической части программы установки. Последовательность работы внутренних сценариев следующая: install2 → xinit → alterator-install2 → alterator-wizard. При необходимости можно вручную загрузить Xorg (команда xinit) и в открывшемся окне терминала запустить alterator-install2 (или alterator-wizard) вручную. Лог-файлы инсталлятора сохраняются в /tmp.
xdriver — отключает автоопределение и принудительно загружает указанный драйвер; можно указать любой драйвер, поддерживаемый xorg. Например, i915, nvidia, radeon, fglrx и т.д.

Существуют универсальные видеодрайверы:

- **vesa** — минимальный драйвер, работает в общем так же, как и любой другой, но ожидает не конкретной видеокарты, а пытается следовать стандартам VESA (многие видеокарты его нормально поддерживают). Этот драйвер умеет только выводить изображение без ускорения и прочих «излишеств», достаточно медленно работает.
- **fbdev** — ещё более «тупой» драйвер. Он передаёт команду связаться с ядром и пытается рисовать картинку через него. Под этим драйвером может оказаться со стороны ядра несколько разных драйверов, но чаще всего это опять же VESA, но особая, ядерная версия. Работает часто это ещё медленнее, но практически всегда. И современная тенденция такова, что пытаются увеличить возможности и быстродействие ядерного модуля.

::: info
Всё описание взято отсюда: [ALT Linux Wiki: Первая помощь - Проблемы с драйвером видеокарты](https://www.altlinux.org/Первая_помощь#Проблемы_с_драйвером_видеокарты)
:::

### Прочие решения известных проблемы

Также, другие проблемы и возможные их решения, вы можете найти в [главе 8 документации NVIDIA](https://download.nvidia.com/XFree86/Linux-x86_64/550.40.07/README/commonproblems.html)


## Известные нерешённые проблемы
### Системы, где есть Nvidia со второй гибридной графикой нет доступа к Wayland сессии:
На сегодняшний день есть проблемы с запуском Wayland, на системах с гибридной графикой, поэтому [правила](https://git.altlinux.org/srpms/g/gdm.git?p=gdm.git;a=blob;f=gdm/data/61-gdm.rules.in;h=a4f841bdfe02138f2a4ef00979a8700caeeadcac;hb=45.0.1-alt3.1#l85) в GDM запрещает работу Wayland в таких случаях.

 Про проблему написано [тут](https://gitlab.gnome.org/GNOME/mutter/-/issues/1572#note_1308007) и [тут](https://gitlab.gnome.org/GNOME/mutter/-/merge_requests/3304#note_1983279).

::: warning
Вы можете отключить правила GDM, [замаскировав их](nvidia.md#после-настроек-для-сессии-wayland-wayland-в-gdm-не-появляется), но тогда можете столкнуться с проблемами!
:::

### Прочие известные нерещённые проблемы
В [главе 9 документации NVIDIA](https://download.nvidia.com/XFree86/Linux-x86_64/550.40.07/README/knownissues.html) можно ознакомиться с известными нерешёнными проблемами у драйверов NVIDIA

### Проблемы c Wayland протоколом

У NVIDIA c wayland существуют нерешённые проблемы. Вы можете ознакомится с ними в [приложении L документации NVIDIA](https://download.nvidia.com/XFree86/Linux-x86_64/550.40.07/README/wayland-issues.html)

## Данные об оборудовании и ПО пользователей за 2024 год.

Ознакомиться с таблецей вы можете перейдя [по ссылке](https://cloud.alt-gnome.ru/index.php/s/JSCj9gxB7j5boPg)

## Nouveau драйвер:
## Смена проприетарных драйверов от NVIDIA на Nouveau

Для установки открытых драйверов NOUVEAU, необходимо следующее:

::: code-group
```shell[apt-get]
su -
apt-get remove nvidia_glx_common
# удаляем initcall_blacklist и nvidia-drm.modeset если они есть
mcedit /etc/sysconfig/grub2 # тут может быть и nano, и vi, и другой редактор
# сохраняем и выходим
grub-mkconfig -o /boot/grub/grub.cfg
# удаляем так-же ссылку на 61-gdm.rules, если она есть
rm /etc/udev/rules.d/61-gdm.rules
# Удаляем правила запрещающие nouveau, если они есть
rm /etc/modprobe.d/blacklist-nvidia-x11.conf
# Удаляем изменение размещения видеопамяти, если оно есть
rm /etc/modprobe.d/nvidia_videomemory_allocation.conf
# ставим драйвер (в нашем случае для ядра un-def)
apt-get install kernel-modules-drm-nouveau-un-def
# (опционально) переустановить xorg обвязку
apt-get install --reinstall xorg-dri-nouveau xorg-drv-nouveau
# перезагрузиться
reboot
```

```shell[epm]
su -
apt-get remove nvidia_glx_common
# удаляем initcall_blacklist и nvidia-drm.modeset если они есть
mcedit /etc/sysconfig/grub2 # тут может быть и nano, и vi, и другой редактор
# сохраняем и выходим
grub-mkconfig -o /boot/grub/grub.cfg
# удаляем так-же ссылку на 61-gdm.rules, если она есть
rm /etc/udev/rules.d/61-gdm.rules
# Удаляем правила запрещающие nouveau, если они есть
rm /etc/modprobe.d/blacklist-nvidia-x11.conf
# Удаляем изменение размещения видеопамяти, если оно есть
rm /etc/modprobe.d/nvidia_videomemory_allocation.conf
# (опционально) переустановить xorg обвязку
epm reinstall xorg-dri-nouveau xorg-drv-nouveau
# перезагрузиться
reboot
```
:::

Как только все загрузилось и мы убедились, что NOUVEAU заработало:

```shell
su -
make-initrd
# подождать пока соберется initrd
# посмотреть что в выводе сборки initrd присутствует Nouveau
reboot
```
## Управление питанием на Nouveau

Nouveau, драйвер с открытым исходным кодом для графических процессоров Nvidia, развивается посредством реверс-инжиниринга. В области управления питанием были как прогрессы, так и регрессы (см. страницу [PowerManagement](https://nouveau.freedesktop.org/PowerManagement.html)).

Однако, начиная с архитектуры Turing, видеокарты получили полную поддержку управления питанием благодаря GSP прошивкам от Nvidia. Видеокарты на базе архитектур **Ampere** и **Ada Lovelace** имеют эту поддержку **по умолчанию**. Для видеокарт на базе архитектуры **Turing необходимо указать специальный параметр в ядре**.

Для того, чтобы на видеокартах **Turing** заработало управление питанием, необходимо прописать в параметр `GRUB_CMDLINE_LINUX_DEFAULT` значение `nouveau.config=NvGspRm=1` и cгенерировать новых grub.cfg:

```shell
su -
mcedit /etc/sysconfig/grub2
grub-mkconfig -o /boot/grub/grub.cfg
```


### Источники:
https://www.altlinux.org/Nvidia

https://www.kernel.org/doc/html/latest/gpu/drm-kms.html

https://wiki.gentoo.org/wiki/NVIDIA/nvidia-drivers/ru

https://en.wikipedia.org/wiki/Nouveau_(software)

https://www.altlinux.org/#Проблемы_с_драйвером_видеокарты

https://x.org/

https://nouveau.freedesktop.org/

https://bugzilla.altlinux.org/39108

https://packages.altlinux.org/ru/sisyphus/srpms/nvidia_glx_common/

https://download.nvidia.com/XFree86/Linux-x86_64/550.40.07/README/powermanagement.html

https://wiki.archlinux.org/title/NVIDIA

https://wiki.archlinux.org/title/NVIDIA/Tips_and_tricks

https://wiki.archlinux.org/title/Kernel_mode_setting

https://wiki.gentoo.org/wiki/NVIDIA/nvidia-drivers
