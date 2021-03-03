---
title: Vysvětlení způsobu, jakým IoT Hub aktualizace zařízení používá IoT technologie Plug and Play | Microsoft Docs
description: Aktualizace zařízení pro IoT Hub používá ke zjišťování a správě zařízení, která podporují dodávání aktualizací přes Air.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 227488f165aaad2f204c647eed17467a4ef561a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662557"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Aktualizace zařízení pro IoT Hub a IoT technologie Plug and Play

Aktualizace zařízení pro IoT Hub používá [IoT technologie Plug and Play](https://docs.microsoft.com/azure/iot-pnp/) ke zjišťování a správě zařízení, která podporují dodávání aktualizací přes Air. Služba aktualizace zařízení bude odesílat a přijímat vlastnosti a zprávy ze zařízení pomocí rozhraní PnP. Aktualizace zařízení pro IoT Hub vyžaduje, aby zařízení IoT implementovala následující rozhraní a ID modelu, jak je popsáno níže.

## <a name="adu-core-interface"></a>Rozhraní ADU Core

Rozhraní ADUCoreInterface se používá k posílání aktualizačních akcí a metadat do zařízení a přijímání stavu aktualizací ze zařízení. Rozhraní ADU Core je rozdělené do dvou vlastností objektu.

Při implementaci tohoto rozhraní je očekávaný název součásti v modelu **"azureDeviceUpdateAgent"** . [Další informace o komponentách Azure IoT PnP](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

### <a name="agent-metadata"></a>Metadata agenta

Metadata agenta obsahují pole, která agent aktualizace zařízení nebo zařízení používá k odesílání informací a stavu do služby aktualizace zařízení.

|Název|Schéma|Směr|Popis|Příklad|
|----|------|---------|-----------|-----------|
|resultCode|integer|zařízení do cloudu|Kód, který obsahuje informace o výsledku poslední akce aktualizace. Může být naplněna buď pro úspěch, nebo selhání a měla by následovat po [specifikaci stavového kódu http](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|integer|zařízení do cloudu|Kód, který obsahuje další informace o výsledku. Lze naplnit buď pro úspěch, nebo při selhání.|0x80004005|
|state|integer|zařízení do cloudu|Jedná se o celé číslo, které označuje aktuální stav agenta aktualizace zařízení. Podrobnosti najdete níže. |Období|
|installedUpdateId|řetězec|zařízení do cloudu|ID aktualizace, která je aktuálně nainstalovaná (prostřednictvím aktualizace zařízení). Tato hodnota bude null u zařízení, které nikdy netrvalo aktualizaci prostřednictvím aktualizace zařízení.|Null|
|`deviceProperties`|Mapa|zařízení do cloudu|Sada vlastností, které obsahují výrobce a model.|Podrobnosti najdete níže.

#### <a name="state"></a>State

Po přijetí akce ze služby aktualizace zařízení je stav hlášený agentem aktualizace zařízení. `State` je hlášena v reakci na `Action` (viz `Actions` níže), která je odeslána agentovi aktualizace zařízení ze služby aktualizace zařízení. Přečtěte si [pracovní postup](understand-device-update.md#device-update-agent) pro požadavky, které se procházejí službou aktualizace zařízení a agentem aktualizace zařízení.

|Název|Hodnota|Popis|
|---------|-----|-----------|
|Období|0|Zařízení je připravené přijmout akci ze služby aktualizace zařízení. Po úspěšné aktualizaci se stav vrátí do `Idle` stavu.|
|DownloadSucceeded|2|Úspěšné stažení.|
|InstallSucceeded|4|Úspěšná instalace.|
|Neúspěšný|255|Během aktualizace došlo k chybě.|

#### <a name="device-properties"></a>Vlastnosti zařízení

Jedná se o sadu vlastností, které obsahují výrobce a model.

|Název|Schéma|Směr|Popis|
|----|------|---------|-----------|
|manufacturer|řetězec|zařízení do cloudu|Výrobce zařízení, který nahlásil `deviceProperties` . Tato vlastnost je čtena z jednoho ze dvou míst – rozhraní ' AzureDeviceUpdateCore ' se nejprve pokusí přečíst hodnotu ' aduc_manufacturer ' ze souboru [konfiguračního souboru](device-update-configuration-file.md) .  Pokud hodnota není naplněna v konfiguračním souboru, bude ve výchozím nastavení ohlášena definice doby kompilace pro ADUC_DEVICEPROPERTIES_MANUFACTURER. Tato vlastnost bude uvedena pouze v době spuštění.|
|model|řetězec|zařízení do cloudu|Model zařízení, který se nahlásil `deviceProperties` . Tato vlastnost je čtena z jednoho ze dvou míst – rozhraní AzureDeviceUpdateCore se nejprve pokusí načíst hodnotu ' aduc_model ' ze souboru [konfiguračního souboru](device-update-configuration-file.md) .  Pokud hodnota není naplněna v konfiguračním souboru, bude ve výchozím nastavení ohlášena definice doby kompilace pro ADUC_DEVICEPROPERTIES_MODEL. Tato vlastnost bude uvedena pouze v době spuštění.|
|aduVer|řetězec|zařízení do cloudu|Verze agenta aktualizace zařízení běžícího na zařízení. Tato hodnota je načtena ze sestavení pouze v případě, že během kompilace ENABLE_ADU_TELEMETRY_REPORTING nastavena na hodnotu 1 (true). Zákazníci si můžou zvolit výslovný souhlas se zasíláním zpráv o verzi nastavením hodnoty na 0 (NEPRAVDA). [Jak přizpůsobit vlastnosti agenta aktualizace zařízení](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|řetězec|zařízení do cloudu|Verze agenta pro optimalizaci doručování, která je v zařízení spuštěná. Hodnota je čtena ze sestavení pouze v případě, že během kompilace ENABLE_ADU_TELEMETRY_REPORTING nastavena na hodnotu 1 (true). Zákazníci si můžou zvolit výslovný souhlas se zasíláním zpráv o verzích nastavením hodnoty na 0 (NEPRAVDA). [Jak přizpůsobit vlastnosti agenta Optimalizace doručení](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Metadata služby

Metadata služby obsahují pole, která služba aktualizace zařízení používá ke komunikaci akcí a dat s agentem aktualizace zařízení.

|Název|Schéma|Směr|Popis|
|----|------|---------|-----------|
|akce|integer|Cloud do zařízení|Jedná se o celé číslo, které odpovídá akci, kterou má agent provádět. Níže uvedené hodnoty.|
|UpdateManifest –|řetězec|Cloud do zařízení|Slouží k popisu obsahu aktualizace. Vygenerováno z [manifestu importu](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|JSON – objekt|Cloud do zařízení|Webový podpis JSON (JWS) s webovými klíči JSON, který se používá pro ověření zdroje.|
|Adresy URL|Mapa|Cloud do zařízení|Mapa `FileHash` pro `DownloadUri` . Instruuje agenta, které soubory se mají stáhnout, a hodnotu hash, která se má použít k ověření, že se soubory stáhly správně.|

#### <a name="action"></a>Akce

`Actions` Níže jsou uvedeny akce podniknuté agentem aktualizace zařízení podle pokynů v rámci služby aktualizace zařízení. Agent aktualizace zařízení ohlásí a `State` (viz `State` část výše), ve kterém se zpracovává `Action` přijaté informace. Přečtěte si [pracovní postup](understand-device-update.md#device-update-agent) pro požadavky, které se procházejí službou aktualizace zařízení a agentem aktualizace zařízení.

|Název|Hodnota|Popis|
|---------|-----|-----------|
|Stáhnout|0|Stažení publikovaného obsahu nebo aktualizace a veškerého potřebného obsahu|
|Instalace|1|Nainstalujte obsah nebo aktualizaci. Obvykle to znamená volání instalačního programu pro obsah nebo aktualizaci.|
|Použít|2|Dokončete aktualizaci. V případě potřeby signalizuje restartování systému.|
|Zrušit|255|Zastavit zpracování aktuální akce a přejít zpět na `Idle` . Bude také sloužit k oznámení agenta ve stavu, do kterého se `Failed` má vrátit `Idle` .|

## <a name="device-information-interface"></a>Rozhraní informací o zařízení

Rozhraní informací o zařízení je koncept, který se používá v rámci [architektury IoT technologie Plug and Play](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play). Obsahuje zařízení k vlastnostem cloudu, které poskytují informace o hardwaru a operačním systému zařízení. Aktualizace zařízení pro IoT Hub používá vlastnosti DeviceInformation. Manufacturer a DeviceInformation. model pro telemetrii a diagnostiku. Další informace o rozhraní informace o zařízení najdete v tomto [příkladu](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

Při implementaci tohoto rozhraní je očekávaný název součásti v modelu **deviceInformation** . [Další informace o komponentách Azure IoT PnP](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

|Název|Typ|Schéma|Směr|Popis|Příklad|
|----|----|------|---------|-----------|-----------|
|manufacturer|Vlastnost|řetězec|zařízení do cloudu|Název společnosti výrobce zařízení. To může být stejné jako název výrobce OEM (Original Equipment Manufacturer).|Contoso|
|model|Vlastnost|řetězec|zařízení do cloudu|Název nebo ID modelu zařízení.|IoT Edge zařízení|
|swVersion|Vlastnost|řetězec|zařízení do cloudu|Verze softwaru na zařízení. swVersion může být verze vašeho firmwaru.|4.15.0 – 122|
|osName|Vlastnost|řetězec|zařízení do cloudu|Název operačního systému v zařízení.|Ubuntu Server 18.04|
|processorArchitecture|Vlastnost|řetězec|zařízení do cloudu|Architektura procesoru v zařízení.|ARM64|
|processorManufacturer|Vlastnost|řetězec|zařízení do cloudu|Název výrobce procesoru v zařízení.|Microsoft|
|totalStorage|Vlastnost|řetězec|zařízení do cloudu|Celkové dostupné úložiště v zařízeních v kilobajtech|2 048|
|totalMemory|Vlastnost|řetězec|zařízení do cloudu|Celková dostupná paměť v zařízení v kilobajtech|256|

## <a name="model-id"></a>ID modelu 

ID modelu je způsob, jakým inteligentní zařízení inzerují své možnosti aplikacím Azure IoT pomocí technologie IoT Plug and Play.To. Další informace o tom, jak vytvářet inteligentní zařízení k inzerování svých schopností aplikacím Azure IoT, najdete v tématu [Příručka pro vývojáře pro zařízení iot technologie Plug and Play](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c).

Aktualizace zařízení pro IoT Hub vyžaduje, aby inteligentní zařízení technologie Plug and Play IoT nainformovalo ID modelu s hodnotou **"dtmi: AzureDeviceUpdate; 1"** jako součást připojení zařízení. [Naučte se oznámit ID modelu](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c#model-id-announcement).
