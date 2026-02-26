# prac2forasos
set the nfs server &amp; nfs common
# Настройка NFS между виртуальными машинами

Инструкция по настройке статических IP-адресов и организации общего доступа к файлам через протокол NFS (Network File System) между двумя виртуальными машинами (VM1 — сервер, VM2 — клиент).

## 📋 Требования

- 2 виртуальные машины на базе Debian/Ubuntu.
- Настроенный второй сетевой интерфейс (`eth1`) для внутренней сети (подсеть `192.168.56.0/24`).
- Права суперпользователя (`sudo`).

## 🌐 1. Настройка сети

Необходимо прописать статические IP-адреса для интерфейса `eth1` на обеих машинах.

### VM1 (Сервер)
Отредактируйте файл конфигурации сети:
```bash
nano /etc/network/interfaces

auto eth1
iface eth1 inet static
address 192.168.56.10
netmask 255.255.255.0
```
### VM2 (клиент) 
```bash
nano /etc/network/interfaces

auto eth1
iface eth1 inet static
address 192.168.56.11
netmask 255.255.255.0
```
### 📂 2. Настройка NFS Сервера (VM1)
```bash
sudo nano /etc/exports

/mnt/raid 192.168.56.11(rw,sync,no_subtree_check,no_root_squash)

## Применение изменений

sudo exportfs -ra
sudo systemctl restart nfs-kernel-server
```
### 💻 3. Настройка NFS Клиента (VM2)
```bash
sudo mkdir -p /mnt/raid_remote

sudo mount 192.168.56.10:/mnt/raid /mnt/raid_remote

sudo nano /etc/fstab

192.168.56.10:/mnt/raid /mnt/raid_remote nfs defaults 0 0
