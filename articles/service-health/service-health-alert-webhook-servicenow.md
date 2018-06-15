---
title: Konfigurace výstrah stavu služeb Azure s ServiceNow | Microsoft Docs
description: Získáte přizpůsobený oznámení o události stavu služby pro vaše instance ServiceNow.
author: shawntabrizi
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 867a8c0b478df9d2b7690b8b914ded7c42558583
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178864"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Konfigurace výstrah stavu služeb s ServiceNow

Tento článek ukazuje, jak integrovat ServiceNow výstrahy stavu služby Azure pomocí webhook, jehož. Po nastavení integrace webhooku s vaší instance ServiceNow, získá při otázky týkající se služby Azure ovlivnit můžete výstrahy prostřednictvím vaší stávající infrastruktury pro oznámení. Pokaždé, když se aktivuje výstraha stavu služby Azure, zavolá webhooku přes ServiceNow na skriptování REST API.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Vytváření v ServiceNow skriptované rozhraní REST API
1.  Zajistěte, aby si zaregistrovali a přihlášení do vaší [ServiceNow](https://www.servicenow.com/) účtu.

2.  Přejděte na **webové služby System** část v ServiceNow a vyberte **skriptování rozhraní REST API**.

    ![V části "Skriptování webová služba" v ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

3.  Vyberte **nový** k vytvoření nové služby REST skriptování.
 
    ![Tlačítko "Nové skriptování rozhraní REST API" v ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

4.  Přidat **název** do rozhraní REST API a nastavte **rozhraní API ID** k `azureservicehealth`.

5.  Vyberte **odeslání**.

    !["REST API nastavení" v ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  Vyberte rozhraní REST API, které jste vytvořili, a v části **prostředky** vyberte **nový**.

    !["Prostředků kartě" v ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

7.  **Název** nový prostředek `event` a změňte **metoda HTTP** k `POST`.

8.  V **skriptu** přidejte následující kód v JavaScriptu:

    >[!NOTE]
    >Je potřeba aktualizovat `<secret>`,`<group>`, a `<email>` hodnotu v níže uvedeném skriptu.
    >* `<secret>` by měl být řetězec náhodných jako identifikátor GUID
    >* `<group>` musí být ServiceNow skupinu, kterou chcete přiřadit k incidentu
    >* `<email>` musí být konkrétní osoby, které chcete přiřadit incident (volitelné)
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
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
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

9.  Na kartě zabezpečení, zrušte zaškrtnutí políčka **vyžaduje ověření** a vyberte **odeslání**. `<secret>` Jste sadu místo chrání toto rozhraní API.

    ![Zaškrtávací políčko "Vyžaduje ověřování" v ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

10.  V části skriptování rozhraní REST API byste měli najít **základní rozhraní API cesta** nové rozhraní API REST:

     !["Základní rozhraní API cestu" v ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

11.  Úplná adresa URL integrace vypadá takto:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Vytvořit výstrahu ServiceNow pomocí portálu Azure
### <a name="for-a-new-action-group"></a>Pro novou skupinu akce:
1. Postupujte podle kroků 1 až 8 v [v tomto článku](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) vytvoření výstrahy k nové skupině Akce.

2. Definování v seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** ServiceNow **URL integraci** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

3. Vyberte **Uložit** po dokončení vytvoření výstrahy.

### <a name="for-an-existing-action-group"></a>Pro stávající skupinu akce:
1. V [portál Azure](https://portal.azure.com/), vyberte **monitorování**.

2. V **nastavení** vyberte **skupiny akcí**.

3. Najděte a vyberte skupinu akce, kterou chcete upravit.

4. Přidejte do seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** ServiceNow **URL integraci** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

5. Vyberte **Uložit** po dokončení aktualizace skupiny akce.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování svoji integraci webhooku prostřednictvím požadavku HTTP POST
1. Vytvoření datové služby stavu, který chcete odeslat. Můžete najít datovou příklad služby stavu webhooku v [Webhooky Azure aktivity protokolu výstrahy](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Vytvořte požadavek HTTP POST takto:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Měli byste obdržet `200 OK` odpověď se zprávou "Incident vytvořený."

4. Přejděte na [ServiceNow](https://www.servicenow.com/) potvrďte, že byl úspěšně nastaven svoji integraci.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy správy problém](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma výstrahy webhooku protokolu činnosti](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Další informace o [oznámení o stavu služby](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Další informace o [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).