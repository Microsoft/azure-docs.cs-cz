---
title: Integrace dat SIM ve vzdálené monitorování řešení – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak integrace dat Telefónica SIM do řešení vzdáleného monitorování.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: c453998eea2a747b2cb608482f0ef9c1ee197ee0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185427"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrace dat SIM v řešení vzdáleného monitorování

Zařízení IoT často připojit ke cloudu pomocí SIM karty, která umožňuje jim odeslat datové proudy z libovolného místa. Řešení vzdáleného monitorování Azure IoT umožňuje integraci dat IoT spravovat připojení tak, aby operátory můžete také sledovat stav zařízení prostřednictvím údaje poskytnuté IoT SIM.

Vzdálené monitorování poskytuje mimo okamžité integrace s připojením Telefónica IoT, umožňuje, že zákazníci, kteří používají jeho připojení platformy IoT synchronizace jejich zařízení všechny připojení dat k jejich řešení. Toto řešení je možné rozšířit na podporu jiných poskytovatelů připojení IoT přes GitHub [úložiště](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

V tomto kurzu se naučíte:

* Integrace dat Telefónica IoT SIM do řešení vzdáleného monitorování
* Zobrazení telemetrie v reálném čase
* Zobrazení dat SIM

## <a name="telefnica-iot-integration-setup"></a>Nastavení integrace Telefónica IoT

### <a name="prerequisites"></a>Požadavky

Tato další funkce vzdáleného monitorování je aktuálně ve verzi preview. Synchronizovat připojení dat do Azure řešení vzdáleného monitorování, postupujte takto:

1. Vyplnit žádost na [Telefónica vaší lokality](https://iot.telefonica.com/contact), vyberte možnost **vzdáleného monitorování Azure**, včetně vaše kontaktní údaje.
2. Telefónica aktivuje vašeho účtu.
3. Pokud klient Telefónica ještě nejste a Užijte si to nebo jiné služby IoT připojení cloudu připravený, navštivte [Telefónica vaší lokality](https://iot.telefonica.com/) a vyberte možnost **připojení**.

### <a name="telefnica-sim-setup"></a>Instalační program Telefónica SIM
Vlastnost "alias" Telefónica IoT SIM vychází Telefónica SIM & Azure Dvojčete zařízení ID přidružení. 

Přejděte do [Telefónica IoT připojení platformy portál](https://m2m-movistar-es.telefonica.com/) > inventář SIM > vyberte váš správce bitových kopií a aktualizovat vaše požadované ID Dvojčete zařízení každý SIM "alias". Tato úloha je možné provést v režimu hromadně (viz část o platformy připojení IoT Telefónica uživatelské příručky).

Tato úloha je možné provést v režimu hromadně (viz část o uživatelské příručky Telefónica IoT připojení platformy)

![Aktualizace Telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Připojte zařízení pro vzdálené monitorování, můžete postupovat podle těchto kurzů pomocí [C](iot-accelerators-connecting-devices-linux.md) nebo [uzel](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Zobrazení telemetrie zařízení a SIM – vlastnosti

Jakmile je váš účet Telefónica správně nakonfigurován a vaše zařízení je připojené, můžete zobrazit podrobnosti o zařízení a dat SIM.

Následující parametry připojení jsou publikovány:

* ICCID
* IP adresa
* Přítomnost sítě
* Stav SIM
* Síťové umístění
* Přenos zpracovaných dat

![Řídicí panel](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Další postup

Teď, když máte přehled o tom, jak integrace dat SIM do vzdáleného monitorování Azure IoT, tady jsou další navrhované kroky pro akcelerátory řešení:

* [Provozovat řešení vzdáleného monitorování Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Realizace pokročilého monitorování](iot-accelerators-remote-monitoring-monitor.md)
* [Správa zařízení](iot-accelerators-remote-monitoring-manage.md)
* [Řešení problémů se zařízeními](iot-accelerators-remote-monitoring-maintain.md)

