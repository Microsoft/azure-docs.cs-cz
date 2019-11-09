---
title: Úlohy služby Azure Batch
titleSuffix: ML Studio (classic) - Azure
description: Přehled služby Azure Batch Services pro úlohy Machine Learning Studio (Classic). Zpracování fondu Batch umožňuje vytvořit fondy, na kterých můžete odesílat dávkové úlohy.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: ad3ae99b04d5386172c9598f35cbe7d64fddcd36
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837707"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Služba Azure Batch pro úlohy Azure Machine Learning Studio (Classic)

Machine Learning zpracování fondu služby Batch poskytuje pro službu Azure Machine Learning vykonávání dávek od zákazníka i škálování spravované zákazníkem. Klasické dávkové zpracování pro strojové učení se provádí v prostředí s více klienty, které omezuje počet souběžných úloh, které můžete odeslat, a úlohy se zařadí do fronty na základě prvního. Tato nejistota znamená, že nemůžete přesně odhadnout, kdy se úloha spustí.

Zpracování fondu Batch umožňuje vytvořit fondy, na kterých můžete odesílat dávkové úlohy. Řídíte velikost fondu a do kterého fondu je úloha odeslána. Vaše úloha BES běží ve vlastním prostoru zpracování, který poskytuje předvídatelný výpočetní výkon a možnost vytvářet fondy zdrojů, které odpovídají zatížení zpracování, které odešlete.

> [!NOTE]
> K vytvoření fondu musíte mít novou Správce prostředkůovou Machine Learningovou webovou službu. Po vytvoření můžete ve fondu spustit jakoukoli webovou službu BES, která je založená na nové Správce prostředků a v klasickém nasazení.

## <a name="how-to-use-batch-pool-processing"></a>Jak používat zpracování fondu Batch

Konfigurace zpracování fondu Batch není aktuálně k dispozici prostřednictvím Azure Portal. Chcete-li použít zpracování fondu Batch, je nutné:

-   Voláním CSS vytvořte účet fondu Batch a získejte adresu URL služby fondu a autorizační klíč.
-   Vytvoření nové webové služby založené na Správce prostředků a fakturačního plánu

Chcete-li vytvořit účet, zavolejte zákaznickou službu a podporu společnosti Microsoft (CSS) a zadejte ID předplatného. Šablony stylů CSS vám budou spolupracovat s vámi k určení vhodné kapacity pro váš scénář. CSS pak nakonfiguruje váš účet s maximálním počtem fondů, které můžete vytvořit, a maximálním počtem virtuálních počítačů, které můžete do každého fondu umístit. Po nakonfigurování účtu se vám poskytne adresa URL služby fondu a autorizační klíč.

Po vytvoření účtu použijete adresu URL a autorizační klíč služby fondu k provádění operací správy fondů ve fondu služby Batch.

![Architektura služby Batch Pool.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Fondy vytvoříte voláním operace vytvořit fond na adrese URL služby fondu, kterou vám poskytne CSS. Když vytváříte fond, určete počet virtuálních počítačů a adresu URL nástroje Swagger. JSON nové Machine Learning webové služby založené na Správce prostředků. Tato webová služba je k dispozici pro stanovení fakturačního přidružení. Služba Batch Pool používá k přidružení fondu k plánu fakturace službu Swagger. JSON. Ve fondu můžete spustit jakoukoli webovou službu BES, která je založená na všech nových Správce prostředků a Classic.

Můžete použít libovolnou novou Správce prostředků webovou službu, ale mějte na paměti, že fakturace za úlohy se účtují na základě fakturačního plánu přidruženého k této službě. Možná budete chtít vytvořit webovou službu a nový plán fakturace specificky pro spuštění úloh fondu Batch.

Další informace o vytváření webových služeb najdete v tématu [nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md).

Po vytvoření fondu odešlete úlohu BES pomocí adresy URL žádostí Batch pro webovou službu. Můžete se rozhodnout odeslat ho do fondu nebo do klasického dávkového zpracování. Chcete-li odeslat úlohu do zpracování fondu Batch, přidejte následující parametr do textu žádosti o odeslání úlohy:

"AzureBatchPoolId": "ID fondu&lt;&gt;"

Pokud parametr nepřidáte, úloha se spustí v prostředí klasického procesu Batch. Pokud má fond dostupné prostředky, úloha se spustí okamžitě. Pokud fond nemá volné prostředky, úloha se zařadí do fronty, dokud nebude k dispozici prostředek.

Pokud zjistíte, že budete pravidelně dosahovat kapacity fondů, a potřebujete vyšší kapacitu, můžete volat šablonu stylů CSS a spolupracovat se zástupcem, aby se zvýšily vaše kvóty.

Příklad požadavku:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Předpoklady při použití zpracování fondu Batch

Zpracování fondu Batch je vždy fakturovatelná služba a ta vyžaduje, abyste ji přiřadíte k fakturačnímu plánu založenému na Správce prostředků. Účtuje se vám jenom počet výpočetních hodin, na kterých je fond spuštěný. bez ohledu na počet úloh spuštěných během daného časového fondu. Pokud vytvoříte fond, bude se vám účtovat výpočetní hodiny všech virtuálních počítačů ve fondu, dokud se fond neodstraní, a to ani v případě, že se ve fondu nespouštějí žádné úlohy služby Batch. Fakturace virtuálních počítačů se spustí, až se dokončí zřizování a zastaví se, až se odstraní. Můžete použít kterýkoli z plánů, které najdete na [stránce s cenami Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Příklad fakturace:

Pokud vytvoříte fond služby Batch se dvěma virtuálními počítači a po 24 hodinách ho odstraníte, váš plán fakturace bude připsán na stranu 48 výpočetních hodin. bez ohledu na to, kolik úloh bylo během daného období spuštěno.

Pokud vytvoříte fond služby Batch se 4 virtuálními počítači a po 12 hodinách ho odstraníte, fakturačnímu plánu bude taky účtováno 48 výpočetních hodin.

Doporučujeme, abyste provedli dotaz na stav úlohy a určili, kdy se úlohy dokončily. Až budou všechny úlohy spuštěné, zavolejte operaci změnit velikost fondu a nastavte počet virtuálních počítačů ve fondu na hodnotu nula. Pokud jsou prostředky fondu krátké a potřebujete vytvořit nový fond, například k fakturaci v rámci jiného plánu fakturace, můžete místo toho odstranit fond, až budou všechny úlohy dokončeny.


| **Použít zpracování fondu Batch, když**    | **V případě použití klasického dávkového zpracování**  |
|---|---|
|Musíte spustit velký počet úloh.<br>Nebo<br/>Musíte mít jistotu, že se úlohy spustí hned.<br/>Nebo<br/>Potřebujete zaručenou propustnost. Například je třeba v daném časovém rámci spustit několik úloh a chtít škálovat výpočetní prostředky tak, aby vyhovovaly vašim potřebám.    | Spouštíte jenom pár úloh.<br/>And<br/> Nepotřebujete spouštět úlohy hned. |
