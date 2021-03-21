---
title: Odesílání upozornění na Azure Service Health pomocí ServiceNow
description: Získejte přizpůsobená oznámení o událostech služby Service Health do vaší instance ServiceNow.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: df6596dd9853a792b5bfdb333361a2b9cd02a347
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588193"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Odesílání upozornění na Azure Service Health s ServiceNow pomocí webhooků

V tomto článku se dozvíte, jak integrovat upozornění služby Azure Service Health s ServiceNow pomocí Webhooku. Po nastavení integrace Webhooku s instancí ServiceNow získáte upozornění prostřednictvím stávající oznamovací infrastruktury, když vás budou mít problémy se službou Azure. Pokaždé, když se aktivuje Azure Service Health výstraha, volá Webhook prostřednictvím skriptované REST API ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Vytvoření skriptových REST API v ServiceNow

1.  Ujistěte se, že jste si zaregistrovali a jste přihlášeni ke svému účtu [ServiceNow](https://www.servicenow.com/) .

1.  V ServiceNow přejděte do části **systémové webové služby** a vyberte **skriptovaná rozhraní REST API**.

    ![Oddíl "skriptovaná webová služba" v ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Vyberte možnost **Nový** a vytvořte novou skriptovou službu REST.
 
    ![Tlačítko "nové skriptované REST API" v ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Přidejte **název** do REST API a nastavte **ID rozhraní API** na `azureservicehealth` .

1.  Vyberte **Odeslat**.

    ![Nastavení "REST API" v ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Vyberte REST API, který jste vytvořili, a na kartě **prostředky** vyberte **Nový**.

    ![Karta prostředků v ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Pojmenujte** nový prostředek `event` a změňte **metodu HTTP** na `POST` .

1.  V části **skript** přidejte následující kód JavaScriptu:

    >[!NOTE]
    >`<secret>` `<group>` Ve skriptu níže musíte aktualizovat hodnotu, a `<email>` .
    >* `<secret>` měl by to být náhodný řetězec, například GUID.
    >* `<group>` měla by se jednat o skupinu ServiceNow, ke které chcete přiřadit incident.
    >* `<email>` měla by to být konkrétní osoba, se kterou chcete incident přiřadit (volitelné).
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

1.  Na kartě zabezpečení zrušte zaškrtnutí políčka **vyžaduje ověření** a vyberte **Odeslat**. `<secret>`Místo toho nastavíte ochranu tohoto rozhraní API.

    ![Zaškrtávací políčko "vyžaduje ověření" v ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Zpátky v části skriptovaná rozhraní REST API byste měli najít **základní cestu rozhraní API** pro vaše nové REST API:

     ![Základní cesta rozhraní API v ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Vaše plná adresa URL integrace vypadá takto:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Vytvoření upozornění pomocí ServiceNow v Azure Portal
### <a name="for-a-new-action-group"></a>Pro novou skupinu akcí:
1. Postupujte podle kroků 1 až 8 v [tomto článku](./alerts-activity-log-service-notifications-portal.md) a vytvořte upozornění s novou skupinou akcí.

1. V seznamu akcí definujte tyto **Akce**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** ServiceNow, kterou jste předtím uložili.

    c. **Název:** Název, alias nebo identifikátor Webhooku.

1. Po dokončení vyberte **Uložit** , aby se výstraha vytvořila.

### <a name="for-an-existing-action-group"></a>Pro existující skupinu akcí:
1. V [Azure Portal](https://portal.azure.com/)vyberte **monitorovat**.

1. V části **Nastavení** vyberte **skupiny akcí**.

1. Vyhledejte a vyberte skupinu akcí, kterou chcete upravit.

1. Přidat do seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** ServiceNow, kterou jste předtím uložili.

    c. **Název:** Název, alias nebo identifikátor Webhooku.

1. Po dokončení aktualizace skupiny akcí vyberte **Uložit** .

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování integrace Webhooku prostřednictvím požadavku HTTP POST
1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Ukázkovou datovou část Webhooku pro stav služby najdete na [webhookech pro výstrahy protokolu aktivit Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Požadavek HTTP POST vytvoříte takto:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste obdržet `200 OK` odpověď se zprávou "vytvořen incident".

1. Pokud chcete ověřit, že se integrace úspěšně nastavila, navštivte [ServiceNow](https://www.servicenow.com/) .

## <a name="next-steps"></a>Další kroky
- Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Přečtěte si o [oznámeních o stavu služby](./service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/alerts/action-groups.md).
