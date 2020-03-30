---
title: Vyhodnocení dopadu nové zásady Azure
description: Seznamte se s procesem, který je třeba dodržovat při zavádění nové definice zásad do prostředí Azure.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463523"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Vyhodnocení dopadu nové zásady Azure

Azure Policy je výkonný nástroj pro správu prostředků Azure podle obchodních standardů a pro splnění potřeb dodržování předpisů. Když lidé, procesy nebo kanály vytvářejí nebo aktualizují prostředky, zásady Azure žádost zkontroluje. Pokud je efekt definice zásad [připojit](./effects.md#deny) nebo [nasaditIfNotExists](./effects.md#deployifnotexists), zásady změní požadavek nebo k němu přidá. Pokud je efekt definice [zásadauditování](./effects.md#audit) nebo [AuditIfNotExists](./effects.md#auditifnotexists), zásada způsobí vytvoření položky protokolu aktivity. A pokud je efekt definice [zásaddeny](./effects.md#deny), zásady zastaví vytváření nebo změnu požadavku.

Tyto výsledky jsou přesně podle potřeby, pokud víte, že zásada je definována správně. Je však důležité ověřit nové zásady funguje tak, jak bylo zamýšleno před povolením změnit nebo blokovat práci. Ověření musí zajistit, že pouze zamýšlené prostředky jsou určeny jako nevyhovující a žádné vyhovující prostředky jsou nesprávně zahrnuty (označované jako _falešně pozitivní_) ve výsledcích.

Doporučený přístup k ověření nové definice zásad je následující kroky:

- Pevně definovat své zásady
- Auditujte stávající zdroje
- Auditovat nové nebo aktualizované požadavky na zdroje
- Nasazení zásad do prostředků
- Průběžné sledování

## <a name="tightly-define-your-policy"></a>Pevně definovat své zásady

Je důležité pochopit, jak se obchodní zásady implementují jako definice zásad a vztah prostředků Azure s jinými službami Azure. Tohoto kroku lze provést [určením požadavků](../tutorials/create-custom-policy-definition.md#identify-requirements) a [určením vlastností prostředků](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Ale je také důležité vidět za úzkou definici vaší obchodní politiky. Uvádí vaše zásady například "Všechny virtuální počítače musí..."? A co ostatní služby Azure, které využívají virtuální počítače, jako je HDInsight nebo AKS? Při definování zásad musíme zvážit, jak tato politika ovlivňuje zdroje, které jsou využívány jinými službami.

Z tohoto důvodu by definice zásad měly být co nejtěsněji definovány a zaměřeny na prostředky a vlastnosti, které je třeba vyhodnotit pro dodržování předpisů.

## <a name="audit-existing-resources"></a>Auditovat existující zdroje

Než začnete chtít spravovat nové nebo aktualizované prostředky s novou definicí zásad, je nejlepší zjistit, jak vyhodnocuje omezenou podmnožinu existujících prostředků, například testovací skupinu prostředků. Použijte [režim](./assignment-structure.md#enforcement-mode)
_vynucení zakázáno_ (DoNotEnforce) na přiřazení zásad zabránit [aktivaci efekt](./effects.md) nebo aktivity položky protokolu z vytváření.

Tento krok vám dává možnost vyhodnotit výsledky dodržování předpisů nové zásady na stávající prostředky bez dopadu na tok práce. Zkontrolujte, zda nejsou žádné kompatibilní prostředky označeny jako nekompatibilní _(falešně pozitivní)_ a zda jsou všechny prostředky, které očekáváte, že nebudou kompatibilní, označeny správně.
Po počáteční podmnožinu prostředků ověří podle očekávání, pomalu rozbalte hodnocení na všechny existující prostředky.

Vyhodnocení stávajících prostředků tímto způsobem také poskytuje příležitost k nápravě nevyhovujících prostředků před úplným implementací nové politiky. Toto vyčištění lze provést ručně nebo prostřednictvím [nápravné úlohy,](../how-to/remediate-resources.md) pokud je efekt definice zásad _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Auditování nových nebo aktualizovaných zdrojů

Jakmile ověříte, že nová definice zásad je správně vykazována na existujících prostředcích, je čas podívat se na dopad zásad při vytváření nebo aktualizaci prostředků. Pokud definice zásady podporuje parametrizaci efektu, použijte [audit](./effects.md#audit). Tato konfigurace umožňuje sledovat vytváření a aktualizaci prostředků a zjistit, jestli nová definice zásad aktivuje položku v protokolu aktivit Azure pro prostředek, který není kompatibilní bez ovlivnění existující práce nebo požadavků.

Doporučujeme aktualizovat a vytvořit nové prostředky, které odpovídají definici zásad, abyste zjistili, že efekt _auditování_ se správně aktivuje, když se očekává. Buďte na pozoru pro požadavky na prostředky, které by neměly být ovlivněny novou definici zásad, které aktivují _auditefekt._
Tyto ovlivněné zdroje jsou dalším příkladem _falešně pozitivních zjištění_ a musí být stanoveny v definici zásad před úplnou implementací.

V případě, že definice zásad se změní v této fázi testování, doporučuje se zahájit proces ověření přes s auditování existujících prostředků. Změna definice zásad pro _falešně pozitivní_ na nové nebo aktualizované zdroje bude mít pravděpodobně také dopad na stávající zdroje.

## <a name="deploy-your-policy-to-resources"></a>Nasazení zásad do prostředků

Po dokončení ověření nové definice zásad s existujícími prostředky a novými nebo aktualizovanými požadavky na prostředky zahájíte proces implementace zásady. Doporučujeme nejprve vytvořit přiřazení zásad pro novou definici zásad na podmnožinu všech prostředků, například skupiny prostředků. Po ověření počátečnínasazení rozšířit rozsah zásad na širší a širší úrovně, jako jsou předplatná a skupiny pro správu. Tohoto rozšíření je dosaženo odebráním přiřazení a vytvořením nového v cílových oborech, dokud nebude přiřazeno k plnému rozsahu prostředků, které mají být pokryty novou definicí zásad.

Pokud jsou během zavádění umístěny prostředky, které by měly být z vaší nové definice zásad vyňaty, zaměňte je jedním z následujících způsobů:

- Aktualizace definice zásad tak, aby byla konkrétnější, aby se snížil nezamýšlený dopad
- Změna rozsahu přiřazení zásad (odebráním a vytvořením nového přiřazení)
- Přidání skupiny zdrojů do seznamu vyloučení pro přiřazení zásad

Jakékoli změny oboru (úroveň nebo vyloučení) by měly být plně ověřeny a komunikovány s organizacemi zabezpečení a dodržování předpisů, aby se zajistilo, že v pokrytí nejsou žádné mezery.

## <a name="monitor-your-policy-and-compliance"></a>Sledování zásad a dodržování předpisů

Implementace a přiřazení definice zásad není posledním krokem. Průběžně sledujte úroveň [dodržování předpisů](../how-to/get-compliance-data.md) prostředků pro novou definici zásad a nastavte příslušné [výstrahy a oznámení Azure Monitoru,](../../../azure-monitor/platform/alerts-overview.md) když jsou identifikována nekompatibilní zařízení. Doporučujeme také vyhodnotit definici zásad a související přiřazení na plánovaném základě k ověření definice zásady je splnění obchodních zásad a dodržování předpisů potřeb. Zásady by měly být odebrány, pokud již nejsou potřeba. Zásady také potřebují aktualizaci čas od času, jak se vyvíjejí základní prostředky Azure a přidávají nové vlastnosti a možnosti.

## <a name="next-steps"></a>Další kroky

- Informace o [struktuře definice zásad](./definition-structure.md).
- Informace o [struktuře přiřazení zásad](./assignment-structure.md).
- Pochopit, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](../how-to/remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).