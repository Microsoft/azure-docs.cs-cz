---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 54f4835a904b897370cf9f075ae3c005b1114992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557287"
---
[Získání stavu koncového bodu](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) v REST API poskytuje stav koncových bodů a také poslední známou chybu k identifikaci důvodu, proč koncový bod není v pořádku. V tabulce níže jsou uvedeny nejběžnější chyby.

|Poslední známá chyba|Popis/kdy k ní dojde|Možné zmírnění|
|-----|-----|-----|
|Dočasný|Došlo k přechodné chybě a IoT Hub operaci zopakovat.|Sledujte [protokoly prostředků tras](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|InternalError|Při doručování zprávy do koncového bodu došlo k chybě.|Jedná se o interní výjimku, ale také Sledujte [protokoly prostředků směrování](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|Neautorizováno|IoT Hub není autorizován pro posílání zpráv do zadaného koncového bodu.|Ověřte, zda je připojovací řetězec pro koncový bod aktuální. Pokud se změní, zvažte aktualizaci na IoT Hub. Pokud koncový bod používá spravovanou identitu, ověřte, že objekt zabezpečení IoT Hub má požadovaná oprávnění pro cíl.|
|Omezené|Při zápisu zpráv do koncového bodu se omezuje IoT Hub.|Zkontrolujte omezení omezení pro ovlivněný koncový bod. V případě potřeby upravte konfigurace koncového bodu pro horizontální navýšení kapacity.|
|Časový limit|Časový limit operace.|Zkuste operaci zopakovat.|
|Nenalezeno|Cílový prostředek neexistuje.|Ujistěte se, že cílový prostředek existuje.|
|Kontejner se nenašel.|Kontejner úložiště neexistuje.|Ujistěte se, že kontejner úložiště existuje.|
|Kontejner zakázán|Kontejner úložiště je zakázaný.|Ujistěte se, že je kontejner úložiště povolený.|
|MaxMessageSizeExceeded|Směrování zpráv má omezení velikosti zprávy 256Kb. Tato velikost zprávy překročila tento limit.|Ověřte, zda lze velikost zprávy zmenšit pomocí menšího počtu vlastností aplikace nebo méně rozšíření zprávy.|
|PartitioningAndDuplicateDetectionNotSupported|Služba Service Bus nemůže mít povolenou detekci duplicit.|Zakáže detekci duplicit z Service Bus nebo zvažte použití entity bez zjištění duplicit.|
|SessionfulEntityNotSupported|Služba Service Bus pravděpodobně nemá povoleny relace.|Zakáže relaci z Service Bus nebo zvažte použití entity bez relací.|
|NoMatchingSubscriptionsForMessage|V tématu služby Service Bus není žádné předplatné pro zápis zprávy.|Vytvořte předplatné pro IoT Hub zpráv, na které se mají směrovat.|
|EndpointExternallyDisabled|Koncový bod není v aktivním stavu, takže IoT Hub může do něj odesílat zprávy.|Povolí koncovému bodu vrátit ho zpátky do aktivního stavu.|
|DeviceMaximumQueueDepthExceeded|Bylo dosaženo limitu velikosti služby Service Bus.|Zvažte odebrání zpráv z cílového Event Hubs, aby bylo možné do Event Hubs ingestovat nové zprávy.|