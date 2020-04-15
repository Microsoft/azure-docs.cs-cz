---
title: Přehled služby Azure Resource Graph
description: Zjistěte, jak služba Azure Resource Graph umožňuje komplexní dotazování prostředků ve velkém měřítku napříč předplatnými a klienty.
ms.date: 03/02/2020
ms.topic: overview
ms.openlocfilehash: f5c091f60faedb76e3ca6cd68505c06f51be21b6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381517"
---
# <a name="what-is-azure-resource-graph"></a>Co je Azure Resource Graph?

Azure Resource Graph je služba v Azure, která je navržená k rozšíření správy prostředků Azure tím, že poskytuje efektivní a výkonné zkoumání prostředků s možností dotazovat se ve velkém měřítku napříč danou sadou předplatných, abyste mohli efektivně řídit vaše prostředí. Tyto dotazy poskytují následující funkce:

- Možnost dotazu ohledně zdrojů s komplexním filtrováním, seskupováním a řazením podle vlastností zdroje.
- Schopnost iterativně prozkoumat prostředky na základě požadavků na zásadsprávné řízení.
- Schopnost posoudit dopad uplatnění zásad v rozsáhlém cloudovém prostředí.
- Možnost [podrobně popsat změny provedené ve vlastnostech prostředků](./how-to/get-resource-changes.md) (náhled).

V této dokumentaci si podrobně projdete jednotlivé funkce.

> [!NOTE]
> Azure Resource Graph povyšuje vyhledávací panel portálu Azure, nové prostředí procházení "Všechny prostředky" a_vizuální rozdíl historie_ [změn](../policy/how-to/determine-non-compliance.md#change-history-preview)
> zásad Azure . Je navržen tak, aby zákazníkům pomohl spravovat rozsáhlá prostředí.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Resource Graph doplňuje Azure Resource Manager

Azure Resource Manager aktuálně podporuje dotazy přes základní pole prostředků, konkrétně – název prostředku, ID, typ, skupina prostředků, předplatné a umístění. Správce prostředků také poskytuje zařízení pro volání jednotlivých poskytovatelů prostředků pro podrobné vlastnosti po jednom prostředku.

S Azure Resource Graph můžete získat přístup k těmto vlastnostem, které poskytovatelé zdrojů vrátí, aniž by bylo nutné provádět individuální vyvolání u každého poskytovatele zdrojů. Seznam podporovaných typů prostředků naleznete v [tabulce a odkazu na typ prostředku](./reference/supported-tables-resources.md). Alternativní způsob, jak zobrazit podporované typy prostředků, je prostřednictvím [prohlížeče schématu aplikace Azure Resource Graph .](./first-query-portal.md#schema-browser)

Pomocí Azure Resource Graph můžete:

- Přístup k vlastnostem vráceným poskytovateli prostředků bez nutnosti provádět jednotlivá volání jednotlivým poskytovatelům prostředků.
- Zobrazení posledních 14 dnů historie změn provedených v prostředku zobrazíte, jaké vlastnosti se změnily a kdy. (Preview)

## <a name="how-resource-graph-is-kept-current"></a>Jak je graf zdrojů udržován aktuální

Když je prostředek Azure aktualizován, Resource Graph je upozorněn správceprostředků na změnu.
Resource Graph pak aktualizuje svou databázi. Resource Graph také provádí pravidelné _úplné skenování_. Tato kontrola zajišťuje, že data grafu prostředků jsou aktuální, pokud jsou zmeškaná oznámení nebo pokud je prostředek aktualizován mimo Správce prostředků.

> [!NOTE]
> Resource Graph `GET` používá k nejnovějším non-preview rozhraní API každého poskytovatele prostředků ke shromažďování vlastností a hodnot. V důsledku toho nemusí být očekávaná vlastnost k dispozici. V některých případech byla použitá verze rozhraní API přepsána, aby poskytovala aktuálnější nebo široce používané vlastnosti ve výsledcích. Podívejte se na [zobrazit verzi rozhraní API pro každou](./samples/advanced.md#apiversion) ukázku typu prostředku pro úplný seznam ve vašem prostředí.

## <a name="the-query-language"></a>Dotazovací jazyk

Teď, když máte lepší pochopení toho, co je Azure Resource Graph, pojďme se ponořit do toho, jak vytvářet dotazy.

Je důležité si uvědomit, že dotazovací jazyk Azure Resource Graph je založený na [dotazovacím jazyce Kusto](/azure/data-explorer/data-explorer-overview) používaném Průzkumníkem dat Azure.

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md).
Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Pokud chcete používat Resource Graph, musíte mít odpovídající oprávnění v [řízení přístupu na základě role](../../role-based-access-control/overview.md), a to alespoň oprávnění ke čtení pro prostředky, které chcete dotazovat. Pokud pro objekt nebo skupinu objektů Azure nemáte alespoň oprávnění `read`, nevrátí se žádné výsledky.

> [!NOTE]
> Resource Graph používá odběry, které jsou k dispozici pro hlavní během přihlášení. Chcete-li zobrazit prostředky nového předplatného přidanéběhem aktivní relace, musí hlavní objekt aktualizovat kontext. Tato akce se stane automaticky při odhlašování a zpět.

Azure CLI a Azure PowerShell používají předplatná, ke kterým má uživatel přístup. Při použití rozhraní REST API přímo, seznam odběrů je poskytován uživatelem. Pokud má uživatel přístup k některému z předplatných v seznamu, výsledky dotazu jsou vráceny pro odběry, ke kterým má uživatel přístup. Toto chování je stejné jako při volání [skupin prostředků – seznam,](/rest/api/resources/resourcegroups/list) \- ke kterému máte přístup, bez jakéhokoli náznaku, že výsledek může být částečný.
Pokud nejsou žádné odběry v seznamu odběrů, které uživatel má příslušná práva, odpověď je _403_ (Zakázáno).

## <a name="throttling"></a>Throttling

Jako bezplatná služba jsou dotazy na Resource Graph omezeny, aby poskytovaly nejlepší prostředí a dobu odezvy pro všechny zákazníky. Pokud vaše organizace chce používat rozhraní API grafu prostředků pro rozsáhlé a časté dotazy, použijte portál "Zpětná vazba" ze [stránky portálu Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Zadejte svůj obchodní případ a zaškrtněte políčko "Microsoft vám může poslat e-mail s vaší zpětnou vazbou", aby vás tým mohl kontaktovat.

Materiál grafu omezení dotazy na úrovni uživatele. Odpověď služby obsahuje následující hlavičky PROTOKOLU HTTP:

- `x-ms-user-quota-remaining`(int): Zbývající kvóta prostředků pro uživatele. Tato hodnota mapuje počet dotazů.
- `x-ms-user-quota-resets-after`(hh:mm:ss): Doba trvání do obnovení spotřeby kvóty uživatele

Další informace naleznete v [tématu Pokyny pro omezené požadavky](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Azure Resource Graph Explorer, součást portálu Azure, umožňuje spouštění dotazů na Graf prostředků přímo na webu Azure Portal. Připněte výsledky jako dynamické grafy a dopracovního postupu portálu můžete poskytnout dynamické informace v reálném čase. Další informace najdete [v tématu První dotaz s Průzkumníkem zdrojů Azure](first-query-portal.md).

Resource Graph podporuje Azure CLI, Azure PowerShell, Azure SDK pro .NET a další. Dotaz je strukturován stejně pro každý jazyk. Zjistěte, jak povolit resource graph pomocí:

- [Portál Azure a Průzkumník grafů prostředků](first-query-portal.md) 
- [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Další kroky

- Spusťte svůj první dotaz pomocí [portálu Azure](first-query-portal.md).
- Spusťte svůj první dotaz pomocí [azure cli](first-query-azurecli.md).
- Spusťte svůj první dotaz s [Azure PowerShell](first-query-powershell.md).