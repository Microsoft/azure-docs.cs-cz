---
title: Azure IoT Connector pro FHIR (Preview) – Průvodce odstraňováním potíží a postupy
description: Řešení potíží s běžným konektorem Azure IoT Connector pro FHIR (Preview) chybové zprávy a podmínky a kopírování mapovacích souborů
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3eef7354f7197f60e8abd1b5522393bf00a6203f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018393"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Azure IoT Connectorem pro FHIR (Preview)

Tento článek popisuje kroky pro řešení běžných FHIRch informací o službě Azure IoT Connector pro rychlé interoperability zdravotní péče (&#174;) * chybové zprávy a podmínky.  

Naučíte se také, jak vytvořit kopie konektoru Azure IoT Connector pro mapování konverze FHIR JSON (například zařízení a FHIR).  

K úpravám a archivaci mimo Azure Portal můžete použít mapovací mapování JSON.  

> [!TIP]
> Pokud otevřete lístek [technické podpory Azure](https://azure.microsoft.com/support/create-ticket/) pro Azure IoT Connector pro FHIR, nezapomeňte zahrnout kopie JSON mapování převodu, které vám pomůžou s procesem řešení potíží.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Ověření platnosti šablon JSON pro Azure IoT Connector pro FHIR (Preview) pro zařízení a mapování FHIR
V této části se dozvíte o procesu ověřování, který Azure IoT Connector pro FHIR provede k ověření šablon JSON pro převod zařízení a mapování FHIR před tím, než povolí jejich uložení pro použití.  Tyto prvky jsou vyžadovány ve formátu JSON pro převod zařízení a FHIR.

**Mapování zařízení**

|Prvek|Vyžadováno|
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

|Prvek|Vyžadováno|
|:------|:-------|
|TypeName|Ano|

> [!NOTE]
> Toto je jediný povinný prvek mapování FHIR, který je v tuto chvíli ověřený.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Chybové zprávy a opravy pro Azure IoT Connector pro FHIR (Preview)

|Zpráva|Zobrazit|Podmínka|Oprava| 
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

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Proč je můj konektor Azure IoT pro FHIR (ve verzi Preview) nezobrazený v Azure API pro FHIR?

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

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>Použití metrik k řešení problémů v Azure IoT Connectoru pro FHIR (Preview)

Azure IoT Connector pro FHIR vygeneruje několik metrik, které poskytují přehled o procesu toku dat. Jedna z podporovaných metrik se nazývá celkový počet *chyb*, což poskytuje počet všech chyb, ke kterým dojde v rámci instance služby Azure IoT Connector pro FHIR.

Každá chyba se zaprotokoluje s řadou přidružených vlastností. Každá vlastnost poskytuje jiným aspektům chyby, která vám může pomoct identifikovat a řešit potíže. V této části jsou uvedeny různé vlastnosti zaznamenané pro každou chybu v metrikě *celkového počtu chyb* a možné hodnoty těchto vlastností.

> [!NOTE]
> Můžete přejít na metriku *celkového počtu chyb* pro instanci Azure IoT Connectoru pro FHIR (Preview), jak je popsáno na [stránce metriky Azure IoT Connector pro FHIR (Preview)](iot-metrics-display.md).

Klikněte na graf *Celkový počet chyb* a potom klikněte na tlačítko *Přidat filtr* pro řez a indexy metriky chyby pomocí kterékoli z vlastností uvedených níže.

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>Operace, kterou provádí Azure IoT Connector pro FHIR (Preview)

Tato vlastnost představuje operaci prováděnou konektorem IoT Connector, pokud došlo k chybě. Operace obvykle představuje fázi toku dat při zpracování zprávy zařízení. Tady je seznam možných hodnot pro tuto vlastnost.

> [!NOTE]
> Další informace o různých fázích toku dat najdete v článku Azure IoT Connector pro FHIR ( [Preview).](iot-data-flow.md)

|Fáze toku dat|Description|
|---------------|-----------|
|Nastavení|Operace specifická pro nastavení instance služby IoT Connector|
|Normalizace|Fáze toku dat, kde se data zařízení normalizují|
|Seskupování|Fáze toku dat, kde se seskupují normalizovaná data|
|FHIRConversion|Fáze toku dat, kde se seskupují normalizovaná data, se transformují na prostředek FHIR.|
|Neznámý|Typ operace je neznámý, pokud došlo k chybě.|

### <a name="the-severity-of-the-error"></a>Závažnost chyby

Tato vlastnost představuje závažnost chyby, která došlo k chybě. Tady je seznam možných hodnot pro tuto vlastnost.

|Závažnost|Popis|
|---------------|-----------|
|Upozornění|Některé drobné problémy v procesu toku dat existují, ale zpracování zprávy zařízení se nezastaví.|
|Chyba|Při zpracování konkrétní zprávy zařízení došlo k chybě a ostatní zprávy můžou být i nadále spouštěny podle očekávání.|
|Kritické|U konektoru IoT se vyskytuje několik problémů na úrovni systému a neočekává se, že se budou zpracovávat žádné zprávy.|

### <a name="the-type-of-the-error"></a>Typ chyby

Tato vlastnost označuje kategorii pro danou chybu, která v podstatě představuje logické seskupení pro podobný typ chyb. Tady je seznam možných hodnot pro tuto vlastnost.

|Typ chyby|Description|
|----------|-----------|
|DeviceTemplateError|Chyby související se šablonami mapování zařízení|
|DeviceMessageError|Při zpracování konkrétní zprávy zařízení došlo k chybám.|
|FHIRTemplateError|Chyby související se šablonami mapování FHIR|
|FHIRConversionError|Při transformaci zprávy na prostředek FHIR došlo k chybám.|
|FHIRResourceError|Chyby související se stávajícími prostředky na serveru FHIR, na které odkazuje konektor IoT Connector|
|FHIRServerError|Chyby, ke kterým dochází při komunikaci se serverem FHIR|
|GeneralError|Všechny ostatní typy chyb|

### <a name="the-name-of-the-error"></a>Název chyby

Tato vlastnost poskytuje název konkrétní chyby. Tady je seznam všech názvů chyb s popisem a přidružených typů chyb, závažnosti a fází toku dat.

|Název chyby|Description|Typy chyb|Závažnost chyby|Fáze toku dat|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|Došlo k chybě, když se na serveru FHIR najde víc prostředků pacienta nebo zařízení pro příslušné identifikátory ve zprávě zařízení.|FHIRResourceError|Chyba|FHIRConversion|
|TemplateNotFoundException|Pro šablonu zařízení nebo mapování FHIR není nakonfigurovaná instance konektoru IoT.|DeviceTemplateError, FHIRTemplateError|Kritické|Normalizace, FHIRConversion|
|CorrelationIdNotDefinedException|ID korelace není zadané v šabloně mapování zařízení. CorrelationIdNotDefinedException je podmíněná chyba, ke které došlo pouze v případě, že sledování FHIR musí seskupovat měření zařízení pomocí ID korelace, ale není správně nakonfigurováno.|DeviceMessageError|Chyba|Normalizace|
|PatientDeviceMismatchException|K této chybě dochází, pokud prostředek zařízení na serveru FHIR odkazuje na prostředek pacienta, který se neshoduje s identifikátorem pacienta uvedeným ve zprávě.|FHIRResourceError|Chyba|FHIRConversionError|
|PatientNotFoundException|Na prostředek FHIR zařízení přidružený k identifikátoru zařízení, který se nachází ve zprávě zařízení, neodkazuje žádný prostředek FHIR pacienta. Poznámka: Tato chyba se projeví jenom v případě, že je instance konektoru IoT nakonfigurovaná s typem překladu *vyhledávání* .|FHIRConversionError|Chyba|FHIRConversion|
|DeviceNotFoundException|Na serveru FHIR přidruženém k identifikátoru zařízení, který se nachází ve zprávě zařízení, neexistuje žádný prostředek zařízení.|DeviceMessageError|Chyba|Normalizace|
|PatientIdentityNotDefinedException|K této chybě dochází, pokud výraz pro rozbor identifikátoru pacienta ze zprávy zařízení není nakonfigurovaný v šabloně mapování zařízení nebo identifikátorem pacient není ve zprávě zařízení přítomen. Poznámka k této chybě dochází jenom v případě, že je typ rozlišení konektoru IoT nastavený na *vytvořit* .|DeviceTemplateError|Kritické|Normalizace|
|DeviceIdentityNotDefinedException|K této chybě dojde, pokud výraz pro analýzu identifikátoru zařízení ze zprávy zařízení není nakonfigurovaný v šabloně mapování zařízení nebo identifikátorem zařízení není přítomna ve zprávě zařízení.|DeviceTemplateError|Kritické|Normalizace|
|NotSupportedException|Při přijetí zprávy zařízení s nepodporovaným formátem došlo k chybě.|DeviceMessageError|Chyba|Normalizace|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Vytváření kopií pro JSON mapování převodu služby Azure IoT Connector pro FHIR (Preview)

Kopírování služby Azure IoT Connector pro soubory mapování FHIR může být užitečné pro úpravy a archivaci mimo Azure Portal Web.

Při otevření lístku podpory, který vám pomůže při řešení potíží, by se měly poskytnout kopie souboru mapování pro technickou podporu Azure.

> [!NOTE]
> Formát JSON je v tuto chvíli jediným podporovaným formátem pro soubory mapování zařízení a FHIR.

> [!TIP]
> Další informace o konektoru Azure IoT Connector pro [zařízení FHIR a mapování konverze FHIR (JSON](iot-mapping-templates.md) )

1. V části **Doplňky** na řídicím panelu prostředků Azure API pro FHIR vyberte **"konektor IoT (Preview)"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Vyberte **konektor** , ze kterého budete kopírovat JSON mapování převodu.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Tento proces je také možné použít pro kopírování a ukládání obsahu JSON **"konfigurace FHIR mapování"** .

3. Vyberte **Konfigurovat mapování zařízení**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Vyberte obsah JSON a proveďte operaci kopírování (například: vyberte Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Proveďte operaci vložení (například: vyberte CTRL + v) do nového souboru v editoru (například Visual Studio Code, Poznámkový blok) a uložte soubor s příponou *. JSON.

> [!TIP]
> Pokud otevřete lístek [technické podpory Azure](https://azure.microsoft.com/support/create-ticket/) pro Azure IoT Connector pro FHIR, nezapomeňte zahrnout kopie JSON mapování převodu, které vám pomůžou s procesem řešení potíží.

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k Azure IoT Connectoru pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro nejčastější dotazy k FHIR](fhir-faq.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview). FHIR je registrovaná ochranná známka změněného HL7 a používá se s oprávněním změněného HL7.