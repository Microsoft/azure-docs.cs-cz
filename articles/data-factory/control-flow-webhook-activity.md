---
title: Webhook aktivity ve službě Azure Data Factory | Dokumentace Microsoftu
description: Aktivita Webhooku pokračovat spuštění kanálu, dokud ověří připojené datové sady s určitá kritéria, který uživatel zadá.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 0b8b892f02e54c3b0ddb155af97ce63ff115bb1f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522904"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook aktivity ve službě Azure Data Factory
Zavěšení aktivitu webu můžete použít k řízení provádění kanálů přes váš vlastní kód. Použití webhooku aktivity, zákazníci volání koncového bodu a předat adresu URL zpětného volání. Spuštění kanálu čeká zpětné volání, které má být volána před pokračováním na další aktivitu.

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


## <a name="type-properties"></a>Typ vlastnosti



Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název aktivity hook webu | String | Ano |
type | Musí být nastaveno na **Webhooku**. | String | Ano |
method | Metoda REST API pro cílový koncový bod. | řetězec. Podporované typy: "POST" | Ano |
url | Cílový koncový bod a cesty | Řetězec (nebo výraz s hodnotu resultType řetězec). | Ano |
Záhlaví | Hlavičky, které se odesílají na požadavek. Například nastavení jazyka a typu na vyžádání: "záhlaví": {"Accept-Language": "en-us", "Content-Type": "application/json"}. | Řetězec (nebo výraz s hodnotu resultType řetězec) | Ano, vyžaduje se hlavička Content-type. "záhlaví": {"Content-Type": "application/json"} |
hlavní část | Představuje datovou část, která je odeslána do koncového bodu. | Tělo předán zpět do zpětného volání identifikátoru URI by měl být platným formátem JSON. Zobrazit schéma datové části požadavku v [schématu datové části požadavku](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) oddílu. | Ano |
Ověřování | Metodu ověřování pro volání koncového bodu. Podporované typy jsou "Základní" nebo "ClientCertificate." Další informace najdete v tématu [ověřování](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) oddílu. Pokud se nevyžaduje ověřování, vylučte tuto vlastnost. | Řetězec (nebo výraz s hodnotu resultType řetězec) | Ne |
timeout | Jak dlouho bude čekat na aktivitu &#39;zpětného&#39; má být volána. Jak dlouho aktivity bude čekat "zpětného' má být volána. Výchozí hodnota je 10mins ("00:10:00"). Format is Timespan i.e. d.hh:mm:ss | String | Ne |

## <a name="additional-notes"></a>Další poznámky

Azure Data Factory předá další vlastnost "zpětného" v textu koncový bod adresy url a bude očekávat tento identifikátor uri, který má být volána před zadaná hodnota časového limitu. Pokud není vyvolána, identifikátor uri, aktivita selže se stavem "Vypršel časový limit".

Aktivita webu hook samotné selže, pouze, pokud selže volání vlastního koncového bodu. Jakákoli chybová zpráva lze přidávat do datové části zpětného volání a použít v následných aktivity.

## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory:

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
