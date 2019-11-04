---
title: Osvědčené postupy pro vytváření hodnocení pomocí Azure Migrate posouzení serveru
description: Poskytuje tipy pro vytváření hodnocení pomocí Azure Migrate posouzení serveru.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: raynew
ms.openlocfilehash: e235116ab77159a0e2e9c66ad09cdb86ce6da1e9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466942"
---
# <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytváření hodnocení

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento článek shrnuje osvědčené postupy při vytváření posouzení pomocí nástroje Azure Migrate Server Assessment Tool.

## <a name="about-assessments"></a>O posouzení

Posouzení, které vytvoříte pomocí Azure Migrate posouzení serveru, jsou snímkem dat v určitém časovém okamžiku. Existují dva typy hodnocení v Azure Migrate.

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení, která vytvářejí doporučení na základě shromážděných údajů o výkonu | Doporučení na velikost virtuálního počítače vychází z dat využití procesoru a paměti.<br/><br/> Doporučení pro typ disku (standardní disková jednotka/SSD nebo Premium – spravované disky) vychází z IOPS a propustnosti místních disků.
**V místním prostředí** | Posouzení, které nepoužívají údaje o výkonu k vytváření doporučení. | Doporučení velikosti virtuálního počítače je založené na velikosti místního virtuálního počítače.<br/><br> Doporučený typ disku je založený na tom, co jste vybrali v nastavení typ úložiště pro posouzení.

### <a name="example"></a>Příklad:
Pokud máte například místní virtuální počítač se čtyřmi jádry na 20% využití a paměť 8 GB s 10% využitím, posouzení bude následující:

- **Posouzení na základě výkonu**:
    - Identifikuje efektivní jádra a paměť na základě jádra (4 x 0,20 = 0,8) a paměti (8 GB × 0,10 = 0,8).
    - Použije faktor pohodlí určený ve vlastnostech posouzení (řekněme 1,3 x) a získá hodnoty, které se použijí pro určení velikosti. 
    - Doporučuje nejbližší velikost virtuálního počítače v Azure, která může podporovat ~ 1,04 jader (0,8 × 1,3) a ~ 1,04 GB (0,8 x 1,3).

- **Vyhodnocení, jak je (jako místní) posouzení**:
    -  Doporučuje virtuální počítač se čtyřmi jádry. 8 GB paměti.

## <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytváření hodnocení

Zařízení Azure Migrate průběžně profiluje vaše místní prostředí a odesílá data o metadatech a výkonu do Azure. Dodržujte tyto osvědčené postupy pro posouzení serverů zjištěných pomocí zařízení:

- **Vytváření hodnocení,** která jsou vyhodnocena jako – jsou vyhodnocena ihned po zobrazení počítačů na portálu Azure Migrate.
- **Vytvořit vyhodnocení na základě výkonu**: po nastavení zjišťování doporučujeme, abyste před spuštěním posouzení na základě výkonu čekali aspoň jeden den:
    - Shromažďování dat o výkonu trvá déle. Čekání alespoň na den zajistí dostatek datových bodů výkonu před spuštěním posouzení.
    - Pokud používáte posouzení na základě výkonu, ujistěte se, že jste profilaci prostředí pro dobu hodnocení. Pokud například vytvoříte posouzení s dobou trvání výkonu nastavenou na jeden týden, budete muset po zahájení zjišťování počkat aspoň týden, aby se všechny datové body shromáždily. Pokud to neuděláte, hodnocení nebude mít hodnocení s pěti hvězdičkami.
- **Přepočítat vyhodnocení**: vzhledem k tomu, že posouzení jsou snímky v čase, nejsou automaticky aktualizovány pomocí nejnovějších dat. Chcete-li aktualizovat posouzení s nejnovějšími daty, je nutné jej přepočítat.

Dodržujte tyto osvědčené postupy pro posouzení serverů importovaných do Azure Migrate prostřednictvím. Soubor CSV:

- **Vytváření hodnocení,** která jsou vyhodnocena jako – jsou vyhodnocena ihned po zobrazení počítačů na portálu Azure Migrate.
- **Vytvoření vyhodnocení na základě výkonu**: pomáhá získat lepší odhad nákladů, zejména pokud máte nadřízenou kapacitu serveru v místním prostředí. Přesnost posouzení na základě výkonu však závisí na datech výkonu určených pro tyto servery. 
- **Přepočítat vyhodnocení**: vzhledem k tomu, že posouzení jsou snímky v čase, nejsou automaticky aktualizovány pomocí nejnovějších dat. Chcete-li aktualizovat posouzení s nejnovějšími importovanými daty, je nutné je přepočítat.

## <a name="best-practices-for-confidence-ratings"></a>Osvědčené postupy pro hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, hodnocení se od 1 hvězdičky (nejnižší) až 5 hvězdiček (nejvyšší) přiřadí k posouzení. Efektivní použití hodnocení spolehlivosti:
- Azure Migrate posouzení serveru potřebuje data o využití pro procesor nebo paměť virtuálních počítačů.
- Pro každý disk připojený k místnímu virtuálnímu počítači potřebuje data pro čtení a zápis IOPS/propustnost.
- Pro každý síťový adaptér připojený k virtuálnímu počítači potřebuje data v/v síti.

V závislosti na procentu datových bodů dostupných pro vybranou dobu trvání je hodnocení spolehlivosti pro posouzení uvedené shrnuté v následující tabulce.

   **Dostupnost datového bodu** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček


## <a name="common-assessment-issues"></a>Běžné problémy s posouzením

Tady je postup, jak řešit některé běžné problémy s prostředím, které mají vliv na posouzení.

###  <a name="out-of-sync-assessments"></a>Posouzení nesynchronizovaných

Pokud po vytvoření posouzení přidáte nebo odeberete počítače ze skupiny, bude hodnocení, které jste vytvořili, označeno jako **nesynchronizované**. Spusťte posouzení znovu (**Přepočítat**), aby odráželo změny skupiny.

### <a name="outdated-assessments"></a>Zastaralá posouzení

Pokud existují místní změny virtuálních počítačů, které jsou ve skupině, která je vyhodnocena, je posouzení označeno jako **zastaralé**. Aby se změny projevily, spusťte posouzení znovu.

### <a name="low-confidence-rating"></a>Hodnocení nízké spolehlivosti

Posouzení nemusí mít všechny datové body z několika důvodů:

- Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Pokud například vytváříte *hodnocení založené na výkonu* s dobou trvání trvání nastavenou na jeden týden, budete muset po zahájení zjišťování všech datových bodů, které se mají shromáždit, počkat aspoň na týden. Kdykoli můžete kliknout na **Přepočítat** a zobrazit nejnovější použitelné hodnocení spolehlivosti. Hodnocení spolehlivosti se dá použít jenom v případě, že vytvoříte hodnocení *založené na výkonu* .

- Během období, pro které se posouzení počítá, se několik virtuálních počítačů vypnulo. Pokud po nějakou dobu byly některé virtuální počítače vypnuté, nástroj Hodnocení serverů nebude schopen za toto období shromáždit data o výkonu.

- Po zahájení zjišťování v nástroji Hodnocení serverů se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takovém případě nebudou k dispozici data o výkonu nových virtuálních počítačů za celou dobu trvání a míra spolehlivosti bude nízká.


## <a name="next-steps"></a>Další kroky

- [Přečtěte si](concepts-assessment-calculation.md) , jak se počítají vyhodnocení.
- [Přečtěte si](how-to-modify-assessment.md) , jak přizpůsobit posouzení.
