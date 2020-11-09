---
title: Zobrazení a konfigurace služby Azure IoT Connector pro FHIR (Preview) metrik
description: Tento článek vysvětluje, jak zobrazit a nakonfigurovat službu Azure IoT Connector pro FHIR (Preview) metriky.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 9a4e2c4dfe8a9de28688afe0dd036cecb7ce2b39
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381214"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Zobrazení a konfigurace služby Azure IoT Connector pro FHIR (Preview) metrik 

V tomto článku se dozvíte, jak zobrazit a nakonfigurovat službu Azure IoT Connector pro prostředky interoperability s rychlým zdravotním prostředím (FHIR&#174;) * metriky.

> [!TIP]
> Pokud se chcete dozvědět, jak nastavit export dat metrik, postupujte podle pokynů v tématu [Export Azure IoT Connectoru pro FHIR (Preview) metriky prostřednictvím nastavení diagnostiky](./iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Zobrazit metriky pro Azure IoT Connector pro FHIR (Preview)

1. Přihlaste se k Azure Portal a potom vyberte rozhraní API Azure pro službu FHIR. 

2. V levém podokně vyberte **metriky**. 

3. Vyberte kartu **IoT Connector** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Snímek obrazovky s podoknem IoT Connector a spojnicové grafy zobrazující počet příchozích a normalizovaných zpráv" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Vyberte konektor IoT, abyste zobrazili jeho metriky. K tomuto rozhraní API Azure pro službu FHIR jsou třeba přidružené čtyři konektory IoT ( *konektor 1* , *konektor 2* atd.).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Snímek obrazovky s podoknem IoT Connector zobrazující karty IoT Connectoru 1, 2, 3 a 4." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Vyberte časové období (například **1 hodinu** , **24 hodin** , **7 dní** nebo **vlastní** ) metrik konektoru IoT, které chcete zobrazit. Výběrem **vlastní** karty můžete pro zobrazení metrik konektoru IoT vytvořit určitou kombinaci data a času.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Snímek obrazovky s podoknem IoT Connector, který zobrazuje graf řádku časového období 1 hodiny pro ' Connector 1 '." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Typy metrik pro Azure IoT Connector pro FHIR (Preview) 

Metriky IoT Connectoru, které můžete zobrazit, jsou uvedené v následující tabulce:

|Typ metriky|Účel metriky| 
|-----------|--------------|
|Počet příchozích zpráv|Zobrazuje počet přijatých nezpracovaných příchozích zpráv (například události zařízení).|
|Počet normalizovaných zpráv|Zobrazí počet normalizovaných zpráv.|
|Počet skupin zpráv|Zobrazí počet skupin, které mají v určeném časovém intervalu agregované zprávy.|
|Průměrná latence normalizované fáze|Zobrazí průměrnou latenci normalizované fáze. Normalizovaná fáze provádí normalizaci nezpracovaných příchozích zpráv.|
|Průměrná latence fáze skupiny|Zobrazí průměrnou latenci fáze skupiny. Fáze skupiny provádí ukládání do vyrovnávací paměti, agregaci a seskupení podle normalizovaných zpráv.| 
|Celkový počet chyb|Zobrazí celkový počet chyb.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Zaměřit se na a nakonfigurovat službu Azure IoT Connector pro FHIR (Preview) metriky

V tomto příkladu se podíváme na **Počet příchozích zpráv** metriky.

1. Vyberte bod v čase, na který se chcete zaměřit.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Snímek obrazovky s podoknem metrika počet příchozích zpráv, který zvýrazní v grafu spojnici jeden bod v čase" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. V podokně **Počet příchozích zpráv** můžete metriku dále přizpůsobit výběrem možnosti **Přidat metrika** , **Přidat filtr** nebo **rozdělit**. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Snímek obrazovky s podoknem metrika počet příchozích zpráv, který zvýrazní tlačítka Přidat metriku, přidat filtr a použít rozdělení." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Závěr 
Přístup k metrikám roviny dat je zásadní pro monitorování a řešení potíží. Azure IoT Connector pro FHIR pomáhá s těmito akcemi prostřednictvím metrik. 

## <a name="next-steps"></a>Další kroky

Získejte odpovědi na nejčastější dotazy týkající se služby Azure IoT Connector pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro FHIR – Nejčastější dotazy](fhir-faq.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview). FHIR je registrovaná ochranná známka změněného HL7 a používá se s oprávněním změněného HL7. 
