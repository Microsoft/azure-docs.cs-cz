---
title: Aktivita Webhooku v Azure Data Factory
description: Aktivita Webhooku nepokračuje v provádění kanálu, dokud neověřuje připojenou datovou sadu s určitými kritérii, které uživatel zadá.
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
ms.openlocfilehash: 8c52bb21276071581a83fb3ee6a3a4a31ba0bb4a
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78400001"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Aktivita Webhooku v Azure Data Factory
Aktivitu Webhooku můžete použít k řízení provádění kanálů pomocí vlastního kódu. Pomocí aktivity Webhooku můžou zákazníci zavolat koncový bod a předat URL zpětného volání. Běh kanálu čeká na vyvolání zpětného volání před pokračováním na další aktivitu.

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
jméno | Název aktivity webového zavěšení | Řetězec | Ano |
type | Musí být nastavené na **Webhook**. | Řetězec | Ano |
method | Metoda rozhraní REST API pro cílový koncový bod | Řetězec. Podporované typy: POST | Ano |
url | Cílový koncový bod a cesta | Řetězec (nebo výraz s hodnotou resultType řetězce). | Ano |
záhlaví | Hlavičky, které se odesílají do žádosti Například chcete-li nastavit jazyk a typ pro požadavek: "hlavičky": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ano, hlavička Content-Type je povinná. "Headers": {"Content-Type": "Application/JSON"} |
těles | Představuje datovou část, která je odeslána do koncového bodu. | Platný kód JSON (nebo výraz s hodnotou resultType JSON) Podívejte se na schéma datové části požadavku v části [schéma datové části požadavku](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Ano |
ověřování | Metoda ověřování používaná pro volání koncového bodu. Podporované typy jsou "Basic" nebo "ClientCertificate". Další informace najdete v části [ověřování](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) . Pokud není vyžadováno ověření, vylučte tuto vlastnost. | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ne |
timeout | Jak dlouho bude aktivita čekat na vyvolání &#39;callBackUri&#39; . Doba, po kterou bude aktivita čekat na vyvolání callBackUri Výchozí hodnota je 10mins ("00:10:00"). Formát je časový rozsah, tj. d. hh: mm: ss. | Řetězec | Ne |
Vykázat stav při zpětném volání | Umožňuje uživateli nahlásit neúspěšný stav aktivity Webhooku, který označí aktivitu jako neúspěšnou. | Logická hodnota | Ne |

## <a name="authentication"></a>Ověřování

Níže jsou uvedené podporované typy ověřování v aktivitě Webhooku.

### <a name="none"></a>Žádná

Pokud není vyžadováno ověřování, nezahrnujte vlastnost "ověřování".

### <a name="basic"></a>Basic

Zadejte uživatelské jméno a heslo, které chcete použít se základním ověřováním.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certifikát klienta

Zadejte obsah souboru PFX a hesla zakódovaného ve formátu base64.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Spravovaná identita

Zadejte identifikátor URI prostředku, pro který bude přístupový token vyžádán pomocí spravované identity pro datovou továrnu. K volání rozhraní API pro správu prostředků Azure použijte `https://management.azure.com/`. Další informace o tom, jak spravované identity fungují, najdete na [stránce s přehledem spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Pokud je vaše Datová továrna nakonfigurovaná s úložištěm Git, musíte přihlašovací údaje uložit v Azure Key Vault pro použití základního nebo ověřování certifikátu klienta. Azure Data Factory neukládá hesla v Gitu.

## <a name="additional-notes"></a>Další poznámky

Azure Data Factory předáte další vlastnost "callBackUri" v těle koncového bodu adresy URL a očekává, že tento identifikátor URI bude vyvolán před zadanou hodnotou časového limitu. Pokud identifikátor URI není vyvolán, aktivita se nezdaří se stavem "vypršela platnost".

Aktivita Webhooku se nezdařila, pokud volání vlastního koncového bodu neproběhne úspěšně. Do textu zpětného volání lze přidat jakoukoli chybovou zprávu a použít ji v následné aktivitě.

Pro každé REST API volání bude klient v případě, že koncový bod neodpoví za 1 min, vyprší časový limit. Jedná se o standardní osvědčené postupy protokolu HTTP. Pokud chcete tento problém vyřešit, musíte v takovém případě implementovat vzor 202, kde koncový bod vrátí hodnotu 202 (přijato) a klient se dotazuje.

1 min. časový limit žádosti neobsahuje žádné aktivity s časovým limitem aktivity. Který bude použit k čekání na callbackUri.

Tělo předané zpět do identifikátoru URI zpětného volání by mělo mít platný formát JSON. Je nutné nastavit hlavičku Content-Type na `application/json`.

Použijete-li možnost "stav sestavy při zpětném volání", je nutné při provádění zpětného volání přidat následující fragment kódu do textu:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Další kroky

Podívejte se na další aktivity toku řízení podporované Data Factory:

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
