---
title: Integrace dat SIM do řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek popisuje, jak integrovat data Telefónica SIM do řešení vzdáleného monitorování.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: 21d84bea808ba5832b81d415001a3fc25a7e1630
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323953"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrace dat SIM do řešení vzdáleného monitorování

Zařízení IoT se často připojují ke cloudu pomocí SIM karty, která umožňuje odesílat datové proudy odkudkoli. Řešení vzdáleného monitorování Azure IoT umožňuje integrovat data o připojení spravovaná službou IoT, takže operátoři můžou sledovat stav zařízení i prostřednictvím dat poskytovaných SIM kartou IoT.

Vzdálené monitorování zajišťuje integraci s připojením Telefónica IoT a umožňuje zákazníkům, kteří využívají svou platformu pro připojení IoT, synchronizovat data SIMs připojení zařízení k jejich řešení. Toto řešení se dá rozšířit tak, aby podporovalo jiné poskytovatele připojení IoT prostřednictvím [úložiště](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)GitHub.

V tomto kurzu se naučíte:

* Integrace dat Telefónica IoT SIM do řešení vzdáleného monitorování
* Zobrazení telemetrie v reálném čase
* Zobrazit data SIM

## <a name="telefnica-iot-integration-setup"></a>Nastavení integrace IoT Telefónica

### <a name="prerequisites"></a>Požadavky

Tato další funkce vzdáleného monitorování je momentálně ve verzi Preview. K synchronizaci dat připojení do řešení vzdáleného monitorování Azure použijte tento postup:

1. Vyplňte žádost na [webu Telefónica's](https://iot.telefonica.com/contact), vyberte možnost **vzdálené monitorování Azure**, včetně vašich kontaktních dat.
2. Telefónica aktivuje váš účet.
3. Pokud ještě nejste klientem Telefónica a chcete využívat tuto službu nebo jiné služby pro cloudové připojení IoT, přejděte na [Web Telefónica's](https://iot.telefonica.com/) a vyberte možnost **připojení**.

### <a name="telefnica-sim-setup"></a>Nastavení SIM Telefónica
Telefónica SIM & přidružení ID zařízení v Azure je založené na vlastnosti "alias" Telefónica IoT SIM. 

Přejděte na [portál Telefónica IoT Connectivity Platform](https://m2m-movistar-es.telefonica.com/) > inventáře SIM > vyberte svou SIM kartu a aktualizujte jednotlivé SIM "alias" s požadovaným příznakem. Tato úloha se dá udělat taky v hromadném režimu (najdete v Telefónica uživatelských příručkách pro konektivitu IoT).

Tato úloha se dá udělat taky v hromadném režimu (najdete v tématu uživatelské příručky k Telefónica IoT Connectivity Platform).

![Telefónica aktualizace](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Pokud chcete zařízení připojit ke vzdálenému monitorování, můžete postupovat podle těchto kurzů pomocí [jazyka C](iot-accelerators-connecting-devices-linux.md) nebo [uzlu](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Zobrazení telemetrie zařízení a vlastností SIM

Jakmile je účet Telefónica správně nakonfigurovaný a vaše zařízení je připojené, můžete zobrazit podrobnosti o zařízení a data SIM.

Publikují se následující parametry připojení:

* ICCID
* IP adresa
* Přítomnost sítě
* Stav SIM
* Síťové umístění
* Spotřebovaný přenos dat

![Snímek obrazovky okna Device Explorer na řídicím panelu vzdáleného monitorování Azure IoT Zobrazí se řádek se zvýrazněnými podrobnostmi o zařízení WeatherStation.](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o tom, jak integrovat data z SIM do Azure IoT Remote Monitoring, je tady navržený další postup pro akcelerátory řešení:

* [Provozování řešení vzdáleného monitorování Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Realizace pokročilého monitorování](iot-accelerators-remote-monitoring-monitor.md)
* [správu zařízení](iot-accelerators-remote-monitoring-manage.md)
* [Řešení potíží se zařízeními](iot-accelerators-remote-monitoring-maintain.md)

