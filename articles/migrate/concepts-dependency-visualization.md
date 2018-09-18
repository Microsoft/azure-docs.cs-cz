---
title: Vizualizace závislostí ve službě Azure Migrate | Dokumentace Microsoftu
description: Poskytuje přehled o vyhodnocení výpočtů ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 84ce20186b882bf07177305291a6f7512ed9ac62
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732019"
---
# <a name="dependency-visualization"></a>Vizualizace závislostí

[Azure Migrate](migrate-overview.md) posuzuje skupiny místních počítačů pro migraci do Azure. Funkce vizualizace závislostí ve službě Azure Migrate můžete použít k vytváření skupin. Tento článek obsahuje informace o této funkci.


## <a name="overview"></a>Přehled

Vizualizace závislostí ve službě Azure Migrate můžete vytvořit skupiny s vysokou spolehlivostí pro posouzení migrace. Pomocí vizualizace závislostí můžete zobrazit síťové závislosti počítačů a identifikovat související počítače, které nepotřebujete migrovat společně do Azure. Tato funkce je užitečná ve scénářích, kde si jich nejste zcela vědomi počítače, které tvoří vaši aplikaci a musí do Azure migrovat společně.

## <a name="how-does-it-work"></a>Jak to funguje?

Azure Migrate používá [Service Map](../operations-management-suite/operations-management-suite-service-map.md) řešení v [Log Analytics](../log-analytics/log-analytics-overview.md) pro vizualizace závislostí.
- Využití vizualizace závislostí, je potřeba přiřadit pracovní prostor Log Analytics, novou nebo existující s projekt Azure Migrate.
- Můžete pouze vytvářet nebo připojit pracovní prostor v rámci stejného předplatného, ve kterém se vytvoří projekt migrace.
- Pracovní prostor Log Analytics připojit k projektu, přejděte na **Essentials** části projektu **přehled** stránky a klikněte na tlačítko **vyžaduje konfiguraci**

    ![Přiřadit pracovní prostor Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Když vytvoříte nový pracovní prostor, musíte zadat název pracovního prostoru. Pracovní prostor se pak vytvoří v oblasti, ve stejném [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migrace.
- Přidružené pracovní prostor je označen jako klíč **projekt migrace**a hodnota ** název projektu, který můžete použít pro vyhledávání na webu Azure Portal.
- Pokud chcete přejít na pracovní prostor přidružený k projektu, můžete přejít na **Essentials** části projektu **přehled** stránce a přístup k pracovnímu prostoru

    ![Přejděte v pracovním prostoru Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Pokud chcete použít vizualizaci závislostí, budete muset stáhnout a nainstalovat agenty na každém v místním počítači, který chcete analyzovat.  

## <a name="do-i-need-to-pay-for-it"></a>Je potřeba dál za něj platit?

Služba Azure Migrate je dostupná bez dalších poplatků. Použití funkce vizualizace závislostí ve službě Azure Migrate vyžaduje řešení Service Map a vyžaduje, abyste k pracovnímu prostoru Log Analytics, novou nebo existující přidružení s projektu Azure Migrate. Funkce vizualizace závislostí ve službě Azure Migrate je zdarma pro prvních 180 dnů ve službě Azure Migrate.

1. Použití jakékoli řešení než Service Map v rámci tohoto pracovního prostoru Log Analytics bude mít za následek [standardní Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) poplatky.
2. Pro zajištění podpory scénářů migrace bez dodatečných nákladů, řešení Service Map nebudou se vám účtovat žádné poplatky za prvních 180 dnů od přidružování projektu Azure Migrate pracovní prostor Log Analytics dne. Po uplynutí 180 dnů Log Analytics účtovat standardní poplatky.

Když si zaregistrujete agentů do pracovního prostoru, použijte ID a klíč zadaný v projektu na stránce postup instalace agenta.

Při odstranění projektu Azure Migrate pracovní prostor se neodstraní společně. Publikovat odstranění projektu, použití řešení Service Map nebudou zdarma a každý uzel se budou účtovat podle placenou úroveň pracovního prostoru Log Analytics.

> [!NOTE]
> Funkci vizualizace závislostí využívá mapu služeb prostřednictvím pracovního prostoru Log Analytics. Od 28. února 2018 se oznámení všeobecné dostupnosti Azure Migrate, tato funkce je teď k dispozici bez dalších poplatků. Budete muset vytvořit nový projekt, aby se pomocí pracovního prostoru bezplatné využití. Existujícím pracovním prostorům před všeobecné dostupnosti jsou stále fakturovatelné, proto doporučujeme, abyste přechod na nový projekt.

Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Jak můžu spravovat pracovní prostor?

Můžete použít pracovní prostor Log Analytics mimo službu Azure Migrate. Není odstraněn, je-li odstranit projekt migrace, ve kterém byla vytvořena. Pokud už nepotřebujete pracovního prostoru, [odstranit](../log-analytics/log-analytics-manage-access.md) ručně.

Neodstraňovat pracovní prostor vytvořený službou Azure Migrate, není-li odstranit projekt migrace. Pokud tak učiníte, funkce vizualizace závislostí nebude fungovat podle očekávání.

## <a name="next-steps"></a>Další postup

[Seskupení počítačů s využitím závislostí počítačů](how-to-create-group-machine-dependencies.md)
