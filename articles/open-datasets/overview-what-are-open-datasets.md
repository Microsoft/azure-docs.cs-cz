---
title: Co jsou otevřené datové sady? Spravované veřejné datové sady
titleSuffix: Azure Open Datasets
description: Seznamte se s otevřenými datovými sadami Azure, pokládanými datovými sadami z veřejné domény, jako jsou počasí, sčítání, svátky a umístění pro obohacení prediktivních řešení.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: 2ab713da87bd67053505289f5c9c8e7b747fd6bd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654843"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Co jsou otevřené datové sady Azure a jak je můžete použít?

[Otevřené datové sady v Azure](https://azure.microsoft.com/services/open-datasets/) jsou spravované veřejné datové sady, které můžete použít k přidání funkcí specifických pro konkrétní scénář do řešení Machine Learning pro přesnější modely. Otevřené datové sady jsou v cloudu na Microsoft Azure a jsou integrované do Azure Machine Learning a snadno dostupné pro Azure Databricks a Machine Learning Studio (Classic). K datovým sadám můžete také přistupovat prostřednictvím rozhraní API a používat je v jiných produktech, například Power BI a Azure Data Factory.

Datové sady zahrnují data z veřejných domén pro počasí, sčítání, svátky, veřejné zabezpečení a umístění, které vám pomůžou naučit modely strojového učení a rozšířit prediktivní řešení. Veřejné datové sady můžete sdílet taky v otevřených datových sadách Azure. 

![Komponenty Azure Open DataSet](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Zpracované, připravené datové sady
Spravované otevřené veřejné datové sady v Azure Open DataSets jsou optimalizované pro využití v pracovních postupech služby Machine Learning. 

Všechny datové sady, které jsou k dispozici, zobrazíte tak, že přejdete do [katalogu Open DataSets v Azure](https://azure.microsoft.com/services/open-datasets/catalog/).

Odborníci přes data často tráví většinu času, který čistí a připravují data pro pokročilé analýzy. Otevřené datové sady se zkopírují do cloudu Azure a předzpracované za účelem úspory času. V pravidelných intervalech jsou data načítána ze zdrojů, například prostřednictvím připojení FTP k národním oceán a správě atmosféry (NOAA). V dalším kroku se data analyzují do strukturovaného formátu a pak se rozšiřují podle potřeby s funkcemi, jako je například PSČ nebo umístění nejbližších povětrnostních stanic.

Datové sady jsou spoluhostovány s cloudovým výpočetním prostředím v Azure, což usnadňuje přístup a manipulaci.  

Následují příklady datových sad, které jsou k dispozici. 

### <a name="weather-data"></a>Data o počasí
 
|Datová sada         | Notebooks     | Popis                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA Integrated Surface data (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Celosvětově hodinově povětrnostní data z NOAA s nejlepším pokrytím v Severní Amerika, Evropě, Austrálii a částech Asie. Aktualizováno denně. |
|[Systém NOAA Global prognóze (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | data předpovědi z NOAAu z 15. dne v USA Aktualizováno denně. |

### <a name="calendar-data"></a>Data kalendáře

|Datová sada         | Notebooks     | Popis                                    |
|----------------|---------------|------------------------------------------------|
|[Veřejné svátky](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Celosvětové veřejné svátky, které zahrnují 41 zemí nebo oblastí od 1970 do 2099. Zahrnuje zemi a to, jestli má většina lidí placené volno. |

## <a name="access-to-datasets"></a>Přístup k datovým sadám  
Pomocí účtu Azure máte přístup k otevřeným datovým sadám pomocí kódu nebo přes rozhraní služby Azure. Data se společně nacházejí v cloudových výpočetních prostředcích Azure pro použití ve vašem řešení Machine Learning.  

Otevřené datové sady jsou dostupné prostřednictvím uživatelského rozhraní Azure Machine Learning a sady SDK. Otevřené datové sady také poskytují poznámkové bloky Azure Notebooks a Azure Databricks, které můžete použít k připojení dat k Azure Machine Learning a Azure Databricks. K datovým sadám lze také přistupovat prostřednictvím sady Python SDK. 

Pro přístup k otevřeným datovým sadám ale nepotřebujete účet Azure. k nim máte přístup z libovolného prostředí Pythonu s Sparkem nebo bez něj.

## <a name="request-or-contribute-datasets"></a>Datové sady pro žádosti nebo Contribute

Pokud nemůžete najít požadovaná data, pošlete nám e-mail s [žádostí o datovou sadu](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) nebo [přispívat na datovou sadu](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Další kroky
* [Ukázkový Poznámkový blok](samples.md)
* [Kurz: regrese modelování pomocí NY taxislužby dat](../machine-learning/tutorial-auto-train-models.md?context=azure%252fopen-datasets%252fcontext%252fopen-datasets-context)
* [Python SDK pro otevřené datové sady](/python/api/azureml-opendatasets/)
