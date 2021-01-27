---
title: Přehled služby Azure Resource Graph
description: Seznamte se s tím, jak služba Azure Resource Graph umožňuje složitý dotazování na prostředky ve velkém rozsahu napříč předplatnými a klienty.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: b5df124d07b8ecfb20f5dec08830d8156e8df2cd
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919137"
---
# <a name="what-is-azure-resource-graph"></a>Co je Azure Resource Graph?

Azure Resource Graph je služba v Azure, která je navržená tak, aby rozšířila správu prostředků Azure tím, že poskytuje efektivní a výkonný průzkum prostředků s možností dotazování ve velkém rozsahu v dané sadě předplatných, abyste mohli efektivně řídit vaše prostředí. Tyto dotazy poskytují následující funkce:

- Možnost dotazu ohledně zdrojů s komplexním filtrováním, seskupováním a řazením podle vlastností zdroje.
- Možnost iterativního zkoumání prostředků na základě požadavků zásad správného řízení.
- Schopnost posoudit dopad uplatnění zásad v rozsáhlém cloudovém prostředí.
- Možnost [podrobností změn provedených ve vlastnostech prostředků](./how-to/get-resource-changes.md) (Preview).

V této dokumentaci si podrobně projdete jednotlivé funkce.

> [!NOTE]
> Azure Resource Graph – pravomoci Azure Portal vyhledávací panel, nové možnosti procházet všechny prostředky a [](../policy/how-to/determine-non-compliance.md#change-history) 
>  _vizuální rozdíly_ v historii změn Azure Policy. Je navržena tak, aby zákazníkům pomohla spravovat rozsáhlá prostředí.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Resource Graph doplňuje Azure Resource Manager

Správce prostředků aktuálně podporuje dotazy přes základní pole prostředků, konkrétně název prostředku, ID, typ, skupinu prostředků, předplatné a umístění. Správce prostředků také poskytuje zařízení pro volání jednotlivých poskytovatelů prostředků pro podrobné vlastnosti jednoho prostředku v daném okamžiku.

S Azure Resource Graph můžete získat přístup k těmto vlastnostem, které poskytovatelé zdrojů vrátí, aniž by bylo nutné provádět individuální vyvolání u každého poskytovatele zdrojů. Seznam podporovaných typů prostředků najdete v [odkazu na typ tabulky a prostředku](./reference/supported-tables-resources.md). Alternativním způsobem, jak zobrazit podporované typy prostředků, je použít [prohlížeč schématu Průzkumníka Azure Resource Graph](./first-query-portal.md#schema-browser).

Pomocí Azure Resource graphu můžete:

- Přístup k vlastnostem vráceným poskytovateli prostředků bez nutnosti udělat jednotlivá volání na každého poskytovatele prostředků.
- Podívejte se na posledních 14 dní historie změn provedených u prostředku, abyste viděli, jaké vlastnosti se změnily a kdy. (Preview)

> [!NOTE]
> Jako funkce ve _verzi Preview_ `type` mají některé objekty k dispozici další vlastnosti, které nejsou správce prostředků. Další informace najdete v tématu [Rozšířené vlastnosti (Preview)](./concepts/query-language.md#extended-properties).

## <a name="how-resource-graph-is-kept-current"></a>Způsob aktuálnosti grafu prostředků

Po aktualizaci prostředku Azure je graf prostředků upozorněn Správce prostředků změny.
Graf prostředků pak aktualizuje svou databázi. Graf prostředků má také pravidelnou _úplnou kontrolu_. Tato kontrola zajistí, že data grafu prostředku jsou aktuální, pokud jsou k dispozici nějaká oznámení nebo když se prostředek aktualizuje mimo Správce prostředků.

> [!NOTE]
> Graf prostředků používá `GET` k získání vlastností a hodnot nejnovější rozhraní API, které není ve verzi Preview každého poskytovatele prostředků. V důsledku toho nemusí být očekávaná vlastnost k dispozici. V některých případech byla použitá verze rozhraní API přepsána tak, aby poskytovala více aktuálních nebo široce používaných vlastností ve výsledcích. Úplný seznam ve vašem prostředí najdete v tématu věnovaném ukázce [verze rozhraní API pro každý typ prostředku](./samples/advanced.md#apiversion) .

## <a name="the-query-language"></a>Dotazovací jazyk

Teď, když máte lepší přehled o tom, co je Azure Resource Graph, se můžeme podrobně, jak vytvářet dotazy.

Je důležité pochopit, že dotazovací jazyk pro Azure Resource Graph je založený na [jazyce dotazů Kusto](/azure/data-explorer/data-explorer-overview) , který používá Azure Průzkumník dat.

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md). Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Pokud chcete použít graf prostředků, musíte mít příslušná práva v [rámci řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) s minimálním přístupem pro čtení k prostředkům, které chcete dotazovat. Pokud pro objekt nebo skupinu objektů Azure nemáte alespoň oprávnění `read`, nevrátí se žádné výsledky.

> [!NOTE]
> V grafu prostředků se při přihlášení používají předplatná, která jsou k objektu zabezpečení k dispozici. Chcete-li zobrazit prostředky nového předplatného přidaného během aktivní relace, objekt zabezpečení musí kontext aktualizovat. Tato akce se provádí automaticky při odhlašování a zpátky v.

Azure CLI a Azure PowerShell používají předplatná, ke kterým má uživatel přístup. Při přímém použití REST API se seznam předplatných poskytuje uživateli. Pokud má uživatel přístup k jakémukoli předplatnému v seznamu, vrátí se výsledky dotazu pro předplatná, ke kterým má uživatel přístup. Toto chování je stejné jako při volání [skupin prostředků – seznam](/rest/api/resources/resourcegroups/list) \- , ke kterému se dostanete skupiny prostředků, ke kterým máte přístup, aniž by bylo nutné uvádět, že výsledek může být částečný. Pokud v seznamu odběrů nejsou žádná předplatná, ke kterým má uživatel příslušná práva, odpověď je _403_ (zakázáno).

> [!NOTE]
> V REST API verze **Preview** `2020-04-01-preview` může být seznam předplatných ommitted.
> Pokud `subscriptions` `managementGroupId` v požadavku nejsou definovány vlastnosti a, je _obor_ nastaven na tenant. Další informace najdete v tématu [rozsah dotazu](./concepts/query-language.md#query-scope).

## <a name="throttling"></a>Throttling

V rámci bezplatné služby jsou dotazy do grafu prostředků omezené, aby poskytovaly nejlepší prostředí a dobu odezvy pro všechny zákazníky. Pokud chce vaše organizace používat Graph API prostředků pro rozsáhlé a časté dotazy, použijte na [stránce portálu pro grafy prostředků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)možnost zpětné vazby na portálu.
Zadejte svůj obchodní případ a v případě, že vás tým bude kontaktovat, vyberte zaškrtávací políčko Microsoft vás může poslat e-mailem o zpětné vazbě.

Graf prostředků omezuje dotazy na úrovni uživatele. Odpověď služby obsahuje následující hlavičky protokolu HTTP:

- `x-ms-user-quota-remaining` (int): zbývající kvóta prostředků pro uživatele. Tato hodnota se mapuje na počet dotazů.
- `x-ms-user-quota-resets-after` (hh: mm: SS): časový interval, po jehož uplynutí se neobnoví spotřeba kvóty uživatele

Další informace najdete v tématu [doprovodné materiály k omezení požadavků](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Průzkumník Azure Resource Graph, součást Azure Portal, umožňuje spouštět dotazy na grafy prostředků přímo v Azure Portal. Připnout výsledky jako dynamické grafy, aby poskytovaly dynamické informace v reálném čase vašemu pracovnímu postupu na portálu. Další informace najdete v tématu [první dotazování pomocí Průzkumníka Azure Resource Graph Exploreru](./first-query-portal.md).

Graf prostředků podporuje rozhraní příkazového řádku Azure CLI, Azure PowerShell, sadu Azure SDK pro Python a další. Dotaz je strukturován pro každý jazyk stejný. Naučte se, jak povolit graf prostředků pomocí:

- [Azure Portal a Průzkumník diagramů prostředků](./first-query-portal.md) 
- [Azure CLI](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dotazovacím jazyce](./concepts/query-language.md).
- Podívejte se na jazyk používaný v [počátečních dotazech](./samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](./samples/advanced.md).
