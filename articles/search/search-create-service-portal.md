---
title: Vytvoření vyhledávací služby na portálu
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu na portálu se dozvíte, jak nastavit prostředek služby Azure Kognitivní hledání v Azure Portal. Vyberte skupiny prostředků, oblasti a SKU nebo cenovou úroveň.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/15/2021
ms.openlocfilehash: 7149233782815deebebde53767a3c654ac2321bb
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547753"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Rychlé zprovoznění: Vytvoření služby Azure Cognitive Search na portálu

[Azure kognitivní hledání](search-what-is-azure-search.md) je prostředek Azure, který se používá k přidání funkce fulltextového vyhledávání do vlastních aplikací. Můžete ji snadno integrovat s jinými službami Azure, které poskytují data nebo další zpracování, s aplikacemi na síťových serverech nebo se softwarem běžícím na jiných cloudových platformách.

Službu Search můžete vytvořit pomocí [Azure Portal](https://portal.azure.com/), který je popsaný v tomto článku. Můžete také použít [Azure PowerShell](search-manage-powershell.md), [Azure CLI](/cli/azure/search)nebo [šablonu služby Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

[![Animovaný GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## <a name="before-you-start"></a>Než začnete

Pro dobu života služby jsou opraveny následující vlastnosti služby – změna kterékoli z nich vyžaduje novou službu. Vzhledem k tomu, že jsou pevně dané, zvažte použití dopadu při vyplňování jednotlivých vlastností:

* Název služby se může nacházet v rámci koncového bodu adresy URL ([recenze tipů](#name-the-service) pro užitečné názvy služeb).
* [Úroveň služby](search-sku-tier.md) má vliv na fakturaci a nastavuje limit nahoru pro kapacitu. Některé funkce nejsou dostupné na úrovni Free.
* Oblast služby může určit dostupnost určitých scénářů. Pokud potřebujete [funkce s vysokým zabezpečením](search-security-overview.md) nebo [obohacení AI](cognitive-search-concept-intro.md), budete muset vytvořit Azure kognitivní hledání ve stejné oblasti jako jiné služby nebo v oblastech, které tuto funkci poskytují. 

## <a name="subscribe-free-or-paid"></a>Předplatné (bezplatné nebo placené)

[Otevřete si bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a použijte bezplatné kredity k vyzkoušení placených služeb Azure. Až kredity vyčerpáte, můžete si účet ponechat a dál používat bezplatné služby Azure, jako například Azure Websites. Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Případně si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure. 

## <a name="find-azure-cognitive-search"></a>Najít Kognitivní hledání Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém horním rohu klikněte na symbol plus (**+ vytvořit prostředek**).

1. Pomocí panelu hledání vyhledejte "Azure kognitivní hledání" nebo přejděte k prostředku prostřednictvím **webu**  >  **Azure kognitivní hledání**.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Vytvoření prostředku na portálu" border="false":::

## <a name="choose-a-subscription"></a>Zvolit předplatné

Pokud máte více než jedno předplatné, vyberte pro vyhledávací službu jednu. Pokud implementujete [dvojité šifrování](search-security-overview.md#double-encryption) nebo jiné funkce, které jsou závislé na identitách spravované služby, vyberte stejné předplatné, které se používá pro Azure Key Vault nebo jiné služby, pro které se používají spravované identity.

## <a name="set-a-resource-group"></a>Nastavení skupiny prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro vaše řešení Azure. Služba vyhledávání je vyžaduje. Je to také užitečné pro správu prostředků, včetně nákladů. Skupina prostředků se může skládat z jedné služby nebo více služeb používaných společně. Pokud například používáte Azure Kognitivní hledání k indexování databáze Azure Cosmos DB, mohli byste pro účely správy nastavit obě služby stejné skupiny prostředků. 

Pokud nekombinujete prostředky do jedné skupiny, nebo pokud jsou stávající skupiny prostředků vyplněné prostředky používanými v nesouvisejících řešeních, vytvořte novou skupinu prostředků jenom pro svůj prostředek Azure Kognitivní hledání. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Vytvoření nové skupiny prostředků" border="false":::

V průběhu času můžete sledovat aktuální a předpokládané náklady nebo si můžete zobrazit poplatky za jednotlivé prostředky. Následující snímek obrazovky znázorňuje druh informací o nákladech, které můžete očekávat při kombinování více prostředků do jedné skupiny.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Správa nákladů na úrovni skupiny prostředků" border="false":::

> [!TIP]
> Skupiny prostředků zjednodušují vyčištění, protože odstraněním skupiny se odstraní všechny služby, které jsou v ní obsažené. U prototypových projektů, které využívají více služeb, spojení všech služeb do stejné skupiny prostředků usnadňuje vyčištění po skončení projektu.

## <a name="name-the-service"></a>Pojmenování služby

V části Podrobnosti o instanci zadejte název služby do pole **Adresa URL** . Název je součástí koncového bodu adresy URL, proti kterému jsou vydávána volání rozhraní API: `https://your-service-name.search.windows.net` . Například pokud chcete, aby byl koncový bod `https://myservice.search.windows.net` , měli byste zadat `myservice` .

Požadavky na název služby:

* Musí být jedinečný v rámci oboru názvů search.windows.net.
* Musí mít délku 2 až 60 znaků.
* Je nutné použít malá písmena, číslice nebo pomlčky (-).
* Nepoužívejte pomlčky ("-") v prvních 2 znacích nebo jako poslední znak.
* Nemusíte používat po sobě jdoucí pomlčky ("--") kdekoli

> [!TIP]
> Pokud si myslíte, že budete používat víc služeb, doporučujeme, abyste do názvu služby jako zásadu vytváření názvů zavedli také oblast (nebo umístění). Služby v rámci stejné oblasti můžou bezplatně vyměňovat data, takže pokud je Azure Kognitivní hledání v Západní USA a máte i další služby v Západní USA, název, který vám `mysearchservice-westus` může při rozhodování o kombinování a připojování prostředků ušetřit cestu na stránku vlastností.

## <a name="choose-a-location"></a>Zvolte umístění.

Azure Kognitivní hledání je k dispozici ve většině oblastí, jak je uvedeno na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/).

Obecně platí, že pokud používáte více služeb Azure, vyberte oblast, která je také hostitelem vašich dat nebo aplikační služby. Tím dojde k minimalizaci nebo zrušení poplatků za šířku pásma pro odchozí data (pro odchozí data se neúčtují žádné poplatky, pokud jsou služby ve stejné oblasti).

Zákazníci, kteří mají požadavky na provozní kontinuitu a zotavení po havárii (BCDR), by měli vytvořit své služby v [regionálních párů](../best-practices-availability-paired-regions.md#azure-regional-pairs). Pokud například pracujete v Severní Amerika, můžete pro každou službu zvolit Východní USA a Západní USA, nebo Střed USA – sever a Jižní centra USA.

Následující funkce jsou k dispozici pouze v určitých oblastech:

* Rozšíření AI vyžaduje, aby Cognitive Services být ve stejné oblasti jako Azure Kognitivní hledání. Nejdříve zvolte [oblast Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) (seznam je menší) a pak zvolte stejnou oblast pro vyhledávací službu.

* Dvojité šifrování je dostupné jenom v určitých oblastech. Další informace najdete v tématu [dvojité šifrování](search-security-overview.md#double-encryption) .

* Podpora zón dostupnosti se v konkrétních oblastech nabízí na službách vytvořených po konkrétních datech. Další informace najdete [v části "zóny dostupnosti" v tématu škálování pro výkon](search-performance-optimization.md#availability-zones).

## <a name="choose-a-pricing-tier"></a>Výběr cenové úrovně

Služba Azure Kognitivní hledání se teď nabízí v [několika cenových úrovních](https://azure.microsoft.com/pricing/details/search/): optimalizováno, Basic, Standard nebo Storage. Každá úroveň má svou vlastní [kapacitu a limity](search-limits-quotas-capacity.md). Pokyny najdete v tématu [Volba cenové úrovně](search-sku-tier.md) .

Basic a Standard jsou nejběžnější možnosti pro produkční úlohy, ale většina zákazníků začíná bezplatnou službou. Mezi hlavní rozdíly mezi úrovněmi patří velikost oddílu a rychlost a omezení počtu objektů, které můžete vytvořit.

Pamatujte na to, že po vytvoření služby se cenová úroveň nedá změnit. Pokud potřebujete vyšší nebo nižší úroveň, budete muset službu znovu vytvořit.

## <a name="create-your-service"></a>Vytvoření služby

Po zadání potřebných vstupů pokračujte a vytvořte službu. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Kontrola a vytvoření služby" border="false":::

Vaše služba se nasadí během několika minut. Průběh můžete sledovat prostřednictvím oznámení Azure. Zvažte možnost připnutí služby na řídicí panel, aby byl v budoucnu snadný přístup.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Monitorování a připnutí služby" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>Získání klíčového a URL koncového bodu

Pokud nepoužíváte portál, programový přístup k vaší nové službě vyžaduje, abyste zadali koncový bod adresy URL a klíč rozhraní API pro ověřování.

1. Na stránce **Přehled** vyhledejte a zkopírujte koncový bod adresy URL na pravé straně stránky.

2. Na stránce **klíče** zkopírujte jeden z klíčů správce (Jedná se o ekvivalentní). K vytváření, aktualizaci a odstraňování objektů ve službě se vyžadují klíče rozhraní API pro správu. Naproti tomu klíče dotazů poskytují přístup pro čtení k obsahu indexu.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Stránka s přehledem služby s koncovým bodem adresy URL" border="false":::

Koncový bod a klíč nejsou pro úlohy založené na portálu vyžadovány. Portál je už propojený s vaším prostředkem Azure Kognitivní hledání s právy správce. Návod pro portál, Začínáme s [rychlým startem: vytvoření indexu služby Azure kognitivní hledání na portálu](search-get-started-portal.md).

## <a name="scale-your-service"></a>Škálování služby

Po zřízení můžete službu škálovat tak, aby vyhovovala vašim potřebám. Pokud jste pro službu Azure Kognitivní hledání zvolili úroveň Standard, můžete škálovat službu ve dvou dimenzích: repliky a oddíly. Pokud byste zvolili úroveň Basic, mohli byste přidávat pouze repliky. Při zřízení bezplatné služby škálování není k dispozici.

***Oddíly***: Umožňují službě ukládat a prohledávat více dokumentů.

***Repliky***: Umožňují službě zpracovat větší množství vyhledávacích dotazů.

Přidáním prostředků se zvýší vaše měsíční náklady. [Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/) vám pomůže porozumět důsledkům přidání prostředků na fakturaci. Mějte na paměti, že prostředky můžete upravit na základě zatížení. Například můžete navýšit prostředky kvůli vytvoření úplného počátečního indexu a později je pak snížit na úroveň vhodnější pro přírůstkové indexování.

> [!Important]
> Služba musí mít [2 repliky pro smlouvu SLA jen pro čtení a 3 repliky pro smlouvu SLA se čtením a zápisem](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Přejděte na stránku vyhledávací služby na webu Azure Portal.
2. V levém navigačním podokně vyberte **Nastavení**  >  **škálovat**.
3. Přetažením posuvníku můžete přidat prostředky obou typů.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Přidání kapacity prostřednictvím replik a oddílů" border="false":::

> [!Note]
> Úložiště na oddíly a rychlost se zvyšují na vyšších úrovních. Další informace najdete v tématu o [kapacitě a omezeních](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Přidání druhé služby

Většina zákazníků používá jenom jednu službu zřízenou v úrovni, která poskytuje [správnou rovnováhu prostředků](search-sku-tier.md). Jedna služba může hostovat více indexů v souladu s [maximálními limity vámi vybrané úrovně](search-capacity-planning.md), přičemž každý index je izolovaný od ostatních indexů. V Azure Kognitivní hledání můžou být požadavky směrovány jenom na jeden index a zároveň se minimalizuje pravděpodobnost náhodného nebo úmyslného načítání dat z jiných indexů ve stejné službě.

I když většina zákazníků používá jenom jednu službu, redundance služby může být nutná v případě následujících provozních požadavků:

+ [Provozní kontinuita a zotavení po havárii (BCDR)](../best-practices-availability-paired-regions.md). Azure Kognitivní hledání v případě výpadku neposkytuje okamžité převzetí služeb při selhání.

+ [Víceklientské architektury](search-modeling-multitenant-saas-applications.md) se někdy volají na dvě nebo více služeb.

+ Globálně nasazené aplikace můžou vyžadovat, aby se služby Search v každé geografické oblasti minimalizovaly při latenci.

> [!NOTE]
> V Azure Kognitivní hledání nemůžete oddělit operace indexování a dotazování; proto nikdy nevytvoříte více služeb pro oddělené úlohy. Index je vždy dotazován ve službě, ve které byl vytvořen (nemůžete vytvořit index v jedné službě a zkopírovat ho do jiné).

Druhá služba není potřebná pro zajištění vysoké dostupnosti. Vysoká dostupnost pro dotazy se dosáhne, když použijete 2 nebo více replik v rámci stejné služby. Aktualizace repliky jsou sekvenční, což znamená, že nejméně jedna je v případě, že je aktualizace služby nasazená, funkční. Další informace o době provozu najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Další kroky

Po zřízení služby můžete na portálu pokračovat a vytvořit svůj první index.

> [!div class="nextstepaction"]
> [Rychlý Start: vytvoření indexu služby Azure Kognitivní hledání na portálu](search-get-started-portal.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)