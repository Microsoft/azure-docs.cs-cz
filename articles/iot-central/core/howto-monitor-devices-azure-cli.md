---
title: Monitorování připojení zařízení pomocí Azure IoT Central Exploreru
description: Sledujte zprávy zařízení a sledujte změny v zařízeních přes rozhraní příkazového řádku IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3f18537b4b038844c9aa824593e354c23c792370
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026780"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorování připojení zařízení s využitím Azure CLI

*Toto téma se týká tvůrců a správců.*

Pomocí rozšíření Azure CLI IoT můžete zobrazit zprávy, které vaše zařízení odesílá IoT Central a sledujte změny v zařízení. Tento nástroj můžete použít k ladění a sledování připojení zařízení a diagnostikování problémů se zprávami zařízení, které nedosáhnou cloudu, nebo zařízení, která nereagují na zdvojené změny.

[Další podrobnosti najdete v referenčních informacích k rozšířením Azure CLI.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Požadavky

+ Rozhraní příkazového řádku Azure je nainstalované a má verzi 2.0.7 nebo vyšší. Verzi Azure CLI zjistíte spuštěním `az --version`. Informace o tom, jak nainstalovat a aktualizovat z [dokumentů Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Pracovní nebo školní účet v Azure, který se přidal jako uživatel do aplikace IoT Central.

## <a name="install-the-iot-central-extension"></a>Instalace rozšíření IoT Central

Spusťte následující příkaz z příkazového řádku pro instalaci:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

Ověřte verzi rozšíření spuštěním 
```cmd/sh
az --version
```
Mělo by se zobrazit rozšíření Azure-CLI-IoT-EXT 0.8.1 nebo vyšší. Pokud není, spusťte
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>Použití rozšíření

Následující části popisují běžné příkazy a možnosti, které můžete použít při spuštění `az iot central`. Chcete-li zobrazit úplnou sadu příkazů a možností, předejte `--help` do `az iot central` nebo kteréhokoli jeho dílčích příkazů.

### <a name="login"></a>Přihlášení

Začněte přihlášením k rozhraní příkazového řádku Azure CLI. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Získání ID aplikace IoT Central aplikace
V části **Správa/nastavení aplikace**zkopírujte **ID aplikace**. Použijete ji v pozdějších krocích.

### <a name="monitor-messages"></a>Monitorovat zprávy
Monitorujte zprávy, které se odesílají do vaší IoT Central aplikace z vašich zařízení. To bude zahrnovat všechny hlavičky a poznámky.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Zobrazit vlastnosti zařízení
Zobrazí aktuální vlastnosti zařízení pro čtení a čtení a zápis pro dané zařízení.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat IoT Central Exploreru, je navržený další krok prozkoumat [správu zařízení IoT Central](howto-manage-devices.md).
