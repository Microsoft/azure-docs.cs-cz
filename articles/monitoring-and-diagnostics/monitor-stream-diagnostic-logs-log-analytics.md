---
title: Stream Azure diagnostických protokolů k analýze protokolů
description: Zjistěte, jak k vysílání datového proudu do pracovního prostoru analýzy protokolů Azure diagnostické protokoly.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: d8966edb6061ed07f5aecb9682fca081ed589040
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083970"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Stream Azure diagnostických protokolů k analýze protokolů

**[Azure diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md)**  Streamovat skoro v reálném čase k analýze protokolů Azure pomocí portálu, rutiny prostředí PowerShell nebo Azure CLI 2.0.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Co můžete dělat s diagnostikou přihlásí analýzy protokolů

Azure Log Analytics je flexibilní protokol hledání a analýzy nástroj, který umožňuje získat přehled o data nezpracovaných log vygenerovaná ze prostředků Azure. Některé možnosti patří:

* **Hledání protokolů** -zápisu pokročilé dotazy přes data protokolu, correlate protokoly z různých zdrojů a to i v generování grafů, které lze připojit do řídicího panelu Azure.
* **Výstrahy** -rozpoznat, kdy se jeden nebo více událostí konkrétní dotazu neodpovídají a stát oznámení e-mailu nebo webhooku voláním.
* **Řešení** -pomocí předdefinovaných zobrazení a řídicí panely, které získáte okamžitý přehled o data protokolu.
* **Pokročilé analýzy** – použít strojové učení a vzor odpovídající algoritmy k identifikaci možné problémy zjištěné při protokolů.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Povolení diagnostických protokolů k analýze protokolů streamování

Můžete povolit vysílání datového proudu diagnostické protokoly prostřednictvím kódu programu, prostřednictvím portálu nebo pomocí [rozhraní REST API Azure monitorování](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings). V obou případech vytvoříte nastavení diagnostiky ve kterém zadáte, pracovní prostor analýzy protokolů a protokolu kategorií a metriky, které se mají posílat do tohoto pracovního prostoru. Diagnostika **kategorie protokolu** je typ protokolu, který může poskytnout prostředku.

Pracovní prostor analýzy protokolů nemusí být ve stejném předplatném jako prostředek emitování protokoly tak dlouho, dokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC do oba odběry.

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

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

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Povolit vysílání datového proudu prostřednictvím [rutin prostředí Azure PowerShell](insights-powershell-samples.md), můžete použít `Set-AzureRmDiagnosticSetting` rutiny s těmito parametry:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Všimněte si, že vlastnost ID pracovního prostoru trvá úplné Azure prostředků ID pracovního prostoru není prostoru ID nebo klíč se na portálu analýzy protokolů.

### <a name="via-azure-cli-20"></a>Via Azure CLI 2.0

Povolit vysílání datového proudu prostřednictvím [Azure CLI 2.0](insights-cli-samples.md), můžete použít [vytvořit az monitorování diagnostiky – nastavení](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) příkaz.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Přidáním dalších kategorií do protokolů diagnostiky tak, že přidáte do pole JSON, který je předán jako slovník `--logs` parametr.

`--resource-group` Argument je jenom v případě požadované `--workspace` není ID objektu.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Jak dotaz na data v Log Analytics?

V okně hledání protokolů v portálu nebo Advanced Analytics prostředí jako součást analýzy protokolů můžete dotazovat diagnostické protokoly jako součást řešení pro správu protokolu v části AzureDiagnostics tabulky. Existují také [několik řešení pro prostředky Azure](../log-analytics/log-analytics-add-solutions.md) instalací získat okamžitý přehled o data protokolu při odesílání do analýzy protokolů.

## <a name="next-steps"></a>Další postup

* [Další informace o diagnostických protokolů Azure.](monitoring-overview-of-diagnostic-logs.md)
