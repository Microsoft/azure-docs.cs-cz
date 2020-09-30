---
title: Most IoT technologie Plug and Play | Microsoft Docs
description: Seznamte se s mostem IoT technologie Plug and Play a Naučte se používat k připojení stávajících zařízení připojených k bráně Windows nebo Linux jako zařízení IoT technologie Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579984"
---
# <a name="iot-plug-and-play-bridge"></a>Most IoT technologie Plug and Play

IoT technologie Plug and Play most je open-source aplikace pro připojení stávajících zařízení připojených k bráně Windows nebo Linux jako zařízení IoT technologie Plug and Play. Po instalaci a konfiguraci aplikace v počítači se systémem Windows nebo Linux ji můžete použít k připojení připojených zařízení ke službě IoT Hub. Pomocí tohoto mostu můžete namapovat rozhraní IoT technologie Plug and Play do telemetrie, která připojená zařízení odesílají, pracují s vlastnostmi zařízení a vyvolávat příkazy.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Na levé straně je k dispozici několik stávajících senzorů (kabelových i bezdrátových) do počítače s Windows nebo Linux, který obsahuje IoT technologie Plug and Play most. IoT technologie Plug and Play most se pak připojí ke službě IoT Hub na pravé straně":::

IoT technologie Plug and Play most se dá nasadit jako samostatný spustitelný soubor na jakémkoli zařízení IoT, v průmyslových počítačích, na serverech nebo v bráně s Windows 10 nebo Linux. Může být také zkompilován do kódu aplikace. Jednoduchý konfigurační soubor JSON oznamuje službě IoT technologie Plug and Play mostu, která připojená zařízení a periferní zařízení by měla být vystavena do Azure.

## <a name="supported-protocols-and-sensors"></a>Podporované protokoly a senzory

IoT technologie Plug and Play most podporuje ve výchozím nastavení následující typy periferních zařízení s odkazy na dokumentaci k adaptéru:

|Okraj|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Ano|Ne|
|[Kamery](https://aka.ms/iot-pnp-bridge-camera)               |Ano|Ne|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Ano|Ano|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Ano|Ano|
|[Sér](https://aka.ms/iot-pnp-bridge-serial)                |Ano|Ano|
|[Periferní zařízení s Windows USB](https://aka.ms/iot-pnp-bridge-usb)  |Ano|Neuvedeno|

>[!Important]
>Vývojáři můžou pomocí pokynů v **[dokumentaci pro vývojáře iot technologie Plug and Play přemostění](https://aka.ms/iot-pnp-bridge-dev-doc)** v rámci dokumentace ke službě IoT technologie Plug and Play Přemostit podporu dalších protokolů zařízení.

## <a name="prerequisites"></a>Předpoklady

### <a name="os-platform"></a>Platforma operačního systému

Podporovány jsou následující platformy a verze operačního systému:

|Platforma  |Podporované verze  |
|---------|---------|
|Windows 10 |     Všechny SKU systému Windows jsou podporovány. Například: IoT Enterprise, server, Desktop, IoT Core. *Pro funkci sledování stavu kamery se doporučuje sestavení 20H1 nebo novějším. Všechny ostatní funkce jsou k dispozici ve všech sestaveních Windows 10.*  |
|Linux     |Testováno a podporováno na Ubuntu 18,04, funkce v jiných distribucích nebyla testována.         |
||

### <a name="hardware"></a>Hardware

- Jakékoli hardwarové platformy, které podporují výše uvedené SKU a verze operačního systému.
- Sériová periferní zařízení, USB, Bluetooth a kamery se nativně podporují. Technologie Plug and Play most IoT můžete rozšířit tak, aby podporoval jakékoli vlastní periferní zařízení nebo senzor ([Viz část periferní zařízení výše](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Vývojové prostředí

K sestavení, rozšiřování a vývoji technologie Plug and Play mostu IoT budete potřebovat:  

- Vývojové prostředí, které podporuje kompilaci C++, jako je například [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/)– Ujistěte se, že při instalaci sady Visual Studio zahrnete úlohu vývoj desktopových aplikací v jazyce c++.
- [Cmake](https://cmake.org/download/) – při instalaci cmake vyberte možnost `Add CMake to the system PATH` .
- Pokud vytváříte systém Windows, budete také muset stáhnout sadu Windows 17763 SDK: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Azure IoT Hub zařízení C SDK](https://github.com/Azure/azure-iot-sdk-c). Zahrnuté skripty sestavení v tomto úložišti automaticky naklonují požadovanou sadu SDK služby Azure IoT C za vás.

### <a name="azure-iot-products-and-tools"></a>Produkty a nástroje Azure IoT

- **Azure IoT Hub** – v předplatném Azure budete potřebovat službu [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) , ke které vaše zařízení připojíte. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/). Pokud Centrum IoT nemáte, [vytvořte ho podle těchto pokynů](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> Služba IoT technologie Plug and Play je aktuálně dostupná ve službě IoT Hub vytvořených v oblastech Střed USA, Severní Evropa a Východní Japonsko. Podpora IoT technologie Plug and Play není součástí centra IoT na úrovni Basic. Pokud chcete komunikovat se zařízením IoT technologie Plug and Play, můžete použít nástroj Azure IoT Explorer. [Stáhněte a nainstalujte si nejnovější verzi Azure IoT Exploreru](./howto-use-iot-explorer.md) pro váš operační systém.

## <a name="iot-plug-and-play-bridge-architecture"></a>Architektura IoT technologie Plug and Play mostu

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Na levé straně je k dispozici několik stávajících senzorů (kabelových i bezdrátových) do počítače s Windows nebo Linux, který obsahuje IoT technologie Plug and Play most. IoT technologie Plug and Play most se pak připojí ke službě IoT Hub na pravé straně":::

## <a name="download-iot-plug-and-play-bridge"></a>Stáhnout IoT technologie Plug and Play most

Můžete si stáhnout předem vytvořenou verzi mostu s podporovanými adaptéry ve službě [IoT technologie Plug and Play mostech](https://aka.ms/iot-pnp-bridge-releases) a rozšířit seznam prostředků pro nejnovější verzi. Stáhněte si nejnovější verzi aplikace pro váš operační systém.

Můžete si také stáhnout a zobrazit zdrojový kód pro [IoT technologie Plug and Play Bridge na GitHubu](https://aka.ms/bridge).

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o architektuře IoT technologie Plug and Play Bridge, další kroky jsou další informace o:

- [Jak používat IoT technologie Plug and Play most](./howto-use-iot-pnp-bridge.md)
- [Přečtěte si referenční informace pro vývojáře GitHubu pro IoT technologie Plug and Play most](https://aka.ms/iot-pnp-bridge-dev-doc)
- [IoT technologie Plug and Play most na GitHubu](https://aka.ms/iotplugandplaybridge)
