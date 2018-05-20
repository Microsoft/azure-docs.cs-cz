---
title: Řešení potíží s Azure Blockchain Workbench
description: Řešení potíží s aplikací Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 419ed6dc76101366e47ae94067f7b671a10c94e2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Řešení potíží s Azure Blockchain Workbench

Skript prostředí PowerShell je k dispozici pro pomoc s vývojáře ladění ani nepodporuje. Skript generuje souhrn a shromažďuje podrobné protokoly pro řešení potíží. Shromážděné protokoly patří:

* Blockchain sítě, jako je Ethereum
* Mikroslužeb Blockchain Workbench
* Application Insights
* Azure monitorování (OMS)

Informace můžete určit další kroky a určit hlavní příčinu problémů. 

## <a name="troubleshooting-script"></a>Řešení potíží s skriptu

Řešení potíží s skript prostředí PowerShell je dostupná na Githubu. [Stáhněte soubor .zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Spusťte skript
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Spustit `collectBlockchainWorkbenchTroubleshooting.ps1` skript pro shromažďování protokolů a vytvořte soubor ZIP obsahující složky informace o odstraňování potíží. Příklad:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skript přijímá následující parametry:

| Parametr  | Popis | Požaduje se |
|---------|---------|----|
| ID předplatného | ID odběru pro vytvoření nebo vyhledat všechny prostředky. | Ano |
| ResourceGroupName | Název skupiny prostředků Azure, kde byla nasazena Blockchain Workbench. | Ano |
| Výstupnísložka | Cesta k vytvoření výstupu. Soubor ZIP. Pokud není zadaný, použije se výchozí hodnota k aktuálnímu adresáři. | Ne |
| LookbackHours | Počet hodin pro použití při stahování telemetrie. Výchozí hodnota je 24 hodin. Maximální hodnota je 90 hodin | Ne |
| OmsSubscriptionId | ID předplatného, které se nasadí OMS. Tento parametr předejte pouze OMS blockchain sítě je nasadit mimo skupinu prostředků Blockchain Workbench.| Ne |
| OmsResourceGroup |Skupinu prostředků, které se nasadí OMS. Tento parametr předejte pouze OMS blockchain sítě je nasadit mimo skupinu prostředků Blockchain Workbench.| Ne |
| OmsWorkspaceName | Název pracovního prostoru OMS. Tento parametr předejte pouze pokud je nasazená OMS sítě blockchain mimo skupinu prostředků Blockchain Workbench | Ne |

## <a name="what-is-collected"></a>Co je shromažďováno?

Výstupní soubor ZIP obsahuje následující strukturu složek:

| Složka nebo soubor | Popis  |
|---------|---------|
| \Summary.txt | Souhrn systému |
| \Metrics\blockchain | Metriky o blockchain |
| \Metrics\Workbench | Metriky o nástroje workbench |
| \Details\Blockchain | Podrobné protokoly blockchain |
| \Details\Workbench | Podrobné protokoly nástroje workbench |

Soubor souhrnu vám dává snímek celkového stavu aplikace a stavu aplikace. Souhrn poskytuje doporučené akce, klade důraz nejvyšší chyby a metadata o spuštění služby.

**Metriky** složka obsahuje metriky z různých součástí systému v čase. Například výstupní soubor `\Details\Workbench\apiMetrics.txt` obsahuje souhrn kódy různých odpovědi a časy odezvy po celou dobu shromažďování. **Podrobnosti** složka obsahuje podrobné protokoly pro konkrétní řešení potíží s Workbench nebo na základní síti blockchain. Například `\Details\Workbench\Exceptions.csv` obsahuje seznam nejnovější výjimky, k nimž došlo v systému, který je užitečný pro řešení potíží s chybami spojenými s inteligentní kontrakty nebo interakce s blockchain. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench architektura](blockchain-workbench-architecture.md)