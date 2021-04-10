---
title: Přehled agenta virtuálního počítače Azure Linux
description: Naučte se instalovat a konfigurovat agenta pro Linux (waagent), abyste mohli spravovat interakci virtuálních počítačů s řadičem prostředků infrastruktury Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 10/17/2016
ms.openlocfilehash: e8851ddd5211536394614727d990a2b52d32bfcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565373"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Porozumění a použití agenta Azure Linux

Agent Microsoft Azure Linux (waagent) spravuje systémy Linux & FreeBSD a interakci virtuálních počítačů s řadičem prostředků infrastruktury Azure. Kromě agenta pro Linux, který poskytuje funkce zřizování, Azure taky nabízí možnost použití Cloud-init pro některé Linux operačních systémech. Agent pro Linux poskytuje pro nasazení pro Linux a FreeBSD IaaS následující funkce:

> [!NOTE]
> Další informace najdete v [souboru Readme](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Zřizování imagí**
  
  * Vytvoření uživatelského účtu
  * Konfigurace typů ověřování SSH
  * Nasazení veřejných klíčů SSH a párů klíčů
  * Nastavení názvu hostitele
  * Publikování názvu hostitele na platformě DNS platformy
  * Vytváření sestav otisků klíčů hostitele SSH pro platformu
  * Správa disků prostředků
  * Formátování a připojení disku prostředků
  * Konfigurace odkládacího prostoru
* **Sítě**
  
  * Spravuje trasy pro zlepšení kompatibility s platformami serverů DHCP.
  * Zajišťuje stabilitu názvu síťového rozhraní.
* **jádro**
  
  * Nakonfiguruje virtuální technologii NUMA (zakázat pro jádro <`2.6.37` ).
  * Spotřebovává entropii technologie Hyper-V pro/dev/random.
  * Konfiguruje časové limity SCSI pro kořenové zařízení (které by mohlo být vzdálené).
* **Diagnostika**
  
  * Přesměrování konzoly na sériový port
* **Nasazení SCVMM**
  
  * Zjistí a spustí agenta VMM pro Linux při spuštění v prostředí System Center Virtual Machine Manager 2012 R2.
* **Rozšíření virtuálního počítače**
  
  * Vložení součásti, kterou vytvořila Microsoft a partneři, do virtuálního počítače se systémem Linux (IaaS), aby bylo možné povolit automatizaci softwaru a konfigurace
  * Implementace odkazu na rozšíření virtuálních počítačů na [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Komunikace
Tok informací z platformy k agentům probíhá prostřednictvím dvou kanálů:

* Spouštěcí DVD připojený k IaaS nasazení. Tento disk DVD obsahuje konfigurační soubor kompatibilní s OVF, který obsahuje informace o všech zřizováních, jiné než skutečné páry klíčů SSH.
* Koncový bod TCP vystavuje REST API, který se používá k získání konfigurace nasazení a topologie.

## <a name="requirements"></a>Požadavky
Následující systémy byly testovány a jsou známy pro práci s agentem Azure Linux:

> [!NOTE]
> Tento seznam se může lišit od oficiálního seznamu [podporovaných distribuce](../linux/endorsed-distros.md).
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Jiné podporované systémy:

* FreeBSD 10 + (agent Azure Linux v 2.0.10 +)

Agent pro Linux závisí na některých systémových balíčcích, aby fungoval správně:

* Python 2.6 nebo novější
* OpenSSL 1.0 nebo novější
* OpenSSH 5.3 nebo novější
* Nástroje systému souborů: sfdisk, fdisk, mkfs, částečněd
* Nástroje pro heslo: chpasswd, sudo
* Nástroje pro zpracování textu: sed, grep
* Síťové nástroje: IP-Route
* Podpora jádra pro připojení systémů souborů UDF.

Ujistěte se, že váš virtuální počítač má přístup k IP adrese 168.63.129.16. Další informace najdete v tématu [co je IP adresa 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="installation"></a>Instalace
Upřednostňovanou metodou instalace a upgrade agenta Azure Linux je instalace použití balíčku ot./min. nebo balíčku DEB z úložiště balíčků distribuce. Všichni [poskytovatelé schválené distribuce](../linux/endorsed-distros.md) integrují balíček agenta Azure Linux do svých imagí a úložišť.

Další možnosti instalace najdete v dokumentaci v [úložišti agenta Azure Linux na GitHubu](https://github.com/Azure/WALinuxAgent) , kde najdete rozšířené možnosti instalace, jako je například instalace ze zdroje nebo vlastní umístění nebo předpony.

## <a name="command-line-options"></a>Možnosti Command-Line
### <a name="flags"></a>Příznaky
* verbose: zvýšit podrobnost zadaného příkazu
* Force: přeskočit interaktivní potvrzení u některých příkazů

### <a name="commands"></a>Příkazy
* Help: vypíše podporované příkazy a příznaky.
* zrušení zřízení: pokus o vyčištění systému a jeho vhodné pro opětovné zřízení. Následující operace odstraní:
  
  * Všechny klíče hostitele SSH (Pokud zřizování. RegenerateSshHostKeyPair je v konfiguračním souboru ' y ')
  * Konfigurace názvový server v/etc/resolv.conf
  * Kořenové heslo z/etc/Shadow (Pokud zřizování. DeleteRootPassword je v konfiguračním souboru ' y ')
  * Zapůjčení klienta DHCP v mezipaměti
  * Obnoví název hostitele na localhost. localdomain.

> [!WARNING]
> Zrušení zřízení nezaručuje, že se image vymaže ze všech citlivých informací a vhodná pro opětovnou distribuci.
> 
> 

* zrušení zřízení + uživatel: provede vše za zrušení zřízení (výše) a odstraní také naposledy zřízený uživatelský účet (získaný z/var/lib/waagent) a přidružená data. Tento parametr je při rušení zřizování image, která se dřív zřídila v Azure, aby se mohla zachytit a znovu použít.
* Version (verze): Zobrazuje verzi waagent
* serialconsole: konfiguruje GRUB tak, aby označil ttyS0 (první sériový port) jako spouštěcí konzolu. Tím se zajistí, že se protokoly spouštění jádra odesílají do sériového portu a zpřístupní se pro ladění.
* démon: Spusťte waagent jako démona pro správu interakce s platformou. Tento argument je zadán jako waagent ve skriptu init waagent.
* spustit: spustit waagent jako proces na pozadí

## <a name="configuration"></a>Konfigurace
Konfigurační soubor (/etc/waagent.conf) řídí akce waagent. V následujícím příkladu vidíte Ukázkový konfigurační soubor:

```config
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

Jsou popsány následující různé možnosti konfigurace. Možnosti konfigurace jsou tři typy; Logická hodnota, řetězec nebo celé číslo. Logické možnosti konfigurace lze zadat jako "y" nebo "n". Klíčové slovo None nelze použít pro některé položky konfigurace typu řetězce jako následující podrobnosti:

**Zřizování. povoleno:**  
```txt
Type: Boolean  
Default: y
```
To uživateli umožňuje povolit nebo zakázat funkce zřizování v agentovi. Platné hodnoty jsou "y" nebo "n". Pokud je zřizování zakázané, zachovají se klíče hostitele SSH a uživatele v imagi a veškerá konfigurace zadaná v rozhraní API zřizování Azure se ignoruje.

> [!NOTE]
> `Provisioning.Enabled`Parametr má ve výchozím nastavení hodnotu "n" na Ubuntu cloudových imagí, které pro zřizování používají Cloud-init.
> 
> 

**Zřizování. DeleteRootPassword:**  
```txt
Type: Boolean  
Default: n
```
Pokud je nastaveno, bude během procesu zřizování vymazáno kořenové heslo v souboru/etc/shadow.

**Zřizování. RegenerateSshHostKeyPair:**  
```txt
Type: Boolean  
Default: y
```
Při nastavení se během procesu zřizování z/etc/ssh/. odstraní všechny páry klíčů hostitele SSH (ECDSA, DSA a RSA). A vygeneruje se jeden nový pár klíčů.

Typ šifrování pro nový pár klíčů lze konfigurovat pomocí položky zřizování. SshHostKeyPairType. Některé distribuce znovu vytvoří páry klíčů SSH pro všechny chybějící typy šifrování při restartování démona SSH (například při restartování počítače).

**Zřizování. SshHostKeyPairType:**  
```txt
Type: String  
Default: rsa
```
To může být nastaveno na typ šifrovacího algoritmu, který je podporován démonem SSH na virtuálním počítači. Obvykle jsou podporované hodnoty "RSA", "DSA" a "ECDsa". "putty.exe" ve Windows nepodporuje "ECDsa". Pokud tedy chcete použít putty.exe ve Windows pro připojení k nasazení Linux, použijte šifrování RSA nebo DSA.

**Zřizování. MonitorHostName:**  
```txt
Type: Boolean  
Default: y
```
Pokud je tato možnost nastavená, waagent monitoruje virtuální počítač Linux pro změny názvu hostitele (vrácených příkazem "hostname") a automaticky aktualizuje konfiguraci sítě v imagi tak, aby odrážela změnu. Aby bylo možné doručit změnu názvu na servery DNS, budou síťové služby restartovány ve virtuálním počítači. Výsledkem je krátká ztráta připojení k Internetu.

**Zřizování. DecodeCustomData**  
```txt
Type: Boolean  
Default: n
```
Pokud je nastaveno, waagent dekódování CustomData z base64.

**Provisioning.ExecuteCustomData**  
```txt
Type: Boolean  
Default: n
```
Pokud je nastaveno, waagent po zřízení spustí CustomData.

**Zřizování. AllowResetSysUser**
```txt
Type: Boolean
Default: n
```
Tato možnost umožňuje resetování hesla pro uživatele sys; Výchozí hodnota je zakázaná.

**Zřizování. PasswordCryptId**  
```txt
Type: String  
Default: 6
```
Algoritmus používaný algoritmem crypt při generování hodnoty hash hesla  
 1. MD5  
 2a – Blowfish  
 5. SHA-256  
 6. SHA-512  

**Zřizování. PasswordCryptSaltLength**  
```txt
Type: String  
Default: 10
```
Délka náhodné soli použitá při generování hodnoty hash hesla

**ResourceDisk. Format:**  
```txt
Type: Boolean  
Default: y
```
Pokud je tato možnost nastavená, disk prostředků poskytovaný platformou se naformátuje a připojí přes waagent, pokud typ systému souborů požadovaný uživatelem v souboru ResourceDisk. FileSystem je jiný než NTFS. Na disku je k dispozici jeden oddíl typu Linux (83). Tento oddíl není naformátován, pokud jej lze úspěšně připojit.

**ResourceDisk. FileSystem:**  
```txt
Type: String  
Default: ext4
```
Určuje typ systému souborů pro disk prostředků. Podporované hodnoty se liší podle distribuce systému Linux. Pokud je řetězec X, pak mkfs. X by se měla nacházet v imagi pro Linux. Image SLES 11 by typicky měly používat "ext3". Image FreeBSD by měly používat UFS2.

**ResourceDisk. přípojný bod:**  
```txt
Type: String  
Default: /mnt/resource 
```
Určuje cestu, ke které je připojen disk prostředků. Disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění.

**ResourceDisk.MountOptions**  
```txt
Type: String  
Default: None
```
Určuje možnosti připojení disku, které se mají předat příkazu Mount-o. Toto je seznam hodnot oddělených čárkou, např. 'nodev,nosuid'. Podrobnosti najdete v tématu Mount (8).

**ResourceDisk.EnableSwap:**  
```txt
Type: Boolean  
Default: n
```
Pokud je nastavená, na disku prostředků se vytvoří odkládací soubor (/swapfile) a přidají se do systémového odkládacího prostoru.

**ResourceDisk.SwapSizeMB:**  
```txt
Type: Integer  
Default: 0
```
Velikost odkládacího souboru v megabajtech

**Log. verbose:**  
```txt
Type: Boolean  
Default: n
```
Při nastavení se zvyšuje úroveň podrobností protokolu. Protokoly waagent do/var/log/waagent.log a využívají funkci systému logrotate pro otočení protokolů.

**Jiného. EnableRDMA**  
```txt
Type: Boolean  
Default: n
```
Při nastavení se agent pokusí nainstalovat a pak načíst ovladač jádra RDMA, který odpovídá verzi firmwaru na základním hardwaru.

**Jiného. RootDeviceScsiTimeout:**  
```txt
Type: Integer  
Default: 300
```
Toto nastavení nakonfiguruje časový limit SCSI v sekundách pro disk s operačním systémem a datové jednotky. Pokud není nastavená, použijí se výchozí nastavení systému.

**Jiného. OpensslPath:**  
```txt
Type: String  
Default: None
```
Toto nastavení se dá použít k zadání alternativní cesty pro binární soubor OpenSSL, který se má použít pro kryptografické operace.

**HttpProxy. Host, HttpProxy. port**  
```txt
Type: String  
Default: None
```
Pokud je nastaveno, Agent použije tuto proxy server k přístupu k Internetu. 

**AutoUpdate. Enabled**
```txt
Type: Boolean
Default: y
```
Povolí nebo zakáže automatickou aktualizaci pro zpracování stavu cíle; Výchozí hodnota je povolena.



## <a name="ubuntu-cloud-images"></a>Ubuntu cloudové image
Cloudové image Ubuntu využívají [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) k provedení mnoha úloh konfigurace, které by jinak bylo možné spravovat agentem Azure Linux. Platí následující rozdíly:

* **Zřizování. povolené** výchozí hodnoty: n v Ubuntu cloudových imagích, které k provádění úkolů zřizování používají Cloud-init.
* Následující konfigurační parametry nemají žádný vliv na Ubuntu cloudových imagí, které používají Cloud-init ke správě disku prostředků a odkládacího prostoru:
  
  * **ResourceDisk. Format**
  * **ResourceDisk. FileSystem**
  * **ResourceDisk. přípojný bod**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Další informace najdete v následujících zdrojích konfigurace přípojného bodu disku prostředku a záměna místa na Ubuntu cloudových imagí během zřizování:
  
  * [Ubuntu wiki: konfigurace odkládacích oddílů](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Vkládání vlastních dat do virtuálního počítače Azure](../windows/tutorial-automate-vm-deployment.md)