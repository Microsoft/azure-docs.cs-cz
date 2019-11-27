---
title: Řešení potíží s Azure blockchain Workbench
description: Řešení potíží s aplikací Azure blockchain Workbench Preview
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324307"
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

Spusťte skript `collectBlockchainWorkbenchTroubleshooting.ps1` pro shromáždění protokolů a vytvoření souboru ZIP obsahujícího složku s informacemi o řešení potíží. Příklad:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skript přijímá následující parametry:

| Parametr  | Popis | Požaduje se |
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

Složka **metriky** obsahuje metriky různých systémových komponent v průběhu času. Například výstupní soubor `\Details\Workbench\apiMetrics.txt` obsahuje souhrn různých kódů odpovědí a dobu odezvy v průběhu období shromažďování. Složka **podrobností** obsahuje podrobné protokoly pro řešení konkrétních problémů s nástrojem Workbench nebo základní blockchainou sítí. Například `\Details\Workbench\Exceptions.csv` obsahuje seznam nejaktuálnějších výjimek, ke kterým došlo v systému, což je užitečné při řešení chyb pomocí inteligentních kontraktů nebo interakcí s blockchain. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průvodce odstraňováním potíží s Application Insights pro Azure blockchain Workbench](https://aka.ms/workbenchtroubleshooting)
