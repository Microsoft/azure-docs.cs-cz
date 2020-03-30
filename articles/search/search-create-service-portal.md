---
title: 'Úvodní příručka: Vytvoření vyhledávací služby na portálu'
titleSuffix: Azure Cognitive Search
description: V tomto portálu rychlý start, zjistěte, jak nastavit prostředek Azure cognitive search na webu Azure Portal. Zvolte skupiny prostředků, oblasti a skladovou položku nebo cenovou úroveň.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77209354"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Úvodní příručka: Vytvoření služby Azure Cognitive Search na portálu

Azure Cognitive Search je samostatný prostředek, který se používá k připojení prostředí vyhledávání do vlastních aplikací. Azure Cognitive Search se snadno integruje s dalšími službami Azure, s aplikacemi na síťových serverech nebo se softwarem spuštěným na jiných cloudových platformách.

V tomto článku se dozvíte, jak vytvořit prostředek na [webu Azure Portal](https://portal.azure.com/).

[![Animovaný gif](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Dáváte přednost prostředí PowerShell? Použijte [šablonu služby](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manageru. Nápovědu k začínáme najdete [v tématu Správa Azure Cognitive Search pomocí PowerShellu](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Předplatné (bezplatné nebo placené)

[Otevřete si bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a použijte bezplatné kredity k vyzkoušení placených služeb Azure. Až kredity vyčerpáte, můžete si účet ponechat a dál používat bezplatné služby Azure, jako například Azure Websites. Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Případně si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure. 

## <a name="find-azure-cognitive-search"></a>Najít Azure Kognitivní vyhledávání

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klikněte na znaménko plus ("+ Vytvořit zdroj") v levém horním rohu.
3. Pomocí vyhledávacího panelu vyhledejte "Azure Cognitive Search" nebo přejděte k prostředku prostřednictvím **služby Web** > **Azure Cognitive Search**.

![Vytvoření prostředku na portálu](./media/search-create-service-portal/find-search3.png "Vytvoření prostředku na portálu")

## <a name="choose-a-subscription"></a>Výběr předplatného

Pokud máte více než jedno předplatné, vyberte jedno pro vyhledávací službu.

## <a name="set-a-resource-group"></a>Nastavení skupiny prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro vaše řešení Azure. Je vyžadována pro vyhledávací službu. To je také užitečné pro správu zdrojů all-up, včetně nákladů. Skupina prostředků se může skládat z jedné služby nebo více služeb používaných společně. Například pokud používáte Azure Cognitive Search indexovat databázi Azure Cosmos DB, můžete obě služby součástí stejné skupiny prostředků pro účely správy. 

Pokud nekombinujete prostředky do jedné skupiny nebo pokud jsou existující skupiny prostředků plné prostředků používaných v nesouvisejících řešeních, vytvořte novou skupinu prostředků pouze pro prostředek Azure Cognitive Search. 

![Vytvoření nové skupiny prostředků](./media/search-create-service-portal/new-resource-group.png "Vytvořte novou skupinu prostředků")

V průběhu času můžete sledovat aktuální a předpokládané náklady all-up nebo můžete zobrazit poplatky za jednotlivé zdroje. Následující snímek obrazovky ukazuje druh informací o nákladech, které můžete očekávat, když zkombinujete více prostředků do jedné skupiny.

![Správa nákladů na úrovni skupiny zdrojů](./media/search-create-service-portal/resource-group-cost-management.png "Správa nákladů na úrovni skupiny zdrojů")

> [!TIP]
> Skupiny prostředků zjednodušují čištění, protože odstranění maže všechny služby v ní. U prototypových projektů, které využívají více služeb, spojení všech služeb do stejné skupiny prostředků usnadňuje vyčištění po skončení projektu.

## <a name="name-the-service"></a>Pojmenujte službu

V poli Podrobnosti instance zadejte název služby v poli **URL.** Název je součástí koncového bodu adresy URL, proti `https://your-service-name.search.windows.net`kterému jsou vydávána volání rozhraní API: . Chcete-li například koncový bod `https://myservice.search.windows.net`, zadejte `myservice`.

Požadavky na název služby:

* Musí být jedinečný v rámci oboru názvů search.windows.net.
* Musí mít délku 2 až 60 znaků.
* Musíte použít malá písmena, číslice nebo pomlčky ("-")
* Nepoužívejte pomlčky ("-") v prvních 2 znacích nebo jako poslední jednotlivý znak
* Po sobě jdoucí pomlčky ("--") nesmíte používat nikde

> [!TIP]
> Pokud si myslíte, že budete používat více služeb, doporučujeme zahrnout oblast (nebo umístění) do názvu služby jako konvenci pojmenování. Služby v rámci stejné oblasti můžete vyměňovat data zdarma, takže pokud Azure Cognitive Search je v `mysearchservice-westus` západní USA a máte další služby také v západní USA, název jako můžete ušetřit výlet na stránku vlastností při rozhodování o tom, jak kombinovat nebo připojit prostředky.

## <a name="choose-a-location"></a>Výběr místa

Jako služba Azure může být Azure Cognitive Search hostovaná v datových centrech po celém světě. Seznam podporovaných oblastí naleznete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/). 

Poplatky za šířku pásma můžete minimalizovat nebo se vyhnout výběrem stejného umístění pro více služeb. Pokud například indexujete data poskytovaná jinou službou Azure (úložiště Azure, Azure Cosmos DB, Azure SQL Database), vytvoření služby Azure Cognitive Search ve stejné oblasti zabrání poplatkům za šířku pásma (při použití odchozích dat se neúčtují žádné poplatky za odchozí data služby jsou ve stejném regionu).

Pokud používáte obohacení umělou ai, vytvořte vyhledávací službu ve stejné oblasti jako kognitivní služby. *Společné umístění služeb Azure Cognitive Search a Cognitive Services ve stejné oblasti je požadavek na obohacení umělou ai.*

> [!Note]
> Střední Indie je v současné době nedostupná pro nové služby. U služeb, které jsou již ve střední Indii, můžete vertikálně navýšit kapacitu bez omezení a vaše služba je v této oblasti plně podporována. Omezení pro tuto oblast je dočasné a je omezeno pouze na nové služby. Tuto poznámku odstraníme, pokud omezení již neplatí.

## <a name="choose-a-pricing-tier-sku"></a>Výběr cenové úrovně (SKU)

[Azure Cognitive Search se momentálně nabízí ve více cenových úrovních:](https://azure.microsoft.com/pricing/details/search/)Free, Basic nebo Standard. Každá úroveň má svou vlastní [kapacitu a limity](search-limits-quotas-capacity.md). Další pokyny získáte v tématu věnovaném [volbě cenové úrovně nebo SKU](search-sku-tier.md).

Základní a standardní jsou nejběžnější volby pro produkční úlohy, ale většina zákazníků začíná se službou Free. Klíčové rozdíly mezi úrovněmi je velikost oddílu a rychlost a omezení počtu objektů, které můžete vytvořit.

Nezapomeňte, že cenovou úroveň nelze změnit po vytvoření služby. Pokud potřebujete vyšší nebo nižší úroveň, budete muset znovu vytvořit službu.

## <a name="create-your-service"></a>Vytvoření služby

Po zadání potřebných vstupů pokračujte a vytvořte službu. 

![Kontrola a vytvoření služby](./media/search-create-service-portal/new-service3.png "Kontrola a vytvoření služby")

Vaše služba je nasazena během několika minut. Můžete sledovat průběh prostřednictvím oznámení Azure. Zvažte připnutí služby na řídicí panel, abyste měli v budoucnu snadný přístup.

![Sledování a připnutí služby](./media/search-create-service-portal/monitor-notifications.png "Sledování a připnutí služby")

## <a name="get-a-key-and-url-endpoint"></a>Získání koncového bodu klíče a adresy URL

Pokud nepoužíváte portál, programový přístup k nové službě vyžaduje, abyste zadali koncový bod adresy URL a ověřovací klíč api.

1. Na stránce **Přehled** vyhledejte a zkopírujte koncový bod adresy URL na pravé straně stránky.

2. Na stránce **Klíče** zkopírujte jeden z klíčů správce (jsou ekvivalentní). Klíče rozhraní API správce jsou vyžadovány pro vytváření, aktualizaci a odstranění objektů ve vaší službě. Naproti tomu klávesy dotazu poskytují přístup pro čtení k obsahu indexu.

   ![Stránka přehledu služby s koncovým bodem url](./media/search-create-service-portal/get-url-key.png "Koncový bod adresy URL a další podrobnosti o službě")

Koncový bod a klíč nejsou potřebné pro úlohy založené na portálu. Portál už je propojený s vaším prostředkem Azure Cognitive Search s právy správce. Pro návod k portálu začněte s [úvodním seznamem: Vytvořte index Azure Cognitive Search na portálu](search-get-started-portal.md).

## <a name="scale-your-service"></a>Škálování služby

Po zřízení můžete službu škálovat tak, aby vyhovovala vašim potřebám. Pokud jste pro službu Azure Cognitive Search zvolili úroveň Standard, můžete službu škálovat ve dvou dimenzích: repliky a oddíly. Pokud byste zvolili úroveň Basic, mohli byste přidávat pouze repliky. Při zřízení bezplatné služby škálování není k dispozici.

***Oddíly***: Umožňují službě ukládat a prohledávat více dokumentů.

***Repliky***: Umožňují službě zpracovat větší množství vyhledávacích dotazů.

Přidáním prostředků se zvýší vaše měsíční náklady. [Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/) vám pomůže porozumět důsledkům přidání prostředků na fakturaci. Mějte na paměti, že prostředky můžete upravit na základě zatížení. Například můžete navýšit prostředky kvůli vytvoření úplného počátečního indexu a později je pak snížit na úroveň vhodnější pro přírůstkové indexování.

> [!Important]
> Služba musí mít [2 repliky pro smlouvu SLA jen pro čtení a 3 repliky pro smlouvu SLA se čtením a zápisem](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Přejděte na stránku vyhledávací služby na webu Azure Portal.
2. V levém navigačním podokně vyberte **Měřítko** > **nastavení**.
3. Přetažením posuvníku můžete přidat prostředky obou typů.

![Přidání kapacity](./media/search-create-service-portal/settings-scale.png "Přidání kapacity prostřednictvím replik a oddílů")

> [!Note]
> Úložiště na oddíl a rychlost se zvyšuje na vyšších úrovních. Další informace naleznete v [tématu kapacita a limity](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Přidání druhé služby

Většina zákazníků používá pouze jednu službu zjišťovnou na úrovni poskytující [správnou rovnováhu zdrojů](search-sku-tier.md). Jedna služba může hostovat více indexů v souladu s [maximálními limity vámi vybrané úrovně](search-capacity-planning.md), přičemž každý index je izolovaný od ostatních indexů. V Azure Cognitive Search požadavky lze přesměrovat pouze na jeden index, minimalizace pravděpodobnost náhodnénebo úmyslné načítání dat z jiných indexů ve stejné službě.

I když většina zákazníků používá jenom jednu službu, redundance služby může být nutná v případě následujících provozních požadavků:

* Zotavení po havárii (výpadek datového centra). Azure Cognitive Search neposkytuje okamžité převzetí služeb při selhání v případě výpadku. Doporučení a pokyny najdete v tématu [Správa služby](search-manage.md).
* Z vašeho zkoumání modelování více tenantů vyplynulo, že využívání dalších služeb je optimální řešení. Další informace najdete v tématu [Návrh pro více tenantů](search-modeling-multitenant-saas-applications.md).
* Pro globálně nasazené aplikace můžete vyžadovat instanci Azure Cognitive Search ve více oblastech, abyste minimalizovali latenci mezinárodního provozu vaší aplikace.

> [!NOTE]
> V Azure Cognitive Search nelze oddělit indexování a dotazování operace; proto byste nikdy vytvořit více služeb pro oddělené úlohy. Index je vždy dotazován ve službě, ve které byl vytvořen (nemůžete vytvořit index v jedné službě a zkopírovat ho do jiné).

Druhá služba není potřebná pro zajištění vysoké dostupnosti. Vysoká dostupnost pro dotazy se dosáhne, když použijete 2 nebo více replik v rámci stejné služby. Aktualizace replik jsou sekvenční, což znamená, že alespoň jeden je funkční při aktualizaci služby je zavedena. Další informace o provozuna naleznete v [tématu Smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Další kroky

Po zřizování služby můžete pokračovat na portálu a vytvořit tak svůj první index.

> [!div class="nextstepaction"]
> [Úvodní příručka: Vytvoření indexu Azure Cognitive Search na portálu](search-get-started-portal.md)
