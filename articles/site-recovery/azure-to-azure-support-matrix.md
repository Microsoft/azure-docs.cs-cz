---
title: Matice podpory pro zotavení po havárii virtuálních počítačů Azure mezi oblastmi Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Obsahuje souhrn požadavků a podpora pro zotavení po havárii virtuálních počítačů Azure z jedné oblasti do jiného pomocí Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 9b905d532dfe71fea7c4ec0377eb53b9e3073907
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926596"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matice podpory pro replikaci virtuálních počítačů Azure z jedné oblasti do jiného

Tento článek shrnuje podporu a požadavky při nastavování zotavení po havárii virtuálních počítačů Azure z jedné oblasti Azure do jiné, použití [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="deployment-method-support"></a>Podpora nasazení – metoda

**Nasazení** |  **Podpora**
--- | ---
**Azure Portal** | Podporuje se.
**PowerShell** | Podporuje se. [Další informace](azure-to-azure-powershell.md)
**REST API** | Podporuje se.
**Rozhraní příkazového řádku** | Aktuálně se nepodporuje.


## <a name="resource-support"></a>Podpora prostředků

**Akce prostředku** | **Podrobnosti**
--- | --- | ---
**Přesun trezorů služby mezi skupinami prostředků** | Nepodporuje se
**Přesunout výpočetní síťi prostředků mezi skupinami prostředků** | Nepodporuje se.<br/><br/> Při přesunutí virtuálního počítače nebo přidružené komponent, jako jsou úložiště nebo sítě po replikaci virtuálního počítače, musíte zakázat a znovu povolte replikaci pro virtuální počítač.
**Replikace virtuálních počítačů Azure z jednoho předplatného do druhého pro zotavení po havárii** | Podporováno ve stejném tenantovi Azure Active Directory.
**Migrace virtuálních počítačů napříč oblastmi v rámci podporovaných geografických clusterů (v rámci a napříč předplatnými)** | Podporováno ve stejném tenantovi Azure Active Directory.
**Migrace virtuálních počítačů ve stejné oblasti** | Nepodporuje se.

## <a name="region-support"></a>Oblasti podpory

Můžete replikovat a obnovovat virtuální počítače mezi všechny dvou oblastí ve stejné zeměpisné clusteru. Geografické clustery jsou definovány dodržujte při tom suverenita a data latence.


**Geografické clusteru** | **Oblasti Azure**
-- | --
Americké | Kanada – východ, Kanada – Jih, střed USA (střed), střed USA – Západ, USA – východ, USA – východ 2, USA – Západ, USA – západ 2, střed USA, USA (střed) – sever
Evropa | Velká Británie – Západ, Velká Británie – Jih, Severní Evropa, západní Evropa, Francie – střed, Francie – jih
Asie | Indie – Jih, střed Indie, jihovýchodní Asie, jihovýchodní Asie, Japonsko – východ, Japonsko – Západ, Korea – střed, Korea – jih
Austrálie   | Austrálie – východ, Austrálie – jihovýchod, Austrálie – střed, Austrálie – střed 2
Azure Government    | Virginie USA (gov), US GOV Iowa, USA (gov) Arizona, US GOV Texas, US DOD – východ, US DOD – střed
Německo | Německo – střed, Německo – severovýchod
Čína | Čína – východ, Čína – sever, Čína North2, Čína – východ 2

>[!NOTE]
>
> - Pro **Brazílie – jih**, můžete replikaci a převzetí služeb při selhání na tyto oblasti: Střední část jihu USA, střed USA – Západ, USA – východ, USA – východ 2, USA – Západ, USA – západ 2 a USA (střed) – sever.
> - Brazílie – jih jde použít jenom jako zdrojové oblasti, ze kterého můžete replikovat virtuální počítače s využitím Site Recovery. Nemůže fungovat jako cílové oblasti. Toto je z důvodu problémů s latencí kvůli geografické vzdálenosti.
> - Můžete pracovat v rámci oblasti, pro které mají odpovídající přístup.
> - Pokud oblast, ve kterém chcete vytvořit trezor nezobrazí, ujistěte se, že vaše předplatné má přístup k vytváření prostředků v dané oblasti.
> - Není-li v oblasti v rámci zeměpisné clusteru při povolení replikace, ujistěte se, že vaše předplatné má oprávnění k vytvoření virtuálních počítačů v dané oblasti.



## <a name="cache-storage"></a>Úložiště mezipaměti

Tato tabulka shrnuje podporu pro účet úložiště mezipaměti, který se používá služba Site Recovery během replikace.

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Obecné účely V2 účty úložiště (horká a studená vrstva přístupu) | Nepodporuje se. | Omezení existuje pro úložiště mezipaměti, protože náklady na transakce pro V2 jsou výrazně vyšší než účtů storage V1.
Pro virtuální sítě brány firewall služby Azure Storage  | Podporováno | Pokud používáte účet úložiště mezipaměti povolena brána firewall nebo cílový účet úložiště, zajistěte, aby ["Povolit důvěryhodné služby Microsoftu"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Operační systémy replikované počítače

Site Recovery podporuje replikaci virtuálních počítačů Azure s operačními systémy, uvedené v této části.

### <a name="windows"></a>Windows

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2019 |
Windows Server 2016  | Jádro serveru, Server s desktopovým prostředím
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | S aktualizací SP1 nebo novější

#### <a name="linux"></a>Linux

**Operační systém** | **Podrobnosti**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Verze podporovaných jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Verze podporovaných jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Servery se systémem Ubuntu s použitím ověřování pomocí hesla a přihlášení a cloud-init balíček ke konfiguraci cloudových virtuálních počítačích, pravděpodobně přihlašování pomocí hesla ve zakázáno na převzetí služeb při selhání (v závislosti na konfiguraci cloudinit). Resetováním hesla z podpory může být založené na heslech přihlášení na virtuálním počítači znovu zapnout > Poradce při potížích s > nabídky nastavení (služby se převzaly virtuálního počítače na webu Azure Portal.
Debian 7 | [Verze podporovaných jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Verze podporovaných jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Jádra podporované verze)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Upgrade replikace počítačů z SP3 do SP4 se nepodporuje. Pokud byl upgradován replikovaného počítače, musíte zakázat replikaci a znovu povolte replikaci po upgradu.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Systém Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Ubuntu pro Azure virtual machines

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9.24 | 3.13.0-24-Generic k 3.13.0-167-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-143-generic,<br/>4.15.0-1023-Azure k 4.15.0-1040-azure |
14.04 LTS | 9.23 | 3.13.0-24-Generic k 3.13.0-165-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-142-generic,<br/>4.15.0-1023-Azure k 4.15.0-1037-azure |
14.04 LTS | 9.22 | 3.13.0-24-Generic k 3.13.0-164-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.15.0-1023-Azure k 4.15.0-1036-azure |
14.04 LTS | 9.21 | 3.13.0-24-Generic k 3.13.0-163-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.15.0-1023-Azure k 4.15.0-1035-azure |
|||
16.04 LTS | 9.24 | 4.4.0-21-Generic k 4.4.0-143-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-46-generic<br/>4.11.0-1009-Azure k 4.11.0-1018-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1040-azure|
16.04 LTS | 9.23 | 4.4.0-21-Generic k 4.4.0-142-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-45-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1037-azure|
16.04 LTS | 9.22 | 4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-43-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1036-azure|
16.04 LTS | 9.21 | 4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-42-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1035-azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Debian pro Azure virtual machines

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | 9.21,9.22,9.23,9.24 | 3.2.0-4-amd64 k 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.21, 9.22, 9.23, 9.24 | 3.16.0-4-amd64 k 3.16.0-7-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra operačního systému SUSE Linux Enterprise Server 12 pro virtuální počítače Azure

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (s aktualizací SP1, SP2, SP3, SP4) | 9.24 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default k 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default k 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (s aktualizací SP1, SP2, SP3, SP4) | 9.23 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default k 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default k 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.22 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default k 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.21 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default k 4.4.162-94.72-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikované počítače – Linux souboru systému/hostovaného úložiště

* Systémy souborů: ext3 ext4, ReiserFS (pouze operačním systémem Suse Linux Enterprise Server), XFS, BTRFS
* Správce svazků: LVM2
* Software s funkcí Multipath: Mapování zařízení


## <a name="replicated-machines---compute-settings"></a>Replikované počítače – výpočetní nastavení

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Velikost | Libovolná velikost virtuálních počítačů Azure s nejméně 2 jádra procesoru a 1 GB paměti RAM | Ověřte [velikosti virtuálních počítačů Azure](../virtual-machines/windows/sizes.md).
Skupiny dostupnosti | Podporováno | Pokud povolíte replikaci pro virtuální počítač Azure s výchozími možnostmi, skupinu dostupnosti se vytvoří automaticky, na základě nastavení zdrojové oblasti. Tato nastavení můžete upravit.
Zóny dostupnosti | Podporováno |
Program hybrid Use Benefit (HUB) | Podporováno | Pokud zdrojový virtuální počítač má licenci centra povolené, testovací převzetí služeb při selhání nebo převzetí služeb při selhání virtuálního počítače také používá licenci ROZBOČOVAČE.
Škálovací sady virtuálních počítačů | Nepodporuje se |
Publikované Image z Galerie Azure – Microsoft | Podporováno | Podporováno, pokud virtuální počítač běží na podporovaném operačním systému.
Image Galerie Azure - publikované třetích stran | Podporováno | Podporováno, pokud virtuální počítač běží na podporovaném operačním systému.
Vlastní Image - publikované třetích stran | Podporováno | Podporováno, pokud virtuální počítač běží na podporovaném operačním systému.
Migrovat virtuální počítače pomocí služby Site Recovery | Podporováno | Pokud virtuální počítač VMware nebo fyzický počítač byl migrován do Azure pomocí Site Recovery, musíte odinstalovat starší verze služby Mobility na počítači a restartujte ho před replikací do jiné oblasti Azure.
Zásady RBAC | Nepodporuje se | Zásady řízení (RBAC) na virtuální počítače se nereplikují do převzetí služeb při selhání virtuálních počítačů v cílové oblasti přístup na základě rolí.

## <a name="replicated-machines---disk-actions"></a>Replikované počítače - disk akce

**Akce** | **Podrobnosti**
-- | ---
Změna velikosti disku na replikovaný virtuální počítač | Podporováno
Přidání disku do replikovaný virtuální počítač | Nepodporuje se.<br/><br/> Potřebujete zakázat replikaci příslušného virtuálního počítače přidejte disk a pak replikaci zase povolte.

## <a name="replicated-machines---storage"></a>Replikované počítače - úložiště

Tato tabulka je automaticky shrnutý podporu pro disk operačního systému virtuálního počítače Azure, datový disk a dočasný disk.

- Je důležité sledovat omezením disku virtuálního počítače a cíle pro [Linux](../virtual-machines/linux/disk-scalability-targets.md) a [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuálních počítačů, aby se zabránilo problémy s výkonem.
- Pokud provádíte nasazení s výchozím nastavením, Site Recovery automaticky vytvoří disks a účtech úložiště na základě nastavení zdroje.
- Pokud upravíte, nezapomeňte že postupovat podle pokynů.

**Komponenta** | **Podpora** | **Podrobnosti**
--- | --- | ---
Maximální velikost disku operačního systému | 2048 GB | [Další informace](../virtual-machines/windows/managed-disks-overview.md) o discích virtuálních počítačů.
Dočasný disk | Nepodporuje se | Dočasný disk je vždy z replikace vyloučit.<br/><br/> Neukládají žádná trvalá data na dočasném disku. [Další informace](../virtual-machines/windows/managed-disks-overview.md).
Maximální velikost datového disku | 4095 GB |
Maximální číslo datového disku | Až 64, v souladu s podporou pro určité velikosti virtuálních počítačů Azure | [Další informace](../virtual-machines/windows/sizes.md) o velikostech virtuálních počítačů.
Četnost změn dat disku | Maximálně 10 MB/s na disku pro premium storage. Maximálně 2 MB/s na disk pro úložiště úrovně Standard. | Pokud Průměrná frekvence změny dat na disku je trvale vyšší než maximální délka, nebude dohnat replikace.<br/><br/>  Pokud se překročí maximální nedojde replikace můžete zachytit, ale může se zobrazit body obnovení mírně zpožděné.
Datový disk - účet úložiště úrovně standard | Podporováno |
Datový disk - účtu služby premium storage | Podporováno | Pokud virtuální počítač obsahuje disky, které jsou rozděleny mezi účty úložiště úrovně standard a premium, můžete vybrat jiný cílový účet úložiště pro každý z disků, zajistěte, že abyste měli stejnou konfiguraci úložiště v cílové oblasti.
Spravovaný disk – standard | Podporované v oblastech Azure, ve kterých je Azure Site Recovery nepodporuje. |
Spravovaný disk – premium | Podporované v oblastech Azure, ve kterých je Azure Site Recovery nepodporuje. |
SSD úrovně Standard | Podporováno |
Redundance | Jsou podporovány LRS a GRS.<br/><br/> ZRS se nepodporuje.
Úložiště Cool a hot | Nepodporuje se | Disky virtuálních počítačů se nepodporují studené a horké úložiště
Prostory úložiště | Podporováno |
Šifrování v klidovém stavu (SSE) | Podporováno | SSE je výchozí nastavení u účtů úložiště.   
Azure Disk Encryption (ADE) pro operační systém Windows | Povoleno pro virtuální počítače [šifrování pomocí aplikace Azure AD](https://aka.ms/ade-aad-app) jsou podporovány |
Azure Disk Encryption (ADE) pro operační systém Linux | Nepodporuje se |
Horké přidat | Podporováno | Povolení replikace pro datový disk, který přidáte do replikovaný virtuální počítač Azure se podporuje pro virtuální počítače, které používají spravované disky.
Horké Odeberte disk | Nepodporuje se | Pokud odeberete datový disk na virtuálním počítači, musíte zakázat replikaci a povolte replikaci pro virtuální počítač znovu.
Vyloučení disku | podpora. Je nutné použít [Powershellu](azure-to-azure-exclude-disks.md) ke konfiguraci. |  Ve výchozím nastavení jsou vyloučeny dočasné disky.
Prostory úložiště s přímým přístupem  | Podporované pro body obnovení konzistentní vzhledem k chybě. Body obnovení konzistentní vzhledem k aplikaci nejsou podporovány. |
Souborový Server se Škálováním  | Podporované pro body obnovení konzistentní vzhledem k chybě. Body obnovení konzistentní vzhledem k aplikaci nejsou podporovány. |
LRS | Podporováno |
GRS | Podporováno |
RA-GRS | Podporováno |
ZRS | Nepodporuje se |
Úložiště Cool a Hot | Nepodporuje se | Disky virtuálního počítače nejsou podporovány na studené a horké úložiště
Pro virtuální sítě brány firewall služby Azure Storage  | Podporováno | Pokud omezit přístup k virtuální síti k účtům úložiště, povolte [Povolit důvěryhodné služby Microsoftu](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Účty úložiště pro obecné účely V2 (i horká a studená úroveň) | Ne | Zvýšit náklady na transakce podstatně porovnání pro obecné účely V1 účty úložiště

>[!IMPORTANT]
> Aby se zabránilo problémům s výkonem, ujistěte se, abyste postupovali podle cíle škálovatelnosti a výkonu disku virtuálního počítače pro [Linux](../virtual-machines/linux/disk-scalability-targets.md) nebo [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuálních počítačů. Pokud používáte výchozí nastavení, nastavení vytvoří Site Recovery požadovaných disků a účty úložiště, v závislosti na konfiguraci zdroje. Je-li přizpůsobit a vybrat vlastní nastavení, postupujte podle disku škálovatelnost a výkonnostní cíle velikostem zdrojových virtuálních počítačů.

## <a name="limits-and-data-change-rates"></a>Omezení a data frekvence změny

Následující tabulka shrnuje omezení Site Recovery.

- Tato omezení se zakládají na našich testováních, ale samozřejmě není pokrýt všechny možné vstupně-výstupních operací kombinace aplikace.
- Skutečné výsledky mohou lišit v závislosti na kombinaci vstupně-výstupních operací aplikace je.
- Existují dvě omezení ke zvážení na disk, četnost změn dat a za data virtuálního počítače změny.
- Například pokud používáme disk úrovně Premium P20, jak je popsáno v následující tabulce, Site Recovery dokáže zpracovat 5 MB provozu na disk, se na maximálně pět těmito disky na virtuální počítač, z důvodu omezení 25 MB/s celková četnost změn na virtuální počítač.

**Cílové úložiště** | **Průměrná zdrojový disk vstupně-výstupních operací** |**Průměrná četnost změn dat zdrojového disku** | **Celková denní četnost změn dat zdrojového disku**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk

## <a name="replicated-machines---networking"></a>Replikované počítače - sítě
**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
NIC | Maximální počet podporovaný pro určité velikosti virtuálních počítačů Azure | Síťové karty se vytvářejí, když virtuální počítač se vytvoří během převzetí služeb při selhání.<br/><br/> Počet síťových adaptérů na převzetí služeb při selhání virtuálního počítače závisí na počet síťových adaptérů na zdrojovém virtuálním počítači při povolení replikace. Je-li přidat nebo odebrat síťovou kartu po povolení replikace, neovlivní počet síťových adaptérů na replikovanému virtuálnímu počítači po převzetí služeb při selhání. Všimněte si, že pořadí síťových rozhraní po převzetí služeb při selhání není zaručeno také být stejný jako původní pořadí.
Internetový nástroj pro vyrovnávání zatížení | Podporováno | Přidružte předkonfigurované zátěže pomocí Azure Automation skriptu v plánu obnovení.
Interní nástroj pro vyrovnávání zatížení | Podporováno | Přidružte předkonfigurované zátěže pomocí Azure Automation skriptu v plánu obnovení.
Veřejná IP adresa | Podporováno | Stávající veřejnou IP adresu přidružit k síťové kartě Vytvoření veřejné IP adresy a přidružte jej k síťovému rozhraní pomocí Azure Automation skriptu v plánu obnovení.
NSG na síťové KARTĚ | Podporováno | Přidružení NSG k síťovému rozhraní pomocí Azure Automation skriptu v plánu obnovení.
NSG na podsítě | Podporováno | Přidružení skupiny zabezpečení sítě s podsítí pomocí Azure Automation skriptu v plánu obnovení.
Vyhrazená IP adresa (statické) | Podporováno | Pokud má síťový adaptér na zdrojovém virtuálním počítači statickou IP adresu a cílová podsíť má stejnou IP adresu k dispozici, je přiřazen k převzetí virtuálního počítače.<br/><br/> Pokud cílová podsíť nemá stejnou IP adresu k dispozici, některou z dostupných IP adres v podsíti je vyhrazen pro virtuální počítač.<br/><br/> Můžete také určit pevnou IP adresu a podsítě v **replikované položky** > **nastavení** > **výpočty a síť**  >  **Síťová rozhraní**.
Dynamická IP adresa | Podporováno | Pokud má síťový adaptér na zdroj dynamických IP adres, NIC se selhání pro virtuální počítač je dynamický ve výchozím nastavení.<br/><br/> Tuto hodnotu můžete změnit na pevnou IP adresu v případě potřeby.
Několik IP adres | Nepodporuje se | Když převzetí služeb při selhání virtuálního počítače, který má síťové rozhraní s několika IP adresami, se ukládají jenom primární IP adresu síťové karty ve zdrojové oblasti. Přiřadit několik IP adres, můžete přidat virtuální počítače, které [plánu obnovení](recovery-plan-overview.md) a připojit skript pro přiřazení dalších IP adres k plánu nebo můžete provést změny ručně nebo pomocí skriptu po převzetí služeb při selhání. 
Traffic Manager     | Podporováno | Traffic Manager vám umožňují předem nakonfigurovat tak, aby provoz se směruje do koncového bodu ve zdrojové oblasti v pravidelných intervalech a ke koncovému bodu v cílové oblasti v případě převzetí služeb při selhání.
Azure DNS | Podporováno |
Vlastní DNS  | Podporováno |
Neověřené proxy | Podporováno | [Další informace]. (site-recovery-azure-to-azure-networking-guidance.md)   
Ověřený proxy server | Nepodporuje se | Pokud virtuální počítač používá ověřený proxy server pro odchozí připojení, nelze replikovat, pomocí Azure Site Recovery.    
Připojení VPN typu site-to-site k místní<br/><br/>(s nebo bez ExpressRoute)| Podporováno | Ujistěte se, že trasy definované uživatelem a skupin zabezpečení sítě jsou nakonfigurovány tak, že není Site Recovery provoz směrován do místní. [Další informace](site-recovery-azure-to-azure-networking-guidance.md)    
Připojení mezi virtuálními SÍTĚMI | Podporováno | [Další informace](site-recovery-azure-to-azure-networking-guidance.md)  
Koncové body služby pro virtuální síť | Podporováno | Pokud pro přístup k virtuální síti se omezení pro účty úložiště, ujistěte se, že jsou povolena důvěryhodným službám Microsoftu přístup k účtu úložiště.
Urychlení sítě | Podporováno | Akcelerované síťové služby musí být povolené na zdrojovém virtuálním počítači. [Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Další postup
- Čtení [sítě pokyny](site-recovery-azure-to-azure-networking-guidance.md) pro replikaci virtuálních počítačů Azure.
- Nasazení zotavení po havárii podle [replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure.md).
