---
title: Vizualizace závislostí ve službě Azure Migrate | Dokumentace Microsoftu
description: Poskytuje přehled o vyhodnocení výpočtů ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: raynew
ms.openlocfilehash: 5880c98b0f77b75dfb10969311408307b0c4afbd
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603358"
---
# <a name="dependency-visualization"></a>Vizualizace závislostí

[Azure Migrate](migrate-overview.md) posuzuje skupiny místních počítačů pro migraci do Azure. Chcete-li počítače seskupit dohromady, můžete použít vizualizaci závislostí. Tento článek obsahuje informace o této funkci.


## <a name="overview"></a>Přehled

Vizualizace závislostí ve službě Azure Migrate můžete vytvořit skupiny pro posouzení migrace s větší jistotou. Pomocí vizualizace závislostí můžete zobrazit sítě závislosti pro konkrétní počítače nebo v skupině počítačů. To je užitečné zajistit, aby žádné funkce nebo ke ztrátě (nebo počítače zapomněli) v procesu migrace, když aplikace a úlohy spouštět napříč několika počítači.  

## <a name="how-does-it-work"></a>Jak to funguje?

Azure Migrate používá [Service Map](../operations-management-suite/operations-management-suite-service-map.md) řešení v [Log Analytics](../log-analytics/log-analytics-overview.md) pro vizualizace závislostí.
- Když vytvoříte projekt Azure Migration, pracovní prostor Log Analytics se vytvoří ve vašem předplatném.
- Název pracovního prostoru je zadaný název projektu migrace, s předponou **migrace-** a volitelně příponami s číslem.
- Přejděte do pracovního prostoru Log Analytics z **Essentials** části projektu **přehled** stránky.
- Vytvořené pracovní prostor je označen jako klíč **projekt migrace**a hodnota **název projektu**. Ty můžete použít pro vyhledávání na webu Azure Portal.  

    ![Pracovní prostor Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Pokud chcete použít vizualizaci závislostí, budete muset stáhnout a nainstalovat agenty na každém v místním počítači, který chcete analyzovat.  

## <a name="do-i-need-to-pay-for-it"></a>Je potřeba dál za něj platit?

Služba Azure Migrate je dostupná bez dalších poplatků. Použití funkce vizualizace závislostí ve službě Azure Migrate vyžaduje řešení Service Map. Při vytváření projektu Azure Migrate Azure Migrate automaticky vytvoří nový pracovní prostor Log Analytics vaším jménem.

> [!NOTE]
> Funkci vizualizace závislostí využívá mapu služeb prostřednictvím pracovního prostoru Log Analytics. Od 28. února 2018 se oznámení všeobecné dostupnosti Azure Migrate, tato funkce je teď k dispozici bez dalších poplatků. Budete muset vytvořit nový projekt, aby se pomocí pracovního prostoru bezplatné využití. Před obecné availaibility existujících pracovních prostorů jsou stále chargable, proto doporučujeme, abyste přechod na nový projekt.

1. Použití jakékoli řešení než Service Map v rámci tohoto pracovního prostoru Log Analytics budou účtovat standardní poplatky Log Analytics.
2. Pro zajištění podpory scénářů migrace bez dodatečných nákladů, řešení Service Map nebudou se vám účtovat žádné poplatky za prvních 180 dnů od vytvoření projektu Azure Migrate, po jejímž uplynutí účtovat standardní poplatky.
3. Pouze pracovní prostor vytvořený jako součást vytváření projektu, budete mít zdarma pro použití.

Když si zaregistrujete agentů do pracovního prostoru, použijte ID a klíč zadaný v projektu na stránce postup instalace agenta. Nelze použít stávající pracovní prostor a přidružte jej k projektu Azure Migrate.

Při odstranění projektu Azure Migrate pracovní prostor se neodstraní společně. Publikovat odstranění projektu, nebudou bezplatné využití řešení Service Map a každý uzel se budou účtovat podle placenou úroveň pracovního prostoru Log Analytics.

Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Jak můžu spravovat pracovní prostor?

Můžete použít pracovní prostor Log Analytics mimo službu Azure Migrate. Není odstraněn, je-li odstranit projekt migrace, ve kterém byla vytvořena. Pokud už nepotřebujete pracovního prostoru, [odstranit](../log-analytics/log-analytics-manage-access.md) ručně.

Neodstraňovat pracovní prostor vytvořený službou Azure Migrate, není-li odstranit projekt migrace. Pokud tak učiníte, závislosti nefungují podle očekávání.

## <a name="next-steps"></a>Další postup

[Seskupení počítačů s využitím závislostí počítačů](how-to-create-group-machine-dependencies.md)
