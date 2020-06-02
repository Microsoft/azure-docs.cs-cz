---
title: Modul s dvojím zápisem JSON – Azure
description: Toto téma popisuje modul s dvojitým formátem JSON pro živé video analýzy v IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a342c59b35c7ebb4b6021163da76bdd3e0d449c3
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266810"
---
# <a name="module-twin-json-schema"></a>Nevlákenný modul – schéma JSON

Vlákna zařízení jsou dokumenty JSON, které ukládají informace o stavu zařízení včetně metadat, konfigurací a podmínek. Azure IoT Hub udržuje takové dvojče pro každé zařízení, které připojíte k IoT Hubu. Podrobné vysvětlení naleznete v tématu [Principy a použití vláken v modulech v IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)

Toto téma popisuje modul s dvojitým formátem JSON pro živé video analýzy v IoT Edge.

> [!NOTE]
> Aby bylo možné získat přístup k Media Services prostředkům a rozhraní Media Services API, musíte nejdřív ověřit. Další informace najdete v tématu [přístup k rozhraní Azure Media Services API](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Moduly s dvojitými vlastnostmi

Live video Analytics na IoT Edge zpřístupňuje následující funkční vlastnosti modulu. 

|Vlastnost |Požaduje se |Dynamická |Popis |
|---|---|---|---|
|applicationDataDirectory |Ano |Ne |Cesta k připojenému svazku pro trvalou konfiguraci. |
|azureMediaServicesArmId |Ano |Ne |Jedinečný identifikátor správy prostředků Azure pro účet Media Services.|
|aadTenantId |Ano |Ne |ID tenanta Azure AD zákazníka.|
|aadServicePrincipalAppId |Ano |Ano |Zákazník vytvořil AppId Azure AD.|
|aadServicePrincipalCertificate |Ano<sup>*</sup>  |Ano |Zákazník vytvořil certifikát AppId Azure AD.|
|aadServicePrincipalPassword |Ano<sup>*</sup>  |Ano |Zákazník vytvořil heslo AppId Azure AD.|
|aadEndpoint |Ne |Ne |Koncový bod služby Azure AD specifický pro Cloud. <br/>Výchozí`https://login.microsoftonline.com` |
|aadResourceId |Ne |Ne |ID cílové skupiny nebo prostředku Azure AD konkrétního cloudu <br/>Výchozí`https://management.core.windows.net/` |
|armEndpoint |Ne |Ne |Koncový bod pro správu prostředků Azure specifický pro Cloud <br/>Výchozí`https://management.azure.com/` |
|diagnosticsLevel |No |Ano |Podrobnosti událostí: <br/>Informace & # x02758; Upozornění & # x02758; Chyba & # x02758; Důležité & # x02758; NTato |
|diagnosticsEventsOutputName |No |Ano |Výstup na rozbočovači pro diagnostické události <br/>(Prázdné znamená, že diagnostika nejsou publikována)|
|operationalEventsOutputName|No|Ano|Výstup centra pro provozní události<br/>(Prázdné znamená, že provozní události nejsou publikovány)
|logLevel|No|Ano|Jeden z následujících produktů: <br/>& # x000B7; Podrobné<br/>& # x000B7; Informace (výchozí)<br/>& # x000B7; Upozornění<br/>& # x000B7; Chyba<br/>& # x000B7; NTato|
|logCategories|No|Ano|Čárkami oddělený seznam následujících: aplikace, MediaPipeline, události <br/>Výchozí: aplikace, události|
|debugLogsDirectory|No|Ano|Adresář pro protokoly ladění. Pokud jsou generovány protokoly, nejsou-li přítomny protokoly ladění, jsou zakázány.

<sup>*</sup>Je nutné zadat buď certifikát zabezpečení služby, nebo heslo. 

Dynamické vlastnosti lze aktualizovat bez restartování modulu. Hodnoty pro několik těchto vlastností můžete získat podle pokynů v článku [získání přístupu k Media Services rozhraní API](../latest/access-api-cli-how-to.md) . 

Další informace o roli volitelného nastavení diagnostiky najdete v článku [monitorování a protokolování](monitoring-logging.md) .

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Další kroky

[Přímé metody](direct-methods.md)
