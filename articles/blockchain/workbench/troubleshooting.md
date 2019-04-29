---
title: Řešení potíží s Azure Blockchain Workbench
description: Řešení potíží s aplikaci Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b55c84773d99c325689fbc5182e75c7cb108d00a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896316"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Řešení potíží s Azure Blockchain Workbench

Skript prostředí PowerShell je k dispozici pro vám pomůže s developer ladění nebo podpory. Skript generuje souhrn a shromažďuje podrobné protokoly pro řešení potíží. Shromážděné protokoly patří:

* Blockchain sítě, jako je například ethereum během
* Mikroslužby Blockchain Workbench
* Application Insights
* Azure Monitoring (monitorování Azure protokoly)

Informace můžete určit další kroky a určení původní příčiny problémů.

## <a name="troubleshooting-script"></a>Řešení potíží s skriptu

Řešení potíží s skript prostředí PowerShell je k dispozici na Githubu. [Stáhněte soubor .zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Spuštění skriptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Spustit `collectBlockchainWorkbenchTroubleshooting.ps1` skript pro shromažďování protokolů a vytvořit souboru ZIP, který obsahuje složku s informace o odstraňování potíží. Příklad:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skript je možné zadat následující parametry:

| Parametr  | Popis | Požaduje se |
|---------|---------|----|
| ID předplatného | ID odběru pro vytvoření nebo vyhledejte všechny prostředky. | Ano |
| ResourceGroupName | Název skupiny prostředků Azure, ve kterém byla nasazena Blockchain Workbench. | Ano |
| OutputDirectory | Cesta k vytvoření výstupu. Soubor ZIP. Pokud není zadán, výchozí hodnota je do aktuálního adresáře. | Ne |
| LookbackHours | Počet hodin pro použití při stahování telemetrická data. Výchozí hodnota je 24 hodin. Maximální hodnota je 90 hodin | Ne |
| OmsSubscriptionId | ID předplatného, ve kterém protokoly Azure monitoru se nasadí. Tento parametr předejte pouze protokoly Azure monitoru pro síť blockchain nasazuje mimo Blockchain Workbench skupinu prostředků.| Ne |
| OmsResourceGroup |Nasazení skupiny prostředků, ve kterém protokoly Azure monitoru. Tento parametr předejte pouze protokoly Azure monitoru pro síť blockchain nasazuje mimo Blockchain Workbench skupinu prostředků.| Ne |
| OmsWorkspaceName | Název pracovního prostoru Log Analytics. Tento parametr předejte pouze pokud se protokoly Azure monitoru pro síť blockchain nasazuje mimo skupinu prostředků Blockchain Workbench | Ne |

## <a name="what-is-collected"></a>Co je shromažďováno?

Výstupní soubor ZIP obsahuje následující strukturu složek:

| Soubor nebo složku | Popis  |
|---------|---------|
| \Summary.txt | Přehled systému |
| \Metrics\blockchain | Metriky o blockchainu |
| \Metrics\Workbench | Metriky o aplikaci workbench |
| \Details\Blockchain | Podrobné protokoly blockchainu |
| \Details\Workbench | Podrobné protokoly aplikace workbench |

Soubor souhrnu poskytuje snímek celkový stav aplikace a stav aplikace. Souhrn poskytuje doporučené akce, zvýrazní se hlavní chyby a metadata o spuštění služby.

**Metriky** složka obsahuje metriky z různých součástí systému v čase. Například výstupní soubor `\Details\Workbench\apiMetrics.txt` obsahuje souhrn kódy různé odezvy a doby odezvy v průběhu období kolekce. **Podrobnosti** složka obsahuje podrobné protokoly pro konkrétní řešení potíží s Workbench nebo základní sítě blockchain. Například `\Details\Workbench\Exceptions.csv` obsahuje seznam nejnovější výjimky, ke kterým došlo v systému, což je užitečné při řešení potíží s interakcí s blockchainem nebo chyby s chytrých kontraktů. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Průvodce odstraňováním potíží Azure Blockchain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
