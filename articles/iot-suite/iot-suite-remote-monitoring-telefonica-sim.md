---
title: Integrace SIM dat v vzdálené monitorování řešení – Azure | Microsoft Docs
description: Tento článek popisuje, jak integrovat Telefónica SIM data do řešení vzdáleného monitorování.
services: iot-suite
suite: iot-suite
author: hegate
manager: timlt
ms.author: hegate
ms.service: iot-suite
ms.date: 05/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 25fefa23eaa0ca38f8d3d888f5782ece221e8b1a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrace SIM dat v řešení vzdáleného monitorování

Zařízení IoT často připojit ke cloudu pomocí SIM karty, která umožňuje jim odeslat datové proudy z libovolného místa. Řešení Azure IoT vzdálené monitorování umožňuje integraci dat spravované připojení IoT, tak, aby operátory také mohou sledovat stav zařízení prostřednictvím údaje poskytnuté IoT SIM.

Vzdálené monitorování poskytuje mimo pole integraci s Telefónica IoT připojení, což zákazníkům, kteří používají jeho platformy IoT připojení synchronizovat své zařízení ne všechny připojení dat k jejich řešení. Toto řešení můžete rozšířit pro zajištění podpory jiných poskytovatelů IoT připojení přes GitHub [úložiště](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

V tomto kurzu se naučíte:

* Integrace Telefónica IoT SIM dat do řešení vzdáleného monitorování
* Zobrazení telemetrie v reálném čase
* Správce bitových kopií dat zobrazení

## <a name="telefnica-iot-integration-setup"></a>Nastavení integrace Telefónica IoT

### <a name="prerequisites"></a>Požadavky

Tato další funkce pro vzdálené monitorování je aktuálně ve verzi preview. K synchronizaci dat připojení do řešení vzdáleného monitorování na Azure, postupujte takto:

1. Vyplnění požadavek na [lokality na Telefónica](https://iot.Telefónica.com/contact), vyberte možnost **Azure vzdálené monitorování**, včetně vašich kontaktních údajů.
2. Telefónica aktivuje váš účet.
3. Pokud klient Telefónica ještě nejste a vy chcete Získejte to nebo jiné služby pro IoT připojení cloudu připraven, navštivte [lokality na Telefónica](https://iot.Telefónica.com/contact) a vyberte možnost **připojení**.

### <a name="telefnica-sim-setup"></a>Instalační program Telefónica SIM
Telefónica SIM & Azure Twin přidružení ID zařízení je založena na vlastnosti "alias" Telefónica IoT SIM. 

Přejděte na [Telefónica IoT připojení platformy portál](https://m2m-movistar-es.telefonica.com/) > SIM inventáře > vyberte váš správce bitových kopií a aktualizujte každý SIM "alias" pomocí své deviceID, požadované Twin. Tento úkol provést také v režimu hromadně (viz Telefónica IoT připojení platformy uživatelské příručky).

Tento úkol provést také v režimu hromadně (viz uživatelské příručky Telefónica IoT připojení platformy)

![Aktualizace Telefónica](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Chcete-li připojit zařízení k vzdáleného monitorování, proveďte tyto kurzy pomocí [C](iot-suite-connecting-devices-linux.md) nebo [uzlu](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Zobrazení telemetrie zařízení a SIM – vlastnosti

Jakmile je váš účet Telefónica správně nakonfigurován a vaše zařízení je připojené, můžete zobrazit podrobnosti o zařízení a SIM data.

Jsou publikovány následující parametry připojení:

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

