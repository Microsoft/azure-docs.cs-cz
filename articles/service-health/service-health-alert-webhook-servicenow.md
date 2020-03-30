---
title: Odeslání výstrah stavu služby Azure pomocí služby ServiceNow
description: Do instance ServiceNow můžete dostávat přizpůsobená oznámení o událostech stavu služby.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 3daae05aabff571010d043cf5602847e95ea29f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654099"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Odesílání výstrah stavu služby Azure pomocí služby ServiceNow pomocí webhooků

Tento článek ukazuje, jak integrovat výstrahy stavu služby Azure s ServiceNow pomocí webhooku. Po nastavení integrace webhooku s vaší instancí ServiceNow se zobrazí upozornění prostřednictvím stávající infrastruktury oznámení, když se vás týkají problémy se službou Azure. Pokaždé, když se aktivuje výstraha stavu služby Azure, zavolá webhookprostřednictvím rozhraní API Scripted REST služby ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Vytvoření skriptovaného rozhraní REST API v ServiceNow

1.  Ujistěte se, že jste se zaregistrovali a jste přihlášeni ke svému účtu [ServiceNow.](https://www.servicenow.com/)

1.  Přejděte do části **Systémové webové služby** v aplikaci ServiceNow a vyberte **skriptovaná síť REST API**.

    ![Část Skriptovaná webová služba v aplikaci ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Chcete-li vytvořit novou službu Scripted REST, vyberte **možnost Nový.**
 
    ![Tlačítko "Nové skriptované rozhraní REST API" v ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Přidejte **název** do rozhraní REST API a nastavte **ID rozhraní API** na `azureservicehealth`.

1.  Vyberte **Odeslat**.

    !["Rest API Nastavení" v ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Vyberte rozhraní REST API, které jste vytvořili, a na kartě **Zdroje** vyberte **Nový**.

    !["Karta zdroje" v ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Pojmenujte** `event` nový prostředek a `POST`změňte **metodu HTTP** na .

1.  V části **Skript** přidejte následující kód Jazyka JavaScript:

    >[!NOTE]
    >Je třeba aktualizovat `<secret>``<group>`, `<email>` a hodnotu ve skriptu níže.
    >* `<secret>`by měl být náhodný řetězec, jako identifikátor GUID
    >* `<group>`by měla být skupina ServiceNow, ke které chcete incident přiřadit
    >* `<email>`by měla být konkrétní osoba, které chcete incident přiřadit (nepovinné)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Na kartě zabezpečení zrušte **zaškrtnutí políčka Vyžaduje ověření** a vyberte **Odeslat**. Nastavené `<secret>` rozhraní chrání toto rozhraní API.

    ![Zaškrtávací políčko Vyžaduje ověření v servicenow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Zpět na skriptované rozhraní API REST, měli byste najít **základní cestu rozhraní API** pro nové rozhraní API REST:

     !["Cesta základního rozhraní API" v ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Vaše úplná adresa URL integrace vypadá takto:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Vytvoření výstrahy pomocí služby ServiceNow na webu Azure Portal
### <a name="for-a-new-action-group"></a>Pro novou skupinu akcí:
1. Postupujte podle kroků 1 až 8 v [tomto článku](../azure-monitor/platform/alerts-activity-log-service-notifications.md) a vytvořte výstrahu s novou skupinou akcí.

1. Definovat v seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** ServiceNow, kterou jste dříve uložili.

    c. **Název:** Název, alias nebo identifikátor webhooku.

1. **Chcete-li** výstrahu vytvořit, vyberte uložit po dokončení možnost Uložit.

### <a name="for-an-existing-action-group"></a>Pro existující skupinu akcí:
1. Na [webu Azure Portal](https://portal.azure.com/)vyberte **Monitor**.

1. V části **Nastavení** vyberte **skupiny akcí**.

1. Najděte a vyberte skupinu akcí, kterou chcete upravit.

1. Přidat do seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** ServiceNow, kterou jste dříve uložili.

    c. **Název:** Název, alias nebo identifikátor webhooku.

1. Chcete-li aktualizovat skupinu **akcí,** vyberte možnost Uložit po dokončení.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování integrace webhooku prostřednictvím požadavku HTTP POST
1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Můžete najít příklad stavu služby webhooku datové části na [Webhooks pro výstrahy protokolu aktivit Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Vytvořte požadavek HTTP POST následujícím způsobem:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste `200 OK` obdržet odpověď se zprávou "Incident vytvořen."

1. Přejděte na [ServiceNow](https://www.servicenow.com/) a potvrďte, že vaše integrace byla úspěšně nastavena.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [konfigurovat oznámení webhooku pro stávající systémy pro správu problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [webhookové schéma výstrahy protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Další informace o [oznámeních o stavu služby](../azure-monitor/platform/service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/platform/action-groups.md).