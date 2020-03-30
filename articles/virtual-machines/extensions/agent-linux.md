---
title: Přehled agenta virtuálního počítače Azure Linuxu
description: Zjistěte, jak nainstalovat a nakonfigurovat Linux Agent (waagent) pro správu interakce virtuálního počítače s Řadičem infrastruktury Azure.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f22fbd77069488e7aaf490f93f42cde747444a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073855"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Principy a používání Azure Linux Agenta

Microsoft Azure Linux Agent (waagent) spravuje Linux & zřizování FreeBSD a interakce virtuálních zařízení s řadičem Azure Fabric. Kromě Linux Agent poskytující zřizovací funkce, Azure také nabízí možnost použití cloud-init pro některé operační systémy Linux. Linux Agent poskytuje následující funkce pro nasazení Linuxu a FreeBSD IaaS:

> [!NOTE]
> Další informace naleznete v [readme](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Zřizování obrázků**
  
  * Vytvoření uživatelského účtu
  * Konfigurace typů ověřování SSH
  * Nasazení veřejných klíčů SSH a dvojic klíčů
  * Nastavení názvu hostitele
  * Publikování názvu hostitele na platformě DNS
  * Hlášení otisku prstu hostitelského klíče SSH platformě
  * Správa disku prostředků
  * Formátování a montáž disku prostředku
  * Konfigurace odkládacího prostoru
* **Síťové služby**
  
  * Spravuje trasy za účelem zlepšení kompatibility se servery DHCP platformy.
  * Zajišťuje stabilitu názvu síťového rozhraní.
* **Kernel**
  
  * Konfiguruje virtuální NUMA `2.6.37`(zakázat pro <jádra )
  * Spotřebovává entropie Hyper-V pro /dev/random
  * Konfiguruje časové režimy SCSI pro kořenové zařízení (které může být vzdálené)
* **Diagnostika**
  
  * Přesměrování konzoly na sériový port
* **Nasazení SCVMM**
  
  * Detekuje a zavádí agenta VMM pro Linux při spuštění v prostředí System Center Virtual Machine Manager 2012 R2
* **Rozšíření virtuálního virtuálního montova**
  
  * Vstříkněte komponentu nastavovnu od Microsoftu a partnerů do virtuálního počítače s Linuxem (IaaS) a povolte automatizaci softwaru a konfigurace
  * Implementace odkazu na rozšíření virtuálního počítače na[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Communication
Tok informací z platformy do agenta probíhá prostřednictvím dvou kanálů:

* Zaváděcí disk DVD připojený pro nasazení IaaS. Tento disk DVD obsahuje konfigurační soubor kompatibilní s OVF, který obsahuje všechny informace o zřizování kromě skutečných párů klíčů SSH.
* Koncový bod Protokolu TCP, který vystavuje rozhraní REST API, který slouží k získání konfigurace nasazení a topologie.

## <a name="requirements"></a>Požadavky
Následující systémy byly testovány a je známo, že pracují s Agentem Azure Linux:

> [!NOTE]
> Tento seznam se může lišit od oficiálního seznamu podporovaných systémů na platformě Microsoft Azure, jak je popsáno zde:[https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* Centos 6,3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Další podporované systémy:

* FreeBSD 10+ (Azure Linux Agent v2.0.10+)

Agent Linuxu závisí na některých systémových balíčcích, aby správně fungoval:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Nástroje souborového systému: sfdisk, fdisk, mkfs, parted
* Nástroje pro hesla: chpasswd, sudo
* Nástroje pro zpracování textu: sed, grep
* Síťové nástroje: ip-route
* Podpora jádra pro montáž souborových systémů UDF.

## <a name="installation"></a>Instalace
Instalace pomocí RPM nebo balíčku DEB z úložiště balíčků vaší distribuce je upřednostňovanou metodou instalace a upgradu agenta Azure Linux. Všichni [schválení poskytovatelé distribuce](../linux/endorsed-distros.md) integrují balíček agenta Azure Linuxu do svých ibiaa a úložišť.

Najdete v dokumentaci v [úložišti Azure Linux Agent na GitHubu](https://github.com/Azure/WALinuxAgent) pro pokročilé možnosti instalace, jako je například instalace ze zdroje nebo do vlastních umístění nebo předpon.

## <a name="command-line-options"></a>Možnosti příkazového řádku
### <a name="flags"></a>Příznaky
* verbose: Zvýšení podrobností zadaného příkazu
* vynutit: Přeskočit interaktivní potvrzení některých příkazů

### <a name="commands"></a>Příkazy
* Nápověda: Zobrazí seznam podporovaných příkazů a příznaků.
* zrušení zřízení: Pokuste se vyčistit systém a učinit jej vhodným pro opětovné zřízení. Následující operace odstraní:
  
  * Všechny klíče hostitele SSH (pokud Provisioning.RegenerateSshHostKeyPair je 'y' v konfiguračním souboru)
  * Konfigurace nameserveru v /etc/resolv.conf
  * Root heslo z /etc/shadow (pokud Provisioning.DeleteRootPassword je 'y' v konfiguračním souboru)
  * Zapůjčení klientů DHCP v mezipaměti
  * Obnoví název hostitele na localhost.localdomain.

> [!WARNING]
> Zrušení zřízení nezaručuje, že obraz je vymazánvšechny citlivé informace a vhodné pro další distribuci.
> 
> 

* deprovision+user: Provádí vše v -deprovision (výše) a také odstraní poslední zřízený uživatelský účet (získané z /var/lib/waagent) a související data. Tento parametr je při zrušení zřizování image, která byla dříve zřizování v Azure, takže může být zachycena a znovu použita.
* verze: Zobrazí verzi waagent
* serialconsole: Nakonfiguruje GRUB označit ttyS0 (první sériový port) jako zaváděcí konzolu. Tím zajistíte, že protokoly spuštění jádra jsou odesílány do sériového portu a zpřístupněny pro ladění.
* Daemon: Spusťte waagent jako daemon pro správu interakce s platformou. Tento argument je určen waagent ve skriptu waagent init.
* start: Spuštění waagent jako proces na pozadí

## <a name="configuration"></a>Konfigurace
Konfigurační soubor (/etc/waagent.conf) řídí akce waagent. Následující ukazuje ukázkový konfigurační soubor:

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

Jsou popsány následující různé možnosti konfigurace. Možnosti konfigurace jsou ze tří typů; Logická hodnota, řetězec nebo celé číslo. Možnosti logické konfigurace lze zadat jako "y" nebo "n". Speciální klíčové slovo "None" může být použito pro některé položky konfigurace typu řetězce jako následující podrobnosti:

**Zřizování.Povoleno:**  
```
Type: Boolean  
Default: y
```
To umožňuje uživateli povolit nebo zakázat zřizování funkce v agentovi. Platné hodnoty jsou "y" nebo "n". Pokud zřizování je zakázáno, SSH hostitelské a uživatelské klíče v image jsou zachovány a všechny konfigurace zadané v rozhraní API zřizování Azure je ignorována.

> [!NOTE]
> Výchozí `Provisioning.Enabled` parametr je "n" na Ubuntu Cloud Images, které používají cloud-init pro zřizování.
> 
> 

**Zřizování.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Pokud je nastaveno, kořenové heslo v souboru /etc/shadow je během procesu zřizování vymazáno.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Pokud je nastavena, všechny dvojice klíčů hostitele SSH (ecdsa, dsa a RSA) jsou během procesu zřizování z /etc/ssh/. A je generován jeden nový pár klíčů.

Typ šifrování pro nový pár klíčů lze konfigurovat podle položky Provisioning.SshHostKeyPairType. Některé distribuce znovu vytvořit dvojice klíčů SSH pro všechny chybějící typy šifrování při restartování demonu SSH (například po restartu).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
To lze nastavit na typ šifrovacího algoritmu, který je podporován daemonem SSH na virtuálním počítači. Obvykle podporované hodnoty jsou "rsa", "dsa" a "ecdsa". "putty.exe" v systému Windows nepodporuje "ecdsa". Takže pokud máte v úmyslu použít tmel.exe v systému Windows pro připojení k nasazení Linuxu, použijte "rsa" nebo "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Pokud je nastavena, waagent monitoruje virtuální počítač Linux pro změny názvu hostitele (jak je vrácena příkazem "hostname") a automaticky aktualizuje konfiguraci sítě v bitové kopii tak, aby odrážela změnu. Chcete-li posunout změnu názvu na servery DNS, je ve virtuálním počítači restartována síť. To má za následek krátkou ztrátu připojení k Internetu.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Pokud je nastavena, waagent dekóduje CustomData z Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Pokud set, waagent provede CustomData po zřizování.

**Zřizování.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Tato možnost umožňuje resetovat heslo pro uživatele sys; výchozí hodnota je zakázána.

**Zřizování.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritmus používaný kryptou při generování algoritmu hash hesla.  
 1 - MD5  
 2a - Foukaná ryba  
 5 - SHA-256  
 6 - SHA-512  

**Zřizování.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Délka náhodné soli použité při generování hash hesla.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Pokud je nastavena, disk prostředků poskytovaný platformou je formátován a připojen waagent, pokud typ souborového systému požadovaný uživatelem v "ResourceDisk.Filesystem" je něco jiného než "ntfs". Na disku je k dispozici jeden oddíl typu Linux (83). Tento oddíl není formátován, pokud jej lze úspěšně připojit.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Určuje typ souborového systému pro disk prostředků. Podporované hodnoty se liší podle distribuce Linuxu. Pokud je řetězec X, pak mkfs. X by měl být přítomen na obrázku Linuxu. Obrázky SLES 11 by měly obvykle používat 'ext3'. FreeBSD obrázky by zde měly používat 'ufs2'.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Určuje cestu, u které je disk prostředku připojen. Disk prostředků je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače.

**Možnosti resourcedisk.mount**  
```
Type: String  
Default: None
```
Určuje možnosti připojení disku, které mají být předány příkazu mount -o. Toto je seznam hodnot oddělených čárkami, ex. 'nodev,nosuid'. Podrobnosti najdete v tématu mount(8).

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Pokud je nastavena, je na disku prostředku vytvořen odkládací soubor (/odkládací soubor) a přidán do odkládacího prostoru systému.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
Velikost odkládacího souboru v megabajtech.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Pokud je nastavena, protokol podrobnostje posílena. Waagent protokoluje /var/log/waagent.log a využívá funkce logrotate systému k otočení protokolů.

**Os. Povolit RDMA**  
```
Type: Boolean  
Default: n
```
Pokud je nastavena, agent se pokusí nainstalovat a načíst ovladač jádra RDMA, který odpovídá verzi firmwaru na podkladovém hardwaru.

**Os. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Toto nastavení konfiguruje časový limit SCSI v sekundách na disku operačního systému a datových jednotkách. Pokud není nastavena, jsou použity výchozí hodnoty systému.

**Os. OpensslPath:**  
```
Type: String  
Default: None
```
Toto nastavení lze použít k určení alternativní cesty pro binární soubor openssl, který se má použít pro kryptografické operace.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Pokud je nastavena, agent používá tento proxy server pro přístup k Internetu. 

**Automatická aktualizace.Povoleno**
```
Type: Boolean
Default: y
```
Povolení nebo zakázání automatické aktualizace pro zpracování stavu cíle. výchozí hodnota je povolena.



## <a name="ubuntu-cloud-images"></a>Obrázky ubuntu cloud
Ubuntu Cloud Images využívají [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) k provádění mnoha konfiguračních úloh, které by jinak spravoval agent Azure Linux. Platí tyto rozdíly:

* **Zřizování.Povoleno** výchozí hodnoty "n" na Ubuntu Cloud Images, které používají cloud-init k provádění úloh zřizování.
* Následující konfigurační parametry nemají žádný vliv na Obrázky Cloud Ubuntu, které používají cloud-init ke správě disku prostředků a odhození místa:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Souborový systém**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Další informace najdete v následujících prostředcích pro konfiguraci přípojného bodu disku prostředků a odhození místa na obrázcích Ubuntu Cloud Images během zřizování:
  
  * [Ubuntu Wiki: Konfigurace odkládacích oddílů](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Vkládání vlastních dat do virtuálního počítače Azure](../windows/classic/inject-custom-data.md)

