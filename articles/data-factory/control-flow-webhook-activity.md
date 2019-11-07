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
ms.openlocfilehash: 3734472d9026d4e355c08b36d5ba58974288daac
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678226"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Aktivita Webhooku v Azure Data Factory
Aktivitu webového zavěšení můžete použít k řízení provádění kanálů pomocí vlastního kódu. Pomocí aktivity Webhooku můžou zákazníci zavolat koncový bod a předat URL zpětného volání. Běh kanálu čeká na vyvolání zpětného volání před pokračováním na další aktivitu.

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
method | Metoda rozhraní REST API pro cílový koncový bod | řetezce. Podporované typy: POST | Ano |
url | Cílový koncový bod a cesta | Řetězec (nebo výraz s hodnotou resultType řetězce). | Ano |
Záhlaví | Hlavičky, které se odesílají do žádosti Například chcete-li nastavit jazyk a typ pro požadavek: "hlavičky": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ano, hlavička Content-Type je povinná. "Headers": {"Content-Type": "Application/JSON"} |
těles | Představuje datovou část, která je odeslána do koncového bodu. | Tělo předané zpět do identifikátoru URI zpětného volání by mělo být platný kód JSON. Podívejte se na schéma datové části požadavku v části [schéma datové části požadavku](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Ano |
ověřování | Metoda ověřování používaná pro volání koncového bodu. Podporované typy jsou "Basic" nebo "ClientCertificate". Další informace najdete v části [ověřování](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) . Pokud není vyžadováno ověření, vylučte tuto vlastnost. | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ne |
timeout | Jak dlouho bude aktivita čekat na vyvolání &#39;callBackUri&#39; . Doba, po kterou bude aktivita čekat na vyvolání callBackUri Výchozí hodnota je 10mins ("00:10:00"). Formát je časový rozsah, tj. d. hh: mm: ss. | Řetězec | Ne |

## <a name="additional-notes"></a>Další poznámky

Azure Data Factory předáte další vlastnost "callBackUri" v těle koncového bodu adresy URL a očekává, že tento identifikátor URI bude vyvolán před zadanou hodnotou časového limitu. Pokud identifikátor URI není vyvolán, aktivita se nezdaří se stavem "vypršela platnost".

Aktivita webového zavěšení se nezdařila pouze v případě, že volání vlastního koncového bodu se nepovede. Do textu zpětného volání lze přidat jakoukoli chybovou zprávu a použít ji v následné aktivitě.

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované Data Factory:

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
