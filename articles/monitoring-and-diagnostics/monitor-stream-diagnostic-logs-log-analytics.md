---
title: Stream diagnostické protokoly Azure do Log Analytics
description: Naučíte se Streamovat diagnostické protokoly Azure do pracovního prostoru Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 134c33ac28c6a094bdc50deb7206db95bf4436fc
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574526"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Stream diagnostické protokoly Azure do Log Analytics

**[Diagnostické protokoly Azure](monitoring-overview-of-diagnostic-logs.md)**  můžete streamování v reálném čase do služby Azure Log Analytics pomocí portálu, rutin prostředí PowerShell nebo příkazového řádku Azure CLI 2.0.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Co můžete dělat s diagnostikou protokolů v Log Analytics

Azure Log Analytics je flexibilní protokolu vyhledávání a analýzy nástroj, který umožňuje získat přehled o Nezpracovaný protokol data vygenerovaná prostředky Azure. Některé funkce patří:

* **Prohledávání protokolů** – zápis upřesňujících dotazů nad vašimi daty protokolů, korelovat protokoly z různých zdrojů a dokonce i generovat grafy, které je možné připnout na řídicí panel Azure.
* **Výstrahy** -rozpoznat, kdy se jeden nebo více událostí odpovídají konkrétním dotazu a informováni pomocí e-mailu nebo webhooku volání.
* **Řešení** – použijte předem sestavené zobrazení a řídicí panely, které vám poskytnou okamžitý přehled o datech log.
* **Pokročilé analýzy** – použití strojového učení a vzor odpovídající algoritmy s cílem identifikovat možné problémy zjištěné při vaše protokoly.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Povolení streamování diagnostických protokolů do Log Analytics

Streamování diagnostických protokolů prostřednictvím kódu programu, prostřednictvím portálu, nebo pomocí můžete povolit [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). V obou případech můžete vytvořit nastavení diagnostiky v které zadáte, pracovnímu prostoru Log Analytics a kategorie protokolů a metrik, které chcete odeslat daném pracovním prostoru. Diagnostika **kategorie protokolu** je typ protokolu, který může poskytnout zdroje.

Pracovní prostor Log Analytics, nemusí být ve stejném předplatném jako prostředek, které vysílá protokoly za předpokladu, že uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diagnostické protokoly Stream pomocí portálu
1. Na portálu přejděte do Azure monitoru a klikněte na **nastavení diagnostiky**

    ![Monitorování bodu služby Azure Monitor](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. Volitelně můžete filtrovat seznam podle skupiny prostředků nebo prostředek typu a potom klikněte na prostředek, pro kterou chcete nastavit nastavení diagnostiky.

3. Pokud neexistuje žádná nastavení pro prostředek jste vybrali, se zobrazí výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiku."

   ![Přidejte nastavení diagnostiky – žádná existující nastavení](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   Pokud existuje stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou už nakonfigurovaná na tento prostředek. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko u **odesílat do Log Analytics**, pak vyberte pracovní prostor Log Analytics.

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly se streamují do tohoto pracovního prostoru, jakmile je vygenerována nová data události. Všimněte si, že může být až 15 minut mezi při události je vygenerován a pokud je zobrazeno v Log Analytics.

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Pokud chcete povolit streamování prostřednictvím [rutin prostředí Azure PowerShell](insights-powershell-samples.md), můžete použít `Set-AzureRmDiagnosticSetting` rutiny s těmito parametry:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Všimněte si, že vlastnost ID pracovního prostoru trvá úplné Azure resource ID pracovního prostoru, není pracovní prostor ID a klíč uvedené na portálu Log Analytics.

### <a name="via-azure-cli-20"></a>Via Azure CLI 2.0

Pokud chcete povolit streamování prostřednictvím [příkazového řádku Azure CLI 2.0](insights-cli-samples.md), můžete použít [az monitor diagnostiky – nastavení vytváření](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) příkazu.

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

Můžete přidat další kategorie pro protokol diagnostiky tak, že přidáte slovníky předané jako pole JSON `--logs` parametru.

`--resource-group` Argument je pouze požadováno pokud `--workspace` není ID objektu.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Jak dotaz na data v Log Analytics?

V okně hledání v protokolu na portálu nebo Advanced Analytics prostředí jako součást Log Analytics se můžete dotazovat diagnostické protokoly jako součást řešení Správa protokolů ve složce AzureDiagnostics tabulky. Existují také [několik řešení pro prostředky Azure](../log-analytics/log-analytics-add-solutions.md) instalací získat okamžitý přehled o data protokolu odesílají do Log Analytics.

## <a name="next-steps"></a>Další postup

* [Další informace o diagnostických protokolech Azure](monitoring-overview-of-diagnostic-logs.md)
