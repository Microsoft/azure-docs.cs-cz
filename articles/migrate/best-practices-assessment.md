---
title: Osvědčené postupy pro vytvoření posouzení pomocí Azure Migrate Server Assessment | Dokumentace Microsoftu
description: Poskytuje tipy pro vytváření posouzení pomocí Azure Migrate Server Assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: d417efd4abf14247af171ea77b479f590e14fe76
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812965"
---
# <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytvoření posouzení

[Azure Migrate](migrate-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky. 

Tento článek shrnuje osvědčené postupy při vytváření posouzení pomocí Azure Migrate Server Assessment tool. 

## <a name="about-assessments"></a>Informace o posouzení

Posouzení, které vytvoříte pomocí Azure Migrate Server Assessment jsou data snímku bodu v čase. Existují dva typy posouzení ve službě Azure Migrate.

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Vyhodnocení, které doporučení na základě výkonu shromážděných dat | Doporučená velikost virtuálního počítače podle data o využití procesoru a paměti.<br/><br/> Doporučení pro typ disku (standard nebo premium spravované disky) podle IOPS a propustnost s místními disky.
**Jako – místní** | Posouzení, které nepoužívají údaje o výkonu pro doporučení. | Doporučená velikost virtuálního počítače je založen na velikosti virtuálního počítače místní<br/><br> Typ doporučené diskové podle výběru v nastavení pro posouzení typu úložiště.

### <a name="example"></a>Příklad
Například pokud máte místní virtuální počítač s čtyři jádra v 20 % využitím a 8 GB paměti s využitím 10 %, posouzení, která bude následující:

- **Na základě výkonu posouzení**:
    - Doporučuje jader a paměti na základě základní (0,8 jader) a využití paměti (0,8 GB).
    - Posouzení použije výchozí faktor komfortu 30 %.
    - Doporučení pro virtuální počítače: ~1.4 jader (0,8 x1.3) a ~1.4 GB paměti.
- **Jako-posouzení (v místním prostředí) je**:
    -  Doporučuje se 4 jádry; virtuálního počítače 8 GB paměti.

## <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytvoření posouzení

Zařízení Azure Migrate průběžně profily v místním prostředí a odesílá metadata a výkonu data do Azure. Použijte tyto osvědčené postupy pro vytvoření posouzení:

- **Vytvořit jako-je posouzení**: Můžete vytvořit jako-je ihned po zjištění posouzení.
- **Vytvořit posouzení na základě výkonu**: Po nastavení zjišťování, doporučujeme vám, že počkat aspoň jeden den před spuštěním posouzení na základě výkonu:
    - Shromažďování dat výkonu trvá určitou dobu. Počkejte aspoň jeden den zajišťuje, že existuje dostatek datových bodů výkonu před spuštěním posouzení.
    - Údaje o výkonu zařízení shromažďuje data v reálném čase body každých 20 sekund pro jednotlivé metriky výkonu a zahrne do jednoho pět minut datového bodu. Zařízení odesílá pět minut datových bodů do Azure za hodinu pro výpočet posouzení.  
- **Umožňuje získat nejnovější data**: Posouzení se automaticky aktualizují pomocí nejnovějších dat. Posouzení aktualizace pomocí nejnovějších dat, budete muset znovu spustit. 
- **Ujistěte se, že odpovídají doby trvání**: Pokud spouštíte posouzení založená na výkon, ujistěte se, že váš profil prostředí po dobu hodnocení. Například pokud vytváříte posouzení s dobu trvání výkonu nastavte na jeden týden, budete muset počkat alespoň týden po spuštění zjišťování pro všechny datové body, které se mají shromažďovat. Pokud to neuděláte, nebude posouzení získání hodnocení pěti hvězdičkami. 
- **Vyhnout se chybějícím datových bodů**: K následujícím potížím může vést k chybějící datové body v rámci interního hodnocení na základě výkonu:
    - Virtuální počítače jsou vypnuté, vyhodnocení a data o výkonu se shromažďují. 
    - Pokud vytvoříte virtuální počítače v měsíci, ve kterém vytváříte historie výkonu. data pro tyto virtuální počítače budou méně než měsíc. 
    - Posouzení se vytvoří ihned po zjištění nebo vyhodnocení času neodpovídá doby shromažďování dat výkonu.

## <a name="best-practices-for-confidence-ratings"></a>Osvědčené postupy pro hodnocení spolehlivosti

Při spuštění posouzení založená na výkon je udělena hodnocení spolehlivosti hodnocení od 1 (nejnižší) do 5 hvězdiček (nejvyšší). Chcete-li použít hodnocení spolehlivosti efektivně:
- Azure Migrate Server Assessment musí data o využití procesoru/paměti virtuálního počítače a data IOPS a propustnosti disku.
- Pro každý síťový adaptér připojený k virtuálnímu počítači potřebuje Azure Migrate sítě ve vstupně-výstupní data.
- Pokud není k dispozici v systému vCenter Server dat o využití, doporučení velikosti provedené službou Azure Migrate nemusí být spolehlivé. 

V závislosti na procento datových bodů, které jsou k dispozici hodnocení spolehlivosti posouzení jsou shrnuty v následující tabulce.

   **Dostupnost datového bodu** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 Star
   81 až 100 % | 5 hvězdiček

- Pokud se zobrazí hodnocení spolehlivosti posouzení, která je nižší než pěti hvězdičkami, počkat aspoň jeden den a pak přepočítat posouzení.
- Nízké hodnocení znamená, že změny velikosti doporučení nemusí být spolehlivé. V takovém případě doporučujeme vám, že upravíte vlastnosti posouzení pro použití jako-je místní posouzení.

## <a name="common-assessment-issues"></a>Běžné problémy posouzení

Tady je postup k vyřešení některé běžné problémy prostředí, které ovlivňují posouzení.

###  <a name="out-of-sync-assessments"></a>Posouzení na více instancí synchronizace

Je-li přidat nebo odebrat počítače ze skupiny, poté, co vytvoříte posouzení, budou označeny posouzení, které jste vytvořili **mimo synchronizace**. Spustit vyhodnocení znovu (**přepočítat**) tak, aby odrážely změny skupiny.

### <a name="outdated-assessments"></a>Zastaralé posouzení

Pokud existují změny v místní virtuální počítače, které jsou ve skupině, který se hodnotí, se označí posouzení **zastaralé**. Tak, aby odrážely změny, spusťte posouzení znovu.

### <a name="missing-data-points"></a>Chybějící datové body

Posouzení nemusí mít všechny datové body z několika důvodů:

- Virtuální počítače může být vypnutý, vyhodnocení a data o výkonu se shromažďují. 
- Může být virtuální počítače vytvořené během jednoho měsíce na výkon, který je na základě historie, proto své údaje o výkonu je méně než měsíc. 
- Posouzení byla vytvořena ihned po zjištění. Aby bylo možné shromažďovat data o výkonu pro určenou dobu, budete muset počkat určenou dobu před spuštěním posouzení. Například pokud chcete posoudit údaje o výkonu pro týden, budete muset počkejte týden po zjišťování. Pokud to neuděláte, posouzení nedostali hodnocení pěti hvězdičkami. 


## <a name="next-steps"></a>Další postup

- [Přečtěte si](concepts-assessment-calculation.md) výpočet posouzení.
- [Přečtěte si](how-to-modify-assessment.md) přizpůsobení posouzení.
