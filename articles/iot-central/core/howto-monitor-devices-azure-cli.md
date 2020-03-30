---
title: Monitorování připojení zařízení pomocí Azure IoT Central Explorer
description: Sledujte zprávy zařízení a sledujte změny dvojčete zařízení prostřednictvím rozhraní CLI centrálního průzkumníka IoT.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 09209c21fe1b2b115c1ba6d6e00fcd0ee59a9393
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365424"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorování připojení zařízení s využitím Azure CLI

*Toto téma se týká tvůrců a správců.*

Pomocí rozšíření Azure CLI IoT zobrazíte zprávy, které vaše zařízení odesílají do služby IoT Central, a sledujte změny v dvojčeti zařízení. Tento nástroj můžete použít k ladění a sledování připojení zařízení a diagnostikovat problémy se zprávami zařízení, které nedosahují cloudu nebo zařízení, která nereagují na změny dvojčat.

[Další podrobnosti najdete v odkazu na rozšíření Azure CLI.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Požadavky

+ Azure CLI nainstalován a je verze 2.0.7 nebo vyšší. Zkontrolujte verzi vašeho Azure CLI spuštěním `az --version`. Přečtěte si, jak nainstalovat a aktualizovat z [dokumentů Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Pracovní nebo školní účet v Azure, který byl přidán jako uživatel v aplikaci IoT Central.

## <a name="install-the-iot-central-extension"></a>Instalace rozšíření IoT Central

Spusťte následující příkaz z příkazového řádku a nainstalujte:

```azurecli
az extension add --name azure-iot
```

Zkontrolujte verzi rozšíření spuštěním:

```azurecli
az --version
```

Měli byste vidět rozšíření azure-iot je 0.8.1 nebo vyšší. Pokud tomu tak není, spusťte:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Použití rozšíření

Následující části popisují běžné příkazy a možnosti, `az iot central`které lze použít při spuštění . Chcete-li zobrazit úplnou sadu příkazů `--help` `az iot central` a možností, přejděte do některého z jeho podpříkazů nebo některý z jeho podpříkazů.

### <a name="login"></a>Přihlásit

Začněte přihlášením do příkazového příkazového příkazu Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Získání ID aplikace aplikace IoT Central
V **nastavení správy/aplikace**zkopírujte **ID aplikace**. Tuto hodnotu použijete v pozdějších krocích.

### <a name="monitor-messages"></a>Sledování zpráv
Sledujte zprávy odesílané do aplikace IoT Central z vašich zařízení. Výstup obsahuje všechny hlavičky a poznámky.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Zobrazit vlastnosti zařízení
Zobrazení aktuálních vlastností zařízení pro čtení a čtení a zápis pro dané zařízení.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat IoT Central Explorer, je dalším krokem prozkoumat [správu zařízení IoT Central](howto-manage-devices.md).
