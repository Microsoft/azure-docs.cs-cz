---
title: Vyhodnotit dopad nové definice Azure Policy
description: Pochopení procesu, který se má provést při zavedení nové definice zásady do prostředí Azure
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: cf52d25aa846388bc387430913a733d5206df82e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590812"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Vyhodnotit dopad nové definice Azure Policy

Azure Policy je výkonný nástroj pro správu prostředků Azure do obchodních standardů a splnění požadavků na dodržování předpisů. Když lidé, procesy nebo kanály vytvoří nebo aktualizují prostředky, Azure Policy zkontroluje požadavek. Když je efekt definice zásad [upravený](./effects.md#modify), [Append](./effects.md#deny) nebo [DeployIfNotExists](./effects.md#deployifnotexists), zásady změní požadavek nebo přidá do něj. Když je efekt definice zásad [audit](./effects.md#audit) nebo [AuditIfNotExists](./effects.md#auditifnotexists), zásada způsobí vytvoření položky protokolu aktivit pro nové a aktualizované prostředky. A když je vliv definice zásad [odepřený](./effects.md#deny), zásada zastaví vytváření nebo změnu žádosti.

Tyto výsledky jsou přesně tak, jak je potřeba, když víte, že je zásada správně definovaná. Je ale důležité ověřit, že nové zásady fungují tak, jak jsou zamýšlené, než je povolíte pro změnu nebo blokování práce. Ověření musí zajistit, aby byly pouze zamýšlené prostředky považovány za nevyhovující a že ve výsledcích nejsou nesprávně zahrnuté žádné odpovídající prostředky (označované jako _falešně pozitivní_).

Doporučený postup pro ověření nové definice zásady je provedením následujících kroků:

- Pevně definovat zásady
- Audit stávajících prostředků
- Auditovat nové nebo aktualizované požadavky na prostředky
- Nasazení zásad do prostředků
- Průběžné sledování

## <a name="tightly-define-your-policy"></a>Pevně definovat zásady

Je důležité porozumět tomu, jak se obchodní zásady implementují jako definice zásad a vztah prostředků Azure s dalšími službami Azure. Tento krok se dosahuje [určením požadavků](../tutorials/create-custom-policy-definition.md#identify-requirements) a [určením vlastností prostředku](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Je ale také důležité, abyste viděli, která jsou nad rámec úzké definice firemních zásad. Má stav zásad například "všechny Virtual Machines musí..."? Co jsou další služby Azure, které využívají virtuální počítače, jako je HDInsight nebo AKS? Při definování zásad je potřeba vzít v úvahu, jak tyto zásady ovlivní prostředky, které používají jiné služby.

Z tohoto důvodu by měly být definice zásad pevně definované a zaměřené na prostředky a vlastnosti, které potřebujete k vyhodnocení dodržování předpisů.

## <a name="audit-existing-resources"></a>Auditovat stávající prostředky

Než budete chtít spravovat nové nebo aktualizované prostředky pomocí nové definice zásad, je nejlepší zjistit, jak vyhodnocuje omezené podmnožiny stávajících prostředků, jako je například testovací skupina prostředků. Pro přiřazení zásad použijte [režim vynucení](./assignment-structure.md#enforcement-mode) _disabled_ (DoNotEnforce), aby se zabránilo tomu, že se [projeví](./effects.md) spuštění nebo záznam z protokolu aktivit.

Tento krok vám umožní vyhodnotit výsledky dodržování předpisů pro nové zásady u existujících prostředků, aniž by to ovlivnilo pracovní tok. Ověřte, že žádné kompatibilní prostředky nejsou označené jako nevyhovující (_falešně pozitivní_) a že všechny prostředky, které očekáváte nedodržující předpisy, jsou správně označeny.
Po počáteční podmnožině prostředků se ověří podle očekávání, pomalu Rozšiřte vyhodnocení na všechny stávající prostředky.

Vyhodnocování stávajících prostředků tímto způsobem také nabízí možnost opravit prostředky, které nedodržují předpisy, před plnou implementací nové zásady. Toto vyčištění se dá provést ručně nebo prostřednictvím [úlohy nápravy](../how-to/remediate-resources.md) , pokud je efekt definice zásad _DeployIfNotExistsý_.

## <a name="audit-new-or-updated-resources"></a>Auditovat nové nebo aktualizované prostředky

Jakmile ověříte, že nová definice zásad bude správně vykazovat stávající prostředky, je čas se podívat na dopad zásad při vytváření nebo aktualizaci prostředků. Pokud definice zásady podporuje efekt Parametrizace, použijte [audit](./effects.md#audit). Tato konfigurace umožňuje monitorovat vytváření a aktualizaci prostředků, aby se zobrazila informace o tom, jestli nová definice zásady aktivuje záznam v protokolu aktivit Azure pro prostředek, který nedodržuje předpisy, aniž by to ovlivnilo existující práci nebo požadavky.

Doporučuje se aktualizovat i vytvořit nové prostředky, které odpovídají definici zásady, a zjistit tak, že se v případě očekávaného chování _audit_ projeví správně. Vyhledá požadavky na prostředky, které by neměly mít vliv na novou definici zásady, která aktivuje tento efekt _auditu_ .
Tyto ovlivněné prostředky jsou dalším příkladem _falešně pozitivních_ hodnot a musí se v definici zásady před plnou implementací opravit.

V případě, že se v této fázi testování změní definice zásad, doporučujeme zahájit proces ověřování pomocí auditování existujících prostředků. Změna definice zásad pro _falešně pozitivní_ u nových nebo aktualizovaných prostředků bude pravděpodobně mít dopad i na stávající prostředky.

## <a name="deploy-your-policy-to-resources"></a>Nasazení zásad do prostředků

Po dokončení ověřování nové definice zásady s existujícími prostředky i novými nebo aktualizovanými požadavky na prostředky zahájíte proces implementace této zásady. Doporučuje se vytvořit přiřazení zásad pro novou definici zásady k podmnožině všech prostředků, jako je třeba skupina prostředků. Po ověření počátečního nasazení rozšířit rozsah zásad na širší a širší úroveň, jako jsou předplatná a skupiny pro správu. Toto rozšíření se dosahuje odebráním přiřazení a vytvořením nového v cílových oborech, dokud není přiřazený k celému oboru prostředků, které mají být zahrnuty do vaší nové definice zásady.

Pokud se při zavedení nacházejí prostředky, které by měly být z vaší nové definice zásad vyloučené, vyřešte je jedním z následujících způsobů:

- Aktualizujte definici zásady tak, aby byla spolehlivější, aby se snížil nezamýšlený dopad.
- Změna oboru přiřazení zásady (odebráním a vytvořením nového přiřazení)
- Přidejte skupinu prostředků do seznamu vyloučení pro přiřazení zásady.

Jakékoli změny rozsahu (úrovně nebo vyloučení) by se měly plně ověřit a sdělit organizacím zabezpečení a dodržování předpisů, aby se zajistilo, že neexistují žádné mezery v pokrytí.

## <a name="monitor-your-policy-and-compliance"></a>Monitorování zásad a dodržování předpisů

Implementace a přiřazení definice zásad není posledním krokem. Průběžně monitorujte úroveň [dodržování předpisů](../how-to/get-compliance-data.md) u prostředků na novou definici zásad a nastavte vhodné [Azure monitor výstrahy a oznámení,](../../../azure-monitor/alerts/alerts-overview.md) když se identifikují zařízení, která nedodržují předpisy. Doporučuje se také vyhodnotit definici zásady a související přiřazení na základě plánu, aby definice zásad splňovala požadavky na obchodní zásady a dodržování předpisů. Zásady by se měly odebrat, pokud už je nepotřebujete. Zásady se taky musí aktualizovat v čase, protože základní prostředky Azure se vyvíjí a přidávají nové vlastnosti a možnosti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [struktuře definic zásad](./definition-structure.md).
- Přečtěte si o [struktuře přiřazení zásad](./assignment-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).