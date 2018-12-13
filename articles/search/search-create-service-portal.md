---
title: Vytvoření služby Azure Search na portálu – Azure Search
description: Zřízení služby Azure Search na webu Azure Portal. Výběr skupiny prostředků, oblasti a SKU nebo cenovou úroveň.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2055ad9baff0c6acc05c9287ca1b8fb08731f8bc
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315974"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Vytvoření služby Azure Search na portálu

Zjistěte, jak vytvořit nebo zřídit službu Azure Search na portálu. 

Dáváte přednost prostředí PowerShell? Použijte [šablonu služby](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manageru. V tématu [Správa služby Azure Search pomocí prostředí PowerShell](search-manage-powershell.md) najdete další informace, které vám pomůžou začít.

## <a name="subscribe-free-or-paid"></a>Předplatné (bezplatné nebo placené)

[Otevřete si bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a použijte bezplatné kredity k vyzkoušení placených služeb Azure. Až kredity vyčerpáte, můžete si účet ponechat a dál používat bezplatné služby Azure, jako například Azure Websites. Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Případně si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure. 

## <a name="find-azure-search"></a>Vyhledání služby Azure Search
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na symbol plus (+ Vytvořit prostředek) v levém horním rohu.
3. Vyberte **Web** > **Azure Search**.

![](./media/search-create-service-portal/find-search3.png)

## <a name="name-the-service-and-url-endpoint"></a>Zadání názvu služby a koncového bodu adresy URL

Název služby je součástí koncového bodu adresy URL, na který jsou prováděna volání rozhraní API: `https://your-service-name.search.windows.net`. Zadejte název služby do pole **URL**. 

Požadavky na název služby:
   * Musí být jedinečný v rámci oboru názvů search.windows.net.
   * Délka musí být 2 až 60 znaků.
   * Používejte malá písmena, číslice nebo pomlčky („-“).
   * Nepoužívejte pomlčku („-“) v prvních 2 znacích nebo jako poslední znak.
   * Nikde nepoužívejte po sobě jdoucí pomlčky („--“).

## <a name="select-a-subscription"></a>Výběr předplatného
Pokud máte více než jedno předplatné, vyberte to, jehož součástí jsou také služby úložiště dat nebo souborů. Služba Azure Search dokáže automaticky rozpoznat úložiště Azure Table a Blob, databázi SQL a databázi Azure Cosmos DB pro indexování prostřednictvím [*indexerů*](search-indexer-overview.md), ale pouze pro služby ve stejném předplatném.

## <a name="select-a-resource-group"></a>Výběr skupiny prostředků
Skupina prostředků je kolekce společně používaných služeb a prostředků Azure. Pokud například používáte Azure Search k indexování databáze SQL, pak by obě služby měly být součástí stejné skupiny prostředků.

> [!TIP]
> Odstraněním skupiny prostředků se odstraní také služby v této skupině. U prototypových projektů, které využívají více služeb, spojení všech služeb do stejné skupiny prostředků usnadňuje vyčištění po skončení projektu. 

## <a name="select-a-hosting-location"></a>Výběr umístění pro hostování 
Protože se jedná o službu Azure, je Azure Search možné hostovat v datových centrech po celém světě. Mějte na paměti, že v různých zeměpisných oblastech se [ceny mohou lišit](https://azure.microsoft.com/pricing/details/search/).

## <a name="select-a-pricing-tier-sku"></a>Výběr cenové úrovně (SKU)
[Služba Azure Search je v současné době nabízena v několika cenových úrovních](https://azure.microsoft.com/pricing/details/search/): Free, Basic nebo Standard. Každá úroveň má svou vlastní [kapacitu a limity](search-limits-quotas-capacity.md). Další pokyny získáte v tématu věnovaném [volbě cenové úrovně nebo SKU](search-sku-tier.md).

Úroveň Standard se obvykle používá pro produkční úlohy, ale většina zákazníků se službou začíná na úrovni Free.

Po vytvoření služby nelze cenovou úroveň změnit. Pokud budete později potřebovat vyšší nebo nižší úroveň, budete muset službu znovu vytvořit.

## <a name="create-your-service"></a>Vytvoření služby

Připněte si službu na řídicí panel, abyste k ní po přihlášení měli snadno přístup.

![](./media/search-create-service-portal/new-service3.png)

## <a name="scale-your-service"></a>Škálování služby
Vytvoření služby může trvat několik minut (15 minut nebo déle v závislosti na úrovni). Po zřízení můžete službu škálovat tak, aby vyhovovala vašim potřebám. Protože jste pro službu Azure Search zvolili úroveň Standard, můžete škálovat dvě dimenze: repliky a oddíly. Pokud byste zvolili úroveň Basic, mohli byste přidávat pouze repliky. Při zřízení bezplatné služby škálování není k dispozici.

***Oddíly***: Umožňují službě ukládat a prohledávat více dokumentů.

***Repliky***: Umožňují službě zpracovat větší množství vyhledávacích dotazů.

Přidáním prostředků se zvýší vaše měsíční náklady. [Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/) vám pomůže porozumět důsledkům přidání prostředků na fakturaci. Mějte na paměti, že prostředky můžete upravit na základě zatížení. Například můžete navýšit prostředky kvůli vytvoření úplného počátečního indexu a později je pak snížit na úroveň vhodnější pro přírůstkové indexování.

> [!Important]
> Služba musí mít [2 repliky pro smlouvu SLA jen pro čtení a 3 repliky pro smlouvu SLA se čtením a zápisem](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Přejděte na stránku vyhledávací služby na webu Azure Portal.
2. V levém navigačním podokně vyberte **Nastavení** > **Škálování**.
3. Přetažením posuvníku můžete přidat prostředky obou typů.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Každá úroveň má jiné [limity](search-limits-quotas-capacity.md) pro celkový počet jednotek vyhledávání povolených v jedné službě (repliky * oddíly = celkový počet jednotek vyhledávání).

## <a name="when-to-add-a-second-service"></a>Přidání druhé služby

Většina zákazníků používá pouze jednu službu zřízenou na úrovni, která poskytuje [správnou rovnováhu prostředků](search-sku-tier.md). Jedna služba může hostovat více indexů v souladu s [maximálními limity vámi vybrané úrovně](search-capacity-planning.md), přičemž každý index je izolovaný od ostatních indexů. Ve službě Azure Search můžou být požadavky směrovány pouze na jeden index, což minimalizuje nebezpečí náhodného nebo záměrného načítání dat z jiných indexů ve stejné službě.

I když většina zákazníků používá jenom jednu službu, redundance služby může být nutná v případě následujících provozních požadavků:

+ Zotavení po havárii (výpadek datového centra). Služba Azure Search neposkytuje rychlé převzetí služeb při selhání v případě výpadku. Doporučení a pokyny najdete v tématu [Správa služby](search-manage.md).
+ Z vašeho zkoumání modelování více tenantů vyplynulo, že využívání dalších služeb je optimální řešení. Další informace najdete v tématu [Návrh pro více tenantů](search-modeling-multitenant-saas-applications.md).
+ U globálně nasazených aplikací můžete potřebovat instanci Azure Search ve více oblastech, abyste minimalizovali latenci při mezinárodní provozu vaší aplikace.

> [!NOTE]
> Ve službě Azure Search nelze oddělit úlohy indexování a dotazování; proto nikdy nevytvářejte více služeb pro oddělené úlohy. Index je vždy dotazován ve službě, ve které byl vytvořen (nemůžete vytvořit index v jedné službě a zkopírovat ho do jiné).
>

Druhá služba není potřebná pro zajištění vysoké dostupnosti. Vysoká dostupnost pro dotazy se dosáhne, když použijete 2 nebo více replik v rámci stejné služby. Aktualizace replik jsou sekvenční, což znamená, že alespoň jedna replika je funkční při nasazení aktualizace služby. Další informace o době provozu najdete v tématu [Smlouvy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Další postup
Po zřízení služby Azure Search jste připraveni [definovat index](search-what-is-an-index.md), abyste mohli nahrát a prohledávat svá data. 

> [!div class="nextstepaction"]
> [Použití služby Azure Search v rozhraní .NET](search-howto-dotnet-sdk.md)
