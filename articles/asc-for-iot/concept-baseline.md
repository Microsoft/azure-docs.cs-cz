---
title: Základní a vlastní kontroly
description: Přečtěte si o konceptu Azure Security Center pro směrný plán IoT.
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
ms.openlocfilehash: c52a3e55e3801eaaac885b9a3c364283f74906ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311645"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Azure Security Center pro směrný plán IoT a vlastní kontroly

Tento článek vysvětluje Azure Security Center pro směrný plán IoT a shrnuje všechny přidružené vlastnosti vlastních kontrol standardních hodnot.

## <a name="baseline"></a>Standardní hodnoty

Směrný plán vytvoří pro každé zařízení standardní chování a usnadňuje navázání neobvyklého chování nebo odchylky od očekávaných standardů.

## <a name="baseline-custom-checks"></a>Vlastní kontroly směrného plánu

Vlastní kontroly směrného plánu vytvoří vlastní seznam kontrol pro každé standardní zařízení pomocí **nevlákenné identity modulu** v zařízení.

## <a name="setting-baseline-properties"></a>Nastavení vlastností směrného plánu

1. V IoT Hub vyhledejte a vyberte zařízení, které chcete změnit.
1. Klikněte na zařízení a pak klikněte na modul **azureiotsecurity** .
1. Klikněte na možnost **Nevlákenovaná identita modulu**.
1. Nahrajte do zařízení soubor **vlastních kontrol standardních kontrol** .
1. Přidejte do modulu zabezpečení vlastnosti směrného plánu a klikněte na **Uložit**.

### <a name="baseline-custom-check-file-example"></a>Příklad souboru vlastní kontrolní kontroly směrného plánu

Konfigurace vlastních kontrol standardních hodnot:

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

## <a name="baseline-custom-check-properties"></a>Vlastnosti vlastní kontroly směrného plánu

| Name| Status | Platné hodnoty| Výchozí hodnoty| Popis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Požadováno: true |Platné hodnoty: **logická hodnota** |Výchozí hodnota: **false** |Maximální časový interval před odesláním zpráv s vysokou prioritou.|
|baselineCustomChecksFilePath |Požadováno: true|Platné hodnoty: **řetězec**, **hodnota null** |Výchozí hodnota: **null** |Úplná cesta k základní konfiguraci XML|
|baselineCustomChecksFileHash |Požadováno: true|Platné hodnoty: **řetězec**, **hodnota null** |Výchozí hodnota: **null** |`sha256sum`konfiguračního souboru XML. Další informace najdete v [referenčních](https://linux.die.net/man/1/sha256sum) informacích k sha256sum. |

Další příklady standardních hodnot najdete v tématu [vlastní standardní hodnoty – příklad 1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) a [vlastní směrný plán – 2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Další kroky

- Přístup k [nezpracovaným datům zabezpečení](how-to-security-data-access.md)
- [Prozkoumání zařízení](how-to-investigate-device.md)
- Pochopení a zkoumání [doporučení zabezpečení](concept-recommendations.md)
- Pochopení a zkoumání [výstrah zabezpečení](concept-security-alerts.md)
