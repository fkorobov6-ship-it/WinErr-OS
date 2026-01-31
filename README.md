# WinErr OS - Сборка Live USB

## Описание
WinErr OS - гибридная операционная система на базе Ubuntu с интерфейсом в стиле Windows/Linux.

## Быстрая установка на существующую Ubuntu

```bash
cd /путь/к/winerr-os
sudo bash filesystem/opt/winerr/install.sh
sudo reboot
```

## Создание Live USB с нуля

### Требования
- Ubuntu 22.04/24.04 (или любой Linux)
- Минимум 20 ГБ свободного места
- Подключение к интернету
- USB флешка минимум 4 ГБ

### Способ 1: Использование Cubic (рекомендуется)

1. **Установите Cubic:**
```bash
sudo apt-add-repository ppa:cubic-wizard/release
sudo apt update
sudo apt install cubic
```

2. **Скачайте базовый Ubuntu ISO:**
```bash
wget https://releases.ubuntu.com/22.04/ubuntu-22.04.4-desktop-amd64.iso
```

3. **Запустите Cubic:**
```bash
cubic
```

4. **В Cubic:**
   - Выберите папку проекта
   - Выберите скачанный Ubuntu ISO
   - Когда откроется терминал chroot:

```bash
# Копируем файлы WinErr
cp -r /путь/к/winerr-os/filesystem/* /

# Устанавливаем зависимости
apt update
apt install -y gnome-tweaks python3-gi fonts-firacode papirus-icon-theme

# Делаем скрипты исполняемыми
chmod +x /opt/winerr/*.py /opt/winerr/*.sh

# Настраиваем автозапуск OOBE
cp /opt/winerr/oobe.desktop /etc/skel/.config/autostart/
```

5. **Завершите в Cubic и создайте ISO**

### Способ 2: Ручная сборка с live-build

```bash
# Установка инструментов
sudo apt install live-build debootstrap squashfs-tools genisoimage

# Создание структуры
mkdir -p winerr-live && cd winerr-live
lb config \
    --architecture amd64 \
    --distribution jammy \
    --archive-areas "main restricted universe multiverse" \
    --bootloader grub-efi \
    --binary-images iso-hybrid \
    --debian-installer live

# Копирование кастомизации
mkdir -p config/includes.chroot/
cp -r /путь/к/winerr-os/filesystem/* config/includes.chroot/

# Добавление пакетов
cat > config/package-lists/winerr.list.chroot << EOF
gnome-tweaks
gnome-shell-extensions
python3-gi
python3-gi-cairo
gir1.2-gtk-3.0
fonts-firacode
papirus-icon-theme
EOF

# Сборка
sudo lb build
```

### Запись на USB

```bash
# Linux
sudo dd if=winerr-os.iso of=/dev/sdX bs=4M status=progress && sync

# Или используйте Ventoy/Rufus
```

## Структура файлов

```
winerr-os/
├── boot/grub/          # Конфигурация загрузчика
├── isolinux/           # Legacy boot
├── casper/             # Ядро и initrd (из Ubuntu)
├── filesystem/
│   ├── etc/skel/       # Шаблон домашней папки
│   ├── usr/share/
│   │   ├── themes/WinErr/  # GTK тема
│   │   └── applications/   # Desktop файлы
│   └── opt/winerr/     # Приложения WinErr
│       ├── oobe.py     # Первоначальная настройка
│       ├── panel.py    # Кастомная панель
│       └── install.sh  # Скрипт установки
└── README.md
```

## Компоненты

### OOBE (Out-of-Box Experience)
Запускается при первом входе для настройки:
- Регион и язык
- Имя пользователя и компьютера
- Пароль (опционально)
- Системные настройки

### GTK Тема
Тёмная тема с акцентами красного цвета (#ff6b63, #ff3b30)

### Панель задач
Кастомная панель в стиле Windows с:
- Кнопкой "Пуск"
- Списком открытых окон
- Системным треем
- Часами

## Лицензия
MIT License - свободное использование и модификация.

## Поддержка
Создано для демонстрационных целей на базе Ubuntu.
