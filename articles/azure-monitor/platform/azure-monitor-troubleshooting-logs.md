---
title: Azure Monitor protokoly řešení potíží (Preview)
description: Pomocí Azure Monitor můžete rychle nebo pravidelně prošetřit problémy, řešit problémy s kódem nebo konfigurací nebo řešit případy podpory, které se často spoléhají na hledání velkého objemu dat pro konkrétní přehledy.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: b3d1ad2d327da60874d2d07ba697b8f5ab815189
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127086"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Azure Monitor protokoly řešení potíží (Preview)
Azure Monitor můžete použít k rychlému a nebo pravidelnému zkoumání problémů, odstraňování potíží s kódem nebo konfigurací nebo řešení případů podpory, které se často spoléhají na hledání velkého objemu dat pro konkrétní přehledy.

>[!NOTE]
> * Protokoly řešení potíží jsou v režimu náhledu.
>* Kontaktujte [tým Log Analytics](mailto:orens@microsoft.com) s případnými dotazy nebo použijte funkci.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Řešení potíží s kódem nebo problémy s konfigurací a dotazování na ně
Pomocí Azure Monitor protokolů řešení potíží můžete načíst záznamy a prozkoumat problémy a problémy s jednodušším a levnějším způsobem pomocí KQL.
Řešení potíží s protokoly uvádí vaše poplatky tím, že vám poskytne základní možnosti pro řešení potíží.

> [!NOTE]
>* Rozhodnutí o režimu řešení potíží je konfigurovatelné.
>* Protokoly řešení potíží lze použít pro konkrétní tabulky, aktuálně v tabulkách "protokoly kontejnerů" a "trasování aplikace".
>* Doba uchovávání dat je 4 dny a je možné ji rozšířit i na náklady.
>* Ve výchozím nastavení tabulky zdědí uchovávání pracovního prostoru. Aby nedošlo k dalším poplatkům, doporučuje se změnit tyto uchovávání tabulek. [Kliknutím sem se dozvíte, jak změnit uchování tabulky](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Zapnout řešení potíží s protokoly v tabulkách

Chcete-li zapnout řešení potíží s protokoly v pracovním prostoru, je nutné použít následující volání rozhraní API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Ověřte, jestli je pro danou tabulku povolená funkce protokolů řešení potíží.
Pokud chcete zjistit, jestli je pro danou tabulku povolený protokol řešení potíží, můžete použít následující volání rozhraní API.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"
                }

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Ověřte, jestli je povolená funkce protokoly řešení potíží pro všechny tabulky v pracovním prostoru.
Chcete-li zjistit, které tabulky mají povolen protokol řešení potíží, můžete použít následující volání rozhraní API.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
 },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Vypnutí protokolů řešení potíží v tabulkách

Chcete-li vypnout protokoly řešení potíží v pracovním prostoru, je nutné použít následující volání rozhraní API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
>[!TIP]
>* Pro spuštění příkazů můžete použít libovolný REST API nástroj. [Další informace](https://docs.microsoft.com/rest/api/azure/)
>* Pro ověřování musíte použít nosný token. [Další informace](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* Příznak "isTroubleshootingAllowed" – popisuje, zda je tabulka ve službě povolena.
>* IsTroubleshootEnabled označuje, jestli je funkce povolená pro tabulku – dá se zapnout nebo vypnout (true nebo false).
>* Když zapnete příznak "isTroubleshootEnabled" pro konkrétní tabulku, opětovné povolení je možné pouze jeden týden po datu předchozího povolení.
>* V současné době je tato podpora podporovaná jenom pro tabulky (některé jiné SKU budou taky v budoucnu podporované) – [Přečtěte si další informace o cenách](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Omezení dotazů pro řešení potíží
Existuje několik omezení pro tabulku, která je označena jako "protokoly pro řešení potíží":
*   Dostanou méně prostředků zpracování, proto nebudou vhodné pro velké řídicí panely, komplexní analýzy nebo mnoho souběžných volání rozhraní API.
*   Dotazy jsou omezené na časový rozsah dvou dnů.
* vymazání nebude fungovat – [Další informace o vymazání](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge)najdete v článku.
* Výstrahy nejsou prostřednictvím této služby podporovány.
## <a name="next-steps"></a>Další kroky
* [Zápis dotazů](https://docs.microsoft.com/azure/data-explorer/write-queries)


