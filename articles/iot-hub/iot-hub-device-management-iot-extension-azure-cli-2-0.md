---
title: "Azure IoT správu zařízení IoT rozšíření pro Azure CLI 2.0 | Microsoft Docs"
description: "Používáte IoT rozšíření pro Azure CLI 2.0 nástroj pro správu zařízení Azure IoT Hub, poskytuje funkci přímé metody a možnosti správy Twin požadované vlastnosti."
services: iot-hub
documentationcenter: 
author: chrissie926
manager: timlt
tags: 
keywords: "Správa zařízení Azure iot, správou zařízení azure iot hub, iot správy zařízení, správou zařízení iot hub"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 07b9f14048b6618863efd5bd8eb8bcc8f52ec735
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Použití rozšíření IoT pro Azure CLI 2.0 pro správu zařízení Azure IoT Hub

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) je nové rozšíření IoT, které přidá do funkcí open source [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest) obsahuje příkazy pro interakci s Azure resource manager a správa koncových bodů. Azure CLI 2.0 obsahuje příkazy pro interakci s Azure resource manager a správa koncových bodů. Například můžete 2.0 rozhraní příkazového řádku Azure k vytvoření virtuálního počítače Azure nebo služby IoT Hub. Rozšíření rozhraní příkazového řádku umožňuje služby Azure k posílení rozhraní příkazového řádku Azure udělíte přístup k další možnosti specifickou pro službu. Rozšíření IoT poskytuje vývojářům IoT příkazového řádku přístup k funkcím služby IoT Hub, IoT okraj a IoT Hub zařízení zřizování služby.

| Možnost správy          | Úkol                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Přímé metody             | Nastavit zařízení fungovat jako je například spuštění nebo zastavení odesílání zpráv nebo restartování zařízení.                                        |
| Vlastnosti Twin potřeby    | Umístěte zařízení do určité stavy, například nastavit DIODU na zelenou nebo nastavení intervalu odeslání telemetrie do 30 minut.         |
| Twin hlášené vlastnosti   | Zjištění hlášené stavu zařízení. Například zařízení ohlásí, že nyní bliká Indikátor.                                    |
| Značky Twin                  | Metadata specifická pro zařízení ukládat v cloudu. Například nasazení umístění prodejních počítače.                         |
| Dotazy twin zařízení        | Dotaz na všechny dvojčata zařízení k načtení ty s libovolné podmínky, jako je identifikace zařízení, která jsou k dispozici pro použití. |

Podrobnější vysvětlení na rozdíly a pokyny k použití těchto možností najdete v článku [pokyny komunikace zařízení cloud](iot-hub-devguide-d2c-guidance.md) a [Cloud zařízení komunikace pokyny](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub trvá dvojče zařízení pro každé zařízení, která se k němu připojuje. Další informace o dvojčata zařízení najdete v tématu [začít pracovat s dvojčata zařízení](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte pomocí rozšíření IoT pro Azure CLI 2.0 pomocí různých možností správy na vývojovém počítači.

## <a name="what-you-do"></a>Co dělat

Spusťte 2.0 rozhraní příkazového řádku Azure a IoT rozšíření pro Azure CLI 2.0 s různé možnosti správy.

## <a name="what-you-need"></a>Co potřebujete

- Kurz [nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončit, která zahrnuje následující požadavky:
  - Aktivní předplatné Azure.
  - V rámci svého předplatného služby Azure IoT hub.
  - Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.

- Zajistěte, aby že vaše zařízení se systémem s klienta aplikace v průběhu tohoto kurzu.

- [Python 2.7x nebo Python 3.x](https://www.python.org/downloads/)

- Nainstalujte rozhraní příkazového řádku Azure 2.0. Jeden způsob, jak nainstalovat v systému Windows je ke stažení a instalaci [MSI](https://aka.ms/InstallAzureCliWindows). Můžete také postupujte podle pokynů pro instalaci [Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) nastavení Azure CLI 2.0 ve vašem prostředí. Minimálně musí být vaší verzi 2.0 rozhraní příkazového řádku Azure 2.0.24 nebo vyšší. Použití `az –version` k ověření. 

- Nainstalujte rozšíření IoT. Nejjednodušší způsob, jak se má spustit `az extension add --name azure-cli-iot-ext`. [V souboru readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) popisuje několik způsobů, jak nainstalovat rozšíření.


## <a name="log-in-to-your-azure-account"></a>Přihlaste se k účtu Azure

Přihlaste se k účtu Azure tak, že spustíte následující příkaz:

```bash
az login
```

## <a name="direct-methods"></a>Přímé metody

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Vlastnosti twin požadovaného zařízení

Nastavení intervalu požadovanou vlastnost = 3000 spuštěním následujícího příkazu:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Tuto vlastnost lze číst ze zařízení.

## <a name="device-twin-reported-properties"></a>Dvojče zařízení hlášené vlastnosti

Umožňuje získáte vlastnosti hlášených zařízení tak, že spustíte následující příkaz:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.reported.interval = 3000
```

Jedna z vlastností je $metadata. $lastUpdated který ukazuje čas poslední toto zařízení odeslání nebo přijetí zprávy.

## <a name="device-twin-tags"></a>Značky twin zařízení

Zobrazení značek a vlastnosti zařízení tak, že spustíte následující příkaz:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Přidání role pole = teploty a vlhkosti do zařízení tak, že spustíte následující příkaz:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Dotazy twin zařízení

Dotaz na zařízení s značkou role = 'teploty a vlhkosti' spuštěním následujícího příkazu:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Dotaz na všechna zařízení kromě těch, které se značkou role = 'teploty a vlhkosti' spuštěním následujícího příkazu:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Další postup

Když jste se naučili sledování zpráv typu zařízení cloud a odesílání zpráv typu cloud zařízení mezi zařízení IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]