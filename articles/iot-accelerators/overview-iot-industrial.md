---
title: Přehled průmyslového IoT Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled průmyslového IoT. Vysvětluje propojené tovární, tovární připojení a bezpečnostní komponenty v IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73828128"
---
# <a name="what-is-industrial-iot-iiot"></a>Co je průmyslový IoT (IIoT)

IIoT je průmyslový internet věcí. IIoT zvyšuje efektivitu průmyslu prostřednictvím aplikace IoT ve zpracovatelském průmyslu. 

## <a name="improve-industrial-efficiencies"></a>Zvýšení průmyslové efektivity

Zvyšte svou provozní produktivitu a ziskovost pomocí připojeného akcelerátoru továrního řešení. Připojte a monitorujte svá průmyslová zařízení a zařízení v cloudu – včetně vašich strojů, které již pracují ve výrobním závodě. Analyzujte data IoT a nazvěte přehledy, které vám pomohou zvýšit výkon celé výrobní haly.

Snižte časově náročný proces přístupu k strojům v továrně s OPC Twin a zaměřte svůj čas na vytváření řešení IIoT. Zjednodušte správu certifikátů a integraci průmyslových aktiv pomocí OPC Vault u sebe a mějte jistotu, že je konektivita aktiv zabezpečena. Tyto mikroslužby poskytují rozhraní API podobné rest nad [součástmi Azure Industrial IoT](https://github.com/Azure/azure-iiot-opc-ua). Rozhraní API služby umožňuje řízení funkce hraničního modulu. 

![Přehled průmyslového IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Další informace o službách Azure Industrial IoT najdete v [tématu úložiště](https://github.com/Azure/azure-iiot-services)GitHub .
Pokud nejste obeznámeni s tím, jak fungují moduly Azure IoT Edge, začněte následujícími články:
- [Informace o Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Moduly Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Propojená továrna

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) je implementace referenční architektury Azure Industrial IoT od Microsoftu, kterou lze přizpůsobit tak, aby splňovala konkrétní obchodní požadavky. Úplný kód řešení je open source a je k dispozici v úložišti GitHub urychlovače řešení Připojené továrny. Můžete ji použít jako výchozí bod pro komerční produkt a nasadit předem vymyšlené řešení do předplatného Azure během několika minut. 

## <a name="factory-floor-connectivity"></a>Konektivita výrobních hal

OPC Twin je komponenta IIoT, která automatizuje zjišťování a registraci zařízení a nabízí dálkové ovládání průmyslových zařízení prostřednictvím rozhraní REST API. OPC Twin, používá Azure IoT Edge a IoT Hub pro připojení cloudu a tovární sítě. OPC Twin umožňuje vývojářům IIoT soustředit se na vytváření aplikací IIoT bez obav o to, jak bezpečně přistupovat k místním počítačům.

## <a name="security"></a>Zabezpečení

OPC Vault je implementace OPC UA Global Discovery Server (GDS), která dokáže konfigurovat, registrovat a spravovat životní cyklus certifikátů pro Server OPC UA a klientské aplikace v cloudu. OPC Vault zjednodušuje implementaci a údržbu bezpečného asset connectivity v průmyslovém prostoru. Automatizací správy certifikátů osvobozuje OPC Vault tovární operátory od ručních a složitých procesů spojených s konektivitou a správou certifikátů.

## <a name="next-steps"></a>Další kroky

Teď, když jste měli úvod do průmyslového IoT a jeho součástí, zde je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Co je OPC Twin](overview-opc-twin.md)
