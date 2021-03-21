---
title: Aktivita Webhooku v Azure Data Factory
description: Aktivita Webhooku nepokračuje v provádění kanálu, dokud neověřuje připojenou datovou sadu s určitými kritérii, které uživatel zadá.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 59aa395db27c26a7c94eebdc0e3b34d7776ee75f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591992"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Aktivita Webhooku v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita Webhooku může řídit provádění kanálů pomocí vlastního kódu. V případě aktivity Webhooku může kód zákazníka zavolat koncovému bodu a předat mu adresu URL zpětného volání. Běh kanálu počká na vyvolání zpětného volání, než pokračuje na další aktivitu.

> [!IMPORTANT]
> Aktivita Webhooku teď umožňuje surfovat stav chyb a vlastní zprávy zpátky do aktivity a kanálu. Nastavte _reportStatusOnCallBack_ na true a do datové části zpětného volání zahrňte _StatusCode_ a _Error_ . Další informace najdete v části [Další poznámky](#additional-notes) .

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
**name** | Název aktivity Webhooku. | Řetězec | Yes |
**textový** | Musí být nastavené na Webhook. | Řetězec | Yes |
**Metoda** | Metoda REST API pro cílový koncový bod. | Řetězec. Podporovaný typ je POST. | Yes |
**Adresa URL** | Cílový koncový bod a cesta. | Řetězec nebo výraz s hodnotou **ResultType** řetězce. | Yes |
**záhlaví** | Hlavičky, které se odesílají do žádosti Tady je příklad, který nastaví jazyk a typ v žádosti: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Řetězec nebo výraz s hodnotou **ResultType** řetězce. | Ano. `Content-Type` `"headers":{ "Content-Type":"application/json"}` Požaduje se záhlaví. |
**těles** | Představuje datovou část, která je odeslána do koncového bodu. | Platný JSON nebo výraz s hodnotou **RESULTTYPE** JSON. Schéma datové části požadavku najdete v tématu [schéma datové části požadavku](./control-flow-web-activity.md#request-payload-schema) . | Yes |
**ověřování** | Metoda ověřování použitá pro volání koncového bodu. Podporované typy jsou "základní" a "ClientCertificate". Další informace najdete v tématu [Ověřování](./control-flow-web-activity.md#authentication). Pokud se ověřování nepožaduje, vylučte tuto vlastnost. | Řetězec nebo výraz s hodnotou **ResultType** řetězce. | No |
**timeout** | Jak dlouho aktivita čeká na vyvolání zpětného volání zadaného parametrem **callBackUri** . Výchozí hodnota je 10 minut ("00:10:00"). Hodnoty mají formát TimeSpan *d*. *HH*:*mm*:*SS*. | Řetězec | No |
**Vykázat stav při zpětném volání** | Umožňuje uživateli ohlásit stav selhání aktivity Webhooku. | Logická hodnota | No |

## <a name="authentication"></a>Authentication

Aktivita Webhooku podporuje následující typy ověřování.

### <a name="none"></a>Žádné

Pokud se ověřování nepožaduje, nezahrnujte vlastnost **ověřování** .

### <a name="basic"></a>Basic

Zadejte uživatelské jméno a heslo, které chcete použít pro základní ověřování.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certifikát klienta

Zadejte obsah souboru PFX v kódování Base64 a heslo.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Spravovaná identita

Pomocí spravované identity objektu pro vytváření dat určete identifikátor URI prostředku, pro který je požadován přístupový token. K volání rozhraní API pro správu prostředků Azure použijte `https://management.azure.com/` . Další informace o tom, jak spravované identity fungují, najdete v tématu [Přehled prostředků spravovaných identit pro Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Pokud je vaše Datová továrna nakonfigurovaná s úložištěm Git, musíte přihlašovací údaje uložit v Azure Key Vault, abyste mohli používat základní ověřování nebo ověřování klientského certifikátu. Azure Data Factory neukládá hesla v Gitu.

## <a name="additional-notes"></a>Další poznámky

Data Factory předá další vlastnost **callBackUri** v těle odeslání na koncový bod adresy URL. Data Factory očekává, že se tento identifikátor URI vyvolá před určenou hodnotou časového limitu. Pokud není identifikátor URI vyvolán, aktivita se nezdařila se stavem "časované vyprší".

Aktivita Webhooku se nezdařila, pokud volání vlastního koncového bodu neproběhne úspěšně. Do těla zpětného volání lze přidat jakoukoli chybovou zprávu a použít ji v pozdější aktivitě.

U každého volání REST API vyprší časový limit, pokud koncový bod neodpoví během jedné minuty. Toto chování je osvědčeným postupem standardu HTTP. Chcete-li tento problém vyřešit, implementujte vzor 202. V aktuálním případě koncový bod vrátí 202 (přijato) a dotaz na klienta.

Časový limit minut na žádosti nemá žádnou akci s časovým limitem aktivity. Druhý se používá pro čekání na zpětné volání určené parametrem **callbackUri**.

Tělo předané zpět do identifikátoru URI zpětného volání musí být platný kód JSON. Nastavte `Content-Type` hlavičku na `application/json` .

Použijete-li vlastnost **stav vlastnosti zpětného volání** , je nutné do těla při zpětném volání přidat následující kód:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se na následující aktivity toku řízení podporované Data Factory:

- [Aktivita podmínky Když](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)