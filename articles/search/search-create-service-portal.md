---
title: Vytvoření služby Azure Search na portálu – Azure Search
description: Zřídí prostředek Azure Search v Azure Portal. Vyberte skupiny prostředků, oblasti a SKU nebo cenovou úroveň.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 08/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0aabf10b3be8f2f13abbba15d514a008f193646c
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649950"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Vytvoření služby Azure Search na portálu

Azure Search je samostatný prostředek, který slouží k připojení vyhledávacího prostředí ve vlastních aplikacích. I když se Azure Search snadno integruje s jinými službami Azure, můžete ho použít také jako samostatnou součást nebo ji integrovat s aplikacemi na síťových serverech nebo se softwarem běžícím na jiných cloudových platformách.

V tomto článku se dozvíte, jak vytvořit prostředek Azure Search v [Azure Portal](https://portal.azure.com/).

[![Animovaný GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Dáváte přednost prostředí PowerShell? Použijte [šablonu služby](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manageru. Nápovědu k části Začínáme najdete v tématu [správa Azure Search pomocí PowerShellu](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Předplatné (bezplatné nebo placené)

[Otevřete si bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a použijte bezplatné kredity k vyzkoušení placených služeb Azure. Až kredity vyčerpáte, můžete si účet ponechat a dál používat bezplatné služby Azure, jako například Azure Websites. Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Případně si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure. 

## <a name="find-azure-search"></a>Vyhledání služby Azure Search

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V levém horním rohu klikněte na symbol plus (+ vytvořit prostředek).
3. Pomocí panelu hledání vyhledejte "Azure Search" nebo přejděte k prostředku prostřednictvím **webové** > **Azure Search**.

![Přejít na prostředek Azure Search](./media/search-create-service-portal/find-search3.png "Navigační cesta k Azure Search")

## <a name="select-a-subscription"></a>Vyberte předplatné

Pokud máte více než jedno předplatné, vyberte to, jehož součástí jsou také služby úložiště dat nebo souborů. Azure Search může automaticky detekovat Azure Table a BLOB Storage, SQL Database a Azure Cosmos DB pro indexování prostřednictvím [*indexerů*](search-indexer-overview.md), ale jenom pro služby v rámci stejného předplatného.

## <a name="set-a-resource-group"></a>Nastavení skupiny prostředků

Skupina prostředků je povinná a je užitečná pro správu prostředků, včetně služby cost management. Skupina prostředků se může skládat z jedné služby nebo více služeb používaných společně. Pokud například používáte Azure Search k indexování databáze Azure Cosmos DB, mohli byste pro účely správy nastavit obě služby stejné skupiny prostředků. 

Pokud nekombinujete prostředky do jedné skupiny, nebo pokud jsou stávající skupiny prostředků vyplněné prostředky používanými v nesouvisejících řešeních, vytvořte novou skupinu prostředků, která bude sloužit jenom pro váš prostředek Azure Search. 

Při používání služby můžete sledovat náklady na aktuální a předpokládané náklady (jak je znázorněno na snímku obrazovky), nebo se můžete posunout dolů a zobrazit poplatky za jednotlivé prostředky.

![Správa nákladů na úrovni skupiny prostředků](./media/search-create-service-portal/resource-group-cost-management.png "Správa nákladů na úrovni skupiny prostředků")

> [!TIP]
> Odstraněním skupiny prostředků se odstraní také služby v této skupině. U prototypových projektů, které využívají více služeb, spojení všech služeb do stejné skupiny prostředků usnadňuje vyčištění po skončení projektu.

## <a name="name-the-service"></a>Pojmenování služby

V části Podrobnosti o instanci zadejte název služby do pole **Adresa URL** . Název je součástí koncového bodu adresy URL, proti kterému jsou vydávána `https://your-service-name.search.windows.net`volání rozhraní API:. Například pokud chcete `https://myservice.search.windows.net`, aby byl koncový bod, měli byste zadat `myservice`.

Požadavky na název služby:

* Musí být jedinečný v rámci oboru názvů search.windows.net.
* Délka musí být 2 až 60 znaků.
* Používejte malá písmena, číslice nebo pomlčky („-“).
* Nepoužívejte pomlčku („-“) v prvních 2 znacích nebo jako poslední znak.
* Nikde nepoužívejte po sobě jdoucí pomlčky („--“).

> [!TIP]
> Pokud si myslíte, že budete používat víc služeb, doporučujeme, abyste do názvu služby jako zásadu vytváření názvů zavedli také oblast (nebo umístění). Služby v rámci stejné oblasti můžou bezplatně vyměňovat data, takže pokud je Azure Search v západní USA a máte i další služby v západní USA, název `mysearchservice-westus` , který vám může při rozhodování o kombinování a připojování prostředků ušetřit cestu na stránku vlastností.

## <a name="choose-a-location"></a>Zvolit umístění

Protože se jedná o službu Azure, je Azure Search možné hostovat v datových centrech po celém světě. Seznam podporovaných oblastí najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/). 

Kliknutím na stejné umístění pro několik služeb můžete snížit nebo vyvarovat poplatky za šířku pásma. Pokud například indexuje data, která poskytuje jiná služba Azure (Azure Storage, Azure Cosmos DB, Azure SQL Database), vytvoření služby Azure Search ve stejné oblasti se vyhne poplatkům za šířku pásma (u odchozích dat se neúčtují žádné poplatky, když jsou služby v nástroji). stejné oblasti).

Pokud navíc používáte obohacení vyhledávání AI, vytvořte službu ve stejné oblasti jako prostředek Cognitive Services. *Společné umístění Azure Search a Cognitive Services ve stejné oblasti je požadavek na obohacení AI*.

> [!Note]
> Střed Indie není v tuto chvíli pro nové služby k dispozici. Pro služby, které jsou už v Střed Indie, můžete škálovat bez omezení a služba je v této oblasti plně podporovaná. Omezení v této oblasti je dočasné a omezené jenom na nové služby. Tato poznámka se odstraní, když už neplatí omezení.

## <a name="choose-a-pricing-tier-sku"></a>Zvolit cenovou úroveň (SKU)

[Azure Search se momentálně nabízí v několika cenových úrovních](https://azure.microsoft.com/pricing/details/search/): Free, Basic nebo Standard. Každá úroveň má svou vlastní [kapacitu a limity](search-limits-quotas-capacity.md). Další pokyny získáte v tématu věnovaném [volbě cenové úrovně nebo SKU](search-sku-tier.md).

Basic a Standard jsou nejběžnější možnosti pro produkční úlohy, ale většina zákazníků začíná bezplatnou službou. Hlavním rozdílem mezi úrovněmi je velikost oddílu a rychlost a omezení počtu objektů, které můžete vytvořit.

Pamatujte, že cenovou úroveň nelze po vytvoření služby změnit. Pokud budete později potřebovat vyšší nebo nižší úroveň, budete muset službu znovu vytvořit.

## <a name="create-your-service"></a>Vytvoření služby

Po zadání potřebných vstupů pokračujte a vytvořte službu. 

![Kontrola a vytvoření služby](./media/search-create-service-portal/new-service3.png "Kontrola a vytvoření služby")

Vaše služba se nasadí během několika minut, což můžete sledovat prostřednictvím oznámení Azure. Zvažte možnost připnutí služby na řídicí panel, aby byl v budoucnu snadný přístup.

![Monitorování a připnutí služby](./media/search-create-service-portal/monitor-notifications.png "Monitorování a připnutí služby")

## <a name="get-a-key-and-url-endpoint"></a>Získání klíčového a URL koncového bodu

Pokud nepoužíváte portál, programový přístup k vaší nové službě vyžaduje, abyste zadali koncový bod adresy URL a klíč rozhraní API pro ověřování.

1. Na stránce Přehled služby Najděte a zkopírujte koncový bod adresy URL na pravé straně stránky.

2. V levém navigačním podokně vyberte **klíče** a pak zkopírujte jeden z klíčů správce (Jedná se o ekvivalentní). K vytváření, aktualizaci a odstraňování objektů ve službě se vyžadují klíče rozhraní API pro správu.

   ![Stránka s přehledem služby s koncovým bodem adresy URL](./media/search-create-service-portal/get-url-key.png "Koncový bod adresy URL a další podrobnosti služby")

Koncový bod a klíč nejsou pro úlohy založené na portálu vyžadovány. Portál je už propojený s vaším prostředkem Azure Search s právy správce. Pro návod k portálu začněte [rychlým startem: Vytvořte Azure Search index na portálu](search-get-started-portal.md).

## <a name="scale-your-service"></a>Škálování služby

Po zřízení můžete službu škálovat tak, aby vyhovovala vašim potřebám. Pokud jste pro službu Azure Search zvolili úroveň Standard, můžete škálovat službu ve dvou dimenzích: repliky a oddíly. Pokud byste zvolili úroveň Basic, mohli byste přidávat pouze repliky. Při zřízení bezplatné služby škálování není k dispozici.

***Oddíly***: Umožňují službě ukládat a prohledávat více dokumentů.

***Repliky***: Umožňují službě zpracovat větší množství vyhledávacích dotazů.

Přidáním prostředků se zvýší vaše měsíční náklady. [Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/) vám pomůže porozumět důsledkům přidání prostředků na fakturaci. Mějte na paměti, že prostředky můžete upravit na základě zatížení. Například můžete navýšit prostředky kvůli vytvoření úplného počátečního indexu a později je pak snížit na úroveň vhodnější pro přírůstkové indexování.

> [!Important]
> Služba musí mít [2 repliky pro smlouvu SLA jen pro čtení a 3 repliky pro smlouvu SLA se čtením a zápisem](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Přejděte na stránku vyhledávací služby na webu Azure Portal.
2. V levém navigačním podokně vyberte **Nastavení** > **Škálování**.
3. Přetažením posuvníku můžete přidat prostředky obou typů.

![Přidat kapacitu](./media/search-create-service-portal/settings-scale.png "Přidání kapacity prostřednictvím replik a oddílů")

> [!Note]
> Úložiště na oddíly a rychlost se zvyšují na vyšších úrovních. Další informace najdete v tématu o [kapacitě a omezeních](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Přidání druhé služby

Většina zákazníků používá jenom jednu službu zřízenou v úrovni, která poskytuje [správnou rovnováhu prostředků](search-sku-tier.md). Jedna služba může hostovat více indexů v souladu s [maximálními limity vámi vybrané úrovně](search-capacity-planning.md), přičemž každý index je izolovaný od ostatních indexů. Ve službě Azure Search můžou být požadavky směrovány pouze na jeden index, což minimalizuje nebezpečí náhodného nebo záměrného načítání dat z jiných indexů ve stejné službě.

I když většina zákazníků používá jenom jednu službu, redundance služby může být nutná v případě následujících provozních požadavků:

* Zotavení po havárii (výpadek datového centra). Služba Azure Search neposkytuje rychlé převzetí služeb při selhání v případě výpadku. Doporučení a pokyny najdete v tématu [Správa služby](search-manage.md).
* Z vašeho zkoumání modelování více tenantů vyplynulo, že využívání dalších služeb je optimální řešení. Další informace najdete v tématu [Návrh pro více tenantů](search-modeling-multitenant-saas-applications.md).
* U globálně nasazených aplikací můžete potřebovat instanci Azure Search ve více oblastech, abyste minimalizovali latenci při mezinárodní provozu vaší aplikace.

> [!NOTE]
> V Azure Search nemůžete oddělit operace indexování a dotazování; proto nikdy nevytvoříte více služeb pro oddělené úlohy. Index je vždy dotazován ve službě, ve které byl vytvořen (nemůžete vytvořit index v jedné službě a zkopírovat ho do jiné).

Druhá služba není potřebná pro zajištění vysoké dostupnosti. Vysoká dostupnost pro dotazy se dosáhne, když použijete 2 nebo více replik v rámci stejné služby. Aktualizace replik jsou sekvenční, což znamená, že alespoň jedna replika je funkční při nasazení aktualizace služby. Další informace o době provozu najdete v tématu [Smlouvy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Další postup

Po zřízení Azure Search služby můžete na portálu pokračovat a vytvořit svůj první index.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Vytvoření indexu Azure Search na portálu](search-get-started-portal.md)
