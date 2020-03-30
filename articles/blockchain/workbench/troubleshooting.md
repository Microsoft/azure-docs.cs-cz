---
title: Řešení potíží s pracovní mitinou Azure Blockchain
description: Jak řešit potíže s aplikací Azure Blockchain Workbench Preview.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324307"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Řešení potíží s Azure Blockchain Workbench Preview

Skript prostředí PowerShell je k dispozici, který pomáhá s laděním nebo podporou vývojářů. Skript generuje souhrn a shromažďuje podrobné protokoly pro řešení potíží. Shromážděné protokoly zahrnují:

* Blockchainová síť, jako je Ethereum
* Mikroslužby Blockchain Workbench
* Application Insights
* Monitorování Azure (protokoly Azure Monitoru)

Tyto informace můžete použít k určení dalších kroků a určení hlavní příčiny problémů.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Skript pro řešení potíží

Skript pro řešení potíží s Prostředím PowerShell je k dispozici na GitHubu. [Stáhněte soubor .zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Spuštění skriptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Spusťte `collectBlockchainWorkbenchTroubleshooting.ps1` skript pro shromažďování protokolů a vytvoření souboru ZIP obsahujícího složku s informacemi o řešení potíží. Například:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skript přijímá následující parametry:

| Parametr  | Popis | Požaduje se |
|---------|---------|----|
| ID předplatného | SubscriptionID k vytvoření nebo vyhledání všech prostředků. | Ano |
| ResourceGroupName | Název skupiny prostředků Azure, kde byl nasazen Blockchain Workbench. | Ano |
| Výstupní adresář | Cesta k vytvoření výstupu . ZIP. Pokud není zadán, výchozí aktuální adresář. | Ne |
| Doba zpětného pohledu | Počet hodin, které mají být při tahání telemetrie. Výchozí hodnota je 24 hodin. Maximální hodnota je 90 hodin | Ne |
| OmsSubscriptionId | ID předplatného, kde se nasadí protokoly Azure Monitor. Tento parametr předavte jenom v případě, že se protokoly Azure Monitoru pro blockchainovou síť nasazují mimo skupinu prostředků Blockchain Workbench.| Ne |
| OmsResourceGroup |Skupina prostředků, kde se nasazují protokoly Azure Monitor. Tento parametr předavte jenom v případě, že se protokoly Azure Monitoru pro blockchainovou síť nasazují mimo skupinu prostředků Blockchain Workbench.| Ne |
| OmsWorkspaceName | Název pracovního prostoru Analýzy protokolů. Tento parametr předavte jenom v případě, že se protokoly Azure Monitoru pro blockchainovou síť nasazují mimo skupinu prostředků Blockchain Workbench. | Ne |

## <a name="what-is-collected"></a>Co se shromažďuje?

Výstupní soubor ZIP obsahuje následující strukturu složek:

| Složka nebo soubor | Popis  |
|---------|---------|
| \Summary.txt | Shrnutí systému |
| \Metriky\blockchain | Metriky o blockchainu |
| \Metriky\Pracovní plocha | Metriky pracovní plochy |
| \Podrobnosti\Blockchain | Podrobné protokoly o blockchainu |
| \Podrobnosti\Pracovní plocha | Podrobné protokoly o pracovní stolce |

Souhrnný soubor poskytuje snímek celkového stavu aplikace a stavu aplikace. Souhrn obsahuje doporučené akce, upozorňuje na hlavní chyby a metadata o spuštěných službách.

Složka **Metriky** obsahuje metriky různých systémových součástí v průběhu času. Například výstupní soubor `\Details\Workbench\apiMetrics.txt` obsahuje souhrn různých kódů odpovědí a doby odezvy v celém období sběru. Složka **Podrobnosti** obsahuje podrobné protokoly pro řešení konkrétních problémů s Workbench nebo základní blockchainovou sítí. Obsahuje například seznam nejnovějších výjimek, `\Details\Workbench\Exceptions.csv` ke kterým došlo v systému, což je užitečné pro řešení chyb pomocí inteligentních smluv nebo interakcí s blockchainem. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průvodce odstraňováním potíží s přehledy aplikací Azure Blockchain Workbench](https://aka.ms/workbenchtroubleshooting)
