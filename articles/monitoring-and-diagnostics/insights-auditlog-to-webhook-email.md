---
title: Volat webhook, jehož u protokolu upozornění aktivita Azure (klasické)
description: Zjistěte, jak směrování aktivity protokolu události do jiných služeb pro vlastní akce. Můžete například posílat zprávy SMS, protokolu chyby nebo upozornění tým prostřednictvím chatu nebo službou zasílání zpráv.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: e825d0f2487c20c8c7f3d210d7180b07742d7173
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262456"
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Volat webhook, jehož na výstrahu protokolu aktivita Azure
Webhooky můžete používat ke směrování Azure oznámení výstrahy k jiným systémům pro následné zpracování nebo pro vlastní akce. Webhook, jehož na výstrahu můžete použít ke směrování ke službám, které odesílají zprávy SMS, do protokolu chyb, upozornit na tým prostřednictvím chatu nebo služby zasílání zpráv nebo pro různé další akce. Můžete také nastavit tak výstrahu protokolu aktivit k odeslání e-mailu, když se aktivuje výstrahu.

Tento článek popisuje, jak nastavit webhooku se volá, když se aktivuje Výstrahy Azure aktivity protokolu. Také ukazuje, jak vypadá pro HTTP POST na webhook, jehož datové části. Informace o nastavení a schéma výstrahu metriky Azure najdete v tématu [konfigurace webhook, jehož na výstrahu Azure metriky](insights-webhooks-alerts.md). 

> [!NOTE]
> Funkce, která podporuje volání webhook, jehož na výstrahu protokolu aktivita Azure v současné době je ve verzi preview.
>
>

Výstraha aktivity protokolu můžete nastavit pomocí [rutin prostředí Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), [rozhraní příkazového řádku a platformy](insights-cli-samples.md#work-with-alerts), nebo [rozhraní REST API Azure monitorování](https://msdn.microsoft.com/library/azure/dn933805.aspx). V současné době nelze pomocí portálu Azure nastavit výstrahu protokolu aktivit.

## <a name="authenticate-the-webhook"></a>Ověření webhooku
Webhooku můžete ověřit pomocí některé z těchto metod:

* **Na základě tokenu autorizace**. Webhook identifikátor URI je uložit s ID tokenu. Příklad: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Základní ověřování**. Webhook se identifikátor URI je uložit pomocí uživatelského jména a hesla. Například: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Datová část schématu
Operaci POST obsahuje následující datové části JSON a schéma pro všechny aktivity na základě protokolu výstrahy. Toto schéma je podobná používaný pro výstrahy na základě metriky.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Název elementu | Popis |
| --- | --- |
| status |Používá pro výstrahy, metriky. Pro aktivitu protokolu výstrahy vždy nastaven na aktivované.|
| Kontext |Kontext události. |
| activityLog | Vlastnosti protokolu události.|
| Autorizace |Řízení přístupu na základě rolí (RBAC) vlastnosti události. Tyto vlastnosti obvykle zahrnují **akce**, **role**, a **oboru**. |
| akce | Akce zachycenou výstrahy. |
| scope | Rozsah výstrahy (tedy prostředků).|
| kanály | Operace. |
| Deklarace identity | Soubor informací, protože má vztah k deklarace identity. |
| volající |Identifikátor GUID nebo uživatelské jméno uživatele, který provedl operaci, deklaraci nebo deklarace hlavní název služby, na základě dostupnosti. Může být pro určité systémová volání hodnotu null. |
| correlationId |Obvykle GUID ve formátu řetězce. Události se **correlationId** patřit do stejné větší akce. Stejné mají obvykle **correlationId** hodnotu. |
| description |Popis výstrahy byla nastavena, když byla výstraha vytvořena. |
| EventSource |Název služby Azure nebo infrastrukturu, které vygenerovalo událost. |
| eventTimestamp |Čas, kdy k události došlo. |
| eventDataId |Jedinečný identifikátor události. |
| úroveň |Jeden z následujících hodnot: kritická, chyba, upozornění, informační nebo Verbose. |
| operationName |Název operace. |
| operationId |Obvykle identifikátor GUID, který je sdílen události. Identifikátor GUID obvykle odpovídá jedné operace. |
| resourceId |ID prostředku ovlivněných prostředků. |
| resourceGroupName |Název skupiny prostředků pro ovlivněných prostředků. |
| resourceProviderName |Poskytovatel prostředků ovlivněných prostředků. |
| status |Hodnotu řetězce, které uvádí stav operace. Běžné hodnoty zahrnují Začínáme, probíhá, bylo úspěšné, neúspěšné, aktivní a vyřešeno. |
| Podřízený stav |Obvykle zahrnuje stavový kód HTTP odpovídající volání REST. Může také obsahovat další řetězce, které popisují podřízeného stavu. Běžné substatus hodnoty zahrnují OK (stavový kód HTTP: 200), které byly vytvořeny (stavový kód HTTP: 201), platné (stavový kód HTTP: 202), ne obsahu (stavový kód HTTP: 204), chybný požadavek (stavový kód HTTP: 400), nebyl nalezen (stavový kód HTTP: 404), konflikt (stavový kód HTTP: 409 ), Vnitřní chybu serveru (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503) a vypršel časový limit brány (kód stavu HTTP: 504). |
| subscriptionId |ID předplatného Azure. |
| submissionTimestamp |Čas, kdy byla generována událost pomocí služby Azure, který požadavek zpracoval. |
| Typ prostředku | Typ prostředku, které vygenerovalo událost.|
| properties |Sada dvojic klíč/hodnota, který obsahuje podrobnosti o události. Například, `Dictionary<String, String>`. |

## <a name="next-steps"></a>Další postup
* Další informace o [protokol aktivit](monitoring-overview-activity-logs.md).
* Zjistěte, jak [spustit skripty Azure Automation (runbooky) na Azure výstrahy](http://go.microsoft.com/fwlink/?LinkId=627081).
* Zjistěte, jak [použití aplikace logiky k odeslání zprávy SMS prostřednictvím Twilio z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). V tomto příkladu je metriky výstrahy, ale můžete ho na spolupráci s výstrahu protokolu aktivity upravit.
* Zjistěte, jak [použití aplikace logiky odeslat zprávu Slack z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). V tomto příkladu je metriky výstrahy, ale můžete ho na spolupráci s výstrahu protokolu aktivity upravit.
* Zjistěte, jak [použití aplikace logiky k odeslání zprávy do fronty Azure z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). V tomto příkladu je metriky výstrahy, ale můžete ho na spolupráci s výstrahu protokolu aktivity upravit.
