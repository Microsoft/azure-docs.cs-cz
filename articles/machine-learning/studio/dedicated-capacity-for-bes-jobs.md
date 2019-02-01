---
title: Služba Azure Batch pro Machine Learning Studio úlohy
titleSuffix: Azure Machine Learning Studio
description: Přehled služby Azure Batch pro úlohy strojového učení. Zpracování fondu služby batch můžete vytvořit fondy, ve kterých můžete odeslat dávkových úloh Hive.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 923ce9b5840ec8d99234b9a3b869005ff59848e3
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494610"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-jobs"></a>Služba Azure Batch pro úlohy Azure Machine Learning Studio

Machine Learning fondu služby Batch zpracování poskytuje spravované zákazníkem škálování pro spuštění služby Azure Machine Learning služby Batch. Klasické dávkové zpracování pro strojové učení probíhá v prostředí s více tenanty, která omezuje počet souběžných úloh, které můžete odeslat a úlohy se zařadí do fronty na základě first-in-first-out. Tento nejistoty znamená, že nelze předvídat přesně kdy se budou spouštět vaše úlohy.

Zpracování fondu služby batch můžete vytvořit fondy, ve kterých můžete odeslat dávkových úloh Hive. Řídíte velikost fondu a do které fondu je úloha odeslána. Úlohy BES se spouští v vlastní zpracování místo poskytování zpracování předvídatelný výkon a schopnost vytvářet fondy zdrojů, které odpovídají zatížení, která odešlete.

## <a name="how-to-use-batch-pool-processing"></a>Použití fondu Batch zpracování

Konfigurace fondu dávkové zpracování není aktuálně k dispozici na webu Azure portal. Použití fondu Batch zpracování, musíte mít:

-   Volání šablony stylů CSS pro vytvoření účtu Batch fondu a získat adresu URL služby fondu a autorizačního klíče
-   Vytvoření webové služby založené na nové Resource Manageru a fakturační plán

K vytvoření účtu, volejte Microsoft zákaznický servis a podporu (CSS) a zadejte ID svého předplatného. Šablony stylů CSS bude spolupracovat s vámi určit příslušné kapacitu pro váš scénář. CSS se pak nakonfiguruje svůj účet s maximální počet fondů, které můžete vytvořit a maximální počet virtuálních počítačů (VM), které můžete umístit v každém fondu. Jakmile se váš účet je nakonfigurovaný, jsou k dispozici vaše adresa URL služby fondu a autorizačního klíče.

Po vytvoření účtu pomocí adresy URL služby fondu a autorizačního klíče k provádění operací správy fondu ve vašem fondu služby Batch.

![Architektura fond služby batch.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Vytvoření fondů zavoláním operace vytvořit fond na adresu URL služby fond, který jste získali šablon stylů CSS. Při vytváření fondu zadejte že počet virtuálních počítačů a adresu URL swagger.json nové Resource Manageru na základě webové služby Machine Learning. Tato webová služba neposkytujeme k navázání fakturační přidružení. Služba fondu služby Batch používá swagger.json k fondu přidružit plán fakturace. Můžete spustit všechny BES webové služby založené na obou nové Resource Manageru a classic zvolíte ve fondu.

Můžete použít libovolné webové službě založené na nové Resource Manageru, ale mějte na paměti, že fakturace pro úlohy, které se účtují proti fakturační plán přidružený k této službě. Můžete chtít vytvořit webovou službu a nové fakturační plán přímo na spouštění úloh fondu služby Batch.

Další informace o vytváření webových služeb najdete v tématu [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

Jakmile vytvoříte fond, odeslání úlohy BES pomocí adresy URL žádosti o služby Batch pro webovou službu. Můžete odeslat ho do fondu nebo classic dávkové zpracování. Odeslání úlohy do fondu dávkové zpracování, přidejte následující parametr do textu žádosti odeslání úlohy:

"AzureBatchPoolId": "&lt;fondu ID&gt;"

Pokud nepřidáte parametr, úloha běží v prostředí procesu klasické služby batch. Pokud fond obsahuje prostředky, úloha se spustí okamžitě. Pokud fond nemá žádné volné prostředky, vaše úloha je zařazena do fronty dokud prostředek není k dispozici.

Pokud zjistíte, že pravidelně dosažení kapacity vašich fondů a potřebujete větší kapacitu, můžete volat šablony stylů CSS a pracovat s zástupce pro zvýšení vaší kvóty.

Příklad žádosti:

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

## <a name="considerations-when-using-batch-pool-processing"></a>Informace týkající se použití fondu Batch zpracování

Dávkové zpracování fondu je vždy na fakturovatelné služba a, který vyžaduje, abyste ho přidružit k fakturačním plánu založené na Resource Manageru. Nebudete dostávat faktury pro množství výpočetního času, které běží fondu bez ohledu na počet úloh během tohoto fondu času spuštění. Pokud vytvoříte fond, vám budeme fakturovat výpočetní hodiny jednotlivých virtuálních počítačů ve fondu až do odstranění fondu i v případě, že nebudou spuštěny žádné úlohy služby batch ve fondu. Fakturace za virtuální počítače se spustí při jejich dokončení zřizování a zastaví, když se odstranily. Můžete použít některý z plánů v [Machine Learning – ceny stránky](https://azure.microsoft.com/pricing/details/machine-learning/).

Příklad fakturace:

Pokud vytvoříte fond služby Batch s 2 virtuálními počítači a odstranit po 24 hodinách fakturačního plánu se odečte 48 hodin výpočetních; bez ohledu na to, kolik úlohy byly spuštěny během tohoto období.

Vytvoření fondu služby Batch pomocí 4 virtuální počítače a odstranit po 12 hodinách fakturačního plánu je také MD 48 – výpočetní hodiny.

Doporučujeme vám, že dotazování stavu úlohy k určení po dokončení úlohy. Při spuštění dokončení všech úloh, volejte operaci změnit velikost fondu nastavit počet virtuálních počítačů ve fondu na nulu. Pokud máte dostatečnou fondu zdrojů a je potřeba vytvořit nový fond, třeba pro fakturaci na jiný fakturační plán, můžete odstranit fondu místo toho při spuštěny všechny úlohy.


| **Použití fondu Batch při zpracování**    | **Použít klasické služby batch při zpracování**  |
|---|---|
|Je potřeba spustit velký počet úloh<br>Nebo<br/>Je potřeba vědět, že vaše úlohy se spustí hned<br/>Nebo<br/>Je třeba garantovanou propustnost. Například budete muset spustit několik úloh v daném časovém rámci a chcete pro horizontální navýšení kapacity výpočetních prostředků podle svých potřeb.    | Používáte pár úloh<br/>A<br/> Není nutné úloh spustit okamžitě |
