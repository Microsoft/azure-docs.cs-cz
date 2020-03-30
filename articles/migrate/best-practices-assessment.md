---
title: Doporučené postupy hodnocení v azure migrate server assessment
description: Tipy pro vytváření hodnocení pomocí Azure Migrate Server Assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185984"
---
# <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytváření hodnocení

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou objevovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum obsahuje nástroje pro migraci Azure a nabídky nezávislého dodavatele softwaru (ISV) od jiných výrobců.

Tento článek shrnuje osvědčené postupy při vytváření hodnocení pomocí nástroje Azure Migrate Server Assessment.

## <a name="about-assessments"></a>O hodnoceních

Hodnocení, která vytvoříte pomocí Azure Migrate Server Assessment, jsou snímek dat v čase. Existují dva typy hodnocení v Azure Migrate.

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení, která doporučení vyhovují na základě shromážděných údajů o výkonnosti | Doporučení velikosti virtuálního počítače je založené na datech využití procesoru a paměti.<br/><br/> Doporučení typu disku (standardní disky HDD/SSD nebo disky spravované s prémií) je založeno na iOPS a propustnosti místních disků.
**As-je v místním prostředí** | Hodnocení, která nepoužívají údaje o výkonu k doporučení. | Doporučení velikosti virtuálního počítače je založené na velikosti místního virtuálního počítače<br/><br> Doporučený typ disku je založen na tom, co vyberete v nastavení typu úložiště pro posouzení.

### <a name="example"></a>Příklad
Například pokud máte místní virtuální počítač se čtyřmi jádry při využití 20 % a pamětí 8 GB s 10 % využitím, budou hodnocení následující:

- **Hodnocení založené na výkonnosti**:
    - Identifikuje efektivní jádra a paměť na základě jádra (4 x 0,20 = 0,8) a využití paměti (8 GB x 0,10 = 0,8).
    - Použije faktor pohodlí specifikovaný ve vlastnostech posouzení (řekněme 1,3x) k získání hodnot, které mají být použity pro velikost. 
    - Doporučuje nejbližší velikost virtuálního počítače v Azure, která podporuje ~1.04 jader (0.8 x 1.3) a ~1.04 GB (0.8 x 1.3) paměti.

- **Jak je (jako místní) hodnocení**:
    -  Doporučuje virtuální ho virtuálního zařízení se čtyřmi jádry; 8 GB paměti.

## <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytváření hodnocení

Zařízení Migrace Azure průběžně profiluje vaše místní prostředí a odesílá do Azure metadata a data o výkonu. Při hodnocení serverů zjištěných pomocí zařízení postupujte podle těchto doporučených postupů:

- **Vytvořit hodnocení podle**toho: Hodnocení podle toho můžete vytvořit okamžitě, jakmile se vaše počítače zobrazí na portálu Migrace Azure.
- **Vytvoření hodnocení založeného na výkonu**: Po nastavení zjišťování doporučujeme počkat alespoň jeden den před spuštěním hodnocení založeného na výkonu:
    - Shromažďování údajů o výkonu vyžaduje čas. Čekání alespoň jeden den zajišťuje, že existuje dostatek bodů dat výkonu před spuštěním hodnocení.
    - Když spouštěte hodnocení založená na výkonu, ujistěte se, že profilujte své prostředí po dobu trvání hodnocení. Pokud například vytvoříte hodnocení s dobou trvání výkonu nastavenou na jeden týden, budete muset počkat alespoň týden po zahájení zjišťování, aby byly shromážděny všechny datové body. Pokud tak nechcete, hodnocení nedostane pětihvězdičkové hodnocení.
- **Přepočítat hodnocení**: Vzhledem k tomu, že hodnocení jsou snímky v čase, nejsou automaticky aktualizovány nejnovějšími daty. Chcete-li aktualizovat hodnocení s nejnovějšími daty, je třeba jej přepočítat.

Postupujte podle těchto doporučených postupů pro hodnocení serverů importovaných do Azure Migrate přes . Soubor CSV:

- **Vytvořit hodnocení podle**toho: Hodnocení podle toho můžete vytvořit okamžitě, jakmile se vaše počítače zobrazí na portálu Migrace Azure.
- **Vytvořit hodnocení založené na výkonu**: To pomáhá získat lepší odhad nákladů, zejména pokud máte nadměrně zřízenou kapacitu serveru v místním prostředí. Přesnost hodnocení na základě výkonu však závisí na údaje o výkonu, které jste určili pro servery. 
- **Přepočítat hodnocení**: Vzhledem k tomu, že hodnocení jsou snímky v čase, nejsou automaticky aktualizovány nejnovějšími daty. Chcete-li aktualizovat hodnocení nejnovějšími importovanými daty, je třeba je přepočítat.

## <a name="best-practices-for-confidence-ratings"></a>Doporučené postupy pro hodnocení spolehlivosti

Při spuštění hodnocení založeného na výkonu se hodnocení uděluje hodnocení spolehlivosti od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší). Efektivní využití hodnocení spolehlivosti:
- Azure Migrate Server Assessment potřebuje data využití procesoru/paměti virtuálního počítače.
- Pro každý disk připojený k místnímu virtuálnímu počítači potřebuje data pro čtení a zápis vstupně-tops/propustnost.
- Pro každý síťový adaptér připojený k virtuálnímu virtuálnímu připojení potřebuje data připojení a vykreslování sítě.

V závislosti na procentním podílu dostupných datových bodů pro vybranou dobu trvání je hodnocení spolehlivosti pro hodnocení uvedeno shrnuté v následující tabulce.

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček


## <a name="common-assessment-issues"></a>Společné otázky hodnocení

Zde je návod, jak řešit některé běžné problémy prostředí, které ovlivňují hodnocení.

###  <a name="out-of-sync-assessments"></a>Nesynchronizovaná hodnocení

Pokud po vytvoření hodnocení přidáte nebo odeberete počítače ze skupiny, bude vytvořené hodnocení označeno jako **nesynchronizované**. Spusťte hodnocení znovu (**Přepočítat**), aby odrážely změny ve skupině.

### <a name="outdated-assessments"></a>Zastaralá hodnocení

Pokud existují místní změny virtuálních počítačů, které jsou ve skupině, která byla posouzena, hodnocení je označeno **zastaralé**. Chcete-li tyto změny zohlednit, spusťte hodnocení znovu.

### <a name="low-confidence-rating"></a>Nízké hodnocení spolehlivosti

Posouzení nemusí mít všechny datové body z několika důvodů:

- Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Například pokud vytváříte *hodnocení založené na výkonu* s dobou trvání výkonu nastavena na jeden týden, budete muset počkat alespoň týden po spuštění zjišťování pro všechny datové body získat shromážděny. Vždy můžete kliknout na **Přepočítat** a zobrazit nejnovější příslušné hodnocení spolehlivosti. Hodnocení spolehlivosti je použitelné pouze v případě, že vytvoříte hodnocení *založené na výkonu.*

- Během období, pro které se posouzení počítá, se několik virtuálních počítačů vypnulo. Pokud po nějakou dobu byly některé virtuální počítače vypnuté, nástroj Hodnocení serverů nebude schopen za toto období shromáždit data o výkonu.

- Po zahájení zjišťování v nástroji Hodnocení serverů se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takovém případě nebudou k dispozici data o výkonu nových virtuálních počítačů za celou dobu trvání a míra spolehlivosti bude nízká.


## <a name="next-steps"></a>Další kroky

- [Přečtěte si,](concepts-assessment-calculation.md) jak se hodnocení počítají.
- [Přečtěte si,](how-to-modify-assessment.md) jak přizpůsobit hodnocení.
