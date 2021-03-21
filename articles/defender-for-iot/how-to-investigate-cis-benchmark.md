---
title: Prozkoumat doporučení pro srovnávací testy modelu CI
titleSuffix: Azure Defender for IoT
description: Proveďte základní a pokročilé šetření na základě doporučení standardních hodnot operačního systému.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809756"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Doporučení pro zkoumání standardních hodnot operačního systému (na základě srovnávacího testu pro CI) 

Proveďte základní a pokročilé šetření na základě doporučení standardních hodnot operačního systému.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Základní zkoumání základních doporučení základního operačního systému  

Doporučení standardních hodnot operačního systému můžete prozkoumat tak, že přejdete na portál Azure Defender pro IoT v části **IoT Hub**. Další informace najdete v tématu Jak [prozkoumat doporučení zabezpečení](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Pokročilé zkoumání základních doporučení pro základní operační systémy  

Tato část popisuje, jak lépe pochopit výsledky testů standardních hodnot operačního systému a dotazování na události v Azure Log Analytics.  

Rozšířené zkoumání základního doporučení pro základní operační systém OS se podporuje jenom pomocí Log Analytics. Než budete pokračovat, připojte Defender pro IoT do Log Analytics pracovního prostoru. Další informace o pokročilých doporučeních pro zabezpečení standardních hodnot operačního systému najdete v tématu Jak [nakonfigurovat Azure Defender pro řešení založené na agentech IoT](how-to-configure-agent-based-solution.md).

Dotaz na události zabezpečení IoT v Log Analytics pro výstrahy:

1. Přejděte na stránku **výstrahy** .

1. Vyberte možnost **prozkoumat doporučení v pracovním prostoru Log Analytics**.

Dotaz na události zabezpečení IoT v Log Analytics pro doporučení:

1. Přejděte na stránku **doporučení** .

1. Vyberte možnost **prozkoumat doporučení v pracovním prostoru Log Analytics**.

1. Pokud chcete zobrazit podrobnosti konkrétního zařízení, vyberte na stránce rychlé zobrazení **podrobností doporučení** možnost **Zobrazit podrobnosti pravidel standardních hodnot operačního systému (OS)** .

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Podívejte se na podrobnosti konkrétního zařízení."::: 

Přímý dotaz na události zabezpečení IoT v pracovním prostoru Log Analytics:

1. Přejděte na stránku **protokoly** .

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="V levém podokně vyberte protokoly.":::

1. Vyberte **prozkoumat výstrahy** nebo vyberte možnost **prozkoumat výstrahy v Log Analytics** možnosti z jakéhokoli doporučení zabezpečení nebo výstrahy.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Užitečné dotazy k prozkoumání prostředků standardních hodnot operačního systému: 

> [!Note]
> `<device-id>`V každém z následujících dotazů se ujistěte, že jste nahradili názvy, které jste přiřadili k vašemu zařízení. 


### <a name="retrieve-the-latest-information"></a>Načíst nejnovější informace

- **Selhání loďstva zařízení**: spusťte následující dotaz, který načte nejnovější informace o kontrolách, které selhaly v rámci loďstva zařízení: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Konkrétní selhání zařízení** – spusťte následující dotaz, který načte nejnovější informace o kontrolách, které selhaly na konkrétním zařízení:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Konkrétní chyba zařízení** – spusťte tento dotaz, aby se načetly nejnovější informace o kontrolách, které obsahují chybu na konkrétním zařízení: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Aktualizujte seznam zařízení pro loďstvo zařízení, u kterého došlo k chybě konkrétní kontroly** – spusťte tento dotaz k načtení aktualizovaného seznamu zařízení (v rámci loďstva zařízení), u kterého došlo k chybě konkrétní kontroly:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Další kroky

[Prozkoumejte doporučení zabezpečení](quickstart-investigate-security-recommendations.md).
 