---
title: Správa zařízení Azure IoT s rozšířením IoT pro rozhraní příkazového příkazového nastavení Azure | Dokumenty společnosti Microsoft
description: Použijte rozšíření IoT pro nástroj Azure CLI pro správu zařízení Azure IoT Hub, který obsahuje přímé metody a možnosti správy požadovaných vlastností twin.
author: chrissie926
manager: ''
keywords: Správa zařízení azure iot, správa zařízení Azure iot hub, iot pro správu zařízení a správa zařízení iot hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 88c3d1f4213b161d5e322349a7f0e1bc1dd952e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239643"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Použití rozšíření IoT pro azure cli pro správu zařízení Azure IoT Hub

![Diagram koncového bodu](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) je open source rozšíření IoT, které přidává do možností [vázací příkazy Kavi .](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) Azure CLI obsahuje příkazy pro interakci s Azure Resource Manager a koncové body pro správu. Například můžete použít Azure CLI k vytvoření virtuálního počítače Azure nebo služby IoT hub. Rozšíření příkazového příkazového příkazu umožňuje službě Azure rozšířit vyvázlá cínek Azure, která vám umožní přístup k dalším funkcím specifickým pro službu. Rozšíření IoT poskytuje vývojářům IoT přístup k příkazovému řádku pro všechny funkce služby IoT Hub, IoT Edge a IoT Hub Device Provisioning Service.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost řízení          | Úkol  |
|----------------------------|-----------|
| Přímé metody             | Nastavíte zařízení tak, aby spouštění nebo zastavování odesílání zpráv nebo restartování zařízení.                                        |
| Dvojité požadované vlastnosti    | Přepni zařízení do určitých stavů, například nastavení led diody na zelenou nebo nastavení intervalu odesílání telemetrie na 30 minut.         |
| Vlastnosti dvojitého vykazuje   | Získejte nahlášený stav zařízení. Zařízení například hlásí, že led dioda nyní bliká.                                    |
| Dvojité značky                  | Uklápěte metadata specifická pro zařízení do cloudu. Například umístění nasazení prodejního automatu.                         |
| Dotazy na dvojče zařízení        | Dotaz na všechna dvojčata zařízení k načtení těchto dvojčat s libovolnými podmínkami, jako je například identifikace zařízení, která jsou k dispozici pro použití. |

Podrobnější vysvětlení rozdílů a pokyny k použití těchto možností naleznete v tématu [Pokyny pro komunikaci mezi zařízeními](iot-hub-devguide-d2c-guidance.md) a pokyny ke komunikaci mezi [cloudem](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub zachová dvojče zařízení pro každé zařízení, které se k němu připojí. Další informace o dvojčatech zařízení najdete [v tématu Začínáme s dvojčaty zařízení](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Co se naučíte

Naučíte se používat rozšíření IoT pro Azure CLI s různými možnostmi správy na vývojovém počítači.

## <a name="what-you-do"></a>Co děláte

Spusťte Azure CLI a rozšíření IoT pro Azure CLI s různými možnostmi správy.

## <a name="what-you-need"></a>Co potřebujete

* Dokončete kurz [online simulátoru Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) nebo jeden z výukových programů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto položky pokrývají následující požadavky:

  - Aktivní předplatné Azure.
  - Azure IoT hub v rámci vašeho předplatného.
  - Klientská aplikace, která odesílá zprávy do centra Azure IoT hub.

* Ujistěte se, že vaše zařízení běží s klientskou aplikací během tohoto kurzu.

* [Python 2.7x nebo Python 3.x](https://www.python.org/downloads/)

* The Azure CLI. Pokud ji potřebujete nainstalovat, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Minimálně vaše verze Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az –version`.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Nainstalujte rozšíření IoT. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-iot`. Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) obsahuje popis několika způsobů instalace rozšíření.

## <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Přihlaste se ke svému účtu Azure spuštěním následujícího příkazu:

```azurecli
az login
```

## <a name="direct-methods"></a>Přímé metody

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Požadované vlastnosti dvojčete zařízení

Spuštěním následujícího příkazu nastavte požadovaný interval vlastností = 3000:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Tuto vlastnost lze číst z vašeho zařízení.

## <a name="device-twin-reported-properties"></a>Vlastnosti ohlášené dvojčeti zařízení

Získejte ohlášené vlastnosti zařízení spuštěním následujícího příkazu:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jedna z ohlášených vlastností dvojčete je $metadata.$lastUpdated, která zobrazuje, kdy aplikace zařízení naposledy aktualizovala svou ohlášenou sadu vlastností.

## <a name="device-twin-tags"></a>Značky dvojčat zařízení

Zobrazte značky a vlastnosti zařízení pomocí následujícího příkazu:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Přidejte roli pole = teplota&vlhkost do zařízení spuštěním následujícího příkazu:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Dotazy na dvojče zařízení

Dotazovací zařízení se značkou role = "teplota&vlhkost" spuštěním následujícího příkazu:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Dotazujte se na všechna zařízení kromě zařízení se značkou role = "teplota&vlhkost" spuštěním následujícího příkazu:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak monitorovat zprávy mezi zařízeními a cloudy a odesílat zprávy z cloudu na zařízení mezi zařízením IoT a službou Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
