---
title: Zobrazení a konfigurace služby Azure IoT Connector pro metriky FHIR (Preview)
description: V tomto článku se dozvíte, jak zobrazit a nakonfigurovat službu Azure IoT Connector pro FHIR (Preview) metriky.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133542"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Zobrazení a konfigurace služby Azure IoT Connector pro metriky FHIR (Preview) 

V tomto článku se dozvíte, jak zobrazit a nakonfigurovat službu Azure IoT Connector pro metriky FHIR *. 

> [!TIP]
> Postupujte podle pokynů v tématu [Export služby Azure IoT Connector pro FHIR (Preview) metriky prostřednictvím nastavení diagnostiky](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) a Naučte se, jak nastavit export dat metrik.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Zobrazit metriky pro Azure IoT Connector pro FHIR (Preview)
1. Pokud chcete zobrazit metriky pro konektory IoT, vyberte v Azure Portal službu Azure API pro službu FHIR. 

2. Přejít na **metriky** 

3. Vyberte kartu **IoT Connector** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Vyberte konektor IoT, pro který chcete zobrazit metriky (například: k tomuto rozhraní Azure API pro službu FHIR jsou přidružené konektory IoT).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT connector1" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> **Vlastní** karta umožňuje vytvořit konkrétní kombinace data a času pro zobrazení metrik konektoru IoT.

5. Vyberte časové období metrik konektoru IoT, které se má zobrazit (například 1 hodina, 24 hodin, 7 dní nebo vlastní).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT connector1" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Typy metrik pro Azure IoT Connector pro FHIR (Preview) 
Zobrazené metriky IoT Connectoru jsou následující:

|Typ metrik|Účel metriky| 
|-----------|--------------|
|Počet příchozích zpráv|Počet přijatých nezpracovaných příchozích zpráv (například: události zařízení).|
|Počet normalizovaných zpráv|Počet normalizovaných zpráv.|
|Počet skupin zpráv|Počet skupin, které mají v určeném časovém intervalu agregované zprávy|
|Průměrná latence normalizované fáze|Průměrná latence fáze normalizování Fáze normalizování slouží k provedení normalizace nezpracovaných příchozích zpráv.|
|Průměrná latence fáze skupiny|Průměrná latence fáze skupiny Fáze skupiny je provádět ukládání do vyrovnávací paměti, agregace a seskupení v normalizovaných zprávách.| 
|Celkový počet chyb|Celkový počet chyb| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Zaměření a konfigurace služby Azure IoT Connector pro FHIR (Preview) metrik
V tomto příkladu se zaměříme na **Počet příchozích zpráv** o metrikách.

1. Vyberte bod v čase, na který se chcete zaměřit.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT connector1" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Z této obrazovky můžete **Přidat metriku** , **Přidat filtr** a **použít rozdělení** pro další úpravy. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT connector1" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Závěr 
Přístup k metrikám roviny dat je zásadní pro monitorování a řešení potíží.  Azure IoT Connector pro FHIR vám pomůže provádět tyto akce prostřednictvím metrik. 

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k Azure IoT Connectoru pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro nejčastější dotazy k FHIR](fhir-faq.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.