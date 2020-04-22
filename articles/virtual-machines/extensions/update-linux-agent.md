---
title: Aktualizace agenta Azure Linuxu z GitHubu
description: Přečtěte si, jak aktualizovat Azure Linux Agenta pro virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: e3b2819b33feba52c3b02f0e2104d4106bd04cbb
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770059"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Jak aktualizovat agenta Azure Linuxu na virtuálním počítači

Chcete-li aktualizovat azure [linuxového agenta](https://github.com/Azure/WALinuxAgent) na virtuálním počítači SIO v Azure, musíte už mít:

- Spuštěný virtuální počítač s Linuxem v Azure.
- Připojení k tomuto virtuálnímu počítači SM systému Linux pomocí ssh.

Vždy byste měli nejprve zkontrolovat balíček v úložišti distribuce Linuxu. Je možné, že balíček k dispozici nemusí být nejnovější verze, nicméně povolení automatické aktualizace zajistí, že Linux Agent bude vždy získat nejnovější aktualizaci. Pokud máte problémy s instalací od správců balíčků, měli byste vyhledat podporu od dodavatele distro.

> ! [POZNÁMKA] Další informace najdete [v tématu Schválené distribuce Linuxu v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minimální podpora agentů virtuálních strojů v Azure
Než budete pokračovat, ověřte [minimální podporu verzí pro agenty virtuálních strojů v Azure.](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

## <a name="updating-the-azure-linux-agent"></a>Aktualizace agenta Azure Linuxu

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Aktualizovat mezipaměť balíčku

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalace nejnovější verze balíčku

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Zkontrolujte, zda je povolena automatická aktualizace.

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povolen:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Povolení spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

#### <a name="restart-agent-for-1404"></a>Restartovat agenta pro 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Restartovací činidlo pro 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Červený klobouk / CentoS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Kontrola dostupných aktualizací

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalace nejnovější verze balíčku

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Zkontrolujte, zda je povolena automatická aktualizace. 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povolen:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Povolení spuštění:

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

#### <a name="check-available-updates"></a>Kontrola dostupných aktualizací

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalace nejnovější verze balíčku

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Zkontrolujte, zda je povolena automatická aktualizace. 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povolen:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Povolení spuštění:

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

#### <a name="check-available-updates"></a>Kontrola dostupných aktualizací

Výše uvedený výstup vám ukáže, zda je balíček aktuální.

#### <a name="install-the-latest-package-version"></a>Instalace nejnovější verze balíčku

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Zkontrolujte, zda je povolena automatická aktualizace. 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povolen:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Povolení spuštění:

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

#### <a name="check-available-updates"></a>Kontrola dostupných aktualizací

Ve výstupu z výše uvedeného vám ukáže, zda je balíček aktuální.

#### <a name="install-the-latest-package-version"></a>Instalace nejnovější verze balíčku

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Zkontrolujte, zda je povolena automatická aktualizace. 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povolen:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Povolení spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/ Debian 7 "Stretch"

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizovat mezipaměť balíčku

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalace nejnovější verze balíčku

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Povolit automatickou aktualizaci agenta
Tato verze Debianu nemá verzi >= 2.0.16, proto pro ni není k dispozici automatická aktualizace. Výstup z výše uvedeného příkazu vám ukáže, zda je balíček aktuální.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizovat mezipaměť balíčku

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalace nejnovější verze balíčku

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Zkontrolujte, zda je povolena automatická aktualizace.
Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povolen:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Povolení spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 a Oracle Linux 7

U oracle linuxu se `Addons` ujistěte, že je úložiště povoleno. Zvolte úpravu `/etc/yum.repos.d/public-yum-ol6.repo`souboru (Oracle `/etc/yum.repos.d/public-yum-ol7.repo`Linux 6) nebo `enabled=0` (Oracle Linux) a změňte řádek pod `enabled=1` **[ol6_addons]** nebo **[ol7_addons]** v tomto souboru.

Chcete-li nainstalovat nejnovější verzi agenta Azure Linux, zadejte:

```bash
sudo yum install WALinuxAgent
```

Pokud úložiště doplňků nenajdete, můžete jednoduše přidat tyto řádky na konec souboru .repo podle verze Oracle Linux:

Pro virtuální počítače Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Pro virtuální počítače Oracle Linux 7:

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

Obvykle je to vše, co potřebujete, ale pokud z https://github.com nějakého důvodu potřebujete nainstalovat přímo, použijte následující kroky.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualizace agenta Linuxu, pokud pro distribuci neexistuje žádný balíček agenta

Nainstalujte wget (existují některé distribuce, které jej ve výchozím nastavení neinstalují, například Red Hat, CentOS a Oracle Linux verze 6.4 a 6.5) zadáním `sudo yum install wget` na příkazovém řádku.

### <a name="1-download-the-latest-version"></a>1. Stáhněte si nejnovější verzi
Otevřete [vydání Azure Linux Agent v GitHubu](https://github.com/Azure/WALinuxAgent/releases) na webové stránce a zjistěte číslo nejnovější verze. (Aktuální verzi můžete vyhledat zadáním `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Pro verzi 2.2.x nebo novější zadejte:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Následující řádek používá jako příklad verzi 2.2.0:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Instalace agenta Azure Linuxu

#### <a name="for-version-22x-use"></a>Pro verzi 2.2.x použijte:
Možná budete muset nejprve nainstalovat balíček `setuptools` -- viz [zde](https://pypi.python.org/pypi/setuptools). Potom následujícím příkazem:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Zkontrolujte, zda je povolena automatická aktualizace.

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povolen:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Povolení spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Restartujte službu waagent
Pro většinu distribucí Linuxu:

```bash
sudo service waagent restart
```

Pro Ubuntu, použijte:

```bash
sudo service walinuxagent restart
```

Pro CoreOS použijte:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Potvrzení verze Azure Linux Agent
    
```bash
waagent -version
```

Pro CoreOS výše uvedený příkaz nemusí fungovat.

Uvidíte, že verze Azure Linux Agent byla aktualizována na novou verzi.

Další informace týkající se Agenta Azure Linuxu najdete v [tématu Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).
