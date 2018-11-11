---
title: Přehled agenta virtuálního počítače Azure s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat agenta pro Linux (waagent) ke správě interakcí virtuálního počítače s Kontrolerem prostředků infrastruktury Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c19d32f6c6f491a91ba6c2219be9fd016b5ec34
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243875"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Princip a používání agenta Azure Linux

Microsoft Azure Linux Agent (waagent) spravuje Linux a FreeBSD zřizování a interakci virtuálních počítačů s Kontrolerem prostředků infrastruktury Azure. Kromě zajištění funkcí, zřízení linuxového agenta Azure navíc poskytuje možnost používat cloud-init pro některé operační systémy Linux. Agenta pro Linux poskytuje následující funkce pro systémy Linux a FreeBSD IaaS nasazení:

> [!NOTE]
> Další informace najdete v tématu [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Zřizování imagí**
  
  * Vytvoření uživatelského účtu
  * Konfigurace typů ověřování SSH
  * Nasazení veřejného klíče SSH a páry klíčů
  * Nastavení názvu hostitele
  * Publikování na platformě DNS název hostitele
  * Generování sestav kryptografický otisk klíče hostitele SSH na platformě
  * Správa prostředků disku
  * Formátování a připojování disku prostředků
  * Konfigurace velikosti odkládacího souboru
* **Sítě**
  
  * Spravuje trasy zlepšit kompatibilitu s servery DHCP platformy
  * Zajišťuje stabilitu název síťového rozhraní
* **Kernel**
  
  * Nakonfiguruje virtuální uzel NUMA (zakázat pro jádra <`2.6.37`)
  * Využívá technologii Hyper-V pro /dev/random
  * Nakonfiguruje SCSI vypršení časových limitů u kořenové zařízení (které by mohly být vzdálený)
* **Diagnostika**
  
  * Přesměrování konzoly sériového portu
* **SCVMM nasazení**
  
  * Zjišťuje a bootstraps agenta VMM pro Linux při spuštění v prostředí System Center Virtual Machine Manager 2012 R2
* **Rozšíření virtuálního počítače**
  
  * Vložení součástí vytvořené Microsoftem a partnery do virtuálního počítače s Linuxem (IaaS) umožňující softwaru a konfigurace služby automation
  * Referenční implementace rozšíření virtuálního počítače na [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Komunikace
Tok informací z platformy k agentovi dojde k přes dva kanály:

* Čas spuštění připojené DVD pro nasazení IaaS. Tento disk DVD zahrnuje CLS OVF konfigurační soubor, který obsahuje všechny informace o zřizování než skutečné keypairs SSH.
* Koncový bod TCP vystavení rozhraní REST API používá k získání nasazení a konfiguraci topologie.

## <a name="requirements"></a>Požadavky
Tyto systémy byly testovány a jsou známé pro práci s agentem Azure Linux:

> [!NOTE]
> Tento seznam může lišit od oficiálního seznamu podporovaných systémech na platformě Microsoft Azure, jak je popsáno zde: [http://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
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

Další podporované systémy:

* FreeBSD 10 + (Azure Linux Agent v2.0.10 +)

Agenta pro Linux, závisí na některé balíčky systém mohl fungovat správně:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Nástroje systému souborů: čtvrceny sfdisk fdisk, mkfs,
* Nástroje pro heslo: chpasswd sudo
* Nástroje pro zpracování textu: sed grep
* Sítě nástroje: směrování ip
* Podpora jádra pro připojení systémy souborů UDF.

## <a name="installation"></a>Instalace
Instalace pomocí RPM nebo DEB balíček z úložiště balíčků vaší distribuce je preferovanou metodu instalace a upgradování agenta Azure Linux. Všechny [poskytovatelé distribuce schválené pro](../linux/endorsed-distros.md) balíček agenta Azure Linux integrovat do své Image a úložiště.

V dokumentaci [agenta Azure Linux úložišti na Githubu](https://github.com/Azure/WALinuxAgent) pro Upřesnit možnosti instalace, například při instalaci ze zdroje nebo do vlastního umístění nebo předpony.

## <a name="command-line-options"></a>Možnosti příkazového řádku
### <a name="flags"></a>příznaky
* verbose: zvyšte úroveň podrobností zadaný příkaz
* Vynutit: přeskočit interaktivní potvrzení pro některé příkazy

### <a name="commands"></a>Příkazy
* Nápověda: seznam podporovaných příkazů a příznaky.
* zrušení zřízení: Pokus o vyčištění systému a že je vhodný pro neukončil. Následující operace odstranění:
  
  * Všechny klíče hostitele SSH (Pokud Provisioning.RegenerateSshHostKeyPair "y" v konfiguračním souboru)
  * Konfigurace názvového serveru v /etc/resolv.conf
  * Kořenové heslo z/etc/shadow (Pokud je u Provisioning.DeleteRootPassword hodnotu y' v konfiguračním souboru)
  * V mezipaměti zapůjčení DHCP klientů
  * Resetuje název hostitele na localhost.localdomain

> [!WARNING]
> Zrušení zřízení nezaručuje, že bitová kopie je vymažou všechny citlivých informací a vhodný pro distribuci.
> 
> 

* zrušení zřízení + uživatel: provádí všechno, co v – zrušení zřízení (viz výše) a také odstranění posledního zřízeného uživatelského účtu (získané z /var/lib/waagent) a přidružená data. Tento parametr je při zrušení zřizování bitovou kopii, která byla dříve zřizování v Azure, tak může zachytit a znovu použít.
* verze: Zobrazí verzi waagent
* serialconsole: nakonfiguruje GRUB k označení ttyS0 (první sériový port) jako spouštěcí konzoly. Tím se zajistí, že jsou protokoly spouštění jádra odesílat sériového portu a k dispozici pro ladění.
* Démon procesu: spustit waagent jako démon ke správě interakci s platformou. Tento argument je určena k waagent ve skriptu waagent init.
* Start: spuštění waagent jako proces na pozadí

## <a name="configuration"></a>Konfigurace
Konfigurační soubor (/ etc/waagent.conf) řídí akce waagent. Následuje ukázkový soubor konfigurace:

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

Následující možnosti konfigurace jsou popsány. Možnosti konfigurace jsou tři typy; Logická hodnota, řetězec nebo celé číslo. Logická konfigurační možnosti lze zadat jako "y" nebo "n". Speciální klíčové slovo, které "None" mohou být použity pro některé položky konfigurace typu řetězec jako následující podrobnosti:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
To umožňuje uživatelům povolit nebo zakázat funkci zřizování v agentovi. Platné hodnoty jsou "y" nebo "n". Pokud zřizování je zakázaná, klíče SSH hostitele a uživatel na obrázku jsou zachovány a veškeré zadané v Azure, rozhraní API zřizování konfigurace je ignorována.

> [!NOTE]
> `Provisioning.Enabled` Parametr má výchozí hodnotu "n" Cloud imagemi Ubuntu, který pomocí cloud-init pro zřizování.
> 
> 

**U Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Pokud sada, kořenové heslo v souboru/etc/shadow se vymažou během procesu zřizování.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Pokud sadu, všechny hostitele páry klíčů SSH (ecdsa, dsa a rsa) jsou během procesu zřizování z/etc/ssh/odstranit. A vygeneruje se jeden nový pár klíčů.

Typ šifrování pro nový pár klíčů je možné konfigurovat v položce Provisioning.SshHostKeyPairType. Při restartování proces démon programu SSH (třeba po restartování), některých distribucích znovu vytvořit páry klíčů SSH pro všechny chybějící typy šifrování.

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
To lze nastavit na typ šifrovací algoritmus, který je podporován proces démon programu SSH na virtuálním počítači. Obvykle podporované hodnoty jsou "rsa", "dsa" a "ecdsa". "putty.exe" na Windows nepodporuje "ecdsa". Ano Pokud máte v úmyslu použít putty.exe ve Windows pro připojení k nasazení systému Linux, použijte "rsa" nebo "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Pokud sada waagent monitoruje virtuálním počítači s Linuxem pro název hostitele změny (jako vrácené příkazem "hostname") a automaticky aktualizujte konfigurace sítí na obrázku tak, aby odrážely změny. Pro vkládání změnu názvu na servery DNS, sítě restartování virtuálního počítače. Stručný postup vede ke ztrátě připojení k Internetu.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Pokud sada waagent dekóduje CustomData z formátu Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Pokud sada waagent provede CustomData po zřízení.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Tato možnost umožňuje heslo pro uživatele sys resetovat; Výchozí je zakázaná.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
{Algoritmus používaný crypt při generování hodnoty hash hesla.  
 1 - MD5  
 2a - Blowfish  
 5 – SHA-256  
 6 – SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Délka náhodného hodnota salt používá při generování hodnoty hash hesla.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Pokud nastavíte, disk počítačových prostředků poskytovaný platformou je ve formátu a připojit waagent, pokud je typ systému souborů požadoval uživatel v "ResourceDisk.Filesystem" než "ntfs". Jeden oddíl typu Linux (83) je k dispozici na disku. Tento oddíl není ve formátu, pokud je možné úspěšně připojit.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Určuje typ systému souborů pro disk prostředků. Podporované hodnoty se liší podle Linuxovou distribuci. Pokud je řetězec X, potom mkfs. X musí být k dispozici na image Linuxu. Image SLES 11 použití "ext3". Obrázky FreeBSD používali "ufs2" zde.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Určuje cestu, na které je připojený disk počítačových prostředků. Je disk počítačových prostředků *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Určuje možnosti připojení disku má být předán příkazu -o připojení. Toto je čárkou oddělený seznam hodnot, např. "nodev, nosuid". Zobrazit mount(8) podrobnosti.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Pokud nastavení odkládacího souboru (/ swapfile) je vytvořen na prostředku disku a přidán do odkládacího souboru v systému.

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
Pokud je vylepšené set, úroveň podrobností protokolu. Waagent zaznamená do /var/log/waagent.log, které využívá funkce logrotate systému obměna protokoly.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Pokud sada, agent se pokusí nainstalovat a pak načíst ovladač jádra rozhraní RDMA, který odpovídá verzi firmwaru v základním hardwaru.

**OPERAČNÍ SYSTÉM. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Toto nastavení konfiguruje SCSI časový limit v sekundách na disku a diskových jednotkách operačního systému. Pokud není nastavena, systému, ve kterém jsou použity výchozí hodnoty.

**OS.OpensslPath:**  
```
Type: String  
Default: None
```
Toto nastavení umožňuje zadat alternativní cesty pro binární pro kryptografické operace openssl.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Pokud sada, agent používá tento proxy server pro přístup k Internetu. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Povolí nebo zakáže automatické aktualizace pro cílový stav zpracování; Výchozí hodnota je povoleno.



## <a name="ubuntu-cloud-images"></a>Ubuntu cloudových Imagí
Využití cloudu Imagemi Ubuntu [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) provádět mnoho úloh konfigurace, které by jinak spravovaly pomocí agenta Azure Linux. Platí následující rozdíly:

* **Provisioning.Enabled** výchozí hodnota je "n" Cloud imagemi Ubuntu, který pomocí cloud-init k provedení úloh pro zřízení.
* Následující parametry konfigurace nemají žádný vliv na Ubuntu cloudových Imagí, které pomocí cloud-init ke správě prostředků disku a záměna prostoru:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Další informace najdete v tématu záměna prostoru imagemi Ubuntu cloudu během zřizování a nakonfigurovat prostředek disku přípojného bodu na následujících odkazech:
  
  * [Ubuntu Wiki: Konfigurace oddílů prohození](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Vkládání vlastních dat do virtuálního počítače Azure](../windows/classic/inject-custom-data.md)

