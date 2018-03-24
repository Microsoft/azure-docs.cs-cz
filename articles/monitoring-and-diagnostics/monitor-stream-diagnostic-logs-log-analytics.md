---
title: Stream Azure diagnostických protokolů k analýze protokolů | Microsoft Docs
description: Zjistěte, jak k vysílání datového proudu do pracovního prostoru analýzy protokolů Azure diagnostické protokoly.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem
ms.openlocfilehash: 517ce3547f471dd1b40c79b2f087b02ad7f51b85
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Stream Azure diagnostických protokolů k analýze protokolů
**[Azure diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md)**  Streamovat skoro v reálném čase k analýze protokolů Azure pomocí portálu, rutiny prostředí PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Co můžete dělat s diagnostikou přihlásí analýzy protokolů

Azure Log Analytics je flexibilní protokol hledání a analýzy nástroj, který umožňuje získat přehled o data nezpracovaných log vygenerovaná ze prostředků Azure. Některé možnosti patří:

* **Hledání protokolů** -zápisu pokročilé dotazy přes data protokolu, correlate protokoly z různých zdrojů a to i v generování grafů, které lze připojit do řídicího panelu Azure.
* **Výstrahy** -rozpoznat, kdy se jeden nebo více událostí konkrétní dotazu neodpovídají a stát oznámení e-mailu nebo webhooku voláním.
* **Řešení** -pomocí předdefinovaných zobrazení a řídicí panely, které získáte okamžitý přehled o data protokolu.
* **Pokročilé analýzy** – použít strojové učení a vzor odpovídající algoritmy k identifikaci možné problémy zjištěné při protokolů.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Povolení diagnostických protokolů k analýze protokolů streamování
Můžete povolit vysílání datového proudu diagnostické protokoly prostřednictvím kódu programu, prostřednictvím portálu nebo pomocí [rozhraní REST API Azure monitorování](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). V obou případech vytvoříte nastavení diagnostiky ve kterém zadáte, pracovní prostor analýzy protokolů a protokolu kategorií a metriky, které se mají posílat do tohoto pracovního prostoru. Diagnostika **kategorie protokolu** je typ protokolu, který může poskytnout prostředku.

Pracovní prostor analýzy protokolů nemusí být ve stejném předplatném jako prostředek emitování protokoly tak dlouho, dokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC do oba odběry.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diagnostické protokoly datového proudu pomocí portálu
1. Na portálu, přejděte do monitorování Azure a klikněte na **nastavení diagnostiky**

    ![Monitorování části monitorování Azure](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. Volitelně můžete seznam filtrovat podle skupiny prostředků nebo typ prostředku, a potom klikněte na prostředek, pro kterou chcete provést nastavení diagnostiky.

3. Pokud neexistuje žádné nastavení na prostředku jste vybrali, zobrazí se výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiky."

   ![Přidat nastavení diagnostiky - žádná existující nastavení](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   Pokud máte stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou již nakonfigurována na tomto prostředku. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko pro **odeslat k analýze protokolů**, pak vyberte pracovní prostor analýzy protokolů.
   
   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly jsou datového proudu do tohoto pracovního prostoru, také se vygeneruje nová data událostí. Všimněte si, že může být až 15 minut až když je události vygenerované až se zobrazí v analýzy protokolů.

### <a name="via-powershell-cmdlets"></a>Via PowerShell Cmdlets
Povolit vysílání datového proudu prostřednictvím [rutin prostředí Azure PowerShell](insights-powershell-samples.md), můžete použít `Set-AzureRmDiagnosticSetting` rutiny s těmito parametry:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Všimněte si, že vlastnost ID pracovního prostoru trvá úplné Azure prostředků ID pracovního prostoru není prostoru ID nebo klíč se na portálu analýzy protokolů.

### <a name="via-azure-cli"></a>Via Azure CLI
Povolit vysílání datového proudu prostřednictvím [rozhraní příkazového řádku Azure](insights-cli-samples.md), můžete použít `insights diagnostic set` příkaz takto:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

Všimněte si, že vlastnost ID pracovního prostoru trvá úplné Azure prostředků ID pracovního prostoru není prostoru ID nebo klíč se na portálu analýzy protokolů.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Jak dotaz na data v Log Analytics?

V okně hledání protokolů v portálu nebo Advanced Analytics prostředí jako součást analýzy protokolů můžete dotazovat diagnostické protokoly jako součást řešení pro správu protokolu v části AzureDiagnostics tabulky. Existují také [několik řešení pro prostředky Azure](../log-analytics/log-analytics-add-solutions.md) instalací získat okamžitý přehled o data protokolu při odesílání do analýzy protokolů.


## <a name="next-steps"></a>Další postup
* [Další informace o diagnostických protokolů Azure.](monitoring-overview-of-diagnostic-logs.md)
