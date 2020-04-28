---
title: Přehled služby Azure průmyslový IoT | Microsoft Docs
description: Tento článek poskytuje přehled o průmyslových IoT. Vysvětluje, jak propojená továrna, připojení k továrnímu uspořádání a součásti zabezpečení v IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3c474b57533cb6165ce7fe77defccd4cb7042b06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189918"
---
# <a name="what-is-industrial-iot-iiot"></a>Co je průmyslový IoT (IIoT)

IIoT je průmyslový Internet věcí. IIoT vylepšuje průmyslové efektivity prostřednictvím aplikace IoT v výrobním odvětví. 

## <a name="improve-industrial-efficiencies"></a>Zvýšení efektivity průmyslu

Zvyšte svou provozní produktivitu a ziskovost pomocí akcelerátoru řešení propojené továrny. Připojte a monitorujte svoje průmyslová zařízení a zařízení v cloudu – včetně vašich počítačů, které už v produkčním prostředí fungují. Analyzujte data IoT pro přehledy, které vám pomůžou zvýšit výkon celé továrního podlaží.

Omezte časově náročný proces přístupu k výrobním počítačům v továrně s OPC vláknem a Zaměřte se na čas při sestavování řešení IIoT. Zjednodušte správu certifikátů a průmyslovou integraci s OPC trezorem a máte jistotu, že je připojení assetů zabezpečené. Tyto mikroslužby poskytují rozhraní API podobné službě [Azure pro průmyslové komponenty IoT](https://github.com/Azure/Industrial-IoT). Rozhraní API služby poskytuje kontrolu nad funkcemi modulu Edge. 

![Přehled průmyslového IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Další informace o službách Azure pro průmyslové služby IoT najdete v [úložišti](https://github.com/Azure/Industrial-IoT) GitHub a v [dokumentaci](https://azure.github.io/Industrial-IoT/).
Pokud nejste obeznámeni s tím, jak Azure IoT Edge moduly fungují, začněte s následujícími články:
- [Informace o Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Moduly Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Propojená továrna

[Propojená továrna](../iot-accelerators/iot-accelerators-connected-factory-features.md) je implementace referenční architektury Azure v průmyslu Azure, kterou je možné přizpůsobit tak, aby splňovala konkrétní obchodní požadavky. Úplný kód řešení je open source a dostupný v úložišti GitHub akcelerátoru řešení propojené továrny. Můžete ji použít jako výchozí bod pro komerční produkt a nasadit předem připravené řešení do předplatného Azure během několika minut. 

## <a name="factory-floor-connectivity"></a>Připojení k továrnímu uspořádání

OPC je IIoT komponenta, která automatizuje zjišťování a registraci zařízení a nabízí vzdálené řízení průmyslových zařízení přes rozhraní REST API. OPC s dvojitou Vlákenou používá Azure IoT Edge a IoT Hub k propojení cloudu a sítě továrny. OPC se může vývojářům IIoT soustředit na vytváření aplikací IIoT, aniž by se museli starat o zabezpečený přístup k místním počítačům.

## <a name="security"></a>Zabezpečení

OPC trezor je implementací serveru OPC UA Global Discovery Server (GDS), který umožňuje konfigurovat, registrovat a spravovat životní cyklus certifikátů pro server OPC a klientské aplikace v cloudu. OPC trezor zjednodušuje implementaci a údržbu připojení k zabezpečenému majetku v oboru průmyslu. Díky automatizaci správy certifikátů OPC trezor uvolňuje výrobní operátory z ručních a složitých procesů přidružených ke správě připojení a certifikátů.

## <a name="next-steps"></a>Další kroky

Teď, když máte Úvod do průmyslového IoT a jeho součástí, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Co je OPC Twin](overview-opc-twin.md)
