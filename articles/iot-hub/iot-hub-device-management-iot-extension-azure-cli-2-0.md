---
title: Správa zařízení Azure IoT pomocí rozšíření IoT pro Azure CLI | Microsoft Docs
description: Pro správu zařízení Azure IoT Hub použijte rozšíření IoT pro Azure CLI, které nabízí přímé metody a možnosti správy požadovaných vlastností.
author: chrissie926
manager: ''
keywords: Správa zařízení Azure IoT, Správa zařízení ve službě IoT Hub, Správa zařízení IoT, Správa zařízení ve službě IoT Hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: a5bc7e195efd62f430fdf2aa0cb606dbcff79528
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567192"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Použití rozšíření IoT pro Azure CLI pro správu zařízení IoT Hub Azure

![Komplexní diagram](media/iot-hub-get-started-e2e-diagram/2.png)

V tomto článku se dozvíte, jak používat rozšíření IoT pro Azure CLI s různými možnostmi správy ve vývojovém počítači. [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) je open source rozšíření IoT, které přináší možnosti [Azure CLI](/cli/azure/overview). Rozhraní příkazového řádku Azure CLI obsahuje příkazy pro komunikaci s koncovými body Azure Resource Manager a správy. Pomocí Azure CLI můžete například vytvořit virtuální počítač Azure nebo centrum IoT. Rozšíření CLI umožňuje službě Azure rozšířit Azure CLI a získat tak přístup k dalším funkcím specifickým pro službu. Rozšíření IoT poskytuje vývojářům IoT přístup k funkcím příkazového řádku pro všechny funkce IoT Hub, IoT Edge a IoT Hub Device Provisioning Service.

| Možnost správy          | Úkol  |
|----------------------------|-----------|
| Přímé metody             | Udělejte zařízení jako takové, jako je spuštění nebo zastavení odesílání zpráv nebo restartování zařízení.                                        |
| Vyplňování požadovaných vlastností    | Vložte zařízení do určitých stavů, jako je například nastavení indikátoru LED na zelenou nebo nastavení intervalu odesílání telemetrie na 30 minut.         |
| Nedokončené vlastnosti   | Načte stav nahlášeného zařízení. Například zařízení nahlásí, že indikátor LED právě bliká.                                    |
| Zdvojené značky                  | Ukládat metadata specifická pro zařízení v cloudu. Například umístění nasazení počítače prodejní Automate.                         |
| Nevlákenné dotazy zařízení        | Dotázat se na všechna vlákna zařízení, aby se tyto vlákna načetly s libovolnými podmínkami, jako je určení zařízení, která jsou k dispozici pro použití. |

Podrobnější vysvětlení rozdílů a pokynů k používání těchto možností najdete v tématu [pokyny k komunikaci mezi zařízeními a cloudem](iot-hub-devguide-d2c-guidance.md) a [pokyny pro komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub přetrvává pro každé zařízení, které se k němu připojuje. Další informace o nevlákenách zařízení najdete v tématu [Začínáme s dvojitými zprávami](iot-hub-node-node-twin-getstarted.md)o zařízení.

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Požadavky

* Dokončete kurz [online simulátoru malin](iot-hub-raspberry-pi-web-simulator-get-started.md) . nebo v některém z kurzů zařízení. Například můžete přejít na [malinu Pi pomocí node.js](iot-hub-raspberry-pi-kit-node-get-started.md) nebo do jednoho z rychlých startů pro [odeslání telemetrie](quickstart-send-telemetry-dotnet.md) . Tyto články se týkají následujících požadavků:

  * Musíte mít aktivní předplatné Azure.
  * Azure IoT Hub v rámci vašeho předplatného.
  * Klientská aplikace, která odesílá zprávy do služby Azure IoT Hub.

* Ujistěte se, že vaše zařízení je spuštěné pomocí klientské aplikace v průběhu tohoto kurzu.

* [Python 2.7 x nebo Python 3. x](https://www.python.org/downloads/)

* Rozhraní příkazového řádku Azure Pokud ho potřebujete nainstalovat, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). Minimální verze rozhraní příkazového řádku Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az –version`.

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

## <a name="device-twin-desired-properties"></a>Požadované vlastnosti vlákna zařízení

Nastavte požadovaný interval vlastnosti = 3000 spuštěním následujícího příkazu:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Tato vlastnost se dá číst z vašeho zařízení.

## <a name="device-twin-reported-properties"></a>Nedokončené hlášené vlastnosti zařízení

Nahlášené vlastnosti zařízení získáte spuštěním následujícího příkazu:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jedna z nedokončených hlášených vlastností je $metadata. $lastUpdated, která zobrazuje čas poslední aktualizace hlášené sady vlastností aplikací zařízení.

## <a name="device-twin-tags"></a>Značky, které jsou v zařízení

Zobrazte značky a vlastnosti zařízení spuštěním následujícího příkazu:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Spuštěním následujícího příkazu přidejte do zařízení roli Field&vlhkosti:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Nevlákenné dotazy zařízení

Spusťte dotaz na zařízení se značkou role = ' teplota&vlhkosti ' spuštěním následujícího příkazu:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Dotázat se na všechna zařízení s výjimkou toho, že s příznakem role = ' teplota&vlhkosti ' spuštěním následujícího příkazu:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak monitorovat zprávy ze zařízení do cloudu a jak odesílat zprávy z cloudu na zařízení mezi zařízením IoT a službou Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]