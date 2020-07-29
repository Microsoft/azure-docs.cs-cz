---
title: Azure IoT Connector pro FHIR (IoT Connector) – Průvodce odstraňováním potíží a postupy
description: Jak řešit běžné chybové zprávy a stavy konektoru IoT Connector a kopírovat soubory mapování
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285573"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>Průvodce odstraňováním potíží konektoru IoT Connector (Preview)

Tento článek popisuje kroky pro řešení běžných chybových zpráv a podmínek služby IoT Connector (Preview).  

Naučíte se také, jak vytvořit kopie souborů mapování služby IoT Connector (Preview) pro úpravy a archivaci mimo Azure Portal.

## <a name="error-messages-and-fixes"></a>Chybové zprávy a opravy

|Zpráva   |Podmínka  |Oprava         |
|----------|-----------|------------|
|Neplatný název mapování. název mapování by měl být Device nebo FHIR.|Zadaný typ mapování není Device nebo FHIR.|Použijte jeden ze dvou podporovaných typů mapování (například Device nebo FHIR).|
|Opakované vygenerování parametrů klíče není definovaných.|Znovu vygenerovat požadavek na klíč|Zahrnout parametry do žádosti o klíč obnovení|
|Bylo dosaženo maximálního počtu instancí konektoru IoT, které lze v tomto předplatném zřídit.|Dosáhlo se kvóty pro předplatné IoT Connectoru (výchozí je 2 na předplatné).|Odstraňte jeden ze stávajících konektorů, použijte jiné předplatné, které nedosáhlo kvóty (2) konektorů na odběr nebo vyžádat zvýšení kvóty předplatného.|
|Pro prostředek Azure API pro FHIR s povoleným konektorem IoT není podporovaný prostředek Move.|Pokus o provedení operace přesunutí na prostředku Azure API pro FHIR s jedním nebo více konektory IoT|Odstranit existující konektory, aby bylo možné provést nebo dokončit operace přesunutí|
|Pro prostředek Azure API pro FHIR je povolené privátní propojení.  Privátní propojení se službou IoT Connector nepodporuje|Pokus o přidání konektoru IoT do Azure API pro prostředek FHIR s povoleným privátním propojením|Vyberte nebo vytvořte Azure API pro prostředek FHIR (verze R4), který nemá povolený privátní odkaz.|
|Konektor IoT není zřízený.|Pokus o použití podřízených služeb (připojení & mapování) v případě, že nebyla zajištěna nadřazená (služba IoT Connector)|Zřízení konektoru IoT|
|Požadavek se nepodporuje.|Konkrétní žádost o rozhraní API není podporovaná.|Použití správné žádosti o rozhraní API|
|Účet neexistuje.|Pokus o přidání konektoru IoT a Azure API pro prostředek FHIR neexistuje.|Vytvořte prostředek Azure API pro FHIR a potom zkuste operaci zopakovat.|
|Verze Azure API pro FHIR prostředků FHIR není pro službu IoT Connector podporovaná.|Pokus o použití konektoru IoT s nekompatibilní verzí Azure API pro prostředek FHIR|Vytvořte nové rozhraní Azure API pro prostředek FHIR (verze R4) nebo použijte existující rozhraní API Azure pro prostředek FHIR (verze R4).

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>Vytváření kopií mapovacích souborů konektoru IoT Connector (Preview)
> [!NOTE]
> Formát JSON je v tuto chvíli jediným podporovaným formátem pro soubory mapování zařízení a FHIR.

> [!TIP]
> Kopírování souborů mapování služby IoT Connector může být užitečné pro úpravy a archivaci mimo web Azure Portal a poskytování technické podpory Azure při otevření lístku podpory.
> 
> Další informace o zařízení konektoru IoT [a mapování souborů JSON pro FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. V části **Doplňky** na řídicím panelu prostředků Azure API pro FHIR vyberte **"konektor IoT (Preview)"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Vyberte **konektor** , ze kterého budete kopírovat mapovací soubory.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Vyberte **Konfigurovat mapování zařízení**.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Tento proces je také možné použít pro kopírování a ukládání obsahu JSON **"konfigurace FHIR mapování"** .

4. Vyberte obsah JSON a proveďte operaci kopírování (například: vyberte Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Konektor IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Proveďte operaci vložení (například: vyberte CTRL + v) do nového souboru v editoru (například Visual Studio Code, Poznámkový blok) a uložte soubor s příponou *. JSON.

> [!TIP]
> Pokud otevřete lístek [technické podpory Azure](https://azure.microsoft.com/support/create-ticket/) pro konektor IoT, nezapomeňte zahrnout kopie souborů mapování, které vám pomůžou s procesem řešení potíží.

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k konektoru IoT

>[!div class="nextstepaction"]
>[Nejčastější dotazy ke konektoru IoT Connector](fhir-faq.md#iot-connector-preview)


FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.