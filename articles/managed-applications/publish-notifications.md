---
title: Azure Managed Applications s oznámeními
description: Nakonfigurujte spravovanou aplikaci s koncovými body Webhooku, abyste dostávali oznámení o vytváření, aktualizacích, odstraňování a chybách na instancích spravované aplikace.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 8cf9fc0b3d9c13ebc5309be6d27c7be0f2e60878
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805684"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure Managed Applications s oznámeními

Oznámení spravované aplikace Azure umožňují vydavatelům automatizovat akce založené na událostech životního cyklu instancí spravovaných aplikací. Vydavatelé můžou zadat koncové body Webhooku vlastního oznámení, aby dostávali oznámení o událostech pro nové a existující instance spravované aplikace. Umožňuje vydavateli nastavit vlastní pracovní postupy v době zřizování, aktualizace a odstraňování aplikací.

## <a name="getting-started"></a>Začínáme
Pokud chcete začít přijímat spravované aplikace, aktivujte si veřejný koncový bod HTTPS a určete ho při publikování definice aplikace katalogu služeb nebo nabídky Marketplace.

Tady jsou doporučené série kroků pro rychlé zprovoznění:
1. Aktivujte veřejný koncový bod HTTPS, který zaznamená příchozí požadavky POST a vrátí `200 OK`.
2. Přidejte koncový bod do definice aplikace katalogu služeb nebo nabídky Marketplace, jak je vysvětleno níže.
3. Vytvořte instanci spravované aplikace, která odkazuje na definici aplikace nebo na nabídku Marketplace.
4. Ověřte, že se oznámení úspěšně přijímají.
5. Povolte autorizaci, jak je vysvětleno níže v části **ověřování koncového bodu** .
6. Podle níže uvedené dokumentace **schématu oznámení** můžete analyzovat požadavky na oznámení a implementovat svoji obchodní logiku na základě oznámení.

## <a name="adding-service-catalog-application-definition-notifications"></a>Přidávání oznámení definice aplikace katalogu služeb
#### <a name="azure-portal"></a>Portál Azure
Pokud chcete začít, přečtěte si téma [publikování aplikace katalogu služeb prostřednictvím Azure Portal](./publish-portal.md) .

![Oznámení definice aplikace katalogu služeb na portálu](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>Rozhraní REST API

> [!NOTE]
> V rámci **notificationEndpoints** ve vlastnostech definice aplikace je v současné době podporován pouze jeden koncový bod.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>Přidávání oznámení o spravovaných aplikacích Marketplace
Další informace najdete v tématu [Vytvoření nabídky aplikací Azure](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Oznámení definice aplikace katalogu služeb na portálu](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Aktivační události
Následující tabulka popisuje všechny možné kombinace EventType + ProvisioningState a jejich aktivační události:

EventType | ProvisioningState | Aktivační událost pro oznámení
---|---|---
PUT | Přijato | Spravovaná skupina prostředků se vytvořila a po vložení aplikace se úspěšně provedla. (Před zahájením nasazení uvnitř spravovaného RGu.)
PUT | Úspěch | Úplné zřízení spravované aplikace bylo po vložení úspěšné.
PUT | Selhalo | Chyba při zřizování instance aplikace v jakémkoli bodě.
PATCH | Úspěch | Po úspěšné opravě instance spravované aplikace za účelem aktualizace značek, zásad přístupu JIT nebo spravované identity.
DELETE | Odstraňování | Jakmile uživatel zahájí odstranění instance spravované aplikace.
DELETE | Odstraněno | Po úplném a úspěšném odstranění spravované aplikace.
DELETE | Selhalo | Po jakékoli chybě během procesu zrušení zřízení, který blokování odstraní.
## <a name="notification-schema"></a>Schéma oznámení
Když nastavíte koncový bod Webhooku pro zpracování oznámení, budete muset analyzovat datovou část, abyste získali důležité vlastnosti, které pak budou fungovat na oznámení. Katalog služeb i oznámení spravované aplikace na webu Marketplace poskytují mnoho stejných vlastností s malým rozdílem popsaným níže.

#### <a name="service-catalog-application-notification-schema"></a>Schéma oznámení aplikace katalogu služeb
Tady je ukázkové oznámení katalogu služeb po úspěšném zřízení instance spravované aplikace.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Pokud se zřizování nezdaří, pošle se do zadaného koncového bodu oznámení s podrobnostmi o chybě.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Schéma oznámení aplikace Marketplace

Tady je ukázkové oznámení katalogu služeb po úspěšném zřízení instance spravované aplikace.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Pokud se zřizování nezdaří, pošle se do zadaného koncového bodu oznámení s podrobnostmi o chybě.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parametr | Popis
---|---
eventType | Typ události, která aktivovala oznámení. (např. "PUT", "PATCH", "DELETE")
applicationId | Plně kvalifikovaný identifikátor prostředku spravované aplikace, pro kterou bylo oznámení aktivované. 
eventTime | Časové razítko události, která aktivovala oznámení (Datum a čas ve formátu UTC ISO 8601)
provisioningState | Stav zřizování instance spravované aplikace. (např. "úspěšné", "neúspěšné", "odstraňování", "odstraněno")
billingDetails | Údaje o fakturaci instance spravované aplikace. Obsahuje resourceUsageId, který se dá použít k dotazování webu Marketplace na podrobnosti o využití.
error | *Zadáno pouze v případě selhání provisioningState*. Obsahuje kód chyby, zprávu a podrobnosti problému, který způsobil chybu.
applicationDefinitionId | *Zadáno pouze pro spravované aplikace katalogu služeb*. Představuje plně kvalifikovaný identifikátor prostředku definice aplikace, pro kterou se zřídila instance spravované aplikace.
plánování | *Zadáno pouze pro spravované aplikace Marketplace*. Představuje vydavatele, nabídku, SKU a verzi instance spravované aplikace.

## <a name="endpoint-authentication"></a>Ověřování koncového bodu
Zabezpečení koncového bodu Webhooku a ověření pravosti oznámení:
- Zadejte parametr dotazu nad identifikátorem URI Webhooku, jako je https://your-endpoint.com?sig=Guid. U každého oznámení proveďte rychlou kontrolu, že parametr dotazu `sig` má `Guid`očekávanou hodnotu.
- Vystavte získání instance spravované aplikace pomocí applicationId. Ověřte, že provisioningState odpovídá provisioningState oznámení, aby se zajistila konzistence.

## <a name="notification-retries"></a>Opakované pokusy o oznámení

Služba oznamování spravovaných aplikací očekává `200 OK` odpověď z koncového bodu Webhooku do oznámení. Služba Notification Service se zopakuje, pokud koncový bod Webhooku vrátí kód chyby HTTP > = 500, 429, nebo pokud je koncový bod dočasně nedostupný. Pokud koncový bod Webhooku nebude k dispozici během 10 hodin, bude zpráva oznámení vyřazena a pokusy se zastaví.

