---
title: Historie verzí plánovače nasazení Azure Site Recovery
description: Známé opravy různých verzí plánovače nasazení obnovení lokality a známých omezeních spolu s jejich data vydání.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927369"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Historie verzí plánovače nasazení Azure Site Recovery

Tento článek obsahuje historii všech verzí technologie Azure Site Recovery Deployment Planner spolu s opravy, známé omezení v každém a jejich data vydání.

## <a name="version-24"></a>Verze 2.4

**Datum vydání: 17. dubna 2019**

**Opravy:**

- Vylepšená Kompatibilita operačního systému, zejména při zpracování chyb založené na lokalizaci.
- Přidání virtuálních počítačů s až 20 MB/s dat změnit frekvence změny kontrolní seznam kompatibility.
- Vylepšené chybové zprávy
  - Přidání podpory pro vCenter 6.7.
  - Přidání podpory pro pracovní stanice systému Windows Server 2019 a Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Verze 2.3

**Datum vydání: 3. prosince 2018**

**Opravy:**

- Opravili jsme problém, která zabránila plánovače nasazení služby generování sestav pomocí zadané cílové umístění a předplatném.

## <a name="version-22"></a>Verze 2.2 

**Datum vydání: 25. dubna 2018**

**Opravy:**

- Operace GetVMList:
  - Opravili jsme problém, který způsobil GetVMList selhat, pokud zadaná složka neexistuje. Se teď vytvoří výchozí adresář, nebo vytvoří adresář zadaný v parametru outputfile.
  - Přidat že podrobnější selhání důvodů, proč GetVMList.
- Byly přidané informace typu virtuálního počítače jako sloupec v seznamu kompatibilních virtuálních počítačů sestavy plánovače nasazení služby.
- Technologie Hyper-V k zotavení po havárii Azure:
  - Vyloučené virtuálních počítačů pomocí sdílené virtuální pevné disky a průchozí disky ze profilace. Operace Startprofiling zobrazuje seznam vyloučených virtuální počítače v konzole.
  - Přidání virtuálních počítačů s více než 64 disků na seznam nekompatibilních virtuálních počítačů.
  - Aktualizovat počáteční replikaci (IR) a faktor komprese rozdílová replikace (DR).
  - Přidání omezená podpora úložiště s protokolem SMB.

## <a name="version-21"></a>Verze 2.1

**Datum vydání: 3. ledna 2018**

**Opravy:**

- Aktualizovat sestavy aplikace Excel.
- Oprava chyby v operaci GetThroughput.
- Přidání možnost omezit počet virtuálních počítačů do profilu nebo generování sestav. Výchozí omezení je 1 000 virtuálních počítačů.
- VMware na zotavení po havárii Azure:
  - Opravili jsme problém, Windows Server 2016 virtuálního počítače přicházející do tabulky kompatibilní. 
  - Aktualizace zprávy o kompatibilitě pro virtuální počítače s Windows rozhraní EFI (Extensible Firmware Interface).
- Aktualizovat VMware do Azure a Hyper-V do Azure, četnost změn dat virtuálního počítače limit na virtuální počítač. 
- Vylepšení spolehlivosti při analýze souboru seznamu virtuálních počítačů.

## <a name="version-201"></a>Verze 2.0.1

**Datum vydání: 7. prosince 2017**

**Opravy:**

- Přidání doporučení k optimalizaci šířky pásma sítě.

## <a name="version-20"></a>Verze 2.0

**Datum vydání: 28. listopadu 2017**

**Opravy:**

- Přidání podpory pro technologii Hyper-V pro zotavení po havárii Azure.
- Kalkulačka dodatečný poplatek.
- Přidání kontroly verze OS pro replikaci z VMware na zotavení po havárii Azure k určení, zda je virtuální počítač kompatibilní nebo nekompatibilní z hlediska ochrany. Nástroj používá řetězec verze operačního systému, který je vrácená serverem vCenter pro tento virtuální počítač. Verze operačního systému hosta je tento uživatel vybral při vytváření virtuálního počítače v prostředí VMware.

**Známá omezení:**

- Pro Hyper-V do Azure zotavení po havárii, virtuální počítač s názvem obsahujícím znaky jako: `,`, `"`, `[`, `]`, a ``` ` ``` nejsou podporovány. Je-li Profilovat, generování sestav selžou nebo budou mít nesprávný výsledek.
- Pro replikaci z VMware na zotavení po havárii Azure nepodporuje virtuální počítač s názvem obsahující čárkami. Je-li Profilovat, selže generování sestav nebo budou mít nesprávný výsledek.

## <a name="version-131"></a>Verze 1.3.1

**Datum vydání: 19. července 2017** 

**Opravy:**

- Byla přidána podpora velkých disků (> 1 TB) v generování sestav. Teď můžete použít Plánovač nasazení služby k plánování replikace pro virtuální počítače, jejichž disky větší než 1 TB (až 4 095 GB).
Další informace o [podpoře velkých disků v Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Verze 1.3

**Datum vydání: 9. května 2017**

**Opravy:**

- Přidání podpory pro spravovaného disku v generování sestav. Počet virtuálních počítačů, které lze umístit do jednoho účtu úložiště se počítá na základě, pokud je vybrán spravovaný disk pro převzetí služeb při selhání nebo testovací převzetí služeb při selhání.

## <a name="version-12"></a>verze 1.2

**Datum vydání: 7. duben 2017**

**Opravy:**

- Přidání spuštění (BIOS nebo EFI) kontrol typů pro každý virtuální počítač k určení, zda je virtuální počítač kompatibilní nebo nekompatibilní z hlediska ochrany.
- Přidání operačního systému zadejte informace pro každý virtuální počítač do kompatibilních virtuálních počítačů a listy nekompatibilní virtuální počítače.
- Přidání podpory pro operaci GetThroughput pro oblastech US Government a Čína Microsoft Azure.
- Bylo přidáno několik dalších kontrol požadovaných součástí pro vCenter a ESXi Server.
- Opravili jsme problém, vygeneruje při nastavení národního prostředí jsou nastaveny na jiné než anglické jazykové nesprávné sestavy.

## <a name="version-11"></a>Verze 1.1

**Datum vydání: 9. března 2017**

**Opravy:**

- Opravili jsme problém, který zabránil profilace virtuálních počítačů, pokud existují dvě nebo více virtuálních počítačů se stejným názvem nebo IP adresou v různých hostitelích ESXi vCenter.
- Opravili jsme problém způsobující, kopírování a vyhledávání pro kompatibilní virtuální počítače a kompatibilní virtuální počítače listů deaktivuje.

## <a name="version-10"></a>Verze 1.0

**Datum vydání: 23. února 2017**

**Známá omezení:**

- Podporuje jenom pro replikaci z VMware do scénáře zotavení po havárii Azure. Pro Hyper-V pro scénáře zotavení po havárii Azure, použijte [nástroj Plánovač kapacity Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Nepodporuje operaci GetThroughput pro oblastech US Government a Čína Microsoft Azure.
- Profil nelze nástroj virtuálních počítačů, pokud vCenter server obsahuje dva nebo více virtuálních počítačů se stejným názvem nebo IP adresou v různých hostitelích ESXi.
V této verzi nástroj při zjištění duplicitních výskytů názvů nebo IP adres virtuálních počítačů v souboru VMListFile profilaci takových virtuálních počítačů přeskočí. Alternativním řešením je profilování virtuálních počítačů pomocí hostitele ESXi namísto serveru vCenter. Nezapomeňte spustit jednu instanci pro každého hostitele ESXi.
