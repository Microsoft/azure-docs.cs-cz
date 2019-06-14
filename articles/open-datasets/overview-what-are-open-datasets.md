---
title: Co jsou otevřené datových sad? Kurátorované veřejných datových sad
titleSuffix: Azure Open Datasets (preview)
description: Přečtěte si o Azure Open datové sady (preview), datové kurátorované sady z veřejné domény, které jsou připravené k použití v řešení strojového učení a analýz. Datové sady zahrnují veřejná data, jako je například počasí, sčítání, svátků a umístění můžete rozšířit řešení prediktivní.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: b01ad5a4fd8808c1e841ba2fb47ea06ece011010
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051030"
---
# <a name="what-are-azure-open-datasets-preview-and-how-can-you-use-them"></a>Co jsou Azure Open datové sady (preview) a tom, jak můžete využít?

[Datové sady Azure Open](https://azure.microsoft.com/services/open-datasets/) jsou kurátorované veřejných datových sad, které vám umožní přidat funkce specifické pro scénář řešení strojového učení pro přesnější modely. Otevřít datové sady jsou v cloudu v Microsoft Azure a jsou snadno dostupné pro Azure Databricks, služby Machine Learning a Machine Learning Studio. Můžete také přístup k datové sady pomocí rozhraní API a jejich použití v jiných produktech, jako je například Power BI a Azure Data Factory.

Datové sady zahrnují data veřejné domény pro počasí, sčítání, dovolené, veřejný pořádek a umístění, které vám pomůžou trénování modelů strojového učení a rozšířit řešení prediktivní. Můžete také sdílet vaše veřejné datové sady v Azure otevřete datové sady. 

![Komponenty Azure Open datové sady](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Kurátorované, připravené datové sady
Kurátorované otevřít veřejných datových sad v Azure otevřete datové sady jsou optimalizované pro použití v pracovních postupech machine learning. 

Odborníci přes data často tráví většinu svého času čištění a příprava dat pro pokročilou analýzu. Otevřít datové sady se zkopírují do cloudu Azure a předzpracovaná ušetřit vám čas. V pravidelných intervalech data získaná ze zdrojů, například pomocí připojení FTP k národním úřadem pro oceán a atmosféru (NOAA), analyzovat do strukturovaného formátu a rozšiřují pak podle potřeby pomocí funkcí, jako je například PSČ nebo umístění nejbližší meteorologická stanice.

Datové sady jsou cohosted s výpočetními prostředky na cloud v Azure přístup k vytváření a manipulaci s jednodušší.  

Následují příklady datové sady k dispozici. 

### <a name="weather-data"></a>Data o počasí
 
|Datová sada         | Poznámkové bloky     | Popis                                    |
|----------------|---------------|------------------------------------------------|
|[Zařízení Surface Data NOAA integrované (investičních službách)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Po celém světě hodinové meteorologická data z NOAA se nejlépe prostorových pokrytí v Severní Amerika, Evropa, Austrálie a části Asie. Denně aktualizují. |
|[Globální NOAA prognózy systému (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15denní USA hodinové předpověď počasí data z NOAA. Denně aktualizují. |

### <a name="calendar-data"></a>Kalendářní data

|Datová sada         | Poznámkové bloky     | Popis                                    |
|----------------|---------------|------------------------------------------------|
|[Státních svátků](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Data po celém světě svátek pokrývající 41 zemích nebo oblastech z 1970 do 2099. Zahrnuje země a zda většina lidí zaplatily čas. |

## <a name="access-to-datasets"></a>Přístup k datovým sadám  
S účtem Azure můžete přistupovat k open datových sad pomocí kódu nebo prostřednictvím Azure služby rozhraní. Data jsou umístěna společně s cloudové výpočetní prostředky Azure pro použití ve vašich řešení strojového učení.  

Otevřít datové sady obsahuje poznámkových bloků Azure a Azure Databricks poznámkové bloky, které vám umožní připojit data do služby Azure Machine Learning a Azure Databricks. Datové sady je přístupný také pomocí sady Python SDK. 

Však není nutné otevřít datových sad; přístup k účtu Azure můžete k nim přistupovat z jakéhokoli prostředí Python bez či Spark.

## <a name="request-or-contribute-datasets"></a>Požádat o nebo přispět datové sady

Pokud nemůžete najít data chcete, pošlete nám e-mail se [požádat o datovou sadu](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) nebo [přispívat datovou sadu](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Další postup
* [Ukázka poznámkového bloku](samples.md)
* [Kurz: Regrese modelování se službou data taxislužby města NY](tutorial-opendatasets-automl.md)
* [Python SDK pro Open datové sady](https://aka.ms/open-datasets-api)
