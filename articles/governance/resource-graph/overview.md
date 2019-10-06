---
title: Přehled grafu prostředků Azure
description: Pochopte, jak služba Azure Resource Graph umožňuje složitou dotazování na prostředky ve velkém měřítku.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
ms.openlocfilehash: bf54f1a96c6be7bbfb19770472752b3f958695c4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976819"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Přehled služby Azure Resource Graph

Azure Resource Graph je služba v Azure, která je navržená tak, aby rozšířila správu prostředků Azure tím, že poskytuje efektivní a výkonné průzkumy prostředků s možností dotazování ve velkém rozsahu v dané sadě předplatných, abyste mohli efektivně řídit vaše hlediska. Tyto dotazy poskytují následující funkce:

- Možnost dotazování prostředků se složitým filtrováním, seskupením a řazením podle vlastností prostředku.
- Možnost iterativního zkoumání prostředků na základě požadavků zásad správného řízení.
- Možnost posoudit dopad použití zásad v obrovském cloudovém prostředí.
- Možnost [podrobností změn provedených ve vlastnostech prostředků](./how-to/get-resource-changes.md) (Preview).

V této dokumentaci získáte podrobné informace o jednotlivých funkcích.

> [!NOTE]
> Azure Resource Graph – pravomoci Azure Portal panel hledání, nové možnosti procházet všechny prostředky a [historie změn](../policy/how-to/determine-non-compliance.md#change-history-preview)Azure Policy 
> _vizuální rozdíl_. Je navržena tak, aby zákazníkům pomohla spravovat rozsáhlá prostředí.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Azure Resource Manager doplněk grafu prostředků

Azure Resource Manager aktuálně podporuje dotazy přes základní pole prostředků, konkrétně název prostředku, ID, typ, skupinu prostředků, předplatné a umístění. Správce prostředků také poskytuje zařízení pro volání jednotlivých poskytovatelů prostředků pro podrobné vlastnosti jednoho prostředku v daném okamžiku.

Pomocí Azure Resource graphu můžete získat přístup k těmto vlastnostem, které poskytovatelé prostředků vrátí, aniž by museli provádět jednotlivá volání na jednotlivé poskytovatele prostředků. Seznam podporovaných typů prostředků **najdete v tabulce** nasazení v [režimu úplného nasazení](../../azure-resource-manager/complete-mode-deletion.md) v části prostředky. Alternativním způsobem, jak zobrazit podporované typy prostředků, je použít [prohlížeč schématu Průzkumníka Azure Resource Graph](./first-query-portal.md#schema-browser).

Pomocí Azure Resource graphu můžete:

- Přístup k vlastnostem vráceným poskytovateli prostředků bez nutnosti udělat jednotlivá volání na každého poskytovatele prostředků.
- Podívejte se na posledních 14 dní historie změn provedených u prostředku, abyste viděli, jaké vlastnosti se změnily a kdy. Tisk

## <a name="how-resource-graph-is-kept-current"></a>Způsob aktuálnosti grafu prostředků

Po aktualizaci prostředku Azure je graf prostředků upozorněn Správce prostředků změny.
Graf prostředků pak aktualizuje svou databázi. Graf prostředků má také pravidelnou _úplnou kontrolu_. Tato kontrola zajistí, že data grafu prostředku jsou aktuální, pokud jsou k dispozici nějaká oznámení nebo když se prostředek aktualizuje mimo Správce prostředků.

## <a name="the-query-language"></a>Dotazovací jazyk

Teď, když máte lepší přehled o tom, co je Azure Resource Graph, se můžeme podrobně, jak vytvářet dotazy.

Je důležité pochopit, že dotazovací jazyk pro Azure Resource Graph je založený na [jazyce dotazů Kusto](../../data-explorer/data-explorer-overview.md) , který používá Azure Průzkumník dat.

Podrobnosti o operacích a funkcích, které se dají použít se službou Azure Resource Graph, najdete v tématu [dotazovací jazyk grafu prostředků](./concepts/query-language.md).
Informace o procházení prostředků najdete v tématu [prozkoumání prostředků](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v grafu prostředků Azure

Pokud chcete použít graf prostředků, musíte mít příslušná práva v [řízení přístupu na základě role](../../role-based-access-control/overview.md) (RBAC) s minimálním přístupem pro čtení k prostředkům, které chcete dotazovat. Bez minimálního `read` oprávnění k objektu nebo skupině objektů Azure nebudou výsledky vracet.

> [!NOTE]
> V grafu prostředků se při přihlášení používají předplatná, která jsou k objektu zabezpečení k dispozici. Chcete-li zobrazit prostředky nového předplatného přidaného během aktivní relace, objekt zabezpečení musí kontext aktualizovat. Tato akce se provádí automaticky při odhlašování a zpátky v.

Azure CLI a Azure PowerShell používají předplatná, ke kterým má uživatel přístup. Při přímém použití REST API se seznam předplatných poskytuje uživateli. Pokud má uživatel přístup k jakémukoli předplatnému v seznamu, vrátí se výsledky dotazu pro předplatná, ke kterým má uživatel přístup. Toto chování je stejné jako při volání [skupin prostředků – seznam](/rest/api/resources/resourcegroups/list) \- získáte skupiny prostředků, ke kterým máte přístup, bez indikace, že výsledek může být částečný.
Pokud v seznamu odběrů nejsou žádná předplatná, ke kterým má uživatel příslušná práva, odpověď je _403_ (zakázáno).

## <a name="throttling"></a>Omezování

V rámci bezplatné služby jsou dotazy do grafu prostředků omezené, aby poskytovaly nejlepší prostředí a dobu odezvy pro všechny zákazníky. Pokud chce vaše organizace používat Graph API prostředků pro rozsáhlé a časté dotazy, použijte na [stránce portálu pro grafy prostředků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)možnost zpětné vazby na portálu.
Zadejte svůj obchodní případ a v případě, že vás tým bude kontaktovat, vyberte zaškrtávací políčko Microsoft vás může poslat e-mailem o zpětné vazbě.

Graf prostředků omezuje dotazy na úrovni uživatele. Odpověď služby obsahuje následující hlavičky protokolu HTTP:

- `x-ms-user-quota-remaining` (int): zbývající kvóta prostředků pro uživatele. Tato hodnota se mapuje na počet dotazů.
- `x-ms-user-quota-resets-after` (hh: mm: SS): časový interval, po jehož uplynutí se neobnoví spotřeba kvóty uživatele

Další informace najdete v tématu [doprovodné materiály k omezení požadavků](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Spuštění prvního dotazu

Průzkumník Azure Resource Graph, součást Azure Portal, umožňuje spouštět dotazy na grafy prostředků přímo v Azure Portal. Připnout výsledky jako dynamické grafy, aby poskytovaly dynamické informace v reálném čase vašemu pracovnímu postupu na portálu. Další informace najdete v tématu [první dotazování pomocí Průzkumníka Azure Resource Graph Exploreru](first-query-portal.md).

Graf prostředků podporuje rozhraní příkazového řádku Azure CLI, Azure PowerShell, sadu Azure SDK pro .NET a další. Dotaz je strukturován pro každý jazyk stejný. Naučte se, jak povolit graf prostředků pomocí:

- [Azure Portal a Průzkumník diagramů prostředků](first-query-portal.md) 
- [Rozhraní příkazového řádku Azure](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Další kroky

- Spusťte první dotaz pomocí [Azure CLI](first-query-azurecli.md).
- Spusťte první dotaz pomocí [Azure PowerShell](first-query-powershell.md).
- Začínáme s [úvodními dotazy](./samples/starter.md)
- Rozšiřte své porozumění pomocí [pokročilých dotazů](./samples/advanced.md).