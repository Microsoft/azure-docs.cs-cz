---
title: Přehled služby Azure Resource Graph
description: Zjistěte, jak služba Graph prostředků Azure umožňuje složitých dotazů na prostředky v potřebném měřítku.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 4724789dd1a0893c5ef4ab112306c185f0e87da7
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274376"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Přehled služby Azure Graph prostředků

Azure Graph prostředků je služba v Azure, která je navržené k rozšíření správy prostředků Azure tím, že poskytuje efektivní a výkonné prostředků zkoumání možnost dotazu ve velkém měřítku napříč danou sadu předplatných tak, aby může efektivně řídit vaše prostředí. Tyto dotazy poskytují následující funkce:

- Možnost dotazu ohledně zdrojů s komplexním filtrováním, seskupováním a řazením podle vlastností zdroje.
- Možnost interaktivně prohlížet prostředky podle požadavků zásad správného řízení.
- Schopnost posoudit dopad uplatnění zásad v rozsáhlém cloudovém prostředí.
- Schopnost [podrobně popisují změny vlastnosti prostředku](./how-to/get-resource-changes.md) (preview).

V této dokumentaci si podrobně projdete jednotlivé funkce.

> [!NOTE]
> Azure Graph prostředků základem vyhledávacího webu Azure portal, nové procházení "Všechny materiály" prostředí a Azure Policy [historii změn](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _visual diff_. Je navržena tak, aby pomáhá zákazníkům spravovat prostředí ve velkém měřítku.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Resource Graph doplňuje Azure Resource Manager

Azure Resource Manager aktuálně podporuje dotazy přes pole základních prostředků, konkrétně – název prostředku, ID, typ, skupinu prostředků, předplatné a umístění. Resource Manageru také poskytuje funkce pro volání poskytovatelů jednotlivých prostředků pro jeden prostředek podrobné vlastnosti v čase.

S Azure Resource Graph můžete získat přístup k těmto vlastnostem, které poskytovatelé zdrojů vrátí, aniž by bylo nutné provádět individuální vyvolání u každého poskytovatele zdrojů. Seznam podporovaných typů prostředků, Hledat **Ano** v [prostředky pro nasazení úplný režim](../../azure-resource-manager/complete-mode-deletion.md) tabulky.

Graf prostředků Azure můžete:

- Přístup k vlastnostem vrácenou poskytovatele prostředků, aniž byste museli provádět jednotlivých volání na každý poskytovatel prostředků.
- Zobrazení posledních 14 dní historie změn provedených na prostředek zobrazíte vlastnosti změnit a kdy. (Preview)

## <a name="how-resource-graph-is-kept-current"></a>Jak prostředků grafu je udržovat je aktuální

Když se aktualizuje prostředek Azure, diagram zdrojů obdrží oznámení pomocí Správce prostředků změny.
Prostředek grafu pak aktualizuje svou databázi. Prostředek grafu také provádí běžný _úplnou kontrolu_. Tato kontrola zajišťuje, že data grafu prostředků aktuální Pokud zmeškaných oznámení nebo pokud je prostředek aktualizován mimo Resource Manageru.

## <a name="the-query-language"></a>Dotazovací jazyk

Teď, když už chápete lépe, co je Azure Resource Graph, pusťme se do vytváření dotazů.

Je důležité pochopit, že je na základě grafu prostředků Azure dotazovací jazyk [Kusto dotazovací jazyk](../../data-explorer/data-explorer-overview.md) používá Průzkumník dat Azure.

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md).
Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Pokud chcete používat Resource Graph, musíte mít odpovídající oprávnění v [řízení přístupu na základě role](../../role-based-access-control/overview.md), a to alespoň oprávnění ke čtení pro prostředky, které chcete dotazovat. Pokud pro objekt nebo skupinu objektů Azure nemáte alespoň oprávnění `read`, nevrátí se žádné výsledky.

> [!NOTE]
> Prostředek grafu používá dostupná předplatná pro objekt zabezpečení při přihlášení. Zobrazte prostředky k novému předplatnému přidali během aktivní relace, musíte aktualizovat objekt zabezpečení kontextu. Tato akce se stane automaticky po odhlášení a znovu přihlaste.

## <a name="throttling"></a>Throttling

Jako bezplatná služba se dotazy do grafu prostředků omezují zajištění nejlepší prostředí a odpovědi pro všechny zákazníky. Pokud vaše organizace chce používat rozhraní Graph API prostředku ve velkém měřítku a častých dotazů, používat portál "Názory" z [stránka portálu prostředků grafu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Zadejte váš obchodní případ a zaškrtněte políčko "Microsoft může poslat e-mail o svůj názor" v pořadí pro tým, který se vás kontaktovat.

Prostředek grafu omezuje dotazy na úrovni uživatele. Odpověď služby obsahuje následující hlavičky protokolu HTTP:

- `x-ms-user-quota-remaining` (int): Zbývající kvóta zdrojů pro uživatele. Tato hodnota se mapuje na počet dotazů.
- `x-ms-user-quota-resets-after` (hh:mm:ss): Doba trvání, dokud se resetuje využití kvóty uživatele

Další informace najdete v tématu [pokyny pro omezené požadavky](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Diagram zdrojů podporuje rozhraní příkazového řádku Azure, Azure Powershellu a Azure SDK pro .NET. Strukturované dotaz stejný pro jednotlivé jazyky. Přečtěte si, jak povolit Resource Graph v [ Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension) a [ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Další postup

- Spusťte svůj první dotaz s [rozhraní příkazového řádku Azure](first-query-azurecli.md).
- Spusťte svůj první dotaz s [prostředí Azure PowerShell](first-query-powershell.md).
- Začněte s [Starter dotazy](./samples/starter.md).
- Přehled s [pokročilé dotazy](./samples/advanced.md).