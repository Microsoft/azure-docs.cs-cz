---
title: Vytvoření služby Azure Search na portálu | Microsoft Docs
description: Zřízení služby Azure Search na portálu.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.topic: quickstart
ms.date: 11/07/2017
ms.author: heidist
ms.openlocfilehash: 9c0b698b68820c2696c95099bc5ff4a466686797
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Vytvoření služby Azure Search na portálu

Zjistěte, jak vytvořit nebo zřídit službu Azure Search na portálu. 

Dáváte přednost prostředí PowerShell? Použijte [šablonu služby](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manageru. V tématu [Správa služby Azure Search pomocí prostředí PowerShell](search-manage-powershell.md) najdete další informace, které vám pomůžou začít.

## <a name="subscribe-free-or-paid"></a>Předplatné (bezplatné nebo placené)

[Otevřete si bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a použijte bezplatné kredity k vyzkoušení placených služeb Azure. Až kredity vyčerpáte, můžete si účet ponechat a dál používat bezplatné služby Azure, jako například Azure Websites. Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Případně si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure. 

## <a name="find-azure-search"></a>Vyhledání služby Azure Search
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na symbol plus (+) v levém horním rohu.
3. Vyberte **Web a mobilní zařízení** > **Azure Search**.

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
[Služba Azure Search je aktuálně k dispozici v několika cenových úrovních](https://azure.microsoft.com/pricing/details/search/): Free, Basic nebo Standard. Každá úroveň má svou vlastní [kapacitu a limity](search-limits-quotas-capacity.md). Další pokyny získáte v tématu věnovaném [volbě cenové úrovně nebo SKU](search-sku-tier.md).

V tomto názorném postupu jsme pro naši službu zvolili úroveň Standard.

Po vytvoření služby nelze cenovou úroveň změnit. Pokud budete později potřebovat vyšší nebo nižší úroveň, budete muset službu znovu vytvořit.

## <a name="create-your-service"></a>Vytvoření služby

Připněte si službu na řídicí panel, abyste k ní po přihlášení měli snadno přístup.

![](./media/search-create-service-portal/new-service3.png)

## <a name="scale-your-service"></a>Škálování služby
Vytvoření služby může trvat několik minut (15 minut nebo déle v závislosti na úrovni). Po zřízení můžete službu škálovat tak, aby vyhovovala vašim potřebám. Protože jste pro službu Azure Search zvolili úroveň Standard, můžete škálovat dvě dimenze: repliky a oddíly. Pokud byste zvolili úroveň Basic, mohli byste přidávat pouze repliky. Při zřízení bezplatné služby škálování není k dispozici.

***Oddíly***: Umožňují službě ukládat a prohledávat více dokumentů.

***Repliky***: Umožňují službě zpracovat větší množství vyhledávacích dotazů.

> [!Important]
> Služba musí mít [2 repliky pro smlouvu SLA jen pro čtení a 3 repliky pro smlouvu SLA se čtením a zápisem](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Přejděte na stránku vyhledávací služby na webu Azure Portal.
2. V levém navigačním podokně vyberte **Nastavení** > **Škálování**.
3. Přetažením posuvníku přidejte repliky nebo oddíly.

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

## <a name="next-steps"></a>Další kroky
Po zřízení služby Azure Search jste připraveni [definovat index](search-what-is-an-index.md), abyste mohli nahrát a prohledávat svá data. 

> [!div class="nextstepaction"]
> [Použití služby Azure Search v rozhraní .NET](search-howto-dotnet-sdk.md)
