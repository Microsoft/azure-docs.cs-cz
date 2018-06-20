---
title: Vizualizace závislostí v Azure migrovat | Microsoft Docs
description: Poskytuje přehled o vyhodnocení výpočtů ve službě Azure migrovat.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 94d4db078175309444db5113506020eae54dda70
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218400"
---
# <a name="dependency-visualization"></a>Vizualizace závislostí

[Azure migrovat](migrate-overview.md) služby vyhodnocuje skupiny místní počítače pro migraci do Azure. K seskupení počítačů, můžete použít závislost vizualizace. Tento článek obsahuje informace o této funkci.


## <a name="overview"></a>Přehled

Vizualizace závislostí v migraci Azure můžete vytvořit skupiny pro vyhodnocení migrace s vyšší jistotou. Pomocí vizualizace závislostí můžete zobrazit závislosti sítě u konkrétních počítačů nebo přes skupiny počítačů. Je to vhodné, ujistěte se, že žádné funkce nebo ke ztrátě (nebo počítače zapomenete) v procesu migrace, když aplikace a úlohy spustit napříč více počítačů.  

## <a name="how-does-it-work"></a>Jak to funguje?

Azure používá migrací [mapy služeb](../operations-management-suite/operations-management-suite-service-map.md) řešení v [analýzy protokolů](../log-analytics/log-analytics-overview.md) pro vizualizaci závislostí.
- Při vytváření projektu Azure migrace pracovní prostor analýzy protokolů se vytvoří ve vašem předplatném.
- Název pracovního prostoru je zadaný název migrace projektu, s předponou **migrovat-** a volitelně vyznačeno s číslem. 
- Přejděte do pracovního prostoru analýzy protokolů z **Essentials** části projektu **přehled** stránky.
- Vytvořeným pracovním prostorem je označené klíč **MigrateProject**a hodnota **název projektu**. To slouží k vyhledávání na webu Azure portal.  

    ![Pracovní prostor Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Pokud chcete použít závislost vizualizace, musíte stáhnout a nainstalovat agenty na každý místní počítač, který chcete analyzovat.  

## <a name="do-i-need-to-pay-for-it"></a>Je potřeba věnovat pro ni?

Služba Azure Migrate je dostupná bez dalších poplatků. Použití funkcí vizualizace závislostí v Azure migrace vyžadují mapy služeb. Při vytváření projektu Azure migrace migrace Azure automaticky vytvoří nový pracovní prostor analýzy protokolů vaším jménem.

> [!NOTE]
> Funkce vizualizace závislostí používá mapy služeb prostřednictvím pracovního prostoru analýzy protokolů. Od 28 února 2018 s oznámení Azure migraci obecné dostupnosti si tato funkce je k dispozici bez dalších poplatků. Budete muset vytvořit nový projekt, aby se pomocí pracovního prostoru volné využití. Existující pracovní prostory před obecné availaibility jsou stále chargable, proto doporučujeme pro přesun do nového projektu.

1. Použití jakékoli řešení než mapy služeb v rámci tohoto pracovního prostoru analýzy protokolů bude platit poplatky standardní analýzy protokolů. 
2. Pro podporu scénáře migrace bez dalších poplatků, řešení mapy služeb nebudou vám narůstat poplatky pro první 180 dnů od vytvoření projektu Azure migrovat, po které budou platit standardní poplatky.
3. Pouze pracovním prostoru, které jsou vytvořené jako součást vytváření projektu bude zdarma pro použití.

Při registraci agentů do pracovního prostoru, použijte ID a klíč zadaný v projektu na stránce Instalace agenta kroky. Nelze použít existujícímu pracovnímu prostoru a přidružte ji k projektu Azure migrovat.

Při odstranění projektu Azure migraci se společně se neodstraní pracovním prostoru. Vystavení odstranění projektu, využití mapy služeb nebudou volné a každý uzel se bude účtovat poplatek podle placené úroveň pracovní prostor analýzy protokolů.

Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/). 

## <a name="how-do-i-manage-the-workspace"></a>Jak lze spravovat pracovního prostoru?

Můžete vytvořit pracovní prostor analýzy protokolů mimo Azure migrovat. Pokud odstraníte migrace projektu, ve kterém byla vytvořena nebude odstraněn. Pokud již nepotřebujete pracovním prostoru [odstranit](../log-analytics/log-analytics-manage-access.md) ručně.

Nemáte odstranit pracovní prostor vytvořené Azure migrovat, pokud odstraňte projekt migrace. Pokud tak učiníte, závislosti nefungují podle očekávání.

## <a name="next-steps"></a>Další postup

[Skupina počítačů pomocí závislosti počítače](how-to-create-group-machine-dependencies.md)