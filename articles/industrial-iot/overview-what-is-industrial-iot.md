---
title: Přehled služby Azure průmyslový IoT
description: Tento článek poskytuje přehled o průmyslových IoT. Vysvětluje připojení a součásti zabezpečení dílenského řízení v IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787912"
---
# <a name="what-is-industrial-iot-iiot"></a>Co je průmyslový IoT (IIoT)?

IIoT (průmyslový Internet věcí) vylepšuje průmyslové efektivity prostřednictvím aplikace IoT v výrobním odvětví.

![Průmyslový IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Zvýšení efektivity průmyslu
Dolepšete svou provozní produktivitu a ziskovost pomocí Azure průmyslových IoT. Omezte časově náročný proces přístupu k prostředkům na pracovišti. Připojte a monitorujte svoje průmyslová zařízení a zařízení v cloudu – včetně počítačů, které už v produkčním prostředí fungují. Analyzujte data IoT pro přehledy, které vám pomůžou zvýšit výkon celé lokality.

## <a name="industrial-iot-components"></a>Komponenty pro průmyslový IoT

**IoT Edge zařízení** Zařízení IoT Edge se skládá z modulů runtime a Edge. 
- *Hraniční moduly* jsou kontejnery Docker, které jsou nejmenší jednotkou výpočtu, jako je OPC Publisher a OPC dvojitá. 
- *Hraniční zařízení* slouží k nasazení takových modulů, které fungují jako prostředníci mezi serverem OPC UA a IoT Hub v cloudu. Další informace o IoT Edge [najdete tady](https://azure.microsoft.com/services/iot-edge/).

**IoT Hub** IoT Hub slouží jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Je to otevřená a flexibilní cloudová platforma jako služba, která podporuje open source sady SDK a několik protokolů. Přečtěte [si další informace o IoT Hub.](https://azure.microsoft.com/services/iot-hub/)

**Moduly průmyslových okrajů**
- *Vydavatel OPC*: Vydavatel OPC se spouští v rámci IoT Edge. Připojuje se k serverům OPC UA a publikuje data telemetrie kódovaná pomocí JSON z těchto serverů ve formátu OPC UA "pub/sub" do Azure IoT Hub. Můžete použít všechny transportní protokoly podporované klientskou sadou SDK pro Azure IoT Hub, například HTTPS, AMQP a MQTT.
- *OPC s dvojitou* délkou: OPC se skládá z mikroslužeb, které používají Azure IoT Edge a IoT Hub k propojení cloudu a sítě továrny. OPC vlákna poskytuje zjišťování, registraci a vzdálené řízení průmyslových zařízení prostřednictvím rozhraní REST API. NeOPCá vlákna nevyžaduje sadu SDK OPC UA (OPC Unified Architecture). Programovací jazyk je nezávislá a může být součástí pracovního postupu bez serveru.
- *Zjišťování*: modul zjišťování reprezentovaný zjistitelnou identitou poskytuje služby zjišťování na hranici, která zahrnuje zjišťování serveru OPC UA. Pokud je zjišťování nakonfigurované a povolené, modul odešle výsledky testu kontroly prostřednictvím IoT Edge a IoT Hub cestu telemetrie do služby připojování. Služba zpracovává výsledky a aktualizuje všechny související identity v registru.


**Zjišťování, registrace a Správa průmyslových prostředků pomocí Azure** Azure průmyslový IoT umožňuje operátorům rostlin zjistit servery s povoleným OPC UA v síti továrny a zaregistrovat je v Azure IoT Hub. Provozní pracovníci se můžou přihlásit k odběru a reagovat na události v produkčním prostředí z libovolného místa na světě. Rozhraní REST API mikroslužeb zrcadlí na straně okrajů služby OPC UA. Jsou zabezpečené pomocí ověřování OAUTH a autorizaci, kterou zálohuje Azure Active Directory (AAD). To umožňuje vašim cloudovým aplikacím procházet adresní prostory serveru nebo proměnné pro čtení a zápis a spouštět metody pomocí protokolu HTTPS a jednoduchých datových částí JSON OPC UA.

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, co je průmyslový IoT, si můžete přečíst o platformě pro průmyslové IoT a vydavateli OPC:

> [!div class="nextstepaction"]
> [Co je průmyslová platforma IoT?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [Co je Vydavatel OPC?](overview-what-is-opc-publisher.md)