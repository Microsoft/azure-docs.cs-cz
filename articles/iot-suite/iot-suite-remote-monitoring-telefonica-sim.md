---
title: Integrace SIM dat v vzdálené monitorování řešení – Azure | Microsoft Docs
description: Tento článek popisuje, jak integrovat Telefonica SIM data do řešení vzdáleného monitorování.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrace SIM dat v řešení vzdáleného monitorování

## <a name="overview"></a>Přehled
Zařízení IoT často připojit ke cloudu pomocí SIM karty, která umožňuje jim odeslat datové proudy z libovolného místa. Řešení Azure IoT vzdálené monitorování umožňuje integraci správy Správce bitových kopií dat, takže operátory také mohou sledovat stav zařízení prostřednictvím údaje poskytnuté SIM. Vzdálené monitorování poskytuje mimo pole integraci s Telefonica IoT, což zákazníkům, kteří používají jeho platformy IoT připojení synchronizovat své zařízení ne všechny připojení data řešení. Toto řešení můžete rozšířit pro zajištění podpory jiných poskytovatelů společnosti telefonní prostřednictvím úložiště GitHub.
V tomto kurzu se naučíte:
* Integrace SIM dat do řešení vzdáleného monitorování
* Zobrazení telemetrie v reálném čase
* Správce bitových kopií dat zobrazení 

## <a name="telefonica-iot-integration-setup"></a>Nastavení integrace Telefonica IoT

### <a name="prerequisites"></a>Požadavky
K synchronizaci dat připojení do řešení vzdáleného monitorování na Azure, postupujte takto:

1.  Vyplnění požadavek na [lokality na Telefonica](https://iot.telefonica.com/contact), vyberte možnost **Azure vzdálené monitorování**, včetně vašich kontaktních údajů.
2.  Telefonica se budou aktivovat svůj účet. 
3.  Pokud klient Telefónica ještě nejste a vy chcete Získejte to nebo jiné služby pro IoT připojení cloudu připraven, navštivte [lokality na Telefonica](https://iot.telefonica.com/contact) a vyberte možnost **připojení**.

### <a name="telefonica-sim-setup"></a>Instalační program Telefonica SIM
Přidružení ID zařízení Telefónica SIM & Azure Twin budou založeny na vlastnosti "alias" Telefónica IoT SIM. 

Přejděte na [Telefónica IoT připojení platformy portál](https://m2m-movistar-es.telefonica.com/) > SIM inventáře > vyberte váš správce bitových kopií a aktualizujte každý SIM "alias" pomocí své deviceID, požadované Twin. 

Tento úkol provést také v režimu hromadně (viz uživatelské příručky Telefónica IoT připojení platformy)

![Aktualizace Telefonica](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Chcete-li připojit zařízení k vzdáleného monitorování, proveďte tyto kurzy pomocí [C](iot-suite-connecting-devices-linux.md) nebo [uzlu](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Zobrazení telemetrie zařízení a SIM – vlastnosti
Jakmile je váš účet Telefonica správně nakonfigurován a vaše zařízení je připojené, můžete zobrazit podrobnosti o zařízení a SIM data.
Následující parametry připojení může být publikována:
* ICCID
* IP adresa
* Přítomnosti sítě
* Stav Správce bitových kopií
* Síťové umístění
* Provoz spotřebované dat

![Řídicí panel](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>Další postup

Teď, když máte přehled o tom, jak integrovat SIM Data do Azure IoT vzdálené monitorování, zde jsou navrhované další kroky pro řešení akcelerátorů:

* [Provozovat řešení Azure IoT vzdálené monitorování](iot-suite-remote-monitoring-explore.md)
* [Realizace pokročilého monitorování](iot-suite-remote-monitoring-monitor.md)
* [Správa zařízení](iot-suite-remote-monitoring-manage.md)
* [Řešení problémů se zařízeními](iot-suite-remote-monitoring-maintain.md)

