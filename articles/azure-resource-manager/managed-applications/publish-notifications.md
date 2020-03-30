---
title: Spravované aplikace s oznámeními
description: Nakonfigurujte spravované aplikace s koncovými body webhooku tak, aby přijímali oznámení o vytváření, aktualizacích, odstraněních a chybách v instancích spravovaných aplikací.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715671"
---
# <a name="azure-managed-applications-with-notifications"></a>Spravované aplikace Azure s oznámeními

Oznámení spravovaných aplikací Azure umožňují vydavatelům automatizovat akce na základě událostí životního cyklu instancí spravovaných aplikací. Vydavatelé mohou určit vlastní koncové body webového háku oznámení pro příjem oznámení událostí o nových a existujících instancích spravovaných aplikací. Vydavatelé mohou nastavit vlastní pracovní postupy v době zřizování, aktualizací a odstranění aplikací.

## <a name="getting-started"></a>Začínáme
Chcete-li začít přijímat spravované aplikace, spusťte veřejný koncový bod HTTPS a zadejte ho při publikování definice aplikace katalogu služeb nebo nabídky Azure Marketplace.

Zde jsou doporučené kroky, jak rychle začít:
1. Stočte veřejný koncový bod HTTPS, který zaznamenává příchozí `200 OK`požadavky POST a vrátí .
2. Přidejte koncový bod do definice aplikace katalogu služeb nebo nabídky Azure Marketplace, jak je vysvětleno dále v tomto článku.
3. Vytvořte instanci spravované aplikace, která odkazuje na definici aplikace nebo nabídku Azure Marketplace.
4. Ověřte, zda jsou přijímána oznámení.
5. Povolte autorizaci, jak je vysvětleno v části **Ověřování koncového bodu** v tomto článku.
6. Postupujte podle pokynů v části **oznámení schématu** tohoto článku analyzovat žádosti o oznámení a implementovat obchodní logiku na základě oznámení.

## <a name="add-service-catalog-application-definition-notifications"></a>Přidání oznámení definice aplikace katalogu služeb
#### <a name="azure-portal"></a>portál Azure
Další informace najdete [v tématu Publikování aplikace katalogu služeb prostřednictvím portálu Azure](./publish-portal.md).

![Oznámení definice aplikace katalogu služeb na webu Azure Portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> V současné době můžete zadat pouze `notificationEndpoints` jeden koncový bod ve vlastnostech definice aplikace.

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Přidání oznámení o spravovaných aplikacích Azure Marketplace
Další informace najdete [v tématu Vytvoření nabídky aplikace Azure](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Oznámení o spravovaných aplikacích Azure Marketplace na webu Azure Portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Aktivační události
Následující tabulka popisuje všechny možné kombinace EventType a ProvisioningState a jejich aktivační události:

Typ události | ZřizováníStát | Aktivační událost pro oznámení
---|---|---
PUT | Accepted | Skupina spravovaných prostředků byla vytvořena a úspěšně promítnuta po zahájení nasazení v rámci spravované skupiny prostředků.
PUT | Úspěch | Úplné zřizování spravované aplikace proběhlo úspěšně po put.
PUT | Failed | Selhání zřizování instance aplikace V libovolném bodě.
Oprava | Úspěch | Po úspěšné patch na spravovanou instanci aplikace aktualizovat značky, zásady přístupu JIT nebo spravované identity.
DELETE | Odstranění | Jakmile uživatel zahájí odstranění instance spravované aplikace.
DELETE | Odstranění | Po úplném a úspěšném odstranění spravované aplikace.
DELETE | Failed | Po jakékoli chybě během procesu zrušení zřízení, který blokuje odstranění.
## <a name="notification-schema"></a>Schéma oznámení
Při zatočení koncového bodu webhooku pro zpracování oznámení, budete muset analyzovat datovou část získat důležité vlastnosti pak jednat na oznámení. Katalog služeb a oznámení spravovaných aplikací Azure Marketplace poskytují mnoho stejných vlastností. Dva malé rozdíly jsou uvedeny v tabulce, která následuje vzorky.

#### <a name="service-catalog-application-notification-schema"></a>Schéma oznámení aplikace katalogu služeb
Tady je ukázkové oznámení katalogu služeb po úspěšném zřizování instance spravované aplikace:
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

Pokud se zřizování nezdaří, bude odesláno oznámení s podrobnostmi o chybě zadanému koncovému bodu.

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

#### <a name="azure-marketplace-application-notification-schema"></a>Schéma oznámení aplikací Azure Marketplace

Tady je ukázkové oznámení katalogu služeb po úspěšném zřizování instance spravované aplikace:
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

Pokud se zřizování nezdaří, bude odesláno oznámení s podrobnostmi o chybě zadanému koncovému bodu.

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
Eventtype | Typ události, která spustila oznámení. (Například PUT, PATCH, DELETE.)
applicationId | Plně kvalifikovaný identifikátor prostředku spravované aplikace, pro kterou bylo oznámení spuštěno.
eventTime | Časové razítko události, která spustila oznámení. (Datum a čas ve formátu UTC ISO 8601.)
provisioningState | Stav zřizování instance spravované aplikace. (Například Úspěšné, Neúspěšné, Odstranění, Odstraněno.)
error | *Zadáno pouze v případě, že zřizováníState se nezdařilo*. Obsahuje kód chyby, zprávu a podrobnosti o problému, který způsobil selhání.
applicationDefinitionId | *Určeno pouze pro aplikace spravované katalogem služeb*. Představuje plně kvalifikovaný identifikátor prostředku definice aplikace, pro kterou byla zřízena instance spravované aplikace.
Plán | *Určeno pouze pro spravované aplikace Azure Marketplace*. Představuje vydavatele, nabídku, skladovou položku a verzi instance spravované aplikace.
billingDetails | *Určeno pouze pro spravované aplikace Azure Marketplace.* Podrobnosti o fakturaci instance spravované aplikace. Obsahuje resourceUsageId, které můžete použít k dotazování Azure Marketplace pro podrobnosti o využití.

## <a name="endpoint-authentication"></a>Ověřování koncového bodu
Chcete-li zabezpečit koncový bod webhooku a zajistit pravost oznámení:
1. Zadejte parametr dotazu nad identifikátorem URI webhooku, například: https\://your-endpoint.com?sig=Guid. U každého oznámení zkontrolujte, `sig` zda má `Guid`parametr dotazu očekávanou hodnotu .
2. Vydat GET na instanci spravované aplikace pomocí applicationId. Ověřte, zda zřizovací stav odpovídá zřizováníStav oznámení k zajištění konzistence.

## <a name="notification-retries"></a>Opakování oznámení

Služba oznámení spravované aplikace `200 OK` očekává odpověď z koncového bodu webhooku na oznámení. Služba oznámení se vrátí, pokud koncový bod webhooku vrátí kód chyby HTTP větší nebo roven 500, pokud vrátí kód chyby 429 nebo pokud je koncový bod dočasně nedostupný. Pokud koncový bod webhooku nebude k dispozici do 10 hodin, oznámení se vypustí a opakování se zastaví.
