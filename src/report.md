
Этот файл не нужен для сдачи проекта и скидывать его в репозиторий проекта не надо
сгененрирован ИИ, поэтому могут быть ошибки.

🔹 1. Установка GNS3 Server (на Linux Server)

1.1. Установка зависимостей
    Порядок выполнения задания 
    sudo apt update
    sudo apt install -y python3-pip python3-venv docker.io qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
    sudo usermod -aG libvirt,docker $USER
    newgrp libvirt  # (или перезайдите в систему)


1.2. Установка GNS3 Server
    ython3 -m venv ~/gns3-server
    source ~/gns3-server/bin/activate
    pip install gns3-server

### **Полное руководство по созданию проекта в GNS3 (Ubuntu/macOS/Windows)**

Это пошаговая инструкция с установкой всех компонентов, настройкой сервера, добавлением образов Cisco IOS и Wireshark.

---

## **1. Установка GNS3 и зависимостей**
### **1.1. Для Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install -y python3-pip python3-venv git curl
sudo apt install -y dynamips ubridge wireshark  # Основные компоненты
sudo usermod -aG ubridge,wireshark $USER       # Даем права текущему пользователю
newgrp wireshark                               # Обновляем группы без перезагрузки
```

### **1.2. Для macOS (через Homebrew):**
```bash
brew install python3 git curl dynamips ubridge wireshark
sudo chmod +x /Applications/Wireshark.app/Contents/MacOS/Wireshark
```

### **1.3. Для Windows:**
1. Скачайте установщики:
   - [GNS3](https://www.gns3.com/software/download)
   - [Wireshark](https://www.wireshark.org/download.html)
2. При установке выберите **"Install WinPcap"** и **"Install USBPcap"**.

---

## **2. Настройка GNS3 Server**
### **2.1. Создание виртуального окружения (Linux/macOS)**
```bash
python3 -m venv ~/gns3-server
source ~/gns3-server/bin/activate
pip install gns3-server
```

### **2.2. Запуск сервера**
```bash
gns3server --log ~/gns3.log  # Логи будут в ~/gns3.log
```
Сервер запустится на `http://localhost:3080`.

---

## **3. Установка GNS3 GUI**
### **3.1. Для Linux/macOS:**
```bash
pip install gns3-gui
gns3  # Запуск графической оболочки
```

### **3.2. Для Windows:**
Просто запустите установленный `GNS3.exe`.

---

## **4. Добавление образа Cisco IOS**
### **4.1. Подготовка образа**
1. Скачайте образ (например, `c3745-adventerprisek9-mz.124-25.bin`).
2. Поместите его в папку:
   ```bash
   mkdir -p ~/GNS3/images/IOS/
   cp /путь/к/c3745-adventerprisek9-mz.124-25.bin ~/GNS3/images/IOS/
   ```

### **4.2. Импорт в GNS3**
1. Откройте GNS3 → `Edit > Preferences > Dynamips > IOS Routers`.
2. Нажмите **New** → Укажите путь к `.bin`-файлу.
3. Выберите:
   - **Platform**: `c3700`
   - **Model**: `3745`
   - **RAM**: `256` MB (можно больше, если позволяет ПК).
4. Нажмите **Finish** → **Save**.

---

## **5. Создание проекта и настройка устройств**
### **5.1. Создание проекта**
1. `File > New Project` → Укажите имя (например, `Cisco_Lab`).
2. GNS3 создаст папку:  
   `~/GNS3/projects/<ID_проекта>/`

### **5.2. Добавление устройств**
1. Перетащите 2 роутера `Cisco 3745` на рабочую область.
2. Соедините их через `FastEthernet0/0` (кнопка **"Add a link"**).

### **5.3. Настройка интерфейсов**
Для каждого роутера:
```cisco
enable
configure terminal
interface FastEthernet0/0
 ip address 10.10.10.1 255.255.255.0  ! Для первого роутера
 ! ip address 10.10.10.2 255.255.255.0 ! Для второго
 no shutdown
end
write memory
```

---

## **6. Захват трафика в Wireshark**
### **6.1. Запуск Wireshark**
1. В GNS3 кликните ПКМ на соединении между роутерами → **Start Capture** → **Wireshark**.
2. Если Wireshark не открывается:
   - Убедитесь, что он установлен (`which wireshark`).
   - Проверьте путь в `Edit > Preferences > Packet Capture`.

### **6.2. Проверка связи**
На одном из роутеров выполните:
```cisco
ping 10.10.10.2
```
В Wireshark должны отобразиться ICMP-пакеты.

---

## **7. Экспорт проекта (без тяжелых файлов)**
### **7.1. Через GUI**
1. `File > Export Project`.
2. Снимите галочки:
   - **Export appliance configurations**
   - **Export IOS images**
3. Сохраните как `.gns3project` (размер ~10-50 КБ).

### **7.2. Вручную**
```bash
tar -czvf cisco_lab.tar.gz \
  ~/GNS3/projects/<ID_проекта>/project.gns3 \
  ~/GNS3/projects/<ID_проекта>/configs/
```

---

## **8. Полезные команды**
| Действие | Команда |
|----------|---------|
| Проверить порты | `netstat -tulnp \| grep 3080` |
| Удалить снапшоты | `find ~/GNS3/projects/ -name "*.ssa" -delete` |
| Очистить кеш | `rm -rf ~/.config/GNS3/dynamips/` |

---

### **Итог**
Теперь у вас есть:
- Рабочий проект с 2 роутерами Cisco.
- Настроенный Wireshark для анализа трафика.
- Оптимизированный экспорт проекта (без образов IOS).

Для публикации в GitHub используйте `.gitignore`:
```gitignore
# Исключить бинарные файлы
*.bin
*.ssa
/project-files/
```
