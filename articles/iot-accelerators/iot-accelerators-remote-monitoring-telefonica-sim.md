---
title: Integrace dat SIM do řešení vzdáleného monitorování – Azure| Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak integrovat data telefónica SIM do řešení vzdáleného monitorování.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442209"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrace dat SIM do řešení vzdáleného monitorování

Zařízení IoT se často připojují ke cloudu pomocí SIM karty, která jim umožňuje odesílat datové toky odkudkoli. Řešení vzdáleného monitorování Azure IoT umožňuje integraci dat spravovaného připojení IoT, takže operátoři můžou také sledovat stav zařízení prostřednictvím dat poskytovaných SIM kartou IoT.

Vzdálené monitorování poskytuje imforaci integrace s telefónica IoT connectivity, což umožňuje zákazníkům pomocí platformy IoT Connectivity Platform synchronizovat data připojení zařízení SI s jejich řešeními. Toto řešení lze rozšířit na podporu dalších poskytovatelů připojení IoT prostřednictvím [úložiště](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)GitHub .

V tomto kurzu se naučíte:

* Integrace dat Telefónica IoT SIM do řešení vzdáleného monitorování
* Zobrazení telemetrie v reálném čase
* Zobrazit data SIM

## <a name="telefnica-iot-integration-setup"></a>Nastavení integrace Telefónica IoT

### <a name="prerequisites"></a>Požadavky

Tato další funkce vzdáleného monitorování je aktuálně ve verzi Preview. Chcete-li synchronizovat data o připojení do řešení vzdáleného monitorování Azure, postupujte takto:

1. Vyplňte požadavek na [webu společnosti Telefónica](https://iot.telefonica.com/contact), vyberte možnost **Vzdálené monitorování Azure**, včetně vašich kontaktních údajů.
2. Telefónica aktivuje váš účet.
3. Pokud ještě nejste klientem společnosti Telefónica a chcete si vychutnat tuto nebo jinou službu IoT Connectivity Cloud Ready, navštivte [stránky společnosti Telefónica](https://iot.telefonica.com/) a vyberte možnost **Připojení**.

### <a name="telefnica-sim-setup"></a>Telefónica NASTAVENÍ SIM karty
Telefónica SIM & přidružení ID zařízení Azure Twin je založeno na vlastnosti "alias" aplikace Telefónica IoT SIM. 

Přejděte na [portál Platformy pro připojení IoT Telefónica](https://m2m-movistar-es.telefonica.com/) > > inventář sim vyberte si SIM kartu a aktualizujte každou SIM "alias" pomocí požadovaného id zařízení Twin. Tento úkol lze provést také v hromadném režimu (viz uživatelské příručky platformy Telefónica IoT Connectivity Platform).

Tento úkol lze provést také v hromadném režimu (viz uživatelské příručky platformy Telefónica IoT Connectivity Platform)

![Telefónica Aktualizace](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Chcete-li připojit zařízení ke vzdálenému monitorování, můžete sledovat tyto kurzy pomocí [c](iot-accelerators-connecting-devices-linux.md) nebo [uzlu](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Zobrazení telemetrie zařízení a vlastností SIM karty

Jakmile je váš účet Telefónica správně nakonfigurován a vaše zařízení je připojeno, můžete zobrazit podrobnosti o zařízení a data SIM karty.

Jsou publikovány následující parametry připojení:

* ICCID
* IP adresa
* Přítomnost v síti
* Stav SIM karty
* Umístění v síti
* Spotřebovaný datový provoz

![Řídicí panel](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o tom, jak integrovat data SIM do vzdáleného monitorování Azure IoT, tady jsou navrhované další kroky pro akcelerátory řešení:

* [Správa řešení vzdáleného monitorování Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Realizace pokročilého monitorování](iot-accelerators-remote-monitoring-monitor.md)
* [Správa zařízení](iot-accelerators-remote-monitoring-manage.md)
* [Řešení problémů se zařízeními](iot-accelerators-remote-monitoring-maintain.md)

