---
title: Základní a vlastní kontroly
description: Přečtěte si o konceptu Azure Defenderu pro směrný plán IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: d97fa4c3c57f6f0dcc5c55b76d839308156c40fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936297"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender pro službu IoT – směrný plán a vlastní kontroly

Tento článek vysvětluje Defender pro směrný plán IoT a shrnuje všechny přidružené vlastnosti vlastních kontrol standardních hodnot.

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

| Name| Status | Platné hodnoty| Výchozí hodnoty| Description |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Požadováno: true |Platné hodnoty: **logická hodnota** |Výchozí hodnota: **false** |Maximální časový interval před odesláním zpráv s vysokou prioritou.|
|baselineCustomChecksFilePath |Požadováno: true|Platné hodnoty: **řetězec**, **hodnota null** |Výchozí hodnota: **null** |Úplná cesta k základní konfiguraci XML|
|baselineCustomChecksFileHash |Požadováno: true|Platné hodnoty: **řetězec**, **hodnota null** |Výchozí hodnota: **null** |`sha256sum` konfiguračního souboru XML. Další informace najdete v [referenčních](https://linux.die.net/man/1/sha256sum) informacích k sha256sum. |

Další příklady standardních hodnot najdete v tématu [vlastní standardní hodnoty – příklad 1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) a [vlastní směrný plán – 2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Další kroky

- Přístup k [nezpracovaným datům zabezpečení](how-to-security-data-access.md)
- [Prozkoumání zařízení](how-to-investigate-device.md)
- Pochopení a zkoumání [doporučení zabezpečení](concept-recommendations.md)
- Pochopení a zkoumání [výstrah zabezpečení](concept-security-alerts.md)
