---
title: Modul s dvojím zápisem JSON – Azure
description: Toto téma popisuje modul s dvojitým formátem JSON pro živé video analýzy v IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8bd86bdc2c8de9ee586e785db2074fa772100420
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87053060"
---
# <a name="module-twin-json-schema"></a>Nevlákenný modul – schéma JSON

Vlákna zařízení jsou dokumenty JSON, které ukládají informace o stavu zařízení včetně metadat, konfigurací a podmínek. Azure IoT Hub udržuje takové dvojče pro každé zařízení, které připojíte k IoT Hubu. Podrobné vysvětlení naleznete v tématu [Principy a použití vláken v modulech v IoT Hub](../../iot-hub/iot-hub-devguide-module-twins.md)

Toto téma popisuje modul s dvojitým formátem JSON pro živé video analýzy v IoT Edge.

> [!NOTE]
> Abyste mohli získat autorizaci pro přístup k prostředkům a rozhraní API služby Media Services, musíte se nejprve ověřit. Další informace najdete v tématu [přístup k rozhraní Azure Media Services API](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Moduly s dvojitými vlastnostmi

Live video Analytics na IoT Edge zpřístupňuje následující funkční vlastnosti modulu. 

|Vlastnost |Požaduje se |Dynamická |Description |
|---|---|---|---|
|applicationDataDirectory |Yes |No |Cesta k připojenému svazku pro trvalou konfiguraci. |
|azureMediaServicesArmId |Yes |No |Jedinečný identifikátor správy prostředků Azure pro účet Media Services.|
|aadTenantId |Yes |No |ID tenanta Azure AD zákazníka.|
|aadServicePrincipalAppId |Yes |Yes |Zákazník vytvořil AppId Azure AD.|
|aadServicePrincipalCertificate |Ano<sup>*</sup>  |Yes |Zákazník vytvořil certifikát AppId Azure AD.|
|aadServicePrincipalPassword |Ano<sup>*</sup>  |Yes |Zákazník vytvořil heslo AppId Azure AD.|
|aadEndpoint |No |No |Koncový bod služby Azure AD specifický pro Cloud. <br/>Výchozí `https://login.microsoftonline.com` |
|aadResourceId |No |No |ID cílové skupiny nebo prostředku Azure AD konkrétního cloudu <br/>Výchozí `https://management.core.windows.net/` |
|armEndpoint |No |No |Koncový bod pro správu prostředků Azure specifický pro Cloud <br/>Výchozí `https://management.azure.com/` |
|diagnosticsLevel |No |Yes |Podrobnosti událostí: <br/>Informace &#x02758; Upozornění &#x02758; Chyba &#x02758; Kritické &#x02758; NTato |
|diagnosticsEventsOutputName |No |Yes |Výstup na rozbočovači pro diagnostické události <br/>(Prázdné znamená, že diagnostika nejsou publikována)|
|operationalEventsOutputName|No|Yes|Výstup centra pro provozní události<br/>(Prázdné znamená, že provozní události nejsou publikovány)
|logLevel|No|Yes|Jeden z následujících produktů: <br/>&#x000B7; Podrobné<br/>&#x000B7; Informace (výchozí)<br/>&#x000B7; Upozornění<br/>&#x000B7; Chyba<br/>&#x000B7; NTato|
|logCategories|No|Yes|Čárkami oddělený seznam následujících: aplikace, MediaPipeline, události <br/>Výchozí: aplikace, události|
|debugLogsDirectory|No|Yes|Adresář pro protokoly ladění. Pokud jsou generovány protokoly, nejsou-li přítomny protokoly ladění, jsou zakázány.

<sup>*</sup>Je nutné zadat buď certifikát zabezpečení služby, nebo heslo. 

Dynamické vlastnosti lze aktualizovat bez restartování modulu. Hodnoty pro několik těchto vlastností můžete získat podle pokynů v článku [získání přístupu k Media Services rozhraní API](../latest/access-api-howto.md) . 

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
