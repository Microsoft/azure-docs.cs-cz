---
title: Principy směrného plánu Centra zabezpečení Azure pro IoT| Dokumenty společnosti Microsoft
description: Přečtěte si o konceptu Centra zabezpečení Azure pro směrný plán IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72176623"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Centrum zabezpečení Azure pro směrné plány IoT a vlastní kontroly

Tento článek vysvětluje Azure Security Center pro směrný plán IoT a shrnuje všechny přidružené vlastnosti vlastníkontroly podle směrného plánu.

## <a name="baseline"></a>Standardní hodnoty

Směrný plán stanoví standardní chování pro každé zařízení a usnadňuje vytvoření neobvyklého chování nebo odchylky od očekávaných norem.  

## <a name="baseline-custom-checks"></a>Vlastní kontroly podle směrného plánu

Vlastní kontroly podle směrného plánu vytvoří vlastní seznam kontrol pro každý směrný plán zařízení pomocí **dvojčete identity modulu** zařízení. 

## <a name="setting-baseline-properties"></a>Nastavení vlastností směrného plánu

1. Ve svém Centru IoT Hub vyhledejte a vyberte zařízení, které chcete změnit.
1. Klikněte na zařízení a potom klikněte na modul **azureiotsecurity.**
1. Klepněte na **položku Twin identity modulu**.
1. Nahrajte soubor **vlastních kontrol směrného plánu** do zařízení.
1. Přidejte do modulu zabezpečení vlastnosti směrného plánu a klepněte na tlačítko **Uložit**.

### <a name="baseline-custom-check-file-example"></a>Příklad souboru vlastního kontrolního souboru podle směrného plánu

Postup konfigurace vlastních kontrol podle směrného plánu:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Vlastní vlastnosti kontroly podle směrného plánu

| Name (Název)| Status | Platné hodnoty| Výchozí hodnoty| Popis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Povinné: true |Platné hodnoty: **Logická hodnota** |Výchozí hodnota: **false** |Maximální časový interval před odesláním zpráv s vysokou prioritou.|
|baselineCustomChecksFilePath |Povinné: true|Platné hodnoty: **String**, **null** |Výchozí hodnota: **null** |Úplná cesta ke konfiguraci xml podle směrného plánu|
|baselineCustomChecksFileHash |Povinné: true|Platné hodnoty: **String**, **null** |Výchozí hodnota: **null** |`sha256sum`konfiguračního souboru XML. Pro další informace použijte [odkaz sha256sum.](https://linux.die.net/man/1/sha256sum) |

Chcete-li zkontrolovat další příklady směrného plánu, podívejte [se na vlastní příklad směrného plánu -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) a vlastní příklad [směrného plánu -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Další kroky

- Přístup k [nezpracovaným bezpečnostním datům](how-to-security-data-access.md)
- [Prozkoumání zařízení](how-to-investigate-device.md)
- Pochopení a zkoumání [bezpečnostních doporučení](concept-recommendations.md)
- Pochopení a prozkoumání [výstrah zabezpečení](concept-security-alerts.md)
