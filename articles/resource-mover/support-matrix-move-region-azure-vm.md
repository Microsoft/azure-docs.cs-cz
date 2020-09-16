---
title: Matice podpory pro přesun virtuálních počítačů Azure do jiné oblasti pomocí Azure Resource stěhovací
description: Přečtěte si podporu pro přesun virtuálních počítačů Azure mezi oblastmi pomocí Azure Resource stěhovací.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: fa71cd502f730844e4f4398d41d06ada56fc2413
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602281"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Podpora pro přesun virtuálních počítačů Azure mezi oblastmi Azure

Tento článek shrnuje podporu a požadavky při přesunu virtuálních počítačů a souvisejících síťových prostředků mezi oblastmi Azure pomocí Resource Centre.

> [!IMPORTANT]
> Prostředek Azure Resource stěhovací je momentálně ve verzi Preview.


## <a name="windows-vm-support"></a>Podpora virtuálních počítačů s Windows

Resource stěhovací podporuje virtuální počítače Azure s těmito operačními systémy Windows.

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2019 | Podporováno pro jádro serveru, server s desktopovým prostředím.
Windows Server 2016  | Podporován jádro serveru, server s desktopovým prostředím.
Windows Server 2012 R2 | Podporuje se.
Windows Server 2012 | Podporuje se.
Windows Server 2008 R2 s aktualizací SP1/SP2 | Podporuje se.<br/><br/> Pro počítače se systémem Windows Server 2008 R2 s aktualizací SP1/SP2 je nutné nainstalovat aktualizaci Windows [Servicing Stack Agent (cestou nadřazené)](https://support.microsoft.com/help/4490628) a [SHA-2 Update](https://support.microsoft.com/help/4474419).  SHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, rozšíření agenta se nenainstaluje nebo upgraduje podle očekávání. Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Podporuje se.
Windows 8.1 (x64) | Podporuje se.
Windows 8 (x64) | Podporuje se.
Windows 7 (x64) s aktualizací SP1 a vyšší | Nainstalujte aktualizaci Windows [Servicing Stack Agent (cestou nadřazené)](https://support.microsoft.com/help/4490628) a [aktualizaci SHA-2](https://support.microsoft.com/help/4474419) na počítače se systémem Windows 7 s aktualizací SP1.  SHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, krok Příprava nebude úspěšný. Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Podpora virtuálních počítačů se systémem Linux

Přesunutí prostředků podporuje virtuální počítače Azure s těmito operačními systémy Linux.

**Operační systém** | **Podrobnosti**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0, 8,1
Server Ubuntu 14,04 LTS | [Podporované verze jádra](#supported-ubuntu-kernel-versions)
Server Ubuntu 16,04 LTS | [Podporovaná verze jádra](#supported-ubuntu-kernel-versions)<br/><br/> Ubuntu servery s použitím ověřování a přihlášení na základě hesla a balíčku Cloud-init ke konfiguraci cloudových virtuálních počítačů můžou mít při převzetí služeb při selhání zakázané přihlášení (v závislosti na konfiguraci Cloud-init). Přihlášení na základě hesla se dá znovu povolit na virtuálním počítači tak, že resetuje heslo z nabídky nastavení > řešení potíží s >em (u virtuálního počítače, u kterého došlo k převzetí služeb při selhání v Azure Portal.
Server Ubuntu 18,04 LTS | [Podporovaná verze jádra](#supported-ubuntu-kernel-versions)
Debian 7 | [Podporované verze jádra](#supported-debian-kernel-versions).
Debian 8 | [Podporované verze jádra](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Podporované verze jádra](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 a 15 SP1. [(Podporované verze jádra)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | 3
SUSE Linux Enterprise Server 11 | OPRAVY
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4 [, 7,5, 7,6, 7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Spuštění jádra kompatibilního s Red Hat nebo nedělitelné podnikové jádro verze 3, 4 & 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Podporované verze jádra Ubuntu

**Vydaná verze** | **Verze jádra** 
--- | --- 
14,04 LTS |  3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure 
16,04 LTS |  4.4.0-21 – obecný pro 4.4.0-171 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné pro 4.15.0-74 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1066 – Azure
18,04 LTS | 4.15.0-20 – obecný pro 4.15.0-74 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0-37 – obecné </br> 5.3.0-19 – Obecné k 5.3.0 – 24 – obecné </br> 4.15.0-1009 – Azure do 4.15.0-1037 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1028 – Azure </br> 5.3.0-1007 – Azure do 5.3.0-1009 – Azure


### <a name="supported-debian-kernel-versions"></a>Podporované verze jádra Debian 

**Vydaná verze** |  **Verze jádra** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 až 3.2.0-6-AMD64, 3.16.0 -0. BPO. 4 – amd64 
Debian 8 |  3.16.0-4-amd64 pro 3.16.0-10-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 11 – amd64 
Debian 8 |  3.16.0-4-amd64 pro 3.16.0-10-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>Podporované verze jádra SUSE Linux Enterprise Server 12 

**Vydaná verze** | **Verze jádra** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3 a SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.34 – Azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>Podporované SUSE Linux Enterprise Server 15 – verze jádra

**Vydaná verze** | **Verze jádra** |
--- |  --- |
SUSE Linux Enterprise Server 15 a 15 SP1 |  Podporují se všechny jádra SUSE 15 a 15.</br></br> 4.12.14-5.5 – Azure na 4.12.14 – 8.22 – Azure |

## <a name="supported-linux-file-systemguest-storage"></a>Podporované systémy souborů Linux/hostované úložiště

* Souborové systémy: ext3, EXT4, XFS, BTRFS
* Správce svazků: LVM2
* Multipath software: Mapovač zařízení


## <a name="supported-vm-compute-settings"></a>Podporovaná výpočetní nastavení virtuálního počítače

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Velikost | Všechny velikosti virtuálních počítačů Azure s alespoň dvěma jádry procesoru a 1 GB paměti RAM | Ověřte [velikosti virtuálních počítačů Azure](../virtual-machines/sizes-general.md).
Skupiny dostupnosti | Aktuálně se nepodporuje. | Pokud přidáte virtuální počítač Azure s nastavenou skupinou dostupnosti do kolekce přesunů s výchozími možnostmi, proces přípravy se nezdařil. Můžete buď přesunout virtuální počítač do zóny dostupnosti, nebo ho přesunout jako virtuální počítač s jednou instancí. Tato nastavení můžete změnit na stránce Upravit vlastnosti cíle.
Zóny dostupnosti | Podporováno | Podporuje se v závislosti na podpoře cílové oblasti.
Image Galerie Azure (publikované Microsoftem) | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Image Galerie Azure (Publikováno třetí stranou)  | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Vlastní image (publikované třetí stranou)| Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Virtuální počítače používající Site Recovery | Nepodporováno | Přesunutí prostředků do oblastí pro virtuální počítače s využitím Site Recovery na back-endu. Pokud již používáte Site Recovery, zakažte replikaci a potom spusťte proces přípravy.
Zásady RBAC | Nepodporováno | Zásady řízení přístupu na základě role (RBAC) na virtuálních počítačích se nekopírují do virtuálního počítače v cílové oblasti.
Rozšíření | Nepodporováno | Rozšíření se nekopírují do virtuálního počítače v cílové oblasti. Nainstalujte je ručně po dokončení přesunutí.


## <a name="supported-vm-storage-settings"></a>Podporovaná nastavení úložiště virtuálního počítače

Tato tabulka shrnuje podporu pro disk s operačním systémem Azure VM, datový disk a dočasný disk. Abyste se vyhnuli problémům s výkonem, je důležité sledovat omezení počtu disků virtuálních počítačů a cíle pro virtuální počítače se systémem [Linux](../virtual-machines/linux/disk-scalability-targets.md) a [Windows](../virtual-machines/windows/disk-scalability-targets.md) .

> [!NOTE]
> Velikost cílového virtuálního počítače by měla být větší nebo rovna zdrojovému virtuálnímu počítači. K ověřování se používají tyto parametry: počet datových disků, počet síťových adaptérů, dostupné procesory a paměť v GB. Pokud se nejedná o chybu, je vydána.


**Komponenta** | **Podpora** | **Podrobnosti**
--- | --- | ---
Maximální velikost disku s operačním systémem | 2048 GB | [Přečtěte si další informace](../virtual-machines/windows/managed-disks-overview.md) o discích virtuálních počítačů.
Dočasný disk | Nepodporováno | Dočasný disk je vždy vyloučený z procesu přípravy.<br/><br/> Na dočasném disku neukládejte žádná trvalá data. [Přečtěte si další informace](../virtual-machines/windows/managed-disks-overview.md#temporary-disk).
Maximální velikost datového disku | 8192 GB pro Managed disks
Minimální velikost datového disku |  2 GB pro spravované disky |
Maximální počet datových disků | Až 64, v souladu s podporou konkrétní velikosti virtuálního počítače Azure | [Přečtěte si další informace](../virtual-machines/windows/sizes.md) o velikostech virtuálních počítačů.
Rychlost změny datového disku | Maximálně 10 MB/s na disk pro Premium Storage. Maximálně 2 MB/s na disk pro úložiště úrovně Standard. | Pokud je průměrná rychlost změny dat na disku nepřetržitě vyšší než maximální, příprava nebude zachytila.<br/><br/>  Pokud je ale maximum překročeno zřídka, přípravek se může zachytit, ale může se zobrazit mírně zpožděné body obnovení.
Datový disk (účet úložiště úrovně Standard) | Nepodporováno | Změňte typ úložiště na Managed disk a pak zkuste virtuální počítač přesunout.
Datový disk (účet služby Storage úrovně Premium) | Nepodporováno | Změňte typ úložiště na Managed disk a pak zkuste virtuální počítač přesunout.
Spravovaný disk (Standard) | Podporováno  |
Spravovaný disk (Premium) | Podporováno |
SSD úrovně Standard | Podporováno |
Generace 2 (spuštění UEFI) | Podporováno
Účet úložiště diagnostiky spouštění | Nepodporováno | Po přesunutí virtuálního počítače do cílové oblasti ho znovu povolte.

### <a name="limits-and-data-change-rates"></a>Limity a míry změny dat

Následující tabulka shrnuje omezení, která vycházejí z našich testů. Nevztahují se na všechny možné kombinace vstupně-výstupních operací aplikace. Skutečné výsledky se liší v závislosti na kombinaci v/v aplikace. Existují dvě omezení, která je potřeba zvážit, četnost změn dat na disku a četnost změn dat na virtuální počítače.

**Cíl úložiště** | **Průměrný počet vstupů a výstupů zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková četnost změn dat zdrojového disku za den**
---|---|---|---
Storage úrovně Standard | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |    336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk

## <a name="supported-vm-networking-settings"></a>Nastavení podporovaná síť virtuálních počítačů

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
NIC | Podporováno | Zadejte existující prostředek v cílové oblasti nebo vytvořte nový prostředek během procesu přípravy. 
Interní nástroj pro vyrovnávání zatížení | Podporováno | Zadejte existující prostředek v cílové oblasti nebo vytvořte nový prostředek během procesu přípravy.  
Veřejný nástroj pro vyrovnávání zatížení | Aktuálně se nepodporuje. | Zadejte existující prostředek v cílové oblasti nebo vytvořte nový prostředek během procesu přípravy.  
Veřejná IP adresa | Podporováno | Zadejte existující prostředek v cílové oblasti nebo vytvořte nový prostředek během procesu přípravy.  
Skupina zabezpečení sítě | Podporováno | Zadejte existující prostředek v cílové oblasti nebo vytvořte nový prostředek během procesu přípravy.  
Rezervovaná (statická) IP adresa | Podporováno | Tuto konfiguraci teď nejde nakonfigurovat. Výchozí hodnota je zdrojová hodnota. <br/><br/> Pokud má síťová karta ve zdrojovém virtuálním počítači statickou IP adresu a v cílové podsíti je k dispozici stejná IP adresa, je přiřazena k cílovému virtuálnímu počítači.<br/><br/> Pokud cílová podsíť nemá k dispozici stejnou IP adresu, iniciace virtuálního počítače se nezdaří.
Dynamická IP adresa | Podporováno | Tuto konfiguraci teď nejde nakonfigurovat. Výchozí hodnota je zdrojová hodnota.<br/><br/> Pokud má síťová karta ve zdroji dynamické přidělování IP adres, síťová karta cílového virtuálního počítače je ve výchozím nastavení také dynamická.
Konfigurace protokolu IP | Podporováno | Tuto konfiguraci teď nejde nakonfigurovat. Výchozí hodnota je zdrojová hodnota.

## <a name="outbound-access-requirements"></a>Požadavky na odchozí přístup

Virtuální počítače Azure, které chcete přesunout, vyžadují odchozí přístup.


### <a name="url-access"></a>Přístup URL

 Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto adresám URL:

**Název** | **Veřejný cloud Azure** | **Podrobnosti** 
--- | --- | --- 
Storage | `*.blob.core.windows.net`  | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. 
Azure Active Directory | `login.microsoftonline.com`  | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. 
Replikace | `*.hypervrecoverymanager.windowsazure.com` | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. 
Service Bus | `*.servicebus.windows.net` | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. 

## <a name="nsg-rules"></a>Pravidla NSG
Pokud k řízení odchozího připojení používáte pravidla skupiny zabezpečení sítě (NSG), vytvořte tato pravidla [značek služeb](../virtual-network/service-tags-overview.md) . Každé pravidlo by mělo umožňovat odchozí přístup na HTTPS (443).
- Vytvořte pravidlo značky úložiště pro zdrojovou oblast.
- Vytvořte pravidlo značek *AzureSiteRecovery* , které umožní přístup ke službě Site Recovery v libovolné oblasti. Tato značka obsahuje závislosti na těchto dalších značkách, takže je pro ně nutné vytvořit pravidla pro:
    - *Azureactivedirectory selhala*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Doporučujeme vám testovat pravidla v neprodukčním prostředí. [Projděte si několik příkladů](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Další kroky

Zkuste [virtuální počítač Azure přesunout](tutorial-move-region-virtual-machines.md) do jiné oblasti s nástrojem Resource stěhovací.
