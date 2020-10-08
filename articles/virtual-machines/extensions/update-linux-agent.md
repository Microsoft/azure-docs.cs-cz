---
title: Aktualizace agenta Azure Linux z GitHubu
description: Informace o tom, jak aktualizovat agenta Azure Linux pro virtuální počítač Linux v Azure
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
ms.openlocfilehash: 882ed23fe9f7e759bef7464d512685163a26b288
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816173"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Postup aktualizace agenta Azure Linux na virtuálním počítači

Pokud chcete aktualizovat [agenta Azure Linux](https://github.com/Azure/WALinuxAgent) na virtuálním počítači se systémem Linux v Azure, musíte mít:

- Běžící virtuální počítač se systémem Linux v Azure.
- Připojení k virtuálnímu počítači se systémem Linux pomocí SSH.

Vždy byste měli nejprve vyhledat balíček v úložišti distribuce Linux. Je možné, že dostupný balíček nemusí být nejnovější verze, ale když povolíte možnost AutoUpdate, zajistíte, aby agent pro Linux vždycky získal nejnovější aktualizaci. Pokud máte problémy s instalací ze Správce balíčků, měli byste požádat dodavatele distribuce o podporu.

> [!NOTE]
> Další informace najdete v tématu [schválené distribuce Linux v Azure](../linux/endorsed-distros.md) .

Než budete pokračovat, ověřte [podporu minimální verze pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) .


## <a name="ubuntu"></a>Ubuntu

Ověřit aktuální verzi balíčku

```bash
apt list --installed | grep walinuxagent
```

Aktualizovat mezipaměť balíčku

```bash
sudo apt-get -qq update
```

Nainstalovat nejnovější verzi balíčku

```bash
sudo apt-get install walinuxagent
```

Zajistěte, aby byla povolena automatická aktualizace. Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najde ' AutoUpdate. Enabled '. Pokud se zobrazí tento výstup, je povolený:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit běh:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Restartujte službu waagengt pro 14,04.

```bash
initctl restart walinuxagent
```

Restartujte službu waagent pro 16,04/17,04.

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

Ověřit aktuální verzi balíčku

```bash
sudo yum list WALinuxAgent
```

Vyhledat dostupné aktualizace

```bash
sudo yum check-update WALinuxAgent
```

Nainstalovat nejnovější verzi balíčku

```bash
sudo yum install WALinuxAgent
```

Zajistěte, aby byla povolená Automatická aktualizace. 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najde ' AutoUpdate. Enabled '. Pokud se zobrazí tento výstup, je povolený:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit běh:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Restartujte službu waagent.

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

Ověřit aktuální verzi balíčku

```bash
sudo yum list WALinuxAgent
```

Vyhledat dostupné aktualizace

```bash
sudo yum check-update WALinuxAgent
```

Nainstalovat nejnovější verzi balíčku

```bash
sudo yum install WALinuxAgent  
```

Zajistěte, aby byla povolena automatická aktualizace. Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najde ' AutoUpdate. Enabled '. Pokud se zobrazí tento výstup, je povolený:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit běh:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Restartujte službu waagent.

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

Ověřit aktuální verzi balíčku

```bash
zypper info python-azure-agent
```

Ověřte dostupné aktualizace. Výše uvedený výstup vám ukáže, jestli je balíček aktuální.

Nainstalovat nejnovější verzi balíčku

```bash
sudo zypper install python-azure-agent
```

Zajistěte, aby byla povolená Automatická aktualizace. 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najde ' AutoUpdate. Enabled '. Pokud se zobrazí tento výstup, je povolený:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit běh:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Restartujte službu waagent.

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

Ověřit aktuální verzi balíčku

```bash
zypper info python-azure-agent
```

Vyhledat dostupné aktualizace

Ve výstupu z výše uvedeného se zobrazí, jestli je balíček aktuální.

Nainstalovat nejnovější verzi balíčku

```bash
sudo zypper install python-azure-agent
```

Zajistěte, aby byla povolená Automatická aktualizace. 

Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najde ' AutoUpdate. Enabled '. Pokud se zobrazí tento výstup, je povolený:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit běh:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Restartujte službu waagent.

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "Stretch"

Ověřit aktuální verzi balíčku

```bash
dpkg -l | grep waagent
```

Aktualizovat mezipaměť balíčku

```bash
sudo apt-get -qq update
```

Nainstalovat nejnovější verzi balíčku

```bash
sudo apt-get install waagent
```

Povolit automatickou aktualizaci agenta: Tato verze Debian nemá verzi >= 2.0.16, proto není pro ni k dispozici automatické aktualizace. Výstup z výše uvedeného příkazu vám ukáže, jestli je balíček aktuální.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "Stretch"

Ověřit aktuální verzi balíčku

```bash
apt list --installed | grep waagent
```

Aktualizovat mezipaměť balíčku

```bash
sudo apt-get -qq update
```

Nainstalovat nejnovější verzi balíčku

```bash
sudo apt-get install waagent
```

Zajistěte, aby byla nejprve povolena automatická aktualizace, a zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najde ' AutoUpdate. Enabled '. Pokud se zobrazí tento výstup, je povolený:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit běh:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 a Oracle Linux 7

V případě Oracle Linux se ujistěte, že `Addons` je úložiště povolené. Vyberte soubor, který chcete upravit `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) nebo `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux), a `enabled=0` v tomto souboru změňte řádek na `enabled=1` **[ol6_addons]** nebo **[ol7_addons]** .

Pak pro instalaci nejnovější verze agenta Azure Linux zadejte:

```bash
sudo yum install WALinuxAgent
```

Pokud nenajdete úložiště doplňku, můžete jednoduše přidat tyto řádky na konec souboru. úložiště podle vaší Oracle Linux verze:

Pro virtuální počítače s Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
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

To je obvykle všechno, co potřebujete, ale pokud z nějakého důvodu potřebujete instalaci instalovat https://github.com přímo, použijte následující postup.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualizace agenta pro Linux, pokud pro distribuci neexistují žádné balíčky agentů

Nainstalujte wget (ve výchozím nastavení je k dispozici několik distribuce, které neinstaluje, jako je Red Hat, CentOS a Oracle Linux verze 6,4 a 6,5), a to tak, že `sudo yum install wget` na příkazovém řádku zadáte.

### <a name="1-download-the-latest-version"></a>1. Stáhněte si nejnovější verzi.
Otevřete [verzi agenta Azure Linux](https://github.com/Azure/WALinuxAgent/releases) na webu GitHub na webové stránce a vyhledejte nejnovější číslo verze. (Svou aktuální verzi můžete vyhledat zadáním `waagent --version` .)

Pro verzi 2.2. x nebo novější zadejte:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Následující řádek používá 2.2.0 verze jako příklad:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Nainstalujte agenta Azure Linux.

Pro verzi 2.2. x použijte: možná budete muset nainstalovat balíček `setuptools` nejdřív – viz [tady](https://pypi.python.org/pypi/setuptools). Potom následujícím příkazem:

```bash
sudo python setup.py install
```

Zajistěte, aby byla povolena automatická aktualizace. Nejprve zkontrolujte, zda je povolena:

```bash
cat /etc/waagent.conf
```

Najde ' AutoUpdate. Enabled '. Pokud se zobrazí tento výstup, je povolený:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Chcete-li povolit běh:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Restartujte službu waagent.
Pro většinu distribuce systému Linux:

```bash
sudo service waagent restart
```

Pro Ubuntu použijte:

```bash
sudo service walinuxagent restart
```

Pro CoreOS použijte:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Potvrďte verzi agenta Azure Linux.
    
```bash
waagent -version
```

V případě CoreOS nemusí fungovat výše uvedený příkaz.

Uvidíte, že verze agenta Azure Linux se aktualizovala na novou verzi.

Další informace týkající se agenta Azure Linux najdete v článku o [souboru Readme agenta Azure Linux](https://github.com/Azure/WALinuxAgent).
