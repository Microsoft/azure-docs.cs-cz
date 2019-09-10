---
title: Řešení potíží s Azure blockchain Workbench Preview
description: Řešení potíží s aplikací Azure blockchain Workbench Preview
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8fec065b629f2f2b93e78a63521ea0ce4669dd4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844051"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Řešení potíží s Azure blockchain Workbench Preview

K dispozici je skript PowerShellu, který vám pomůže s vývojářským laděním nebo podporou. Skript vygeneruje Shrnutí a shromáždí podrobné protokoly pro řešení potíží. Shromážděné protokoly zahrnují:

* Blockchain síť, jako je například Ethereem
* Mikroslužby blockchain Workbench
* Application Insights
* Monitorování Azure (protokoly Azure Monitor)

Tyto informace můžete použít k určení dalších kroků a určení hlavní příčiny problémů.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Skript pro řešení potíží

Skript pro řešení potíží s PowerShellem je k dispozici na GitHubu. [Stáhněte soubor .zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Spuštění skriptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

`collectBlockchainWorkbenchTroubleshooting.ps1` Spusťte skript pro shromáždění protokolů a vytvořte soubor ZIP obsahující složku s informacemi o řešení potíží. Příklad:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skript přijímá následující parametry:

| Parametr  | Popis | Požadováno |
|---------|---------|----|
| SubscriptionID | SubscriptionID pro vytvoření nebo vyhledání všech prostředků. | Ano |
| ResourceGroupName | Název skupiny prostředků Azure, do které se nasadila aplikace blockchain Workbench | Ano |
| OutputDirectory | Cesta pro vytvoření výstupu Soubor ZIP. Pokud není zadaný, použije se ve výchozím nastavení aktuální adresář. | Ne |
| LookbackHours | Počet hodin, které se mají použít při přijímání telemetrie Výchozí hodnota je 24 hodin. Maximální hodnota je 90 hodin. | Ne |
| OmsSubscriptionId | ID předplatného, kde se nasadí protokoly Azure Monitor Tento parametr předejte jenom v případě, že protokoly Azure Monitor pro síť blockchain se nasazují mimo skupinu prostředků blockchain Workbench.| Ne |
| OmsResourceGroup |Skupina prostředků, ve které se nasadí protokoly Azure Monitor Tento parametr předejte jenom v případě, že protokoly Azure Monitor pro síť blockchain se nasazují mimo skupinu prostředků blockchain Workbench.| Ne |
| OmsWorkspaceName | Název Log Analytics pracovního prostoru. Tento parametr předejte jenom v případě, že protokoly Azure Monitor pro síť blockchain se nasazují mimo skupinu prostředků blockchain Workbench. | Ne |

## <a name="what-is-collected"></a>Co se shromažďuje?

Výstupní soubor ZIP obsahuje následující strukturu složek:

| Složka nebo soubor | Popis  |
|---------|---------|
| \Summary.txt | Souhrn systému |
| \Metrics\blockchain | Metriky o blockchain |
| \Metrics\Workbench | Metriky o aplikaci Workbench |
| \Details\Blockchain | Podrobné protokoly o blockchain |
| \Details\Workbench | Podrobné protokoly o aplikaci Workbench |

Soubor Shrnutí poskytuje snímek celkového stavu aplikace a stavu aplikace. Souhrn obsahuje doporučené akce, nejdůležitější hlavní chyby a metadata o spuštěných službách.

Složka **metriky** obsahuje metriky různých systémových komponent v průběhu času. Například výstupní soubor `\Details\Workbench\apiMetrics.txt` obsahuje souhrn různých kódů odpovědí a dobu odezvy v průběhu období shromažďování. Složka **podrobností** obsahuje podrobné protokoly pro řešení konkrétních problémů s nástrojem Workbench nebo základní blockchainou sítí. `\Details\Workbench\Exceptions.csv` Obsahuje například seznam nejaktuálnějších výjimek, ke kterým došlo v systému, což je užitečné při řešení chyb pomocí inteligentních kontraktů nebo interakcí s blockchain. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Průvodce odstraňováním potíží s Application Insights pro Azure blockchain Workbench](https://aka.ms/workbenchtroubleshooting)
