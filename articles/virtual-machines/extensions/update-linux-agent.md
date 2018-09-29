---
title: Aktualizace agenta Azure Linux z Githubu | Dokumentace Microsoftu
description: Zjistěte, jak aktualizovat agenta Azure Linux pro virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: roiyz
ms.openlocfilehash: 0926859037c358aaf94ec51d9614cc9c02629e7f
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451931"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Jak aktualizovat agenta Azure Linux ve virtuálním počítači

Chcete-li aktualizovat vaše [agenta Azure Linux](https://github.com/Azure/WALinuxAgent) na virtuálního počítače s Linuxem v Azure, musíte už mít:

- Spuštění virtuálního počítače s Linuxem v Azure.
- Připojení k této virtuální počítač s Linuxem pomocí protokolu SSH.

Vždy zkontrolujte balíčku v úložišti distribuce Linuxu nejprve. Je možné že k dispozici balíček nemusí být, že nejnovější verze, ale povolení automatických aktualizací se ujistěte se, že agenta pro Linux se vždy zobrazí nejnovější aktualizace. Pokud máte problémy instalace ze správců balíčků, byste se měli obrátit podporu – od dodavatele distribuce.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Podpora agenta minimální virtuálních počítačů v Azure
Ověřte, [minimální podporované verze pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) než budete pokračovat.

## <a name="updating-the-azure-linux-agent"></a>Aktualizace Azure Linux Agent

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Aktualizace mezipaměti balíčku

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolená Automatická aktualizace

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Vyhledejte "AutoUpdate.Enabled". Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pokud chcete povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

#### <a name="restart-agent-for-1404"></a>Restartovat agenta 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Restartovat agenta 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizace mezipaměti balíčku

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Povolit automatické aktualizace agenta
Tato verze Debianu nemá verze > = 2.0.16, proto není k dispozici pro něj automatických aktualizací. Výstup z výše uvedeného příkazu se zobrazí, pokud balíček je aktuální.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizace mezipaměti balíčku

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolená Automatická aktualizace 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Vyhledejte "AutoUpdate.Enabled". Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pokud chcete povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat nebo CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Zkontrolujte dostupné aktualizace

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolená Automatická aktualizace 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Vyhledejte "AutoUpdate.Enabled". Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pokud chcete povolit spuštění:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Zkontrolujte dostupné aktualizace

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolená Automatická aktualizace 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Vyhledejte "AutoUpdate.Enabled". Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pokud chcete povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Zkontrolujte dostupné aktualizace

Výše výstupu se zobrazí, pokud balíček je aktuální.

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolená Automatická aktualizace 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Vyhledejte "AutoUpdate.Enabled". Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pokud chcete povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Zkontrolujte dostupné aktualizace

Ve výstupu výše zobrazí se pokud balíček je aktuální.

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolená Automatická aktualizace 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Vyhledejte "AutoUpdate.Enabled". Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pokud chcete povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 a 7

Oracle Linux, ujistěte se, že `Addons` úložiště je povolená. Zvolte upravit soubor `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) nebo `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) a změňte řádek `enabled=0` k `enabled=1` pod **[ol6_addons]** nebo **[ol7_addons]** v tomto soubor.

Pokud chcete nainstalovat nejnovější verzi agenta Azure Linux, zadejte:

```bash
sudo yum install WALinuxAgent
```

Pokud se nepodařilo najít doplněk úložiště můžete jednoduše přidejte tyto řádky na konci souboru .repo podle vaší verze Oracle Linux:

Pro virtuální počítače s Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Pro virtuální počítače s Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Pak zadejte:

```bash
sudo yum update WALinuxAgent
```

Obvykle to je všechno, co potřebujete, ale pokud z nějakého důvodu potřebujete ho nainstalujte z https://github.com přímo, pomocí následujícího postupu.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualizovat agenta pro Linux, pokud neexistuje žádný agent balíček pro distribuci

Nainstalujte wget (existují některé distribuce, které nechcete nainstalovat ve výchozím nastavení, jako je například verze Red Hat, CentOS nebo Oracle Linux 6.4 a 6.5) tak, že zadáte `sudo yum install wget` na příkazovém řádku.

### <a name="1-download-the-latest-version"></a>1. Stáhněte si nejnovější verzi
Otevřít [verzi agenta Azure Linux ve službě GitHub](https://github.com/Azure/WALinuxAgent/releases) ve webové stránky a přečtěte si nejnovější číslo verze. (Aktuální verze můžete najít zadáním `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Pro verzi 2.2.x nebo později, zadejte:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Tento řádek používá verzi 2.2.0 jako příklad:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Instalace agenta Azure Linux

#### <a name="for-version-22x-use"></a>Pro verzi 2.2.x, použijte:
Budete muset nainstalovat balíček `setuptools` nejprve – Viz [tady](https://pypi.python.org/pypi/setuptools). Potom následujícím příkazem:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolená Automatická aktualizace

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Vyhledejte "AutoUpdate.Enabled". Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pokud chcete povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Restartujte službu waagent
Pro většinu distribuce Linuxu:

```bash
sudo service waagent restart
```

Pro Ubuntu použijte:

```bash
sudo service walinuxagent restart
```

CoreOS použijte:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Zkontrolujte verzi agenta Azure Linux
    
```bash
waagent -version
```

Výše uvedený příkaz CoreOS, nemusí fungovat.

Uvidíte, že verze agenta Azure Linux byla aktualizována na novou verzi.

Další informace týkající se agenta Azure Linux najdete v tématu [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).
