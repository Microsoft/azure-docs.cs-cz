---
title: Monitorování nasazení IoT Edge – Azure IoT Edge
description: Monitorování na vysoké úrovni, včetně edgeHub a edgeAgent hlášených vlastností a metrik automatického nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: f4f2f8522f6d3d1039673803d946323deb485db9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200252"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorování nasazení IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge poskytuje vytváření sestav, které vám umožní monitorovat informace v reálném čase o modulech nasazených do vašich IoT Edgech zařízení. Služba IoT Hub načte stav ze zařízení a zpřístupňuje je operátorovi. Monitorování je také důležité pro [nasazení ve velkém měřítku](module-deployment-monitoring.md) , která zahrnují Automatická nasazení a navrstvená nasazení.

Zařízení i moduly mají podobná data, například připojení, takže se hodnoty získávají podle ID zařízení nebo ID modulu.

Služba IoT Hub shromažďuje data hlášená nevlákenou zařízení a modulu a poskytuje počty různých stavů, které zařízení mohou mít. Služba IoT Hub uspořádá tato data do čtyř skupin metrik:

| Typ | Description |
| --- | ---|
| Targeted | Zobrazuje IoT Edge zařízení, která se shodují s podmínkou cílení nasazení. |
| Použito | Zobrazuje cílené IoT Edge zařízení, na která necílí jiné nasazení s vyšší prioritou. |
| Úspěšné generování sestav | Zobrazuje IoT Edge zařízení, která ohlásila, že se moduly úspěšně nasadily. |
| Selhání generování sestav | Zobrazuje IoT Edge zařízení, která oznámily, že jeden nebo více modulů nebyl úspěšně nasazeny. Pokud chcete tuto chybu prozkoumat, připojte se vzdáleně k těmto zařízením a Prohlédněte si soubory protokolů. |

Služba IoT Hub zpřístupňuje tato data, aby je bylo možné monitorovat v Azure Portal a v rozhraní příkazového řádku Azure CLI.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorování nasazení v Azure Portal

Pokud chcete zobrazit podrobnosti o nasazení a monitorovat zařízení, která ho používají, postupujte podle následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k IoT Hub.
1. V nabídce levého podokna vyberte **IoT Edge** .
1. Vyberte kartu **nasazení IoT Edge** .
1. Zkontrolujte seznam nasazení. Pro každé nasazení si můžete zobrazit následující podrobnosti:

    | Sloupec | Popis |
    | --- | --- |
    | ID | Název nasazení. |
    | Typ | Typ **nasazení, nasazení nebo nasazení** v **vrstveném** prostředí. |
    | Cílová podmínka | Značka používaná k definování cílových zařízení. |
    | Priorita | Prioritní číslo přiřazené k nasazení. |
    | Systémové metriky | Počet vláken zařízení v IoT Hub, která odpovídají podmínce cíle. **Použito** určuje počet zařízení, u nichž byl obsah nasazení použit pro jejich modul v IoT Hub. |
    | Metriky zařízení | Počet IoT Edgech zařízení hlásících úspěch nebo chyby z IoT Edge modulu runtime klienta. |
    | Vlastní metriky | Počet IoT Edgech zařízení hlásících data pro všechny metriky, které jste pro nasazení definovali. |
    | Čas vytvoření | Časové razítko od vytvoření nasazení. Toto časové razítko se používá k přerušení vztahů, pokud dvě nasazení mají stejnou prioritu. |

1. Vyberte nasazení, které chcete monitorovat.  
1. Na stránce **Podrobnosti nasazení** se posuňte dolů k dolní části a vyberte kartu **cílová podmínka** . Výběrem **Zobrazit zobrazíte** seznam zařízení, která odpovídají cílové podmínce. Můžete změnit podmínku a také **prioritu**. Pokud jste provedli změny, vyberte **Uložit** .

   ![Zobrazení cílových zařízení pro nasazení](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Vyberte kartu **metriky** . Pokud zvolíte metriku z rozevíracího seznamu **Vybrat metriku** , zobrazí se pro zobrazení výsledků tlačítko **Zobrazit** . Můžete také vybrat **Upravit metriky** a upravit kritéria pro jakékoli vlastní metriky, které jste definovali. Pokud jste provedli změny, vyberte **Uložit** .

   ![Zobrazit metriky pro nasazení](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Chcete-li provést změny v nasazení, přečtěte si téma [Změna nasazení](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorování nasazení pomocí Azure CLI

Podrobnosti o jednom nasazení zobrazíte pomocí příkazu [az IoT Edge Deployment show](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show) :

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Příkaz pro zobrazení nasazení má následující parametry:

* **--Deployment-ID** – název nasazení, které existuje ve službě IoT Hub. Jedná se o požadovaný parametr.
* **--hub – název** – název centra IoT, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu. `az account set -s [subscription name]`

Prozkoumejte nasazení v příkazovém okně. Vlastnost **metriky** uvádí počet pro každou metriku, která je vyhodnocována jednotlivými rozbočovači:

* **targetedCount** – systémová metrika, která určuje počet vláken zařízení v IoT Hub, které odpovídají podmínce cíle.
* **appliedCount** – systémová metrika určuje počet zařízení, ve kterých byl obsah nasazení aplikován na vlákna v modulu IoT Hub.
* **reportedSuccessfulCount** – metrika zařízení, která určuje počet IoT Edge zařízení v rámci generování sestav nasazení v IoT Edge modulu runtime klienta.
* **reportedFailedCount** – metrika zařízení, která určuje počet IoT Edge zařízení v hlášení nasazení, které se nezdařily z modulu runtime klienta IoT Edge.

Pomocí příkazu [az IoT Edge Deployment show-metric](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show-metric) můžete zobrazit seznam ID zařízení nebo objektů pro každou z těchto metrik:

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Příkaz pro zobrazení metriky nasazení má následující parametry:

* **--Deployment-ID** – název nasazení, které existuje ve službě IoT Hub.
* **--metrika-ID** – název metriky, pro kterou chcete zobrazit seznam ID zařízení, například `reportedFailedCount` .
* **--hub – název** – název centra IoT, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu `az account set -s [subscription name]` .

Chcete-li provést změny v nasazení, přečtěte si téma [Změna nasazení](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Další kroky

Naučte se [monitorovat vlákna modulu](how-to-monitor-module-twins.md), primárně IoT Edge agenta a moduly runtime centra IoT Edge, pro připojení a stav nasazení IoT Edge.
