---
title: Jednoduché protokoly zkušenosti ve službě Azure Monitor (Preview) | Dokumenty společnosti Microsoft
description: Prostředí jednoduché protokoly umožňuje vytvářet základní dotazy v Azure Monitor bez přímé interakce s KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660253"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Jednoduché protokoly prostředí ve službě Azure Monitor (Preview)
Azure Monitor poskytuje [bohaté prostředí](get-started-portal.md) pro vytváření [dotazů protokolu](log-query-overview.md) pomocí jazyka KQL. Nemusí vyžadovat plný výkon KQL i když a raději zjednodušené prostředí pro základní požadavky na dotazy. Jednoduché protokoly umožňuje vytvářet základní dotazy bez přímé interakce s KQL. Můžete také použít jednoduché protokoly jako nástroj pro učení pro KQL, jak budete vyžadovat složitější dotazy.

> [!NOTE]
> Jednoduché protokoly je aktuálně implementována jako test pouze pro Cosmos DB a trezory klíčů. Prosím, podělte se o své zkušenosti se společností Microsoft prostřednictvím [User Voice,](https://feedback.azure.com/forums/913690-azure-monitor) aby nám pomohli určit, zda budeme rozbalit a vydat tuto funkci.


## <a name="scope"></a>Rozsah
Prostředí jednoduché protokoly načte data z *AzureDiagnostics*, *AzureMetrics*a *AzureActivity* tabulky pro vybraný prostředek. 

## <a name="using-simple-logs"></a>Použití jednoduchých protokolů
Přejděte do libovolné houskového db nebo trezoru klíčů ve svém předplatném Azure s [diagnostickým nastavením nakonfigurovaným pro shromažďování protokolů v pracovním prostoru Log Analytics](../platform/resource-logs-collect-storage.md). Klepnutím na **položku Protokoly** v nabídce **Monitorování** otevřete možnost Jednoduché protokoly.

![Nabídka](media/simple-logs/menu.png)

Vyberte **pole** a **operátor** a zadejte **hodnotu** pro porovnání. Chcete-li přidat další kritéria, klepněte **+** na tlačítko **A/Nebo.**

![Kritéria](media/simple-logs/criteria.png)

Chcete-li zobrazit výsledky dotazu, klepněte na tlačítko **Spustit.**

## <a name="view-and-edit-kql"></a>Zobrazení a úprava KQL
Vyberte **editor dotazů,** chcete-li otevřít kql generovaný dotazem Jednoduché protokoly v úplném prostředí Log Analytics. 

![Editor dotazů](media/simple-logs/query-editor.png)

Můžete přímo upravit KQL a použít další funkce v Log Analytics, jako jsou filtry k dalšímu upřesnění výsledků.

![Upravit KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Další kroky

- Dokončete kurz o [používání Log Analytics na webu Azure Portal](get-started-portal.md).
- Dokončete kurz [psaní dotazů](get-started-portal.md)protokolu .
