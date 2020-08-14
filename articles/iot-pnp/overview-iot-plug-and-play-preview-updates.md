---
title: Co je nového? Aktualizace IoT technologie Plug and Play Preview | Microsoft Docs
description: Seznamte se s novinkami v rámci aktualizace IoT technologie Plug and Play verze Preview.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: c415ffdaa2eb3ad6a76cd48c3a895b6618dd3986
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208057"
---
# <a name="iot-plug-and-play-preview-refresh"></a>Aktualizace IoT technologie Plug and Play Preview

Tento článek popisuje klíčové změny v sadách SDK, knihovnách, nástrojích a službách v rámci služby IoT technologie Plug and Play verze Preview v červenci 2020. Předchozí verze technologie Plug and Play IoT Preview byla v srpnu 2019.

## <a name="digital-twins-definition-language-dtdl"></a>Jazyk DTDL (Digital autodefinition Definition Language)

Tato verze přidává podporu pro [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) a zastaralá [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview).

V následujícím seznamu jsou uvedeny hlavní rozdíly mezi DTDL V1 a DTDL v2. V DTDL v2:

- ID modelu mají předponu `dtmi` místo `urn` . Identifikátory digitálního vlákna (DTMI) nahrazují `urn` předpony digitálního vyčíslení, které se používají v DTDL v1. Verze nyní předchází `;` . Například dřív, kde jste používali `urn:CompanyName:Model:1` , můžete nyní použít `dtmi:CompanyName:Model;1` .
- Nastavte na `@context` `dtmi:dtdl:context;2` místo `http://azureiot.com/v1/contexts/IoTModel.json` .
- K modelování zařízení použijte typ **rozhraní** místo **CapabilityModel** typů.
- **Komponenty** nahrazují instance **rozhraní** . Můžete definovat **relace** a **součásti** jako součást obsahu **rozhraní**.
- **Rozhraní** může dědit z jiného **rozhraní**.
- DTDL můžete rozšířit pomocí _rozšiřitelných sémantických typů_. Tento rozšiřitelný systém typů nabízí větší flexibilitu než pevně zakódované sémantické typy, jako je teplota a vlhkost v DTDL v1.
- Vlastnost **displayUnit** byla odstraněna.
- V názvu nemůžete použít počáteční ani koncové podtržítko. Úvodní podtržítka v názvu jsou vyhrazena pro použití systémem.

Pokud chcete pracovat s DTDL V1, musíte použít předchozí verzi sady SDK a Azure IoT Explorer 0.10. x. Pokud chcete pracovat s DTDL v2, potřebujete nejnovější verzi sady SDK a Azure IoT Explorer 0,11. x.

## <a name="no-component-and-multiple-component-implementations"></a>Žádná součást a implementace více komponent

Jednoduchá zařízení, která používají několik telemetrie, příkazů nebo vlastností, se dají popsat v jednom rozhraní bez použití komponent. Jakékoli stávající zařízení se může stát IoT technologie Plug and Play tím, že oznamuje **ID modelu** bez jakýchkoli změn stávající implementace zařízení.

Složitější zařízení můžou seskupit telemetrie, příkazy a vlastnosti do různých rozhraní a spravovat tak složitost a povolit opakované použití v zařízeních. Tato zařízení se musí aktualizovat, aby se mohla řídit sada jednoduchých pravidel definovaných ve službě IoT technologie Plug and Play v části pravidla pro [zprávy ve verzi Preview](concepts-convention.md).

## <a name="registration-and-discovery"></a>Registrace a zjišťování

V této verzi zařízení oznamuje své **ID modelu** s IoT Hub při každém připojení. IoT Hub ukládá do mezipaměti **ID modelu** povolující back-end řešení pro načtení **ID modelu** pomocí vlastnosti s dvojitou pamětí zařízení `modelId` . **ID modelu** lze také načíst z `$metadata.$model` digitálního vlákna.

## <a name="microsoft-defined-interfaces"></a>Rozhraní definovaná společností Microsoft

Následující rozhraní definovaná společností Microsoft jsou zastaralá a nejsou publikována v novém úložišti modelů:

- **urn: azureiot: ModelDiscovery: DigitalTwin: 1**
- **urn: azureiot: ModelDiscovery: ModelInformation: 1**

Následující rozhraní je Publikováno v novém úložišti modelu: `dtmi:azure:DeviceManagement:DeviceInformation;1` k dispozici v adrese URL [ https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation ; 1? API-Version = 2020-05 -01-Preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

Struktura události [zdroje událostí](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) **DigitalTwinChangeEvents** se změnila tak, aby používala formát **JSON-patch** . Tato změna představuje zásadní změnu bez podpory zpětné kompatibility.

## <a name="message-routing"></a>Směrování zpráv

Zprávy telemetrie mají v rámci kolekce [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) následující změny.

Teď obsahuje vlastnost **schématu DT-** data, která je **ID modelu** registrované zařízením.

Vlastnost **DT-Subject** reprezentuje součást odesílající zprávu telemetrie.

Vlastnost **iothub-Interface-Name** je zastaralá.

## <a name="device-and-service-sdks"></a>Sady SDK pro zařízení a služby

Neexistuje žádná zpětná kompatibilita s předchozími verzemi Preview sady SDK. Pokud přejdete na nejnovější verzi Preview sady SDK, budete muset změnit kód.

V případě přístupu založeného na konvenci není potřeba používat samostatné klientské sady SDK pro zařízení. Z této verze Preview se stávající knihovny **DigitalTwinClient** ve všech jazycích zastaraly. Místo toho byly aktualizovány klientské sady SDK IoT Hub zařízení, aby obsahovaly možnost oznámit **ID modelu**.

Zařízení, která nepoužívají komponenty, vyžadují minimální změny kódu – pouze oznamujeme **ID modelu**. Složitější zařízení, která používají víc součástí, můžou pro implementaci těchto [konvence](concepts-convention.md)vyžadovat některé z opakovaně použitelných funkcí. Ukázky zařízení zahrnují sadu funkcí, které můžete použít při implementaci zařízení.

### <a name="service-sdks"></a>Sady SDK pro služby

Sada SDK služby je dostupná v [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) a [Pythonu](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/README.md).

## <a name="vs-code-extension"></a>Rozšíření VS Code

Rozšíření [Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) poskytuje podporu vytváření obsahu pro DTDL V1, integraci s předchozí verzí úložiště modelu a generování kódu.

Pokud požadujete podporu vytváření DTDL V2 v VS Code, nainstalujte nové [rozšíření DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) v vs Code. Rozšíření neposkytuje integraci s úložištěm modelu nebo generováním kódu. Správa modelů v úložišti se teď provádí pomocí [webového uživatelského rozhraní](https://aka.ms/iotmodelrepo) nebo [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest).

## <a name="digital-twin-service-side-rest-apis"></a>Digitální vlákna REST API na straně služby

[Digitální rozhraní REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) a datové části na straně služby se změnily. Podporovaná rozhraní API jsou:

- Načtení digitálního vlákna.
- Zavolejte příkaz.
- Aktualizace digitálního vlákna pomocí datové části **JSON-patch**

V této verzi jsou nadále podporovány existující rozhraní REST API.

## <a name="model-repository"></a>Úložiště modelů

Teď je k dispozici jediné úložiště modelů, které obsahuje veřejné publikované modely, a soukromé modely společnosti chráněné pomocí RBAC. Všechny modely mají jedinečný identifikátor a po vytvoření jsou neměnné.

Existující úložiště modelu společnosti z předchozí verze nejsou v této verzi podporovaná. Ke správě starých modelů DTDL v1 můžete dál používat web [Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com/products) . Tento web už ale nebudete moct používat k registraci, testování a certifikaci zařízení.

## <a name="azure-iot-central"></a>Azure IoT Central

Služba Azure IoT Central se momentálně aktualizuje, aby podporovala technologie Plug and Play aktualizace pro IoT ve verzi Preview.
