---
title: Co jsou otevřené datové sady? Kurátorské veřejné datové sady
titleSuffix: Azure Open Datasets
description: Seznamte se s otevřenými datovými sadami Azure, kurátorovanými datovými sadami z veřejné domény, jako je počasí, sčítání lidu, svátky a umístění, abyste obohatili prediktivní řešení.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: fd5697f9c325dc4ad866c333ce1b20e008ebfa24
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73606153"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Co jsou datové sady Azure Open a jak je můžete použít?

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) jsou sestavené veřejné datové sady, které můžete použít k přidání funkcí specifických pro scénář do řešení strojového učení pro přesnější modely. Otevřené datové sady jsou v cloudu v Microsoft Azure a jsou integrované do Azure Machine Learning a snadno dostupné pro Azure Databricks a Machine Learning Studio (klasické). Datové sady můžete také přistupovat prostřednictvím api a používat je v jiných produktech, jako je Power BI a Azure Data Factory.

Datové sady obsahují data veřejné domény pro počasí, sčítání lidu, svátky, veřejnou bezpečnost a umístění, které vám pomohou trénovat modely strojového učení a obohacovat prediktivní řešení. Veřejné datové sady můžete také sdílet v otevřených datových sadách Azure. 

![Součásti otevřených datových sad Azure](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Kurátorské, připravené datové sady
Přehledné otevřené veřejné datové sady v otevřených datových sadách Azure jsou optimalizované pro spotřebu v pracovních postupech strojového učení. 

Datoví vědci často tráví většinu svého času čištěním a přípravou dat pro pokročilou analýzu. Otevřené datové sady se zkopírují do cloudu Azure a předem zpracují, aby vám ušetřily čas. V pravidelných intervalech jsou data získávána ze zdrojů, například připojením FTP k Národní správě oceánů a atmosféry (NOAA). Dále jsou data analyzována do strukturovaného formátu a podle potřeby obohacena o funkce, jako je PSČ nebo umístění nejbližší meteorologické stanice.

Datové sady jsou cohosted s cloudovými výpočetními prostředky v Azure, které usnadňují přístup a manipulaci.  

Následují příklady dostupných datových sad. 

### <a name="weather-data"></a>Údaje o počasí
 
|Datová sada         | Poznámkové bloky     | Popis                                    |
|----------------|---------------|------------------------------------------------|
|[Noaa Integrovaná povrchová data (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Celosvětově hodinová data o počasí z NOAA s nejlepším prostorovým pokrytím v Severní Americe, Evropě, Austrálii a částech Asie. Aktualizováno denně. |
|[Globální předpovědní systém NOAA (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15-denní usa hodinové předpovědi počasí data z NOAA. Aktualizováno denně. |

### <a name="calendar-data"></a>Data kalendáře

|Datová sada         | Poznámkové bloky     | Popis                                    |
|----------------|---------------|------------------------------------------------|
|[Svátcích](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Celosvětové údaje o státních svátcích, které zahrnují 41 zemí nebo regionů v letech 1970 až 2099. Zahrnuje zemi a to, zda většina lidí zaplatila volno. |

## <a name="access-to-datasets"></a>Přístup k datovým souborům  
S účtem Azure můžete přistupovat k otevřeným datovým sestavvám pomocí kódu nebo prostřednictvím rozhraní služby Azure. Data se společně namiňují s cloudovými výpočetními prostředky Azure pro použití ve vašem řešení strojového učení.  

Otevřené datové sady jsou dostupné prostřednictvím uzla Azure Machine Learning a sady SDK. Otevřené datové sady také poskytují poznámkové bloky Azure a poznámkové bloky Azure Databricks, které můžete použít k připojení dat k Azure Machine Learning a Azure Databricks. Datové sady lze také přistupovat prostřednictvím Python SDK. 

Pro přístup k otevřeným datovým sestavám však nepotřebujete účet Azure. můžete k nim přistupovat z libovolného prostředí Pythonu s nebo bez Spark.

## <a name="request-or-contribute-datasets"></a>Žádost o datové sady nebo datové sady přispívat

Pokud nemůžete najít požadovaná data, pošlete nám e-mail [s žádostí o datovou sadu](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) nebo [přispějte datovou sadou](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Další kroky
* [Ukázkový poznámkový blok](samples.md)
* [Kurz: Regresní modelování s daty taxi NY](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Sada Python SDK pro otevřené datové sady](/python/api/azureml-opendatasets/?view=azure-ml-py)
