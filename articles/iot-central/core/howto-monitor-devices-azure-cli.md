---
title: Monitorování připojení zařízení pomocí Azure IoT Central Exploreru
description: Sledujte zprávy zařízení a sledujte změny v zařízeních přes rozhraní příkazového řádku IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 62981686c7aadc713c4abc78075be8613fe0af45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199302"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorování připojení zařízení s využitím Azure CLI

*Toto téma se týká vývojářů zařízení a tvůrců řešení.*

Pomocí rozšíření Azure CLI IoT můžete zobrazit zprávy, které vaše zařízení odesílá IoT Central a sledujte změny v zařízení. Tento nástroj můžete použít k ladění a sledování připojení zařízení a diagnostikování problémů se zprávami zařízení, které nedosáhnou cloudu, nebo zařízení, která nereagují na zdvojené změny.

[Další podrobnosti najdete v referenčních informacích k rozšířením Azure CLI.](/cli/azure/ext/azure-iot/iot/central)

## <a name="prerequisites"></a>Předpoklady

+ Rozhraní příkazového řádku Azure je nainstalované a má verzi 2.7.0 nebo vyšší. Ověřte verzi rozhraní příkazového řádku Azure spuštěním `az --version` . Informace o tom, jak nainstalovat a aktualizovat z [dokumentů Azure CLI](/cli/azure/install-azure-cli)
+ Pracovní nebo školní účet v Azure, který se přidal jako uživatel do aplikace IoT Central.

## <a name="install-the-iot-central-extension"></a>Instalace rozšíření IoT Central

Spusťte následující příkaz z příkazového řádku pro instalaci:

```azurecli
az extension add --name azure-iot
```

Ověřte verzi rozšíření spuštěním:

```azurecli
az --version
```

Měli byste vidět, že rozšíření Azure-IoT je 0.9.9 nebo novější. Pokud tomu tak není, spusťte příkaz:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Použití rozšíření

Následující části popisují běžné příkazy a možnosti, které můžete použít při spuštění `az iot central` . Chcete-li zobrazit úplnou sadu příkazů a možností, předejte `--help` `az iot central` nebo libovolné dílčí příkazy.

### <a name="login"></a>Přihlásit

Začněte přihlášením k rozhraní příkazového řádku Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Získání ID aplikace IoT Central aplikace
V části **Správa/nastavení aplikace** zkopírujte **ID aplikace**. Tuto hodnotu použijete v pozdějších krocích.

### <a name="monitor-messages"></a>Monitorovat zprávy
Monitorujte zprávy, které se odesílají do vaší IoT Central aplikace z vašich zařízení. Výstup zahrnuje všechny hlavičky a poznámky.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Zobrazit vlastnosti zařízení
Zobrazí aktuální vlastnosti zařízení pro čtení a čtení a zápis pro dané zařízení.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, navržený další krok si přečtěte informace o [připojení zařízení v Azure IoT Central](./concepts-get-connected.md).