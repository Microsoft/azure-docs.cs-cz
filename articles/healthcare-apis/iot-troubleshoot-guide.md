---
title: Azure IoT Connector pro FHIR (Preview) – Průvodce odstraňováním potíží a postupy
description: Řešení potíží s běžným konektorem Azure IoT Connector pro FHIR (Preview) chybové zprávy a podmínky kopírování souborů mapování
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: jasteppe
ms.openlocfilehash: 8c372a865e34b2cbd1b5b3e6d8619c3ef0f438e0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460418"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Azure IoT Connectorem pro FHIR (Preview)

Tento článek popisuje kroky pro řešení běžných konektorů Azure IoT Connector pro FHIR chybové zprávy a podmínky.  

Naučíte se také, jak vytvořit kopie služby Azure IoT Connector pro soubory mapování FHIR JSON pro úpravy a archivaci mimo Azure Portal nebo za účelem poskytování technické podpory Azure při otevření lístku podpory. 

## <a name="error-messages-and-fixes"></a>Chybové zprávy a opravy

|Zpráva   |Podmínka  |Oprava         |
|----------|-----------|------------|
|Neplatný název mapování. název mapování by měl být Device nebo FHIR.|Zadaný typ mapování není Device nebo FHIR.|Použijte jeden ze dvou podporovaných typů mapování (například Device nebo FHIR).|
|Opakované vygenerování parametrů klíče není definovaných.|Znovu vygenerovat požadavek na klíč|Zahrnout parametry do žádosti o klíč obnovení|
|Dosáhlo se maximálního počtu instancí IoT Connector *, které se dají zřídit v tomto předplatném.|Dosáhlo se kvóty pro předplatné Azure IoT Connector pro FHIR (výchozí je (2) na předplatné).|Odstraňte jednu z existujících instancí služby Azure IoT Connector pro FHIR, použijte jiné předplatné, které nedosáhlo kvóty předplatného, nebo požádejte o zvýšení kvóty předplatného.|
|Pro prostředek Azure API pro FHIR s povoleným konektorem IoT není podporovaný prostředek Move.|Pokus o provedení operace přesunutí u prostředku Azure API pro FHIR, který má jednu nebo víc instancí konektoru Azure IoT Connector pro FHIR|Odstraní existující instance služby Azure IoT Connector pro FHIR k provedení a dokončení operace přesunutí.|
|Konektor IoT není zřízený.|Pokus o použití podřízených služeb (připojení & mapování), pokud není zajištěná nadřazená (Azure IoT Connector pro FHIR)|Zřízení konektoru Azure IoT pro FHIR|
|Požadavek se nepodporuje.|Konkrétní žádost o rozhraní API není podporovaná.|Použití správné žádosti o rozhraní API|
|Účet neexistuje.|Pokus o přidání konektoru Azure IoT pro FHIR a neexistuje rozhraní API Azure pro prostředek FHIR.|Vytvořte prostředek Azure API pro FHIR a potom zkuste operaci zopakovat.|
|Verze Azure API pro FHIR prostředků FHIR není pro službu IoT Connector podporovaná.|Pokus o použití služby Azure IoT Connector pro FHIR s nekompatibilní verzí Azure API pro prostředek FHIR|Vytvořte nové rozhraní Azure API pro prostředek FHIR (verze R4) nebo použijte existující rozhraní API Azure pro prostředek FHIR (verze R4).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Vytváření kopií služby Azure IoT Connector pro soubory mapování FHIR (Preview)
Kopírování služby Azure IoT Connector pro soubory mapování FHIR může být užitečné při úpravách a archivaci mimo Azure Portal web a poskytování technické podpory Azure při otevření lístku podpory.

> [!NOTE]
> Formát JSON je v tuto chvíli jediným podporovaným formátem pro soubory mapování zařízení a FHIR.

> [!TIP]
> Další informace o konektoru Azure IoT pro [zařízení FHIR a mapování souborů JSON pro FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. V části **Doplňky** na řídicím panelu prostředků Azure API pro FHIR vyberte **"konektor IoT (Preview)"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Vyberte **konektor** , ze kterého budete kopírovat mapovací soubory.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Tento proces je také možné použít pro kopírování a ukládání obsahu JSON **"konfigurace FHIR mapování"** .

3. Vyberte **Konfigurovat mapování zařízení**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Vyberte obsah JSON a proveďte operaci kopírování (například: vyberte Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Proveďte operaci vložení (například: vyberte CTRL + v) do nového souboru v editoru (například Visual Studio Code, Poznámkový blok) a uložte soubor s příponou *. JSON.

> [!TIP]
> Pokud otevřete lístek [technické podpory Azure](https://azure.microsoft.com/support/create-ticket/) pro Azure IoT Connector pro FHIR, nezapomeňte zahrnout kopie souborů mapování, které vám pomůžou s procesem řešení potíží.

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k Azure IoT Connectoru pro FHIR.

>[!div class="nextstepaction"]
>[Nejčastější dotazy k Azure IoT Connectoru pro FHIR (Preview)](fhir-faq.md#iot-connector-preview)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.