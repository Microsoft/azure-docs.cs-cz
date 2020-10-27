---
title: Export metrik konektoru Azure IoT pro FHIR (Preview) přes nastavení diagnostiky
description: Tento článek vysvětluje, jak exportovat službu Azure IoT Connector pro FHIR (Preview) metriky prostřednictvím nastavení diagnostiky.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/16/2020
ms.author: jasteppe
ms.openlocfilehash: d7779c74a562e1237db863d7759b2adcffa2bddf
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558546"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Export metrik konektoru Azure IoT pro FHIR (Preview) přes nastavení diagnostiky

V tomto článku se dozvíte, jak exportovat protokoly metrik služby Azure IoT Connector pro FHIR *. Funkce, která umožňuje protokolování metriky, je [**nastavení diagnostiky**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) v Azure Portal. 

> [!TIP]
> Podle pokynů v části [Povolení protokolování diagnostiky v rozhraní Azure API pro FHIR a Azure IoT Connector pro FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) nastavte protokolování auditu.

## <a name="enable-metric-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Povolení protokolování metrik pro Azure IoT Connector pro FHIR (Preview)
1. Pokud chcete povolit protokolování metrik pro Azure IoT Connector pro FHIR, vyberte rozhraní Azure API pro službu FHIR v Azure Portal 

2. Přejít na **nastavení diagnostiky** 

3. Vybrat **+ Přidat nastavení diagnostiky**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. V dialogovém okně **název nastavení diagnostiky** zadejte název.

5. Vyberte metodu, kterou chcete použít pro přístup k diagnostickým protokolům:

    1. **Archivujte do účtu úložiště** pro auditování nebo ruční kontrolu. Účet úložiště, který chcete použít, musí být již vytvořen.
    2. **Streamování do centra událostí** pro ingestování prostřednictvím služby třetí strany nebo vlastního analytického řešení. Než budete moct nakonfigurovat tento krok, budete muset vytvořit obor názvů centra událostí a zásady centra událostí.
    3. **Streamování do** pracovního prostoru Log Analytics v Azure monitor. Než budete moct vybrat tuto možnost, budete muset vytvořit pracovní prostor analýzy protokolů.

6. Vyberte **chyby, provoz a latenci** pro Azure IoT Connector pro FHIR a všechny další kategorie metrik, které chcete zachytit pro Azure API pro FHIR.

7. Vyberte **Uložit** .

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT connector1" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Může trvat až 15 minut, než se první protokoly metriky zobrazí v úložišti dle vašeho výběru.  
 
Další informace o tom, jak pracovat s diagnostickými protokoly, najdete v [dokumentaci k protokolu prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview) .

## <a name="conclusion"></a>Závěr 
Přístup k protokolům metrik je nezbytný pro monitorování a řešení potíží.  Azure IoT Connector pro FHIR umožňuje provádět tyto akce prostřednictvím protokolů metrik. 

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k Azure IoT Connectoru pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro nejčastější dotazy k FHIR](fhir-faq.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.
