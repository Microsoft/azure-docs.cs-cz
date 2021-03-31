---
title: Historie verze Plánovač nasazení služby Azure Site Recovery
description: Známé různé verze Site Recovery Plánovač nasazení verzích a známá omezení spolu s daty jejich vydání.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133807"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Historie verze Plánovač nasazení služby Azure Site Recovery

Tento článek obsahuje historii všech verzí Plánovač nasazení služby Azure Site Recovery spolu s opravami, známá omezení v jednotlivých verzích a jejich datum vydání.

## <a name="version-252"></a>Verze 2,52

**Datum vydání: 4. června 2020**

**Řeší**

- Přidání podpory pro vCenter 7,0
- Přidání podpory pro následující operační systémy:

    - SUSE Linux Enterprise 15 (se všemi dílčími verzemi)
    - Red Hat Enterprise Linux 8 (s dílčími verzemi)


## <a name="version-251"></a>Verze 2,51

**Datum vydání: 22. srpna 2019**

**Řeší**

- Opravili jsme problém s doporučeními pro náklady Plánovač nasazení verze 2,5

## <a name="version-25"></a>Verze 2,5

**Datum vydání: 29. července 2019**

**Řeší**

- U virtuálních počítačů VMware a fyzických počítačů se doporučení aktualizuje, aby byla založená na replikaci do Managed Disks.
- Přidání podpory pro Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 nebo novější

## <a name="version-24"></a>Verze 2,4

**Datum vydání: 17. dubna 2019**

**Řeší**

- Vylepšená kompatibilita s operačním systémem, konkrétně při zpracování chyb založených na lokalizaci.
- Do kontrolního seznamu kompatibility se přidaly virtuální počítače s až 20 MB/s přenosovou rychlostí (změny).
- Vylepšení chybových zpráv
- Přidání podpory pro vCenter 6,7.
- Přidala se podpora pro Windows Server 2019 a pracovní stanici Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Verze 2,3

**Datum vydání: 3. prosince 2018**

**Řeší**

- Opravili jsme problém, který zabránil Plánovač nasazení generování sestavy se zadaným cílovým umístěním a předplatným.

## <a name="version-22"></a>Verze 2,2 

**Datum vydání: 25. dubna 2018**

**Řeší**

- Operace GetVMList:
  - Opravili jsme problém, který způsobil, že GetVMList selže, pokud Zadaná složka neexistuje. Nyní buď vytvoří výchozí adresář, nebo vytvoří adresář zadaný v parametru Výstupní_soubor.
  - Bylo přidáno podrobnější důvody selhání pro GetVMList.
- Informace o typu virtuálního počítače byly přidány jako sloupec v seznamu kompatibilních virtuálních počítačů sestavy Plánovač nasazení.
- Zotavení po havárii z Hyper-V do Azure:
  - Vyloučené virtuální počítače se sdílenými virtuálními počítači a průchozí disky z profilace. Operace Startprofiling zobrazí seznam vyloučených virtuálních počítačů v konzole nástroje.
  - Do seznamu nekompatibilních virtuálních počítačů se přidaly virtuální počítače s více než 64 disky.
  - Aktualizace kompresního faktoru počáteční replikace (IR) a rozdílová replikace (DR).
  - Přidání omezené podpory pro úložiště SMB.

## <a name="version-21"></a>Verze 2,1

**Datum vydání: 3. ledna 2018**

**Řeší**

- Aktualizace sestavy aplikace Excel
- Opravené chyby v operaci getpropustnosti.
- Přidání možnosti pro omezení počtu virtuálních počítačů pro profilaci nebo generování sestavy. Výchozí limit je 1 000 virtuálních počítačů.
- Zotavení po havárii z VMware do Azure:
  - Opravili jsme problém s virtuálním počítačem s Windows serverem 2016, který přechází do nekompatibilní tabulky. 
  - Aktualizované zprávy o kompatibilitě pro virtuální počítače s Windows EFI (Extensible Firmware Interface).
- Aktualizace VMware na Azure a Hyper-V do Azure, omezení četnosti dat virtuálních počítačů na virtuální počítač. 
- Vylepšená spolehlivost analýzy souborů seznamu virtuálních počítačů.

## <a name="version-201"></a>Verze 2.0.1

**Datum vydání: 7. prosince 2017**

**Řeší**

- Bylo přidáno doporučení pro optimalizaci šířky pásma sítě.

## <a name="version-20"></a>Verze 2,0

**Datum vydání: 28. listopadu 2017**

**Řeší**

- Přidala se podpora pro zotavení po havárii z Hyper-V do Azure.
- Přidání cenové kalkulačky
- Přidání kontroly verze operačního systému pro zotavení po havárii VMware do Azure, aby bylo možné zjistit, jestli je virtuální počítač kompatibilní nebo nekompatibilní s ochranou. Nástroj používá řetězec verze operačního systému, který je vrácen serverem vCenter pro daný virtuální počítač. Je to verze hostovaného operačního systému, kterou uživatel zvolil při vytváření virtuálního počítače ve VMware.

**Známá omezení:**

- Pro zotavení po havárii z Hyper-V do Azure se jako virtuální počítač s názvem, který obsahuje tyto znaky: `,` , `"` ,, `[` `]` a ``` ` ``` nepodporuje. Při profilování se generování sestav nezdaří nebo bude mít nesprávný výsledek.
- Pro zotavení po havárii z VMware do Azure se virtuální počítač s názvem, který obsahuje čárka, nepodporuje. Pokud profilace, generování sestav selže nebo dojde k nesprávnému výsledku.

## <a name="version-131"></a>Verze 1.3.1

**Datum vydání: 19. července 2017** 

**Řeší**

- Přidání podpory pro velké disky (> 1 TB) při generování sestav. Nyní můžete použít Plánovač nasazení k naplánování replikace pro virtuální počítače, které mají velikost disků větší než 1 TB (až 4095 GB).
Další informace o [podpoře velkých disků v Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Verze 1,3

**Datum vydání: 9. května 2017**

**Řeší**

- Přidání podpory pro spravovaný disk v generování sestav. Počet virtuálních počítačů, které se dají umístit do jednoho účtu úložiště, se vypočítá na základě toho, jestli je pro převzetí služeb při selhání nebo testovací převzetí služeb při selhání vybraný spravovaný disk.

## <a name="version-12"></a>Verze 1,2

**Datum vydání: 7. dubna 2017**

**Řeší**

- Byl přidán typ spouštění (BIOS nebo EFI) pro každý virtuální počítač, abyste zjistili, jestli je virtuální počítač kompatibilní nebo nekompatibilní s ochranou.
- Pro každý virtuální počítač se přidaly informace o typu operačního systému na listech kompatibilních a nekompatibilních virtuálních počítačů.
- Přidání podpory pro operaci getpropustnosti pro státní správu USA a Čínu Microsoft Azure oblasti.
- Bylo přidáno několik dalších kontrol požadovaných součástí pro vCenter a ESXi Server.
- Opravili jsme problém s vygenerováním nesprávné sestavy, když jsou nastavení národního prostředí nastavená na jinou než angličtinu.

## <a name="version-11"></a>Verze 1,1

**Datum vydání: 9. března 2017**

**Řeší**

- Opravili jsme problém, který znemožňuje profilování virtuálních počítačů, pokud existují dva nebo víc virtuálních počítačů se stejným názvem nebo IP adresou v různých hostitelích vCenter ESXi.
- Opravili jsme problém, který způsobil, že kopírování a hledání na listech kompatibilních virtuálních počítačů a nekompatibilních virtuálních počítačů bylo zakázané.

## <a name="version-10"></a>Verze 1,0

**Datum vydání: 23. února 2017**

**Známá omezení:**

- Podporuje se jenom pro scénáře zotavení po havárii z VMware do Azure. Pro scénáře zotavení po havárii z Hyper-V do Azure použijte [Nástroj pro plánování kapacity technologie Hyper-v](./hyper-v-deployment-planner-overview.md).
- Nepodporuje operaci getpropustnosti pro oblasti státní správy USA a Číny Microsoft Azure.
- Nástroj cann't Profile virtuální počítače, pokud Server vCenter obsahuje dva nebo více virtuálních počítačů se stejným názvem nebo IP adresou v různých hostitelích ESXi.
V této verzi nástroj při zjištění duplicitních výskytů názvů nebo IP adres virtuálních počítačů v souboru VMListFile profilaci takových virtuálních počítačů přeskočí. Alternativním řešením je profilování virtuálních počítačů pomocí hostitele ESXi namísto serveru vCenter. Zajistěte, aby pro každého hostitele ESXi běžela jedna instance.
