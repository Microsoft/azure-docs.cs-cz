---
title: Matice podpory pro zotavení po havárii virtuálního počítače Azure s Azure Site Recovery
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačů Azure do sekundární oblasti s Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: 3e66d71011937c3e22c6f1537bcd448d4aa3bbf3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605508"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matice podpory pro zotavení po havárii virtuálního počítače Azure mezi oblastmi Azure

Tento článek shrnuje podporu a předpoklady pro zotavení po havárii virtuálních počítačích Azure z jedné oblasti Azure do jiné pomocí služby [Azure Site Recovery.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Podpora metody nasazení

**Nasazení** |  **Podpora**
--- | ---
**portál Azure** | Podporuje se.
**PowerShell** | Podporuje se. [Další informace](azure-to-azure-powershell.md)
**REST API** | Podporuje se.
**Rozhraní příkazového řádku** | Aktuálně se nepodporuje.


## <a name="resource-support"></a>Podpora prostředků

**Akce Zdroje** | **Podrobnosti**
--- | ---
**Přesunutí trezorů mezi skupinami prostředků** | Nepodporuje se
**Přesunutí výpočetních/úložných/síťových prostředků napříč skupinami prostředků** | Není podporováno.<br/><br/> Pokud přesunete virtuální počítače nebo přidružené součásti, jako je úložiště nebo síť po replikaci virtuálního počítače, je potřeba zakázat a znovu povolit replikaci pro virtuální počítače.
**Replikace virtuálních počítačů Azure z jednoho předplatného do druhého pro zotavení po havárii** | Podporované v rámci stejného klienta Služby Azure Active Directory.
**Migrace virtuálních aplikací napříč oblastmi v rámci podporovaných geografických clusterů (v rámci předplatných i napříč nimi)** | Podporované v rámci stejného klienta Služby Azure Active Directory.
**Migrace virtuálních mích ve stejné oblasti** | Není podporováno.

## <a name="region-support"></a>Podpora oblastí

Virtuální montovny můžete replikovat a obnovovat mezi libovolnými dvěma oblastmi v rámci stejného geografického clusteru. Geografické clustery jsou definovány zachování latence dat a suverenity v mysli.


**Geografický klastr** | **Oblasti Azure**
-- | --
Americké | Kanada – východ, Kanada – střed, střed USA – jih, střed USA, západ – střed USA, východní USA 2, Západní USA, Západní USA 2, Střední USA, Střed USA, Střed USA
Evropa | Velká Británie – západ, Velká Británie – jih, severní Evropa, západní Evropa, Jižní Afrika – západ, Jižní Afrika – sever, Norsko – západ, Norsko – západ
Asie | Jižní Indie, Střední Indie, Západní Indie, Jihovýchodní Asie, Východní Asie, Japonsko – východ, Japonsko – západ, Korea – střed, Korea – jih
Austrálie    | Austrálie – východ, Austrálie – jihovýchod, Austrálie – střed, Austrálie – střed 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Německo    | Německo – střed, Německo Severovýchod
Čína | Čína východ, Čína sever, Čína sever2, Čína východ2
Omezené oblasti vyhrazené pro obnovu po havárii v zemi |Německo – sever vyhrazeno pro Německo – západ– střed, Švýcarsko – západ vyhrazeno pro Švýcarsko Sever, Francie – jih vyhrazeno pro Francii – střed, SAE Central vyhrazeno pro zákazníky ze SAE – sever

>[!NOTE]
>
> - V **případě Brazílie – jih**můžete replikovat a přepojit převzetí služeb při selhání do těchto oblastí: –střed USA – jih, střed USA – západ, USA – východ, 2 – východ USA– USA – západ, USA – západ a usa – sever.
> - Brazílie – jih lze použít jenom jako zdrojovou oblast, ze které se virtuální servery můžou replikovat pomocí site recovery. Nemůže fungovat jako cílová oblast. Důvodem jsou problémy s latencí kvůli zeměpisným vzdálenostem. Všimněte si, že pokud převzetí služeb při selhání z Brazílie jih jako zdrojové oblasti k cíli, navrácení služeb po uchvátí do Brazílie – jih z cílové oblasti je podporována.
> - Můžete pracovat v oblastech, pro které máte odpovídající přístup.
> - Pokud se oblast, ve které chcete vytvořit trezor, nezobrazuje, ujistěte se, že vaše předplatné má přístup k vytváření prostředků v této oblasti.
> - Pokud při povolení replikace nevidíte oblast v rámci geografického clusteru, ujistěte se, že vaše předplatné má oprávnění k vytváření virtuálních počítačů v této oblasti.



## <a name="cache-storage"></a>Úložiště mezipaměti

Tato tabulka shrnuje podporu pro účet úložiště mezipaměti používaný site recovery během replikace.

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Účty úložiště Pro obecné účely V2 (horká a studená úroveň) | Podporuje se | Použití GPv2 se nedoporučuje, protože transakční náklady na V2 jsou podstatně vyšší než účty úložiště V1.
Premium Storage | Nepodporuje se | Standardní účty úložiště se používají pro ukládání do mezipaměti, které pomáhají optimalizovat náklady.
Brány firewall Azure Storage pro virtuální sítě  | Podporuje se | Pokud používáte účet úložiště mezipaměti s povolenou bránou firewall nebo cílový účet úložiště, ujistěte se, že [povolujete důvěryhodné služby společnosti Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Také se ujistěte, že povolíte přístup alespoň k jedné podsíti zdrojové sítě.


## <a name="replicated-machine-operating-systems"></a>Replikované operační systémy strojů

Site Recovery podporuje replikaci virtuálních počítačů Azure s operačními systémy uvedenými v této části. Vezměte prosím na vědomí, že pokud již replikující počítač je následně upgradován (nebo snížen) na jiné hlavní jádro, je třeba zakázat replikaci a znovu povolit replikaci po upgradu.

### <a name="windows"></a>Windows


**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2019 | Podporováno pro jádro serveru, server se zkušenostmi s počítačem.
Windows Server 2016  | Podporované jádro serveru, server se zkušenostmi s počítačem.
Windows Server 2012 R2 | Podporuje se.
Windows Server 2012 | Podporuje se.
Systém Windows Server 2008 R2 s sp1/SP2 | Podporuje se.<br/><br/> Od verze [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozšíření služby Mobility pro virtuální počítače Azure je třeba nainstalovat [aktualizaci zásobníku windows údržby (SSU)](https://support.microsoft.com/help/4490628) a [aktualizaci SHA-2](https://support.microsoft.com/help/4474419) na počítačích se systémem Windows Server 2008 R2 SP1/SP2.  SHA-1 není podporována od září 2019 a pokud není povoleno podepisování kódu SHA-2, rozšíření agenta se nenainstaluje nebo neupgraduje podle očekávání. Další informace o [upgradu sha-2 a požadavcích](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Podporuje se.
Windows 8.1 (x64) | Podporuje se.
Windows 8 (x64) | Podporuje se.
Windows 7 (x64) s sp1 a dále | Od verze [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozšíření služby Mobility pro virtuální počítače Azure je třeba nainstalovat [aktualizaci zásobníku windows údržby (SSU)](https://support.microsoft.com/help/4490628) a [aktualizaci SHA-2](https://support.microsoft.com/help/4474419) na počítačích se systémem Windows 7 s aktualizací SP1.  SHA-1 není podporována od září 2019 a pokud není povoleno podepisování kódu SHA-2, rozšíření agenta se nenainstaluje nebo neupgraduje podle očekávání. Další informace o [upgradu sha-2 a požadavcích](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Operační systém** | **Podrobnosti**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5,[7,6, 7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [Podporované verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Podporovaná verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Servery Ubuntu používající ověřování založené na heslech a přihlášení a balíček cloud-init pro konfiguraci cloudových virtuálních počítačů mohou mít při převzetí služeb při selhání zakázáno přihlášení založené na heslech (v závislosti na konfiguraci cloudinitu). Přihlášení na základě hesla lze znovu povolit ve virtuálním počítači resetováním hesla z nabídky Poradce při potížích s > nastavení montovny na webu Podpory > řešení potíží (selhání virtuálního počítače na webu Azure Portal.
Ubuntu 18.04 LTS Server | [Podporovaná verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Podporované verze jádra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 a 15 SP1. [(Podporované verze jádra)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | Aktualizace SP3<br/><br/> Upgrade replikačních počítačů z sp3 na sp4 není podporován. Pokud byl inovovaný počítač inovován, je třeba po upgradu zakázat replikaci a znovu povolit replikaci.
SUSE Linux Enterprise Server 11 | Aktualizace SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Spuštění jádra kompatibilního s Red Hat nebo Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Ubuntu pro virtuální počítače Azure

**Vydat** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
|||
16,04 LTS | 9.32 | 4.4.0-21-generický až 4.4.0-171-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-74-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1066-azure|
16,04 LTS | 9.31 | 4.4.0-21-generický až 4.4.0-170-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-72-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1063-azure|
16,04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generický až 4.4.0-166-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-66-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1061-azure|
16,04 LTS | 9.29 | 4.4.0-21-generický až 4.4.0-164-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-64-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-generický až 4.15.0-74-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-37-generický </br> 5.3.0-19-generický až 5.3.0-24-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1028-azure </br> 5.3.0-1007-azure až 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-generický až 4.15.0-72-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-37-generický </br> 5.3.0-19-generický až 5.3.0-24-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1025-azure </br> 5.3.0-1007-azur|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generický až 4.15.0-66-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-32-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generický až 4.15.0-64-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-29-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Debianu pro virtuální počítače Azure

**Vydat** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 až 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 až 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 až 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 až 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 až 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra SUSE Linux Enterprise Server 12 pro virtuální počítače Azure

**Vydat** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.32 | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.31 | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.30 | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.29 | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra SUSE Linux Enterprise Server 15 pro virtuální počítače Azure

**Vydat** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 a 15 SP1 | 9.32 | Podporována jsou všechna jádra SUSE 15 a 15.</br></br> 4.12.14-5.5-azure až 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikované počítače - Linux souborový systém / hostovací úložiště

* Souborové systémy: ext3, ext4, XFS, BTRFS
* Správce hlasitosti: LVM2
* Software pro vícecestník: Mapovač zařízení


## <a name="replicated-machines---compute-settings"></a>Replikované počítače – výpočetní nastavení

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Velikost | Libovolná velikost virtuálního počítače Azure s minimálně 2 jádry procesoru a 1 GB paměti RAM | Ověřte [velikosti virtuálních počítačů Azure](../virtual-machines/windows/sizes.md).
Skupiny dostupnosti | Podporuje se | Pokud povolíte replikaci pro virtuální počítač Azure s výchozími možnostmi, vytvoří se sada dostupnosti automaticky na základě nastavení zdrojové oblasti. Tato nastavení můžete změnit.
Zóny dostupnosti | Podporuje se |
Výhody hybridního použití (HUB) | Podporuje se | Pokud má zdrojový virtuální virtuální hotel povolenou licenci HUB, testovací převzetí služeb při selhání nebo převzetí služeb při selhání virtuálního uživatele také používá licenci HUB.
Škálovací sady virtuálních počítačů | Nepodporuje se |
Image galerie Azure – publikováno Microsoftem | Podporuje se | Podporované, pokud virtuální počítač běží na podporovaném operačním systému.
Image Galerie Azure – publikováno třetí strany | Podporuje se | Podporované, pokud virtuální počítač běží na podporovaném operačním systému.
Vlastní obrázky - Třetí strana publikována | Podporuje se | Podporované, pokud virtuální počítač běží na podporovaném operačním systému.
Virtuální aplikace migrované pomocí site recovery | Podporuje se | Pokud virtuální počítač VMware nebo fyzický počítač byl migrován do Azure pomocí site recovery, je třeba odinstalovat starší verzi služby Mobility spuštěné na počítači a restartovat počítač před replikací do jiné oblasti Azure.
Zásady RBAC | Nepodporuje se | Zásady řízení přístupu na základě rolí (RBAC) na virtuálních počítačích nejsou replikovány do virtuálního počítače s podporou převzetí služeb při selhání v cílové oblasti.
Rozšíření | Nepodporuje se | Rozšíření nejsou replikovány do virtuálního virtuálního soudu s podporou převzetí služeb při selhání v cílové oblasti. Po převzetí služeb při selhání je třeba jej nainstalovat ručně.

## <a name="replicated-machines---disk-actions"></a>Replikované počítače – akce disku

**Akce** | **Podrobnosti**
-- | ---
Změna velikosti disku na replikovaném virtuálním počítači | Podporováno na zdrojovém virtuálním počítači před převzetím služeb při selhání. Není nutné zakázat nebo znovu povolit replikaci.<br/><br/> Pokud změníte zdrojový virtuální virtuální ms po převzetí služeb při selhání, změny nejsou zachyceny.<br/><br/> Pokud změníte velikost disku na virtuálním počítači Azure po převzetí služeb při selhání, změny nejsou zachyceny site recovery a navrácení služeb po obnovení bude původní velikost virtuálního počítače.
Přidání disku do replikovaného virtuálního počítače | Podporuje se

## <a name="replicated-machines---storage"></a>Replikované stroje - úložiště

Tato tabulka shrnula podporu disku operačního systému Azure VM, datového disku a dočasného disku.

- Je důležité dodržovat limity disku virtuálního počítače a cíle pro [Linux](../virtual-machines/linux/disk-scalability-targets.md) a [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuální počítače, aby se zabránilo problémům s výkonem.
- Pokud nasadíte s výchozím nastavením, site recovery automaticky vytvoří disky a účty úložiště na základě nastavení zdroje.
- Pokud si přizpůsobíte, ujistěte se, že dodržujete pokyny.

**Komponenta** | **Podpora** | **Podrobnosti**
--- | --- | ---
Maximální velikost disku operačního systému | 2048 GB | [Další informace](../virtual-machines/windows/managed-disks-overview.md) o discích virtuálních počítačů.
Dočasný disk | Nepodporuje se | Dočasný disk je vždy vyloučen z replikace.<br/><br/> Neukládejte žádná trvalá data na dočasný disk. [Další informace](../virtual-machines/windows/managed-disks-overview.md).
Maximální velikost datového disku | 8192 GB pro spravované disky<br></br>4095 GB pro nespravované disky|
Minimální velikost datového disku | Žádné omezení pro nespravované disky. 2 GB pro spravované disky |
Maximální počet datových disků | Až 64, v souladu s podporou pro konkrétní velikost virtuálního počítače Azure | [Přečtěte si další informace](../virtual-machines/windows/sizes.md) o velikostech virtuálních počítače.
Rychlost změny datového disku | Maximálně 10 mb/s na disk pro úložiště premium. Maximálně 2 mb/s na disk pro standardní úložiště. | Pokud je průměrná rychlost změny dat na disku trvale vyšší než maximální, replikace nedožene.<br/><br/>  Pokud je však maximální počet překročen sporadicky, replikace může dohnat, ale může se zobrazit mírně zpožděné body obnovení.
Datový disk - standardní účet úložiště | Podporuje se |
Datový disk – účet úložiště premium | Podporuje se | Pokud má virtuální počítače disky rozložené mezi účty úložiště premium a standard, můžete vybrat jiný cílový účet úložiště pro každý disk, abyste měli zajištěno, že máte stejnou konfiguraci úložiště v cílové oblasti.
Spravovaný disk - standardní | Podporované v oblastech Azure, ve kterých je podporována Azure Site Recovery. |
Spravovaný disk – prémii | Podporované v oblastech Azure, ve kterých je podporována Azure Site Recovery. |
SSD úrovně Standard | Podporuje se |
Redundance | LRS a GRS jsou podporovány.<br/><br/> ZRS není podporována.
Chladné a horké skladování | Nepodporuje se | Disky virtuálních počítačů nejsou podporované v chladném a horkém úložišti
Prostory úložiště | Podporuje se |
Šifrování v klidovém stavu (SSE) | Podporuje se | SSE je výchozí nastavení pro účty úložiště.
Šifrování v klidovém stavu (CMK) | Podporuje se | Pro spravované disky jsou podporovány softwarové i hsm klíče.
Šifrování disku Azure (ADE) pro operační systém Windows | Podporované pro virtuální počítače se spravovanými disky. | Virtuální počítače používající nespravované disky nejsou podporované. <br/><br/> Klíče chráněné modulu hesm nejsou podporovány. |
Šifrování disku Azure (ADE) pro operační systém Linux | Podporované pro virtuální počítače se spravovanými disky. | Virtuální počítače používající nespravované disky nejsou podporované. <br/><br/> Klíče chráněné modulu hesm nejsou podporovány. |
Horké přidání    | Podporuje se | Povolení replikace pro datový disk, který přidáte do replikovaného virtuálního počítače Azure, je podporované pro virtuální počítače, které používají spravované disky.
Odstranit disk s hot    | Nepodporuje se | Pokud odeberete datový disk ve virtuálním počítači, budete muset zakázat replikaci a znovu povolit replikaci pro virtuální počítač.
Vyloučení disku | Podpora. Ke konfiguraci je nutné použít [prostředí PowerShell.](azure-to-azure-exclude-disks.md) |    Dočasné disky jsou ve výchozím nastavení vyloučeny.
Prostory úložiště – přímé  | Podporováno pro body obnovení konzistentní selhání. Body obnovení konzistentní aplikace nejsou podporovány. |
Souborový server s horizontálním navýšením kapacity  | Podporováno pro body obnovení konzistentní selhání. Body obnovení konzistentní aplikace nejsou podporovány. |
Drbd | Disky, které jsou součástí instalace DRBD, nejsou podporovány. |
LRS | Podporuje se |
GRS | Podporuje se |
RA-GRS | Podporuje se |
ZRS | Nepodporuje se |
Chladné a horké úložiště | Nepodporuje se | Disky virtuálních počítačů nejsou podporovány v chladném a horkém úložišti
Brány firewall Azure Storage pro virtuální sítě  | Podporuje se | Pokud omezíte přístup virtuální sítě na účty úložiště, povolte [povolit důvěryhodné služby společnosti Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Účty úložiště Pro obecné účely V2 (horká i studená úroveň) | Podporuje se | Transakční náklady se podstatně zvyšují ve srovnání s účty úložiště Pro obecné účely V1
Generace 2 (UEFI boot) | Podporuje se

>[!IMPORTANT]
> Chcete-li se vyhnout problémům s výkonem, ujistěte se, že dodržujete cíle škálovatelnosti a výkonu disku virtuálních počítačů virtuálních počítačů [pro Linux](../virtual-machines/linux/disk-scalability-targets.md) nebo [Windows.](../virtual-machines/windows/disk-scalability-targets.md) Pokud použijete výchozí nastavení, site recovery vytvoří požadované disky a účty úložiště na základě konfigurace zdroje. Pokud upravíte a vyberete vlastní nastavení, postupujte podle cílů škálovatelnosti disku a výkonu pro zdrojové virtuální počítače.

## <a name="limits-and-data-change-rates"></a>Limity a rychlost změny dat

Následující tabulka shrnuje limity obnovení webu.

- Tyto limity jsou založeny na našich testech, ale samozřejmě nepokrývají všechny možné kombinace vstupně-neo., ale samozřejmě nepokrývají všechny možné kombinace vstupně-neocích aplikací.
- Skutečné výsledky se mohou lišit v závislosti na kombinaci vstupně-vstupně-va aplikace.
- Existují dvě omezení, která je třeba zvážit, na konve dat disku a na konve dat virtuálního počítače.

**Cíl úložiště** | **Průměrné vstupně-to diskové vstupně-to** |**Průměrná četnost změn dat zdrojového disku** | **Celkový počet změn dat zdrojového disku za den**
---|---|---|---
Storage úrovně Standard | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |    336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk

## <a name="replicated-machines---networking"></a>Replikované stroje - sítě
**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
NIC | Maximální počet podporovaný pro konkrétní velikost virtuálního počítače Azure | Síťové karty se vytvoří při vytvoření virtuálního počítače během převzetí služeb při selhání.<br/><br/> Počet nic na virtuálním počítači s podporou převzetí služeb při selhání závisí na počtu nic na zdrojovém virtuálním počítači, když byla povolena replikace. Pokud po povolení replikace přidáte nebo odeberete nic, nebude to mít vliv na počet nic na replikovaném virtuálním počítači po převzetí služeb při selhání. Všimněte si také, že pořadí síťových karty po převzetí služeb při selhání není zaručeno, že bude stejné jako původní pořadí.
Internetový nástroj pro vyrovnávání zatížení | Podporuje se | Přidružte předkonfigurovaný systém vyrovnávání zatížení pomocí skriptu Azure Automation v plánu obnovení.
Interní systém vyrovnávání zatížení | Podporuje se | Přidružte předkonfigurovaný systém vyrovnávání zatížení pomocí skriptu Azure Automation v plánu obnovení.
Veřejná IP adresa | Podporuje se | Přidružte existující veřejnou IP adresu k nic. Nebo vytvořte veřejnou IP adresu a přidružte ji k nic pomocí skriptu Azure Automation v plánu obnovení.
NSG na nic | Podporuje se | Přidružte soubor zabezpečení zabezpečení k železniční pokryvné kalbě pomocí skriptu Azure Automation v plánu obnovení.
Skupina nsg v podsíti | Podporuje se | Přidružte skupinu zabezpečení sítě k podsíti pomocí skriptu Azure Automation v plánu obnovení.
Vyhrazená (statická) IP adresa | Podporuje se | Pokud má síťová konekve na zdrojovém virtuálním počítači statickou IP adresu a cílová podsíť má k dispozici stejnou IP adresu, přiřadí se k virtuálnímu počítači, který selhal.<br/><br/> Pokud cílová podsíť nemá k dispozici stejnou adresu IP, jedna z dostupných IP adres v podsíti je vyhrazena pro virtuální hod.<br/><br/> Můžete také zadat pevnou adresu IP a podsíť v**rozhraních Nastavení** > **výpočetních a síťových** > **síťových replikovaných** **položek** > .
Dynamická IP adresa | Podporuje se | Pokud je nic na zdroji dynamické adresování IP adres, nic na převzetí přes virtuální počítač je také dynamický ve výchozím nastavení.<br/><br/> V případě potřeby ji můžete upravit na pevnou ADRESU IP.
Několik IP adres | Nepodporuje se | Při převzetí služeb při selhání virtuálního počítači, který má nic s více IP adresami, pouze primární IP adresa nic ve zdrojové oblasti je zachována. Chcete-li přiřadit více adres IP, můžete přidat virtuální počítačdo [plánu obnovení](recovery-plan-overview.md) a připojit skript pro přiřazení dalších adres IP k plánu, nebo můžete provést změnu ručně nebo se skriptem po převzetí služeb při selhání.
Traffic Manager     | Podporuje se | Traffic Manager můžete předem nakonfigurovat tak, aby provoz byl pravidelně směrován do koncového bodu ve zdrojové oblasti a do koncového bodu v cílové oblasti v případě převzetí služeb při selhání.
Azure DNS | Podporuje se |
Vlastní DNS    | Podporuje se |
Neověřený proxy server | Podporuje se | [Další informace](site-recovery-azure-to-azure-networking-guidance.md)
Ověřený proxy server | Nepodporuje se | Pokud virtuální počítač používá ověřený proxy server pro odchozí připojení, nelze ho replikovat pomocí Azure Site Recovery.
Připojení k místnímu serveru VPN<br/><br/>(s ExpressRoute nebo bez něj)| Podporuje se | Ujistěte se, že UDR a nsg jsou nakonfigurovány tak, aby provoz obnovení webu nebyl směrován do místního. [Další informace](site-recovery-azure-to-azure-networking-guidance.md)
Připojení virtuální sítě k virtuální síti    | Podporuje se | [Další informace](site-recovery-azure-to-azure-networking-guidance.md)
Koncové body služby pro virtuální síť | Podporuje se | Pokud omezujete přístup virtuální sítě k účtům úložiště, ujistěte se, že důvěryhodné služby Společnosti Microsoft mají povolený přístup k účtu úložiště.
Urychlení sítě | Podporuje se | Akcelerované sítě musí být povolena na zdrojovém virtuálním počítači. [Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Další kroky
- Přečtěte si [pokyny k síti](site-recovery-azure-to-azure-networking-guidance.md) pro replikaci virtuálních počítačů Azure.
- Nasazení zotavení po havárii [replikací virtuálních počítačích Azure](site-recovery-azure-to-azure.md).
