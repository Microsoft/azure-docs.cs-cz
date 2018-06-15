---
title: Virtuální počítač Azure Linux Agent přehled | Microsoft Docs
description: Informace o instalaci a konfiguraci agenta systému Linux (příkaz waagent) ke správě virtuálního počítače interakci s Kontroleru prostředků infrastruktury Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
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
ms.author: szark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be3955c670382af1a2b558e8e7d656ca5a1f353d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30839032"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Informace o používání Azure Linux Agent
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Úvod
Microsoft Azure Linux Agent (příkaz waagent) spravuje Linux & FreeBSD zřizování a virtuálních počítačů interakci s Kontroleru prostředků infrastruktury Azure. Poskytuje následující funkce pro systémy Linux a FreeBSD IaaS nasazení:

> [!NOTE]
> Viz Azure Linux agent [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md) další podrobnosti.
> 
> 

* **Zřizování bitové kopie**
  
  * Vytvoření uživatelského účtu
  * Konfigurace typů ověřování SSH
  * Nasazení veřejné klíče SSH a páry klíčů
  * Nastavení názvu hostitele
  * Publikování název hostitele pro platformu DNS
  * Vytváření sestav otisk klíče SSH hostitele pro platformu
  * Správa prostředků disku
  * Formátování a připojování prostředků disku
  * Konfigurace velikosti odkládacího souboru
* **Sítě**
  
  * Spravuje trasy zlepšit kompatibilitu s servery DHCP platformy
  * Zajišťuje stabilitu název síťového rozhraní
* **Kernel**
  
  * Nakonfiguruje virtuální technologie NUMA (zakázat jádra < 2.6.37)
  * Využívá šifrování technologie Hyper-V pro /dev/random
  * Nakonfiguruje SCSI vypršení časových limitů pro kořenové zařízení (který může být vzdálený)
* **Diagnostika**
  
  * Přesměrování konzoly sériového portu
* **Nasazení SCVMM**
  
  * Zjišťuje a bootstraps agenta nástroje VMM pro Linux v prostředí System Center Virtual Machine Manager 2012 R2
* **Rozšíření virtuálního počítače**
  
  * Vložit součásti autorem je do virtuálního počítače s Linuxem (IaaS) Chcete-li povolit softwaru a konfigurace automatizace Microsoftu a partnerů.
  * Odkaz na implementaci rozšíření virtuálního počítače na [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Komunikace
Informace o toku od platformy agenta dojde k prostřednictvím dva kanály:

* Při spouštění počítače připojit DVD pro nasazení IaaS. Tento disk DVD zahrnuje kompatibilní se standardem OVF konfigurační soubor, který obsahuje všechny informace o zřizování než skutečná keypairs SSH.
* Koncový bod TCP vystavení rozhraní REST API použít k získání nasazení a konfiguraci topologie.

## <a name="requirements"></a>Požadavky
Tyto systémy byly testovány a jsou známé pro práci s Azure Linux Agent:

> [!NOTE]
> Tento seznam se můžou lišit od oficiálního seznam podporovaných systémů na platformě Microsoft Azure podle postupu popsaného tady: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Jiné podporované systémy:

* FreeBSD 10 + (Azure Linux Agent v2.0.10 +)

Agenta systému Linux, aby mohl správně fungovat závisí na některé balíčky systému:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Nástroje systému souborů: čtvrceny sfdisk, fdisk, mkfs,
* Nástroje pro hesla: chpasswd, sudo
* Nástroje pro zpracování textu: menšit grep
* Síťové nástroje: trasy protokolu ip
* Podpora jádra pro připojení UDF systémy.

## <a name="installation"></a>Instalace
Instalace pomocí ot. / min nebo bázi DEB balíček z úložiště balíčků vaší distribuce je upřednostňovaný způsob instalace a upgrade Azure Linux Agent. Všechny [schválené distribuční zprostředkovatelé](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) balíček Azure Linux agent integrovat do svých bitové kopie a úložiště.

V dokumentaci v [Azure Linux Agent úložišti na Githubu](https://github.com/Azure/WALinuxAgent) pro Upřesnit možnosti instalace, například při instalaci ze zdroje nebo vlastní umístění nebo předpony.

## <a name="command-line-options"></a>Možnosti příkazového řádku
### <a name="flags"></a>Příznaky
* verbose: zvýšit podrobnost zadaný příkaz
* Vynutit: přeskočit interaktivní potvrzení pro některé příkazy

### <a name="commands"></a>Příkazy
* Nápověda: seznam podporovaných příkazů a příznaky.
* deprovision: Pokus o vyčištění systému a nastavit jej jako vhodný pro zřizování znovu. Tuto operaci odstraněny následující:
  
  * Všechny klíče SSH hostitele (Pokud Provisioning.RegenerateSshHostKeyPair 'y' v konfiguračním souboru)
  * Konfigurace názvový server v /etc/resolv.conf
  * Kořenové heslo z/etc/shadow (Pokud Provisioning.DeleteRootPassword 'y' v konfiguračním souboru)
  * V mezipaměti klienta zapůjčení DHCP
  * Resetuje název hostitele na localhost.localdomain

> [!WARNING]
> Zrušení zřízení nezaručuje, že bitová kopie je nezaškrtnuté všech citlivých informací a je určená pro opětovnou distribuci.
> 
> 

* deprovision + uživatele: provede vše pod - deprovision (výše) a taky odstraní poslední účet zřízení uživatele (získaný z /var/lib/waagent) a související data. Tento parametr je při jeho rušení obrázek, který byl dříve zřizování v Azure, může být zachycen a znovu použít.
* verze: Zobrazí verzi příkaz waagent
* serialconsole: nakonfiguruje GRUB označit ttyS0 (první sériového portu) jako spouštěcí konzoly. To zajišťuje, že jsou protokoly spuštění jádra posílají do sériového portu a k dispozici pro ladění.
* Démon: Spusťte příkaz waagent jako démon ke správě interakci s platformou. Tento argument je určena k příkaz waagent ve skriptu příkaz waagent init.
* spustit: Spusťte příkaz waagent jako proces na pozadí

## <a name="configuration"></a>Konfigurace
Konfigurační soubor (nebo etc/waagent.conf) akce příkaz waagent ovládací prvky. Ukázkový soubor konfigurace je zobrazena níže:

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

Možnosti konfigurace jsou podrobně popsány v níže. Možnosti konfigurace jsou tři typy; Logická hodnota, řetězec nebo celé číslo. Možnosti konfigurace Boolean lze zadat jako "y" nebo "n". Speciální klíčové slovo "Žádný" může být použita pro některé řetězec typ konfigurace položky podle popisu níže.

**Provisioning.Enabled:**  
Typ: logická hodnota  
Výchozí: y

To umožňuje uživatelům povolit nebo zakázat funkci zřizování v agentovi. Platné hodnoty jsou "y" nebo "n". Pokud zřizování je zakázaná, hostitele a uživatel klíčů SSH na obrázku jsou zachovány a veškeré zadané ve službě Azure zřizování rozhraní API konfigurace je ignorována.

> [!NOTE]
> `Provisioning.Enabled` Parametr výchozí hodnota je "n" Image Ubuntu cloudu, který použít cloudové init pro zřizování.
> 
> 

**Provisioning.DeleteRootPassword:**  
Typ: logická hodnota  
Výchozí: n

Pokud je sada, kořenové heslo v souboru/etc/stínové vymazat během procesu zřizování.

**Provisioning.RegenerateSshHostKeyPair:**  
Typ: logická hodnota  
Výchozí: y

Pokud během procesu zřizování z etc/ssh/se odstraní sady, všechny hostitele páry klíčů SSH (ecdsa, dsa a rsa). A je generována jeden nový pár klíčů.

Typ šifrování pro nový pár klíčů lze konfigurovat v položce Provisioning.SshHostKeyPairType. Upozorňujeme, že některé distribuce bude znovu vytvořit páry klíčů SSH pro všechny chybějící typy šifrování při restartování démon procesu SSH (například při restartování).

**Provisioning.SshHostKeyPairType:**  
Typ: Řetězec  
Výchozí: rsa

To je možné nastavit na typ algoritmus šifrování, který je podporován proces démon programu SSH na virtuálním počítači. Obvykle podporované hodnoty jsou "rsa", "dsa" a "ecdsa". Všimněte si, že "putty.exe" v systému Windows nepodporuje "ecdsa". Ano Pokud máte v úmyslu použít pro připojení k nasazení Linux putty.exe v systému Windows, použijte "rsa" nebo "dsa".

**Provisioning.MonitorHostName:**  
Typ: logická hodnota  
Výchozí: y

Pokud nastavíte, příkaz waagent bude monitorovat Linux virtuálního počítače pro název hostitele změny (jak vrácené příkazem "název hostitele") a automaticky aktualizovat v bitové kopii, aby odrážely změny konfigurace sítě. Pro vkládání změnu názvu na servery DNS, sítě restartuje ve virtuálním počítači. To způsobí Stručný postup ke ztrátě připojení k Internetu.

**Provisioning.DecodeCustomData**  
Typ: logická hodnota  
Výchozí: n

Pokud nastavíte, příkaz waagent bude dekódovat CustomData z formátu Base64.

**Provisioning.ExecuteCustomData**  
Typ: logická hodnota  
Výchozí: n

Pokud nastavíte, příkaz waagent provede CustomData po zřízení.

**Provisioning.AllowResetSysUser** typ: Boolean výchozí: n

Tato možnost umožňuje heslo pro uživatele sys resetování; Výchozí je zakázána.

**Provisioning.PasswordCryptId**  
Typ: Řetězec  
Výchozí: 6

Algoritmus používaný crypt při generování hodnoty hash hesla.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
Typ: Řetězec  
Výchozí: 10

Délka náhodných salt používá při generování hodnoty hash hesla.

**ResourceDisk.Format:**  
Typ: logická hodnota  
Výchozí: y

Pokud nastavíte, prostředků disku poskytované platformou bude formátu a připojené pomocí příkaz waagent, pokud je typ systému souborů požadované uživatelem v "ResourceDisk.Filesystem" jakoukoli jinou hodnotu než "ntfs". Jeden oddíl typu Linux (83) bude k dispozici na disku. Všimněte si, že tento oddíl nebude naformátovaný, pokud ho můžete úspěšně připojit.

**ResourceDisk.Filesystem:**  
Typ: Řetězec  
Výchozí: ext4

Určuje typ systému souborů pro prostředek disku. Podporované hodnoty se liší podle distribuce systému Linux. Pokud je řetězec X, potom mkfs. X by měla být k dispozici na bitovou kopii systému Linux. Bitové kopie SLES 11 by měl obvykle používají 'ext3'. Obrázky FreeBSD tady by měl použít 'ufs2'.

**ResourceDisk.MountPoint:**  
Typ: Řetězec  
Výchozí hodnota: / mnt nebo prostředků 

Určuje cestu, kde je prostředek disk připojený. Všimněte si, že je disk prostředků *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit.

**ResourceDisk.MountOptions**  
Typ: Řetězec  
Výchozí: žádná

Určuje možnosti připojení disku má být předán příkazu -o připojení. Toto je čárkami oddělený seznam hodnot, např. 'nodev, nosuid'. V tématu mount(8) podrobnosti.

**ResourceDisk.EnableSwap:**  
Typ: logická hodnota  
Výchozí: n

Pokud nastavení odkládacího souboru (/ swapfile) je vytvořen na prostředku disku a přidat do odkládacího souboru v systému.

**ResourceDisk.SwapSizeMB:**  
Typ: celé číslo  
Výchozí: 0

Velikost odkládacího souboru v megabajtech.

**Logs.Verbose:**  
Typ: logická hodnota  
Výchozí: n

Pokud je boosted sady podrobností protokolu. Příkaz Waagent /var/log/waagent.log v protokolech a využívá funkce logrotate systému otočení protokoly.

**OS.EnableRDMA**  
Typ: logická hodnota  
Výchozí: n

Pokud nastavíte, agent se pokusí nainstalovat a pak můžete načíst ovladač jádra RDMA, která odpovídá verzi firmwaru v základní hardware.

**OPERAČNÍ SYSTÉM. RootDeviceScsiTimeout:**  
Typ: celé číslo  
Výchozí: 300

Tím se nakonfiguruje SCSI časový limit v sekundách na disku a datové jednotky operačního systému. Pokud není nastavena, systém, které budou použity výchozí hodnoty.

**OS.OpensslPath:**  
Typ: Řetězec  
Výchozí: žádná

Tímto lze zadat alternativní cestu ke openssl binární pro kryptografické operace.

**HttpProxy.Host, HttpProxy.Port**  
Typ: Řetězec  
Výchozí: žádná

Pokud nastavíte, agent použije tento proxy server pro přístup k Internetu. 

**AutoUpdate.Enabled** typ: Boolean výchozí: y

Povolit nebo zakázat automatickou aktualizaci pro cíl stav zpracování; Výchozí hodnota je povolena.



## <a name="ubuntu-cloud-images"></a>Ubuntu cloudu obrázků
Všimněte si, že Ubuntu cloudu Image využívat [cloudu init](https://launchpad.net/ubuntu/+source/cloud-init) mnoho úkoly konfigurace, které by jinak spravovány nástrojem Azure Linux Agent.  Je třeba počítat následující rozdíly:

* **Provisioning.Enabled** výchozí hodnota je "n" Image Ubuntu cloudu, který použít cloudové init k provádění úloh pro zřízení.
* Následující konfigurační parametry nemají vliv na cloudu bitové kopie Ubuntu, použít cloudové init ke správě prostředků disku a záměna prostoru:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**
* Podrobnosti najdete v následujících zdrojích nakonfigurovat prostředek disku přípojného bodu a záměna prostoru Ubuntu cloudu Image při zřizování:
  
  * [Ubuntu Wiki: Konfigurace oddílů Swap](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Vložení vlastní Data do virtuálního počítače Azure](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

