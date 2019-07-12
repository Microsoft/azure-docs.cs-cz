---
title: Přizpůsobení posouzení pro posouzení migrace serveru Azure | Dokumentace Microsoftu
description: Popisuje, jak přizpůsobit posouzení vytvořená pomocí Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807923"
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

Tento článek popisuje, jak přizpůsobit posouzení vytvořená pomocí Azure Migrate Server Assessment.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a soukromého a veřejného cloudu virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).

Azure Migrate Server Assessment tool můžete vytvořit posouzení místních virtuálních počítačů VMware a virtuálních počítačů Hyper-V během přípravy na migraci do Azure. 

## <a name="about-assessments"></a>Informace o posouzení

Existují dva typy posouzení, která můžete spustit pomocí Azure Migrate Server Assessment.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě výkonu shromážděných dat | **Doporučená velikost virtuálního počítače**: Na základě dat využití procesoru a paměti.<br/><br/> **Typ disku (standard nebo premium spravovaného disku) doporučuje**: Na základě vstupně-výstupních operací a propustnosti s místními disky.
**Jako místní** | Posouzení podle velikosti na místě. | **Doporučená velikost virtuálního počítače**: Na základě velikosti virtuálního počítače místní<br/><br> **Typ disku doporučujeme**: Podle nastavení typ úložiště, které vyberete pro posouzení.


## <a name="how-is-an-assessment-done"></a>Jak se dělá posouzení?

Posouzení služby Azure Migrate má tři fáze. Posouzení začíná vhodnost analýzy, za nímž následuje velikosti, a nakonec měsíční odhadu nákladů. Počítače s pouze přesune později v případě úspěšného předchozí. Například pokud na počítači selže kontrola vhodnost pro Azure, je označen jako nevhodný pro Azure a změny velikosti a ocenění nebude provedeno.

## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/> Azure Migrate v současné době podporuje tyto cílové oblasti: Austrálie – východ, Austrálie – jihovýchod, Brazílie – Jih, Kanada – střed, Kanada – východ, střed Indie, střed USA, Čína – východ, Čína – sever, východní Asie, východní USA, východní USA 2, Německo – střed, Německo – severovýchod, Japonsko – východ, Japonsko – Západ, Korea – střed, Korea – Jih, severní USA (střed), Severní Evropa, střední část jihu USA, jihovýchodní Asie, Indie – Jih, Velká Británie – Jih, Velká Británie – Západ, USA Arizona, USA (gov) Gov Texas, USA (gov) Virginia, USA (střed) – Západ, západní Evropa, Indie – Západ, USA – západ a západní USA 2.<br/> Cílová oblast je standardně nastavená na Západní USA 2.
**Typ úložiště** | Disky nebo Standard standardní HHD disky SSD nebo Premium.<br/> Když zadáte typ úložiště jako automatické v posouzení, disk doporučení je založeno na data o výkonu disků (IOPS a propustnost).<br/> Pokud chcete zadat typ úložiště jako Premium nebo Standard, vybrat posouzení doporučuje disk SKU v rámci typu úložiště.<br/> Pokud chcete dosáhnout jednu instanci virtuálního počítače SLA 99,9 % dostupnost, můžete nastavit typ úložiště jako Premium managed disks. Potom všechny disky v posouzení doporučí jako Premium managed disks. <br/> Azure Migrate podporuje pro posouzení migrace jenom spravované disky.<br/> 
**Rezervované instance (RI)** | Tuto vlastnost zadejte, pokud obsahují rezervované instance v Azure. Odhad nákladů v posouzení se rezervované instance slevy vezměte v úvahu. Rezervované instance jsou momentálně podporuje jenom pro průběžné platby ve službě Azure Migrate.
**Kritérium určení velikosti** | Používá k nastavení správné velikosti virtuálních počítačů. Nastavení velikosti může být založené na výkonu nebo **jako místní**, bez zohlednění historie výkonu.
**Historie výkonu** | Doba trvání má zohlednit při vyhodnocování výkonu virtuálních počítačů. Tato vlastnost je použitelná, pouze pokud je určení velikosti na základě výkonu.
**Percentilové využití** | Hodnota percentilu sady vzorků výkonu, který se používá pro určení správné velikosti virtuálních počítačů. Tato vlastnost je použitelná, pouze pokud je určení velikosti na základě výkonu.
**Řada virtuálních počítačů** | Řada virtuálních počítačů, která se použije k odhadu velikostí. Pokud máte například produkční prostředí, které se nechystáte migrovat na virtuální počítače řady A-Series v Azure, můžete vyloučit řadu A-Series ze seznamu nebo řad. Určení velikosti se provádí pouze na základě vybraných řad.
**Faktor komfortu** | Azure Migrate Server Assessment počítá s rezervou (faktor komfortu) během posouzení. Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry.
**Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Fakturační měna. 
**Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure.<br/> Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud se vaše virtuální počítače běžet 24 x 7 v Azure, můžete určit dobu trvání (počet dnů za měsíc) a počtu hodin za den pro kterou se bude spuštěna a odhady nákladů, které by se dělalo odpovídajícím způsobem.<br/> Výchozí hodnota je 31 dnů za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určuje, zda máte software assurance a nárok [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. | Výchozí hodnota je Yes (Ano).


## <a name="edit-assessment-properties"></a>Upravit vlastnosti posouzení

Chcete-li upravit vlastnosti posouzení po vytvoření posouzení, postupujte takto:

1. V projektu Azure Migrate klikněte na tlačítko **servery**.
2. V **Azure Migrate: Server Assessment**, klikněte na počet posouzení.
3. V **posouzení**, klikněte na příslušné posouzení > **upravit vlastnosti**.
5. Upravit vlastnosti posouzení podle výše uvedené tabulce.
6. Klikněte na tlačítko **Uložit** aktualizovat posouzení.


Při vytváření posouzení můžete také upravit vlastnosti posouzení.


## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
