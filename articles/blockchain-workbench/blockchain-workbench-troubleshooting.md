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
ms.openlocfilehash: df7fd71bee287a063e8d586de38522f3b71e14d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
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
| Výstupnísložka | Cesta k vytvoření výstupu. Soubor ZIP. Pokud není zadaný, použije se výchozí hodnota k aktuálnímu adresáři. | Ne
| OmsSubscriptionId | Id předplatného, které se nasadí OMS. Tento parametr předejte pouze OMS blockchain sítě je nasadit mimo skupinu prostředků Blockchain Workbench.| Ne |
| OmsResourceGroup |Skupinu prostředků, které se nasadí OMS. Tento parametr předejte pouze OMS blockchain sítě je nasadit mimo skupinu prostředků Blockchain Workbench.| Ne |
| OmsWorkspaceName | Název pracovního prostoru OMS. Tento parametr předejte pouze pokud je nasazená OMS sítě blockchain mimo skupinu prostředků Blockchain Workbench | Ne |

## <a name="what-is-collected"></a>Co je shromažďováno?

Výstupní soubor ZIP obsahuje následující strukturu složek:

| Složka \ souboru | Popis  |
|---------|---------|
| \Summary.txt | Souhrn systému |
| \metrics\blockchain | Metriky o blockchain |
| \metrics\workbench | Metriky o nástroje workbench |
| \details\blockchain | Podrobné protokoly blockchain |
| \details\workbench | Podrobné protokoly nástroje workbench |

Soubor souhrnu vám dává snímek celkového stavu aplikace a stavu aplikace. Souhrn poskytuje doporučené akce, klade důraz nejvyšší chyby a metadata o spuštění služby.

## <a name="next-steps"></a>Další postup

* [Azure Blockchain Workbench architektura](blockchain-workbench-architecture.md)