---
title: Konfigurace výstrah stavu služby Azure s použitím ServiceNow | Dokumentace Microsoftu
description: Získáte přizpůsobená oznámení o události služby service health k vaší instanci ServiceNow.
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
ms.openlocfilehash: 1f5984f8f28832c33d3a5a844fde72e7286ad251
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433785"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Konfigurace výstrah stavu služby s použitím ServiceNow

Tento článek ukazuje, jak integrovat výstrah stavu služby Azure s použitím ServiceNow pomocí webhooku. Po nastavení integraci webhooků s instancí ServiceNow, získáváte upozornění pomocí vaší stávající infrastruktury oznámení, kdy vás ovlivňují problémy se službami Azure. Pokaždé, když se aktivuje upozornění služby Azure Service Health, zavolá webhook prostřednictvím rozhraní API REST pro skripty pro ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Vytváření skriptů rozhraní REST API v ServiceNow
1.  Ujistěte se, že nemáte registrovanou službu a přihlášení do vaší [ServiceNow](https://www.servicenow.com/) účtu.

1.  Přejděte **webové služby System** části ServiceNow a vyberte **skripty rozhraní REST API**.

    ![V části "Skripty webová služba" v ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Vyberte **nový** vytvořit novou službu REST skripty.
 
    !["Nové skripty rozhraní REST API" tlačítko v ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Přidat **název** k rozhraní REST API a sady **ID rozhraní API** k `azureservicehealth`.

1.  Vyberte **odeslat**.

    !["REST API nastavení" v ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Vyberte rozhraní REST API, které jste vytvořili, a v části **prostředky** kartě vyberte **nový**.

    !["Prostředek kartě" v ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Název** nový prostředek `event` a změnit **metodu HTTP** k `POST`.

1.  V **skript** části, přidejte následující kód jazyka JavaScript:

    >[!NOTE]
    >Je potřeba aktualizovat `<secret>`,`<group>`, a `<email>` hodnotu v níže uvedeném skriptu.
    >* `<secret>` náhodný řetězec jako identifikátor GUID by měl být.
    >* `<group>` Skupina ServiceNow, kterou chcete přiřadit incident, aby měli
    >* `<email>` by měla být konkrétní osoby, kterou chcete přiřadit incident (volitelné)
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

1.  Na kartě zabezpečení, zrušte zaškrtnutí políčka **vyžaduje ověření** a vyberte **odeslat**. `<secret>` Je sada místo toho chrání toto rozhraní API.

    ![Zaškrtávací políčko "Vyžaduje ověřování" v ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Zpět v sekci skripty rozhraní REST API, měli byste najít **základní rozhraní API cesta** pro nové rozhraní REST API:

     !["Základní rozhraní API cesty" v ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Úplnou adresu URL integrace vypadá takto:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Vytvořit upozornění použitím ServiceNow na portálu Azure portal
### <a name="for-a-new-action-group"></a>Pro nová skupina akcí:
1. Postupujte podle kroků 1 až 8 v [v tomto článku](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) vytvoření výstrahy se nová skupina akcí.

1. Definování v seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** ServiceNow **adresu URL integrace** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

1. Vyberte **Uložit** po dokončení vytvoření výstrahy.

### <a name="for-an-existing-action-group"></a>Pro existující skupiny akcí:
1. V [webu Azure portal](https://portal.azure.com/)vyberte **monitorování**.

1. V **nastavení** vyberte **skupiny akcí**.

1. Vyhledejte a vyberte skupinu akcí, které chcete upravit.

1. Přidat do seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** ServiceNow **adresu URL integrace** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

1. Vyberte **Uložit** po dokončení aktualizovat skupinu akcí.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování vaší integraci webhooků prostřednictvím požadavku HTTP POST
1. Vytvořte datová část služby stavu, který chcete odeslat. Můžete najít příkladu služby health datová část webhooku v [upozornění protokolů Webhooky Azure aktivity](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

1. Vytvoření požadavku HTTP POST následujícím způsobem:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste obdržet `200 OK` odpovědi se zprávou "Vytvořen Incident."

1. Přejděte na [ServiceNow](https://www.servicenow.com/) potvrďte, že vaše integrace byl nastaven úspěšně.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy pro správu problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Další informace o [služby oznámení o stavu](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Další informace o [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).