---
title: Principy aktualizace zařízení pro Azure IoT Hub agenta | Microsoft Docs
description: Pochopení aktualizace zařízení pro Azure IoT Hub agenta.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: efe5d0171463668bda19a0d0445fc67f3734aaee
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561232"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Přehled agenta služby Device Update pro službu IoT Hub

Agent aktualizace zařízení se skládá ze dvou koncepčních vrstev:

* Vrstva rozhraní je postavená na [službě Azure IoT technologie Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) , která umožňuje tok zpráv mezi agenty aktualizací zařízení a službami aktualizace zařízení.
* Vrstva platformy zodpovídá za akce aktualizace v rámci vysoké úrovně stahování, instalace a použití, které mohou být specifické pro platformu nebo zařízení.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implementace agenta." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Vrstva rozhraní

Vrstva rozhraní se skládá z [rozhraní adu Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) a [rozhraní informací o zařízení](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Tato rozhraní spoléhají na výchozí hodnoty konfiguračního souboru. Výchozí hodnoty zahrnují aduc_manufacturer a aduc_model pro rozhraní AzureDeviceUpdateCore a model a výrobce pro rozhraní DeviceInformation. [Další informace](device-update-configuration-file.md) o konfiguračním souboru.

### <a name="adu-core-interface"></a>Rozhraní ADU Core

Rozhraní ADU Core je primárním komunikačním kanálem mezi agentem aktualizace zařízení a službami. [Přečtěte si další informace](device-update-plug-and-play.md#adu-core-interface) o tomto rozhraní.

### <a name="device-information-interface"></a>Rozhraní informací o zařízení

Rozhraní informací o zařízení slouží k implementaci `Azure IoT PnP DeviceInformation` rozhraní. [Přečtěte si další informace](device-update-plug-and-play.md#device-information-interface) o tomto rozhraní.

## <a name="the-platform-layer"></a>Vrstva platformy

Vrstva platformy má dvě implementace. Vrstva platformy simulátoru má triviální implementaci akcí aktualizace a používá se k rychlému testování a hodnocení aktualizací zařízení IoT Hub Services a nastavení. Když je agent aktualizace zařízení sestavený s vrstvou platformy simulátoru, odkazujeme na něj jako na agent simulátoru aktualizace zařízení nebo jenom simulátor. [Přečtěte si další informace](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) o tom, jak používat agenta simulátoru. Vrstva platformy Linux se integruje s [optimalizací doručení](https://github.com/microsoft/do-client) pro stažení a používá se v naší referenční imagi malin. PI a všech klientech, kteří se spouštějí v systémech Linux.

### <a name="simulator-platform-layer"></a>Vrstva platformy simulátoru

Implementaci vrstvy simulátoru simulátoru najdete v části `src/platform_layers/simulator_platform_layer` a lze ji použít k testování a vyhodnocení aktualizace zařízení pro IoT Hub.  Mnohé z akcí v "simulátoru" implementace jsou pozměněny tak, aby se snížily fyzické změny v experimentu s aktualizací zařízení pro IoT Hub.  Pomocí této vrstvy platformy můžete provést aktualizaci na konci na konci. [Přečtěte si další informace](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) o spuštění agenta simulátoru.

### <a name="linux-platform-layer"></a>Vrstva platformy Linux

Implementaci vrstvy platformy Linux lze najít v portálu `src/platform_layers/linux_platform_layer` a integruje se s [klientem Optimalizace doručení](https://github.com/microsoft/do-client/releases) pro stahování a používá se v našem referenční imagi malin. PI a všech klientech, kteří se spouštějí v systémech Linux.

Tuto vrstvu lze integrovat s různými obslužnými rutinami aktualizace pro implementaci instalačního programu. Například `SWUpdate` obslužná rutina aktualizace vyvolá skript prostředí pro volání do `SWUpdate` spustitelného souboru za účelem provedení aktualizace.

## <a name="update-handlers"></a>Aktualizovat obslužné rutiny

Obslužné rutiny aktualizace jsou komponenty, které zpracovávají obsah nebo části aktualizace specifické pro instalační službu. Aktualizace implementace obslužných rutin jsou v `src/content_handlers` .

### <a name="simulator-update-handler"></a>Obslužná rutina aktualizace simulátoru

Obslužné rutiny aktualizace simulátoru používá vrstva platformy simulátoru a dá se použít s platformou platformy Linux k falšování interakcí s obslužnou rutinou obsahu. Obslužná rutina aktualizace simulátoru implementuje rozhraní API obslužné rutiny aktualizace s většinou bez operace. Implementaci obslužné rutiny aktualizace simulátoru najdete níže:
* [Simulátor aktualizace bitové kopie](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Simulátor apt aktualizace balíčku](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Poznámka: pole InstalledCriteria v rozhraní AzureDeviceUpdateCore PnP by mělo být SHA256 hodnota hash obsahu. Toto je stejná hodnota hash, která je k dispozici v [objektu import manifestu](import-update.md#create-device-update-import-manifest). [Přečtěte si další informace](device-update-plug-and-play.md) o `installedCriteria` `AzureDeviceUpdateCore` rozhraní a.

### <a name="swupdate-update-handler"></a>`SWUpdate` Aktualizovat obslužnou rutinu

`SWUpdate`Obslužná rutina aktualizace se integruje s `SWUpdate` spustitelným souborem příkazového řádku a dalšími příkazy prostředí pro implementaci aktualizací a/B konkrétně referenčních imagí pro maliny PI. [Zde](https://github.com/Azure/iot-hub-device-update/releases)najdete nejnovější referenční image pro malin. `SWUpdate`Obslužná rutina aktualizace je implementována v [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Obslužná rutina aktualizace APT

Obslužná rutina aktualizace APT zpracuje manifest aktualizace specifický pro APT a vyvolá APT pro instalaci nebo aktualizaci zadaného balíčku debian.

## <a name="self-update-device-update-agent"></a>Samoobslužná aktualizace agenta aktualizace zařízení

Agent aktualizace zařízení a jeho závislosti se dají aktualizovat prostřednictvím aktualizace zařízení pro IoT Hub kanál. Pokud používáte aktualizaci založenou na bitové kopii, zahrňte do nové image nejnovějšího agenta aktualizace zařízení. Pokud používáte aktualizaci založenou na balíčku, zahrňte do manifestu apt jako jakýkoli jiný balíček agenta aktualizací zařízení a jeho požadovanou verzi. [Přečtěte si další informace](device-update-apt-manifest.md) o manifestu apt. V části vlastnosti zařízení v [zařízení IoT](../iot-hub/iot-hub-devguide-device-twins.md)můžete ověřit nainstalovanou verzi agenta aktualizace zařízení a agenta pro optimalizaci doručení. [Další informace o vlastnostech zařízení najdete v části rozhraní adu Core](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Další kroky
[Vysvětlení konfiguračního souboru agenta aktualizace zařízení](device-update-configuration-file.md)