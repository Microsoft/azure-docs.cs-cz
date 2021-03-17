---
title: Řešení potíží Azure s využitím rezervace
description: Tento článek vám pomůže pochopit a řešit problémy s rezervacemi Azure, které na webu Azure Portal vykazují žádné nebo nulové (0) využití. Obsahuje také vysvětlení použití, které se neshoduje.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207425"
---
# <a name="troubleshoot-reservation-utilization"></a>Řešení potíží s využitím rezervace

Tento článek vám pomůže pochopit a řešit problémy s rezervacemi Azure, které na webu Azure Portal vykazují žádné nebo nulové (0) využití. Obsahuje také vysvětlení použití, které se neshoduje.

## <a name="symptoms"></a>Příznaky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a přejděte k položce **Rezervace** .
1. V seznamu rezervací vyhledejte míru využití rezervace ve sloupci **Využití (%)** . Hodnotou může být 0 %.
1. Vyberte rezervaci.
1. Na stránce přehledu rezervací se procento využití v grafu nemusí shodovat s hodnotou zobrazenou v seznamu rezervací.

## <a name="cause"></a>Příčina

Sloupec **Využití (%)** na webu Azure Portal zobrazuje hodnotu pro aktuální den. Hodnota se vypočítává průběžně, jak data o využití přicházející z míst, kde jsou prostředky spouštěny. Azure používá využití k výpočtu procent využití.

Některé prostředky hlásí využití pomaleji než jiné. Kromě toho některé typy produktů, jako jsou databáze SQL, pomohou hlásit data o využití pomalu.

Latence může způsobit, že výpočet využití zobrazí nižší hodnoty, než je skutečné využití. Rozdíl je znatelný na hranicích dnů. V takových případech, pokud Azure nezíská data o využití do čtyř až osmi hodin, vypočítá hodnotu 0 %. Hodnota 0 % se zobrazí, protože údaje o využití ještě nedorazily, a zdá se, že rezervace nepoužívá výhodu pro žádné prostředky.

Jakmile údaje o využití dorazí, tato hodnota se změní a zobrazí se správný počet procent. Když se shromáždí všechny údaje o využití, určí se správná hodnota a zobrazí se přesně v grafu.

## <a name="solution"></a>Řešení

Pokud zjistíte, že údaje o využití neodpovídají vašim očekáváním, projděte si graf, ve kterém získáte nejlepší představu o skutečném využití. Všechny hodnoty starší než dva dny by měly být přesné. Dlouhodobější průměry za sedm až 30 dnů by měly být přesné.

## <a name="next-steps"></a>Další kroky

- Další informace o správě rezervací najdete v tématu [Správa rezervací prostředků Azure](manage-reserved-vm-instance.md).