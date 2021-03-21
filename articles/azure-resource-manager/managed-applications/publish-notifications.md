---
title: Spravované aplikace s oznámeními
description: Nakonfigurujte spravované aplikace pomocí koncových bodů Webhooku, abyste dostávali oznámení o vytváření, aktualizacích, odstraňování a chybách na instancích spravovaných aplikací.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 2a2e9d429d494c35c49a5b0a3e10b291fd8f24a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633933"
---
# <a name="azure-managed-applications-with-notifications"></a>Spravované aplikace Azure s oznámeními

Oznámení spravované aplikace Azure umožňují vydavatelům automatizovat akce založené na událostech životního cyklu instancí spravovaných aplikací. Vydavatelé můžou zadat koncové body Webhooku vlastního oznámení, aby dostávali oznámení o událostech pro nové a existující instance spravované aplikace. Vydavatelé můžou nastavit vlastní pracovní postupy v době zřizování, aktualizace a odstraňování aplikací.

## <a name="getting-started"></a>Začínáme
Pokud chcete začít přijímat spravované aplikace, aktivujte si veřejný koncový bod HTTPS a určete ho při publikování definice aplikace katalogu služeb nebo nabídky Azure Marketplace.

Tady je doporučený postup pro rychlé zprovoznění:
1. Aktivujte veřejný koncový bod HTTPS, který zaznamená příchozí požadavky POST a vrátí `200 OK` .
2. Přidejte koncový bod do definice aplikace katalogu služeb nebo nabídky Azure Marketplace, jak je vysvětleno dále v tomto článku.
3. Vytvořte instanci spravované aplikace, která odkazuje na definici aplikace nebo na nabídku Azure Marketplace.
4. Ověřte příjem oznámení.
5. Povolte autorizaci, jak je vysvětleno v části **ověřování koncového bodu** tohoto článku.
6. Podle pokynů v části **schéma oznámení** v tomto článku můžete analyzovat požadavky na oznámení a implementovat svoji obchodní logiku na základě oznámení.

## <a name="add-service-catalog-application-definition-notifications"></a>Přidat oznámení definice aplikace katalogu služeb
#### <a name="azure-portal"></a>portál Azure
Informace o tom, jak začít, najdete v tématu [publikování aplikace katalogu služeb prostřednictvím Azure Portal](./publish-portal.md).

![Oznámení definice aplikace katalogu služeb v Azure Portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> V současné době můžete ve vlastnostech definice aplikace v nástroji nastavit pouze jeden koncový bod `notificationEndpoints` .

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Přidat Azure Marketplace oznámení o spravovaných aplikacích
Další informace najdete v tématu [Vytvoření nabídky aplikací Azure](../../marketplace/create-new-azure-apps-offer.md).

![Azure Marketplace oznámení spravované aplikace v Azure Portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Aktivační události
Následující tabulka popisuje všechny možné kombinace EventType a ProvisioningState a jejich aktivační události:

Typ události | ProvisioningState | Aktivační událost pro oznámení
---|---|---
PUT | Přijato | Spravovaná skupina prostředků se vytvořila a po vložení aplikace se úspěšně provedla. (před tím, než se nasazování do spravované skupiny prostředků dokončí).
PUT | Úspěšný | Úplné zřízení spravované aplikace bylo po vložení úspěšné.
PUT | Neúspěšný | Chyba při zřizování instance aplikace v jakémkoli bodě.
POUŽITA | Úspěšný | Po úspěšné opravě instance spravované aplikace aktualizujte značky, zásady přístupu JIT nebo spravovanou identitu.
DELETE | odstraňování | Jakmile uživatel zahájí odstranění instance spravované aplikace.
DELETE | Odstraněné | Po úplném a úspěšném odstranění spravované aplikace.
DELETE | Neúspěšný | Po jakékoli chybě během procesu zrušení zřízení, který blokování odstraní.
## <a name="notification-schema"></a>Schéma oznámení
Když nastavíte koncový bod Webhooku pro zpracování oznámení, budete muset analyzovat datovou část, abyste získali důležité vlastnosti, které pak budou fungovat na oznámení. Služba Service Catalog a Azure Marketplace oznámení o spravovaných aplikacích poskytují mnoho stejných vlastností. V tabulce, která následuje za ukázkami, jsou popsaný dva malé rozdíly.

#### <a name="service-catalog-application-notification-schema"></a>Schéma oznámení aplikace katalogu služeb
Tady je ukázkové oznámení katalogu služeb po úspěšném zřízení instance spravované aplikace:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
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
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
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

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace schéma oznámení aplikace

Tady je ukázkové oznámení katalogu služeb po úspěšném zřízení instance spravované aplikace:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
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
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
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
eventType | Typ události, která aktivovala oznámení. (Například PUT, PATCH, DELETE.)
applicationId | Plně kvalifikovaný identifikátor prostředku spravované aplikace, pro kterou bylo oznámení aktivované.
eventTime | Časové razítko události, která aktivovala oznámení (Datum a čas ve formátu UTC ISO 8601)
provisioningState | Stav zřizování instance spravované aplikace. (Například úspěch, selhalo, odstranění, odstraněno.)
error | *Zadané jenom v případě, že se provisioningState nezdařil*. Obsahuje kód chyby, zprávu a podrobnosti problému, který způsobil chybu.
applicationDefinitionId | *Určeno jenom pro aplikace spravované v katalogu služeb*. Představuje plně kvalifikovaný identifikátor prostředku definice aplikace, pro kterou se zřídila instance spravované aplikace.
rozhraní | *Určeno pouze pro Azure Marketplace spravované aplikace*. Představuje vydavatele, nabídku, SKU a verzi instance spravované aplikace.
billingDetails | *Určeno pouze pro Azure Marketplace spravované aplikace.* Údaje o fakturaci instance spravované aplikace. Obsahuje resourceUsageId, který můžete použít k dotazování Azure Marketplace v podrobnostech o využití.

## <a name="endpoint-authentication"></a>Ověřování koncového bodu
Zabezpečení koncového bodu Webhooku a ověření pravosti oznámení:
1. Zadejte parametr dotazu pro identifikátor URI Webhooku, například: https \: //Your-Endpoint.com? SIG = GUID. U každého oznámení ověřte, zda parametr dotazu `sig` má očekávanou hodnotu `Guid` .
2. Vystavte GET pro instanci spravované aplikace pomocí applicationId. Ověřte, že provisioningState odpovídá provisioningState oznámení, aby se zajistila konzistence.

## <a name="notification-retries"></a>Opakované pokusy o oznámení

Služba oznamování spravovaných aplikací očekává `200 OK` odpověď z koncového bodu Webhooku do oznámení. Služba Notification Service se zopakuje, pokud koncový bod Webhooku vrátí kód chyby HTTP větší nebo rovnou 500, pokud vrátí kód chyby 429 nebo pokud je koncový bod dočasně nedostupný. Pokud koncový bod Webhooku nebude k dispozici během 10 hodin, bude zpráva oznámení vyřazena a pokusy se zastaví.
