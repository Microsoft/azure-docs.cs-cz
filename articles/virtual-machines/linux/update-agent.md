---
title: Aktualizovat Azure Linux Agent z webu GitHub | Microsoft Docs
description: Zjistěte, jak aktualizovat Azure Linux Agent pro váš virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 21165621478cc413b34a81345d6d3945f78148c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Postup aktualizace Azure Linux Agent na virtuálním počítači

Chcete-li aktualizovat vaše [Azure Linux Agent](https://github.com/Azure/WALinuxAgent) na virtuální počítač s Linuxem v Azure, musíte už mít:

- Spuštěného virtuálního počítače s Linuxem v Azure.
- Připojení k této virtuální počítač s Linuxem pomocí protokolu SSH.

Vždy zkontrolujte pro balíček v úložišti distro Linux nejdřív. Je možné, k dispozici balíček nemusí být, že na nejnovější verzi, ale povolení automatických aktualizací se ujistěte se, že Linux Agent bude vždy získat nejnovější aktualizace. Budete mít problémy instalace ze Správce balíčku, byste se měli obrátit dodavatele distro podpory.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Podpora agenta minimální virtuálních počítačů v Azure
Ověřte [minimální verzi podporu pro agenty virtuálního počítače v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) než budete pokračovat.

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

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolena automatická aktualizace

Nejdřív zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu příkaz waagent

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
Tato verze Debian nemá na verzi > = 2.0.16, proto není k dispozici pro něj automatických aktualizací. Výstup z výše uvedeném příkazu se zobrazí, pokud tento balíček je aktuální.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Klára" / Debian 9 "Stretch"

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
#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolena automatická aktualizace 

Nejdřív zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu příkaz waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>Red Hat nebo CentOS

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

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolena automatická aktualizace 

Nejdřív zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit spuštění:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu příkaz waagent

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

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolena automatická aktualizace 

Nejdřív zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu příkaz waagent

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

Výše uvedené výstup se zobrazí, pokud tento balíček je aktuální.

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolena automatická aktualizace 

Nejdřív zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu příkaz waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Zkontrolujte aktuální verzi balíčku

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Zkontrolujte dostupné aktualizace

Ve výstupu výše to vám ukáže, pokud je balíček maximálně datum.

#### <a name="install-the-latest-package-version"></a>Nainstalujte nejnovější verzi balíčku

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolena automatická aktualizace 

Nejdřív zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Restartujte službu příkaz waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 a 7

Pro Oracle Linux, ujistěte se, že `Addons` je povoleno úložiště. Vyberte, chcete-li upravit soubor `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) nebo `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) a změňte řádek `enabled=0` k `enabled=1` pod **[ol6_addons]** nebo **[ol7_addons]** v tomto soubor.

Poté nainstalujte nejnovější verzi Azure Linux Agent, zadejte:

```bash
sudo yum install WALinuxAgent
```

Pokud nenajdete rozšíření úložiště můžete jednoduše přidat tyto řádky na konci souboru .repo podle vaší verze Oracle Linux:

Pro virtuální počítače, Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Pro virtuální počítače, Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Poté zadejte:

```bash
sudo yum update WALinuxAgent
```

Obvykle to je vše, budete potřebovat, ale pokud z nějakého důvodu musíte nainstalovat z https://github.com přímo, použijte následující postup.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualizovat agenta systému Linux, pokud žádný balíček agenta existuje pro distribuci

Nainstalujte wget (existují zde některých distribucích, který nemusíte instalovat ve výchozím nastavení, jako je například Redhat a CentOS, Oracle Linux verze 6.4 a 6.5) tak, že zadáte `sudo yum install wget` na příkazovém řádku.

### <a name="1-download-the-latest-version"></a>1. Stáhnout nejnovější verzi
Otevřete [verzi Azure Linux Agent v Githubu](https://github.com/Azure/WALinuxAgent/releases) ve webové stránky a zjistěte nejnovější číslo verze. (Vaší aktuální verzí můžete vyhledat zadáním `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Pro verzi 2.2.x nebo novější, zadejte:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Následující řádek používá verzi 2.2.0 jako příklad:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Nainstalujte agenta Azure Linux

#### <a name="for-version-22x-use"></a>Pro verzi 2.2.x, použijte:
Budete muset nainstalovat balíček `setuptools` nejprve – Viz [zde](https://pypi.python.org/pypi/setuptools). Potom spusťte:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Ujistěte se, že je povolena automatická aktualizace

Nejdřív zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najít 'AutoUpdate.Enabled'. Pokud se zobrazí tento výstup, je povoleno:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit spuštění:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Restartujte službu příkaz waagent
Pro většinu distribucích systému Linux:

```bash
sudo service waagent restart
```

Ubuntu použijte:

```bash
sudo service walinuxagent restart
```

Pro jádro operačního systému použijte:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Ověřit verzi Azure Linux Agent
    
```bash
waagent -version
```

Pro jádro operačního systému nemusí fungovat výše uvedeném příkazu.

Zobrazí se, že verze Azure Linux Agent je aktualizovaná na novou verzi.

Další informace týkající se Azure Linux Agent najdete v tématu [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).
