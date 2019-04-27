---
title: Přehled Azure průmyslového IoT | Dokumentace Microsoftu
description: Přehled průmyslového IoT
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bda40470e3ccf3a5d7b23dca38b21090e864b16a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888471"
---
# <a name="what-is-industrial-iot-iiot"></a>Co je průmyslového IoT (IIoT)

IIoT je průmyslového Internetu věcí. IIoT zvyšuje efektivitu průmyslové prostřednictvím aplikace IoT ve výrobním průmyslu. 

## <a name="improve-industrial-efficiencies"></a>Zlepšení efektivity průmyslu

Vylepšete svoji provozní produktivitu a zisk s akcelerátoru řešení propojené továrny. Propojte a monitorujte své průmyslová vybavení a zařízení v cloudu, včetně strojů, které už v továrně pracují. Analyzujte data z IoT a získávejte z nich informace, které vám pomůžou zvýšit výkon celého závodu.

Omezit časově náročný proces přístup k factory floor počítačů pomocí Dvojčete OPC a váš čas soustředit na vytváření IIoT řešení. Zjednodušení správy certifikátů a integrace průmyslové asset s trezorem OPC a si jisti, že je zabezpečené připojení k prostředku. Tyto mikroslužby poskytují podobné REST API nad [průmyslového IoT Azure komponenty](https://github.com/Azure/azure-iiot-opc-ua). Rozhraní API služby vám dává kontrolu nad funkce modulu edge. 

![Přehled průmyslového IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Další informace o službách Azure průmyslového IoT, najdete v článku Githubu [úložiště](https://github.com/Azure/azure-iiot-services).
Pokud nejste obeznámeni s fungování modulů Azure IoT Edge, začněte najdete v následujících článcích:
- [Informace o Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Moduly Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Propojená továrna

[Pro propojenou továrnu](../iot-accelerators/iot-accelerators-connected-factory-features.md) je implementace společnosti Microsoft Azure průmyslového IoT referenční architektura, kterou lze přizpůsobit podle konkrétních podnikových požadavků. V úložišti GitHub akcelerátor řešení připojená továrna je úplné řešení kód open-source a k dispozici. Můžete ho použít jako výchozí bod pro komerční produkt a během několika minut nasadit do svého předplatného Azure předem připravené řešení. 

## <a name="factory-floor-connectivity"></a>Objekt pro vytváření floor připojení

OPC dvojče je součást IIoT, který automatizuje registraci a zjišťování zařízení a nabízí vzdáleného řízení průmyslová zařízení prostřednictvím rozhraní REST API. Dvojče OPC používá pro připojení v cloudu a objekt pro vytváření sítí Azure IoT Edge a IoT Hub. Dvojče OPC umožňuje vývojářům IIoT soustředit na vytváření IIoT aplikací bez starostí o tom, jak bezpečně přistupovat k na místních počítačích.

## <a name="security"></a>Zabezpečení

Implementace sady OPC UA globální zjišťování serveru (GDS), která můžete konfigurovat, registrace a správa životního cyklu certifikátu pro server OPC UA a klientských aplikací v cloudu je trezor OPC. Trezor OPC usnadňuje implementaci a údržbu připojení k zabezpečeným prostředkem v průmyslových místa. Automatizací správy certifikátů trezor OPC uvolní operátory objekt pro vytváření z ruční a komplexní procesy, které jsou přidružené k připojení a certifikát správy.

## <a name="next-steps"></a>Další postup

Teď, když jsme měli Úvod do průmyslového IoT a jeho komponenty, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Co je OPC Dvojčat](overview-opc-twin.md)
