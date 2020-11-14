---
title: Export metrik konektoru Azure IoT pro FHIR (Preview) přes nastavení diagnostiky
description: Tento článek vysvětluje, jak exportovat službu Azure IoT Connector pro FHIR (Preview) metriky prostřednictvím nastavení diagnostiky.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 14fd5378f37ebfc20b2d7084c08f15ea8f7a00b2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630528"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Export metrik konektoru Azure IoT pro FHIR (Preview) přes nastavení diagnostiky

V tomto článku se dozvíte, jak exportovat službu Azure IoT Connector pro rychlé interoperability prostředků (FHIR&#174;) * protokolů metrik. Funkce, která umožňuje protokolování metrik, je [**nastavení diagnostiky**](../azure-monitor/platform/diagnostic-settings.md) v Azure Portal. 

> [!TIP]
> Podle pokynů v části [Povolení protokolování diagnostiky v rozhraní Azure API pro FHIR a Azure IoT Connector pro FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) nastavte protokolování auditu.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Povolení protokolování metrik pro Azure IoT Connector pro FHIR (Preview)
1. Pokud chcete povolit protokolování metrik pro Azure IoT Connector pro FHIR, vyberte rozhraní Azure API pro službu FHIR v Azure Portal 

2. Přejít na **nastavení diagnostiky** 

3. Vybrat **+ Přidat nastavení diagnostiky**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. V dialogovém okně **název nastavení diagnostiky** zadejte název.

5. Vyberte metodu, kterou chcete použít pro přístup k diagnostickým protokolům:

    1. **Archivujte do účtu úložiště** pro auditování nebo ruční kontrolu. Účet úložiště, který chcete použít, musí být již vytvořen.
    2. **Streamování do centra událostí** pro ingestování prostřednictvím služby třetí strany nebo vlastního analytického řešení. Než budete moct nakonfigurovat tento krok, budete muset vytvořit obor názvů centra událostí a zásady centra událostí.
    3. **Streamování do** pracovního prostoru Log Analytics v Azure monitor. Aby bylo možné vybrat tuto možnost, bude nutné vytvořit pracovní prostor analýzy protokolů.

6. Vyberte **chyby, provoz a latenci** pro Azure IoT Connector pro FHIR.  Vyberte všechny další kategorie metrik, které chcete zachytit pro Azure API pro FHIR.

7. Vyberte **Uložit**.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Může trvat až 15 minut, než se první protokoly metrik zobrazí v úložišti dle vašeho výběru.  
 
Další informace o tom, jak pracovat s diagnostickými protokoly, najdete v [dokumentaci k protokolu prostředků Azure](../azure-monitor/platform/platform-logs-overview.md) .

## <a name="conclusion"></a>Závěr 
Přístup k protokolům metrik je nezbytný pro monitorování a řešení potíží.  Azure IoT Connector pro FHIR umožňuje provádět tyto akce prostřednictvím protokolů metrik. 

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k Azure IoT Connectoru pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro nejčastější dotazy k FHIR](fhir-faq.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview). FHIR je registrovaná ochranná známka změněného HL7 a používá se s oprávněním změněného HL7.