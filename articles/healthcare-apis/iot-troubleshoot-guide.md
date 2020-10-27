---
title: Azure IoT Connector pro FHIR (Preview) – Průvodce odstraňováním potíží a postupy
description: Řešení potíží s běžným konektorem Azure IoT Connector pro FHIR (Preview) chybové zprávy a podmínky a kopírování mapovacích souborů
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: jasteppe
ms.openlocfilehash: 4a1a23ca2d0b30a192c30b331c588d13a2a261a7
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558512"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Azure IoT Connectorem pro FHIR (Preview)

Tento článek popisuje kroky pro řešení běžných konektorů Azure IoT Connector pro FHIR * chybové zprávy a podmínky.  

Naučíte se také, jak vytvořit kopie konektoru Azure IoT Connector pro mapování konverze FHIR JSON (například zařízení a FHIR).  

K úpravám a archivaci mimo Azure Portal můžete použít mapovací mapování JSON.  

> [!TIP]
> Pokud otevřete lístek [technické podpory Azure](https://azure.microsoft.com/support/create-ticket/) pro Azure IoT Connector pro FHIR, nezapomeňte zahrnout kopie JSON mapování převodu, které vám pomůžou s procesem řešení potíží.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Ověření platnosti šablon JSON pro Azure IoT Connector pro FHIR (Preview) pro zařízení a mapování FHIR
V této části se dozvíte o procesu ověřování, který Azure IoT Connector pro FHIR provede k ověření šablon JSON pro převod zařízení a mapování FHIR před tím, než povolí jejich uložení pro použití.  Tyto prvky jsou vyžadovány ve formátu JSON pro převod zařízení a FHIR.

**Mapování zařízení**

|Prvek|Povinné|
|:-------|:------|
|TypeName|Ano|
|TypeMatchExpression|Ano|
|DeviceIdExpression|Ano|
|TimestampExpression|Ano|
|Hodnoty []. Hodnoty|Ano|
|Hodnoty []. ValueExpression|Ano|

> [!NOTE]
> Hodnoty []. Hodnoty ValueName a Values []. ValueExpression
>
> Tyto prvky jsou požadovány pouze v případě, že je v poli položka hodnoty – je platný pro nemapované hodnoty. Toto se používá, když je telemetrie odeslána událost. Například: když je zařízení IoMT přenosném vloženo nebo odebráno. Prvky nemají žádné hodnoty s výjimkou názvu, který Azure IoT Connector pro FHIR odpovídá a emituje. V případě konverze FHIR se konektor Azure IoT pro FHIR mapuje na koncept s podporou kódu na základě sémantického typu – neplní se žádné skutečné hodnoty.

**Mapování FHIR**

|Prvek|Povinné|
|:------|:-------|
|TypeName|Ano|

> [!NOTE]
> Toto je jediný povinný prvek mapování FHIR, který je v tuto chvíli ověřený.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Chybové zprávy a opravy pro Azure IoT Connector pro FHIR (Preview)

|Zpráva|Zobrazit|Stav|Oprava| 
|-------|---------|---------|---|
|Neplatný název mapování. název mapování by měl být Device nebo FHIR.|Rozhraní API|Zadaný typ mapování není Device nebo FHIR.|Použijte jeden ze dvou podporovaných typů mapování (například: Device nebo FHIR).|
|Ověření se nezdařilo. Požadované informace chybí nebo nejsou platné.|Rozhraní API a Azure Portal|Došlo k pokusu o uložení mapování převodu chybějících potřebných informací nebo elementů.|Přidejte chybějící informace mapování konverze nebo element a pokuste se znovu uložit mapování převodu.|
|Opětovné vygenerování parametrů klíče není definováno.|Rozhraní API|Znovu vygenerovat požadavek na klíč|Zahrňte parametry do žádosti o klíč obnovení.|
|Bylo dosaženo maximálního počtu instancí konektoru IoT, které lze v tomto předplatném zřídit.|Rozhraní API a Azure Portal|Dosáhlo se kvóty Azure IoT Connector pro předplatné FHIR (výchozí je (2) na jedno předplatné).|Odstraňte jednu z existujících instancí služby Azure IoT Connector pro FHIR.  Použijte jiné předplatné, které nedosáhlo kvóty předplatného.  Vyžádejte si zvýšení kvóty předplatného.|
|Pro prostředek FHIR Azure API pro službu IoT Connector se nepodporuje přesunutí prostředku.|Rozhraní API a Azure Portal|Došlo k pokusu o provedení operace přesunutí u prostředku Azure API pro FHIR, který má jednu nebo víc instancí konektoru Azure IoT Connector pro FHIR.|Odstraní existující instance služby Azure IoT Connector pro FHIR, aby se operace přesunu mohla provést.|
|Konektor IoT není zřízený.|Rozhraní API|Pokus o použití podřízených služeb (připojení & mapování) v případě, že nebyla zřízena nadřazená (Azure IoT Connector pro FHIR).|Zřízení konektoru Azure IoT pro FHIR.|
|Požadavek se nepodporuje.|Rozhraní API|Konkrétní žádost o rozhraní API není podporovaná.|Použijte správnou žádost o rozhraní API.|
|Účet neexistuje.|Rozhraní API|Došlo k pokusu o přidání konektoru Azure IoT pro FHIR a neexistuje rozhraní API Azure pro prostředek FHIR.|Vytvořte prostředek Azure API pro FHIR a pak zkuste operaci zopakovat.|
|Verze Azure API pro FHIR prostředků FHIR není pro službu IoT Connector podporovaná.|Rozhraní API|Pokoušíte se použít Azure IoT Connector pro FHIR s nekompatibilní verzí Azure API pro prostředek FHIR.|Vytvořte nové rozhraní Azure API pro prostředek FHIR (verze R4) nebo použijte existující rozhraní API Azure pro prostředek FHIR (verze R4).

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Proč je můj konektor Azure IoT pro FHIR (ve verzi Preview) nezobrazený v Azure API pro FHIR?

|Potenciální problémy|Opravy|
|----------------|-----|
|Data se pořád zpracovávají.|Data se přechází do Azure API pro FHIR v dávkách (každých 15 minut).  Je možné, že se data stále zpracovávají a další čas je potřeba, aby se data zachovala v rozhraní Azure API pro FHIR.|
|JSON mapování převodu zařízení není nakonfigurované.|Nakonfigurujte a uložte formát JSON pro mapování převodu zařízení.|
|JSON mapování FHIR Conversion není nakonfigurovaný.|Nakonfigurujte a uložte vyhovující JSON mapování FHIR převodů.|
|Zpráva zařízení neobsahuje očekávaný výraz definovaný v mapování zařízení.|Ověřte, že JsonPath výrazy definované v mapování zařízení odpovídají tokenům definovaným ve zprávě zařízení.|
|Prostředek zařízení se nevytvořil v rozhraní API Azure pro FHIR (typ řešení: jenom pro vyhledávání) *.|Vytvořte platný prostředek zařízení v rozhraní API Azure pro FHIR. Ujistěte se, že prostředek zařízení obsahuje identifikátor, který se shoduje s identifikátorem zařízení zadaným v příchozí zprávě.|
|V rozhraní API Azure pro FHIR se nevytvořil prostředek pacienta (typ řešení: jenom pro vyhledávání) *.|Vytvořte platný prostředek pacienta v rozhraní API Azure pro FHIR.|
|Odkaz zařízení. pacient není nastavený, nebo je odkaz neplatný (typ řešení: jenom pro vyhledávání) *.|Ujistěte se, že prostředek zařízení obsahuje platný [odkaz](https://www.hl7.org/fhir/device-definitions.html#Device.patient) na prostředek pacienta.| 

* [Rychlý Start: nasazení konektoru Azure IoT Connector (Preview) pomocí Azure Portal](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) pro funkční popis konektoru Azure IoT Connector pro typy rozlišení FHIR (například: Lookup nebo Create).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Vytváření kopií pro JSON mapování převodu služby Azure IoT Connector pro FHIR (Preview)
Kopírování služby Azure IoT Connector pro soubory mapování FHIR může být užitečné pro úpravy a archivaci mimo Azure Portal Web.

Při otevření lístku podpory, který vám pomůže při řešení potíží, by se měly poskytnout kopie souboru mapování pro technickou podporu Azure.

> [!NOTE]
> Formát JSON je v tuto chvíli jediným podporovaným formátem pro soubory mapování zařízení a FHIR.

> [!TIP]
> Další informace o konektoru Azure IoT Connector pro [zařízení FHIR a mapování konverze FHIR (JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates) )

1. V části **Doplňky** na řídicím panelu prostředků Azure API pro FHIR vyberte **"konektor IoT (Preview)"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Vyberte **konektor** , ze kterého budete kopírovat JSON mapování převodu.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT connector1" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Tento proces je také možné použít pro kopírování a ukládání obsahu JSON **"konfigurace FHIR mapování"** .

3. Vyberte **Konfigurovat mapování zařízení** .

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT connector1" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Vyberte obsah JSON a proveďte operaci kopírování (například: vyberte Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT connector1" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Proveďte operaci vložení (například: vyberte CTRL + v) do nového souboru v editoru (například Visual Studio Code, Poznámkový blok) a uložte soubor s příponou *. JSON.

> [!TIP]
> Pokud otevřete lístek [technické podpory Azure](https://azure.microsoft.com/support/create-ticket/) pro Azure IoT Connector pro FHIR, nezapomeňte zahrnout kopie JSON mapování převodu, které vám pomůžou s procesem řešení potíží.

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k Azure IoT Connectoru pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro nejčastější dotazy k FHIR](fhir-faq.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.
