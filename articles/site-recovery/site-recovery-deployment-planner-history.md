---
title: Historie verzí plánovače nasazení webu Azure
description: Známé různé opravy plánovače nasazení webu a známá omezení spolu s jejich daty vydání.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433412"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Historie verzí plánovače nasazení webu Azure

Tento článek obsahuje historii všech verzí Plánovače nasazení webu Azure spolu s opravami, známými omezeními v každém a jejich daty vydání.

## <a name="version-251"></a>Verze 2.51

**Datum vydání: Srpen 22, 2019**

**Opravy:**

- Opraven problém s doporučením nákladů v plánovači nasazení verze 2.5

## <a name="version-25"></a>Verze 2.5

**Datum vydání: Červenec 29, 2019**

**Opravy:**

- Pro virtuální počítače vmware a fyzické počítače doporučení je aktualizován a na základě replikace na spravované disky.
- Přidána podpora pro Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 nebo novější

## <a name="version-24"></a>Verze 2.4

**Datum vydání: Duben 17, 2019**

**Opravy:**

- Vylepšená kompatibilita operačního systému, konkrétně při zpracování chyb založených na lokalizaci.
- Do kontrolního seznamu kompatibility byly přidány virtuální aplikace s rychlostí změny dat až 20 Mb/s (konve).
- Vylepšené chybové zprávy
- Přidána podpora pro vCenter 6.7.
- Přidána podpora pro pracovní stanici Windows Server 2019 a Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Verze 2.3

**Datum vydání: Prosinec 3, 2018**

**Opravy:**

- Byl opraven problém, který planneru nasazení zabránil ve generování sestavy s poskytnutým cílovým umístěním a odběrem.

## <a name="version-22"></a>Verze 2.2 

**Datum vydání: Duben 25, 2018**

**Opravy:**

- GetVMList operace:
  - Byl opraven problém, který způsoboval selhání seznamu GetVMList, pokud zadaná složka neexistuje. Nyní buď vytvoří výchozí adresář, nebo vytvoří adresář zadaný v parametru výstupního souboru.
  - Přidány podrobnější důvody selhání pro GetVMList.
- Přidány informace o typu virtuálního počítače jako sloupec v seznamu kompatibilních virtuálních počítačů v sestavě Plánovač nasazení.
- Zotavení po havárii z technologie Hyper-V do Azure:
  - Vyloučené virtuální počítače se sdílenými virtuálními disky a disky PassThrough z profilování. Operace Startprofiling zobrazuje seznam vyloučených virtuálních mů v konzole.
  - Přidány virtuální počítače s více než 64 disky do seznamu nekompatibilních virtuálních počítačů.
  - Byl aktualizován faktor komprese počáteční replikace (IR) a delta replikace (DR).
  - Přidána omezená podpora pro úložiště SMB.

## <a name="version-21"></a>Verze 2.1

**Datum vydání: Leden 3, 2018**

**Opravy:**

- Byla aktualizována sestava aplikace Excel.
- Opraveny chyby v operaci GetThroughput.
- Přidána možnost omezit počet virtuálních disek na profil nebo generovat sestavu. Výchozí limit je 1 000 virtuálních můe.
- VMware do Azure zotavení po havárii:
  - Byl opraven problém s připojením virtuálního zařízení Windows Server 2016 do nekompatibilní tabulky. 
  - Aktualizované zprávy o kompatibilitě pro rozšiřitelné virtuální servery EFI (EFI) Windows.
- Aktualizováno VMware na Azure a Hyper-V na Azure, omezení dat virtuálních počítačích na virtuální počítač. 
- Vylepšená spolehlivost analýzy souborů seznamu virtuálních vod.

## <a name="version-201"></a>Verze 2.0.1

**Datum vydání: Prosinec 7, 2017**

**Opravy:**

- Přidáno doporučení pro optimalizaci šířky pásma sítě.

## <a name="version-20"></a>Verze 2.0

**Datum vydání: Listopad 28, 2017**

**Opravy:**

- Přidána podpora pro technologie Hyper-V pro zotavení po havárii Azure.
- Přičtená kalkulátor nákladů.
- Přidána verze operačního systému pro VMware do Azure zotavení po havárii k určení, pokud virtuální počítač je kompatibilní nebo nekompatibilní pro ochranu. Nástroj používá řetězec verze operačního systému, který je vrácen serverem vCenter pro tento virtuální virtuální měnový virtuální ms. Je to verze hostovaného operačního systému, kterou uživatel vybral při vytváření virtuálního virtuálního rozhraní ve vmware.

**Známá omezení:**

- Pro zotavení po havárii Hyper-V do Azure virtuální `,`počítač `"` `[`s `]`názvem ``` ` ``` obsahující znaky jako: , , , a nejsou podporovány. Pokud profilované, generování sestavy se nezdaří nebo bude mít nesprávný výsledek.
- Pro zotavení po havárii VMware azure virtuální počítač s názvem obsahující čárkou není podporován. Pokud profilované, generování sestavy selže nebo bude mít nesprávný výsledek.

## <a name="version-131"></a>Verze 1.3.1

**Datum vydání: Červenec 19, 2017** 

**Opravy:**

- Přidána podpora pro velké disky (> 1 TB) při generování sestavy. Teď můžete pomocí Plánovače nasazení plánovat replikaci pro virtuální počítače, které mají velikost disku větší než 1 TB (až 4095 GB).
Další informace o [podpoře velkých disků v Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Verze 1.3

**Datum vydání: 9. května 2017**

**Opravy:**

- Přidána podpora spravovaného disku při generování sestavy. Počet virtuálních počítačů, které lze umístit do jednoho účtu úložiště, se vypočítá na základě toho, zda je spravovaný disk vybrán pro převzetí služeb při selhání nebo testování převzetí služeb při selhání.

## <a name="version-12"></a>Verze 1.2

**Datum vydání: 7. dubna 2017**

**Opravy:**

- Přidaný typ spouštění (BIOS nebo EFI) kontroluje pro každý virtuální počítač, aby zjistil, jestli je virtuální počítač kompatibilní nebo nekompatibilní pro ochranu.
- Byly přidány informace o typu operačního systému pro každý virtuální počítač v kompatibilních virtuálních počítačích a nekompatibilních listech virtuálních počítačích.
- Přidána podpora pro operaci GetThroughput pro oblasti Microsoft Azure v USA a Číně.
- Bylo přidáno několik dalších kontrol požadovaných součástí pro vCenter a ESXi Server.
- Opraven problém s nesprávným generováním sestavy, když je nastavení národního prostředí nastaveno na neanglicky.

## <a name="version-11"></a>Verze 1.1

**Datum vydání: Březen 9, 2017**

**Opravy:**

- Byl opraven problém, který zabránil profilování virtuálních počítačů, pokud existují dva nebo více virtuálních počítačů se stejným názvem nebo IP adresou napříč různými hostiteli vCenter ESXi.
- Byl opraven problém, který způsoboval zakázání kopírování a vyhledávání kompatibilních virtuálních počítačích a nekompatibilních listů virtuálních počítačích.

## <a name="version-10"></a>Verze 1.0

**Datum vydání: Únor 23, 2017**

**Známá omezení:**

- Podporuje jenom pro scénáře zotavení po havárii v systému VMware azure. Pro scénáře zotavení po havárii technologie Hyper-V do Azure použijte [nástroj plánovač kapacity Technologie Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Nepodporuje operaci GetThroughput pro oblast microsoft azure v USA a Číně.
- Nástroj nemůže profilovat virtuální servery, pokud má server vCenter dva nebo více virtuálních počítačů se stejným názvem nebo IP adresou napříč různými hostiteli ESXi.
V této verzi nástroj při zjištění duplicitních výskytů názvů nebo IP adres virtuálních počítačů v souboru VMListFile profilaci takových virtuálních počítačů přeskočí. Alternativním řešením je profilování virtuálních počítačů pomocí hostitele ESXi namísto serveru vCenter. Ujistěte se, že spustit jednu instanci pro každého hostitele ESXi.
