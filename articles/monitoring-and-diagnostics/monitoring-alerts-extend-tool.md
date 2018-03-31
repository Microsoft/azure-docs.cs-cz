---
title: Spuštění rozšíření výstrahy od OMS do Azure | Microsoft Docs
description: Nástroje a rozhraní API, podle kterého rozšíří výstrahy od OMS do Azure výstrahy, lze provést zákazníci odpojit.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 5e8a66214efc27e8e59da354e54d68bb48d274cc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="initiate-extending-alerts-from-oms-into-azure"></a>Zahájit rozšíření výstrahy od OMS do Azure
Od **23 duben 2018**, všechny zákazníky používající výstrahy, které jsou nakonfigurované v [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), bude rozšířeno do Azure. Výstrahy, které jsou rozšířené Azure chovají stejným způsobem jako v OMS. Možnosti monitorování zůstanou beze změn. Rozšíření výstrahy vytvořené v OMS do Azure poskytuje řadu výhod. Další informace o výhodách a proces prodloužení výstrahy od OMS do Azure najdete v tématu [rozšířit výstrahy z OMS Azure](monitoring-alerts-extend.md).

Zákazníci chtějí přesunutí výstrahy z OMS do Azure okamžitě, lze provést pomocí jedné z možností uvádí.

## <a name="option-1---using-oms-portal"></a>Možnost 1 - portálu OMS
Pokud chcete odpojit iniciovat rozšíří výstrahy od OMS do Azure, postupujte podle kroků uvedených níže.

1. Na stránce Přehled služby Operations Management Suite (OMS) přejděte do nastavení a pak části výstrahy. Klikněte na tlačítko s názvem "Rozšířit do Azure", zvýrazněných v obrázku níže.

    ![Stránka nastavení výstrah OMS s možností rozšíření](./media/monitor-alerts-extend/ExtendInto.png)

2. Po kliknutí na tlačítko Krok 3 průvodce se zobrazí, s prvním krokem, kterým poskytuje podrobnosti o procesu. Kliknutím na tlačítko Další, aby bylo možné pokračovat.

    ![Rozšíření výstrahy od OMS do Azure – krok 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. V druhém kroku, systém se zobrazí souhrn navrhované změny, pomocí seznamu příslušnou [skupiny akcí](monitoring-action-groups.md), pro výstrahy v OMS. Pokud jsou podobné akce vidět napříč více než jednu výstrahu - systému navrhne přidružit všechny z nich skupinu jedné akce.  Akce skupiny navrhované, postupujte podle zásad vytváření názvů: *WorkspaceName_AG_ #Number*. Aby bylo možné pokračovat, klikněte na tlačítko Další.
Ukázka obrazovce níže.

    ![Rozšíření výstrahy od OMS do Azure – krok 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. V posledním kroku průvodce můžete pokládat OMS při plánování rozšíření všechny výstrahy do Azure – vytváření nových skupin akce a přidružení s výstrahami, jak je znázorněno na obrazovce starší. Chcete-li pokračovat, vyberte kliknutím na tlačítko Dokončit a potvrďte příkazového řádku na zahájení procesu. Zákazníci Volitelně můžete zadat taky e-mailové adresy, na které se mu OMS k odeslání zprávy na dokončení zpracování.

    ![Rozšíření výstrahy od OMS do Azure – krok 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. Po dokončení Průvodce ovládacího prvku se vraťte na stránku nastavení výstrah a možnost "Rozšířit do Azure" bude odebrána. Na pozadí se naplánuje OMS výstrahy v OMS na Azure; To může nějakou dobu trvat a při zahájení operace pro krátké doby výstrahy v OMS nebudete mít k dispozici pro úpravy. Prostřednictvím hlavička se zobrazí aktuální stav, a pokud e-mailových adres kde zadaný během kroku 4, budou informace, když proces na pozadí úspěšně rozšiřuje všechny výstrahy do Azure. 

6. Výstrahy budou nadále uvedené na OMS, i po získání úspěšně rozšířit do Azure.

    ![Po rozšíření výstrahy v OMS do Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Možnost 2 – pomocí rozhraní API
Pro zákazníky, kteří chtějí prostřednictvím kódu programu řízení nebo automatizovat proces rozšíření výstrahy v OMS do Azure; Společnost Microsoft poskytuje nové rozhraní API AlertsVersion pod analýzy protokolů.

Log Analytics AlertsVersion API je dosáhl standardu RESTful a je přístupný prostřednictvím rozhraní REST API Azure Resource Manager. V tomto dokumentu najdete příklady kterých je přístup k rozhraní API z příkazového řádku pomocí prostředí PowerShell [ARMClient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API služby Azure Resource Manager. Použití ARMClient a prostředí PowerShell je jedním z mnoha možností pro přístup k rozhraní API. Rozhraní API bude výstup výsledků ve formátu JSON, povolení použití výsledky v mnoha různými způsoby prostřednictvím kódu programu.

Pomocí GET na volání rozhraní API jeden můžete získat ve výsledku souhrn navrhované změny, jako seznam odpovídající [skupiny akcí](monitoring-action-groups.md) pro výstrahy v OMS, formátu JSON. Pokud jsou podobné akce vidět napříč více než jednu výstrahu – systém navrhne vytvořit přidružte k všechny z nich skupinu jedné akce.  Akce skupiny navrhované, postupujte podle zásad vytváření názvů: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> ZÍSKAT volání rozhraní API nebude mít za následek výstrahy v OMS získávání rozšířit do Azure. Pouze podrobně jako odpověď souhrn navrhované změny. Pokud chcete potvrdit, tyto změny provést pro rozšíření výstrahy do Azure, příspěvku na volání musí být provedeno do rozhraní API.

Pokud je úspěšné, společně s odpovědi 200 OK GET volání rozhraní API, by je třeba zadat JSON seznam výstrah spolu s navrhovaná akce skupiny. Ukázková odpověď níže:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
V případě neexistují žádné výstrahy v pracovním prostoru zadaný, společně s 200 OK odpověď pro tuto operaci GET by byl ve formátu JSON:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Pokud všechny výstrahy v pracovním prostoru zadaný už se rozšířily do Azure – odpověď na volání GET by:
```json
{
    "version": 2
}
```

K zahájení plánování rozšíření výstrahy v OMS na Azure, inicializujte metodu POST SMĚŘUJÍCÍ do rozhraní API. Provádění tohoto volání příkazu potvrdí uživatele záměr a také přijetí výstrahy v OMS rozšířit do Azure a provést změny, které je uvedené v odpovědi GET volání rozhraní API. Uživatel může volitelně můžete zadat seznam e-mailové adresy, na které OMS bude Poštovní sestavy, až se dokončí proces na pozadí plánované rozšíření výstrahy v OMS na Azure úspěšně.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Výsledkem rozšíření OMS výstrahy do Azure, může se lišit souhrn zadaný pomocí GET - na účet všechny změny v systému. Jakmile naplánováno, bude výstrahy v OMS není dočasně k dispozici pro úpravy nebo modifikace - při mohou být vytvořeny nové výstrahy. 

Pokud je požadavek POST úspěšné, musí vrátit odpovědi 200 OK spolu s:
```json
{
    "version": 2
}
```
Indikující, že výstrahy se rozšířily do Azure, podle verze 2. Tato verze je jenom pro kontrolu, pokud výstrahy se rozšířily do Azure a mít žádný vliv využití s [Log Analytics vyhledávání API](../log-analytics/log-analytics-api-alerts.md). Jakmile výstrahy jsou rozšířené úspěšně do Azure, všechny e-mailové adresy zadané během GET zašle sestavu s podrobnostmi provést změny.


A nakonec, pokud všechny výstrahy v pracovním prostoru zadaný již naplánované na Azure – odpověď na hodnotu POST bude 403 Zakázáno.


## <a name="next-steps"></a>Další postup

* Další informace o nové [prostředí Azure výstrahy](monitoring-overview-unified-alerts.md).
* Další informace o [protokolu výstrahy ve výstrahách Azure](monitor-alerts-unified-log.md).
