---
title: Vyhrazené kapacity pro Machine Learning dávky spuštění služby úlohy | Microsoft Docs
description: Přehled služby Azure Batch pro Machine Learning úlohy.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 5a3d2c976ca4deef093f2640e56634acf911f4c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>Služba Azure Batch pro Machine Learning úlohy

Zpracování Machine Learning fondu Batch poskytuje spravované zákazníkem škálování pro službu Azure Machine Learning dávky spuštění. Classic dávkové zpracování pro strojové učení probíhá v prostředí více klientů, které omezuje počet souběžných úloh, které vám mohou odesílat a úlohy se zařadí do fronty na základě objektů first in first out. Tato nejistoty znamená, že nemůžete vědět, když se spustí úlohu.

Zpracování fondu batch můžete vytvářet fondy, na kterých můžete odeslat úlohy batch. Velikost fondu a do které fondu je úloha odeslána. Vaše BES úloha spouští ve vlastním prostoru zpracování poskytnutí zpracování předvídatelný výkon a schopnost vytvářet fondy zdrojů, které odpovídají zpracování zatížení, která odešlete.

## <a name="how-to-use-batch-pool-processing"></a>Jak používat zpracování fondu služby Batch

Konfigurace zpracování fondu Batch není aktuálně k dispozici prostřednictvím portálu Azure. Chcete-li použít zpracování fondu Batch, postupujte takto:

-   Volání šablon stylů CSS k vytvoření účtu fondu Batch a získat adresu URL služby fondu a autorizační klíč
-   Vytvoření nového správce prostředků na základě webové služby a fakturační plán

K vytvoření účtu, volejte Microsoft zákaznický servis a podporu (CSS) a zadejte ID předplatného. Šablon stylů CSS bude fungovat s určit příslušné kapacitu pro váš scénář. Šablon stylů CSS, nakonfiguruje váš účet s maximální počet fondy, které můžete vytvořit a maximální počet virtuálních počítačů (VM), které můžete umístit v každém fondu. Jakmile je váš účet nakonfigurován, jsou k dispozici adresu URL služby fondu a autorizační klíč.

Po vytvoření účtu použijete adresu URL služby fondu a autorizační klíč mohli provádět operace správy fondu na vaše fondu služby Batch.

![Architektura fondu služby batch.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Vytvořit fondy voláním operace vytvořit fond na adresu URL služby fondu, které jste získali šablon stylů CSS. Když vytvoříte fond, zadejte že počet virtuálních počítačů a adresu URL swagger.json nového správce zdrojů na základě webové službě Machine Learning. Této webové služby je určen k vytvoření přidružení fakturace. Služba fondu Batch používá swagger.json k fondu přidružit plán fakturace. Můžete spustit všechny BES webové služby, na základě i nové Resource Manager a classic, zvolíte ve fondu.

Můžete použít žádné nové správce prostředků na základě webové služby, ale mějte na paměti, že fakturace pro úlohy připsány fakturační plán spojené s touto službou. Můžete vytvořit nový plán fakturace speciálně pro spuštění úlohy fondu služby Batch a webové služby.

Další informace o vytváření webových služeb najdete v tématu [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

Jakmile vytvoříte fond, odešlete úlohu BES pomocí adresy URL žádosti Batch pro webovou službu. Můžete ho odeslat do fondu nebo classic dávkové zpracování. Se odeslat úlohu pro zpracování fondu Batch, přidáte do textu úlohy odeslání požadavku na následující parametr:

"AzureBatchPoolId": "&lt;fond ID&gt;"

Pokud je nemůžete přidat parametr, úloha běží v prostředí procesu classic batch. Pokud fondu dostupné prostředky, úloha spuštění okamžitě. Pokud fond nemá volné prostředky, vaše úloha je zařazena do fronty dokud prostředek k dispozici.

Pokud zjistíte, že nedostanete pravidelně kapacitu fondech a je třeba zvýšené kapacity, můžete volat šablon stylů CSS a pracovat s zástupce ke zvýšení vaší kvóty.

Příklad žádost:

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

## <a name="considerations-when-using-batch-pool-processing"></a>Informace týkající se použití zpracování fondu služby Batch

Zpracování fondu batch je vždy na fakturovatelný služba a který vyžaduje, abyste ji přidružit plán fakturace na základě Resource Manager. Fakturuje se pouze pro počet – výpočetní hodiny, které běží fondu; bez ohledu na počet úloh spustit během tohoto fondu čas. Pokud vytvoříte fond, se vám účtuje výpočetní hodiny každého virtuálního počítače ve fondu až do odstranění fondu i v případě, že nebudou spuštěny žádné úlohy batch ve fondu. Fakturace pro virtuální počítače, spustí, když jejich dokončení zřizování a zastaví, když byla odstraněna. Můžete použít některou z plánů v nalezen [Machine Learning – ceny stránky](https://azure.microsoft.com/pricing/details/machine-learning/).

Příklad fakturace:

Pokud chcete vytvořit fondu služby Batch s 2 virtuální počítače a odstranit po 24 hodinách cenového plánu je odečte 48 hodin výpočetní; bez ohledu na to, kolik úlohy byly spuštěné během této doby.

Pokud jste vytvoření fondu služby Batch s 4 virtuální počítače a odstranit po 12 hodinách, cenového plánu je také MD 48 výpočetní hodiny.

Doporučujeme, abyste cyklické dotazování stav úlohy k určení, kdy dokončení úloh. Po dokončení všech úloh spuštěna, volání operaci změnit velikost fondu nastavit počet virtuálních počítačů ve fondu na nulu. Pokud jste krátké u fondu prostředků a je nutné vytvořit nový fond, například k vyúčtování proti jiný fakturační plán, můžete odstranit fondu místo při spuštění dokončení všech úloh.


| **Použít při zpracování fondu služby Batch**    | **Použít classic při zpracování dávky**  |
|---|---|
|Budete muset spustit velký počet úloh<br>Nebo<br/>Je třeba vědět, že vaše úlohy se spustí okamžitě<br/>Nebo<br/>Je nutné zaručenou propustnost. Například musíte spustit určitý počet úloh v zadaném časovém období a chcete škálovat svoje výpočetní prostředky podle svých potřeb.    | Používáte několika úloh<br/>A<br/> Nepotřebujete úlohy, které mají spustit okamžitě |
