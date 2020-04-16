---
title: Aktivita webhooku v Azure Data Factory
description: Aktivita webhooku nepokračuje v provádění kanálu, dokud neověří připojenou datovou sadu s určitými kritérii, která uživatel zadá.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417874"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Aktivita webhooku v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Webhooku aktivity můžete řídit provádění kanálů prostřednictvím vlastního kódu. S aktivitou webhooku může kód zákazníků volat koncový bod a předat mu adresu URL zpětného volání. Spuštění kanálu čeká na vyvolání zpětného volání před pokračováním další aktivity.

## <a name="syntax"></a>Syntaxe

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
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
**Jméno** | Název aktivity webhooku. | Řetězec | Ano |
**Typ** | Musí být nastavena na "WebHook". | Řetězec | Ano |
**Metoda** | Metoda rozhraní REST API pro cílový koncový bod. | Řetězec. Podporovaný typ je "POST". | Ano |
**Adresu url** | Cílový koncový bod a cesta. | Řetězec nebo výraz s hodnotou **resultType** řetězce. | Ano |
**Záhlaví** | Hlavičky, které jsou odeslány do požadavku. Zde je příklad, který nastaví jazyk a `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`typ na žádost: . | Řetězec nebo výraz s hodnotou **resultType** řetězce. | Ano. Záhlaví `Content-Type` jako `"headers":{ "Content-Type":"application/json"}` je povinné. |
**Tělo** | Představuje datovou část, která je odeslána do koncového bodu. | Platný JSON nebo výraz s hodnotou **resultType** json. Schéma [datové části naleznete](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) v tématu Schéma datové části požadavku. | Ano |
**Ověřování** | Metoda ověřování používaná k volání koncového bodu. Podporované typy jsou "Basic" a "ClientCertificate". Další informace najdete v tématu [Ověřování](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Pokud ověřování není vyžadováno, vylučte tuto vlastnost. | Řetězec nebo výraz s hodnotou **resultType** řetězce. | Ne |
**timeout** | Jak dlouho aktivita čeká na zpětné volání zadané **callBackUri,** které mají být vyvolány. Výchozí hodnota je 10 minut ("00:10:00"). Hodnoty mají formát TimeSpan *d*. *hh*:*mm*:*ss*. | Řetězec | Ne |
**Stav sestavy při zpětném volání** | Umožňuje uživateli nahlásit neúspěšný stav aktivity webhooku. | Logická hodnota | Ne |

## <a name="authentication"></a>Authentication

Aktivita webhooku podporuje následující typy ověřování.

### <a name="none"></a>Žádná

Pokud ověřování není vyžadováno, nezahrnejte vlastnost **ověřování.**

### <a name="basic"></a>Základní

Zadejte uživatelské jméno a heslo, které se má použít při základním ověřování.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klientský certifikát

Zadejte obsah kódu Base64 souboru PFX a heslo.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Spravovaná identita

Pomocí spravované identity datové továrny určete identifikátor URI prostředku, pro který je požadován přístupový token. Chcete-li volat rozhraní API `https://management.azure.com/`pro správu prostředků Azure, použijte . Další informace o tom, jak spravované identity fungují, najdete v tématu [spravované identity pro prostředky Azure přehled](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Pokud je vaše továrna na data nakonfigurovaná pomocí úložiště Git, musíte uložit přihlašovací údaje do služby Azure Key Vault, abyste měli používat základní ověřování nebo ověřování klientského certifikátu. Azure Data Factory neukládá hesla v Gitu.

## <a name="additional-notes"></a>Další poznámky

Data Factory předá další vlastnost **callBackUri** v těle odeslané koncovému bodu URL. Data Factory očekává, že tento identifikátor URI bude vyvolán před zadanou hodnotou časového času. Pokud identifikátor URI není vyvolána, aktivita se nezdaří se stavem "TimedOut".

Webhook aktivita se nezdaří, když se nezdaří volání vlastního koncového bodu. Všechny chybové zprávy lze přidat do těla zpětného volání a použít v pozdější aktivity.

Pro každé volání rozhraní REST API klienta časový mat, pokud koncový bod nereaguje do jedné minuty. Toto chování je standardní http osvědčený postup. Chcete-li tento problém vyřešit, implementujte vzor 202. V aktuálním případě koncový bod vrátí 202 (Přijato) a dotazování klienta.

Časový čas jedné minuty na žádost nemá nic společného s časovým časem aktivity. Ten se používá k čekání na zpětné volání určené **callbackUri**.

Tělo předané zpět do identifikátoru URI zpětného volání musí být platné JSON. Nastavte `Content-Type` záhlaví `application/json`na .

Při použití **stav sestavy na zpětné volání** vlastnost, musíte přidat následující kód do těla při volání:

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

Podívejte se na následující aktivity toku řízení podporované factory:

- [Aktivita podmínky Když](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
