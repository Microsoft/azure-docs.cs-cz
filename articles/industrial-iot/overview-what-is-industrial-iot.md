---
title: Přehled služby Azure průmyslový IoT
description: Tento článek poskytuje přehled o průmyslových IoT. Vysvětluje připojení a součásti zabezpečení dílenského řízení v IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813267"
---
# <a name="what-is-industrial-iot-iiot"></a>Co je průmyslový IoT (IIoT)?

![Průmyslový IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure průmyslový Internet věcí (IIoT) je sada modulů a služeb Azure, které integrují výkon cloudu do podlahové a výrobní dílny. Díky standardním otevřeným rozhraním, jako je [Open Platform Unified Communications (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/), vám Azure IIoT umožní integrovat data z prostředků a senzorů – včetně těch, které už jsou v produkčním prostředí Azure spuštěné, do cloudu Azure. Pokud máte data v cloudu, můžete je používat rychleji a pružně jako zpětná vazba při vývoji transformativní obchodních a průmyslových procesů.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Zjišťování, registrace a Správa průmyslových prostředků pomocí Azure

Azure průmyslový IoT umožňuje operátorům rostlin zjistit servery s povoleným OPC UA v síti továrny a zaregistrovat je v Azure IoT Hub. Provozní pracovníci se můžou přihlásit k odběru a reagovat na události v továrně z libovolného místa na světě, získávat výstrahy a poplachy a reagovat na ně v reálném čase.

IIoT poskytuje sadu mikroslužeb, které implementují funkce OPC UA. Rozhraní REST API mikroslužeb zrcadlí na straně okraj služby OPC UA. Jsou zabezpečené pomocí ověřování OAUTH a autorizaci, kterou zálohuje Azure Active Directory (AAD). To umožňuje vašim cloudovým aplikacím procházet adresní prostory serveru nebo proměnné pro čtení a zápis a spouštět metody pomocí protokolu HTTPS a jednoduchých datových částí JSON OPC UA. Hraniční služby se implementují jako Azure IoT Edge moduly a běží místně. Cloudové mikroslužby jsou implementovány jako mikroslužby ASP.NET s rozhraním REST a běží na spravovaných službách Azure Kubernetes nebo samostatně na Azure App Service. Pro hraniční i cloudové služby poskytuje IIoT předem připravené kontejnery Docker v Microsoft Container Registry (MCR), který tento krok pro zákazníka odebere. Hraniční a cloudové služby se vzájemně využívají a musí se používat společně. IIoT také nabízí snadno použitelné skripty nasazení, které umožňují nasadit celou platformu jediným příkazem.

Kromě toho je možné používat rozhraní REST API spolu s libovolným programovacím jazykem prostřednictvím zveřejněné specifikace Open API (Swagger). To znamená, že při integraci OPC UA do řešení pro správu cloudu si můžou vývojáři zdarma vybrat technologii, která odpovídá jejich dovedností, zájmům a možnostem architektury. Například plný zásobník pro vývojáře webu, který vyvíjí aplikaci pro alarm a řídicí panel události, může napsat logiku pro reakci na události v JavaScriptu nebo TypeScript bez rozchodu do sady OPC UA SDK, C, C++, Java nebo C#.

## <a name="manage-certificates-and-trust-groups"></a>Správa certifikátů a skupin důvěryhodnosti

Azure průmyslový IoT spravuje certifikáty aplikací OPC UA a seznam důvěryhodných strojových strojů a řídicích systémů, aby bylo zajištěno zabezpečení komunikace mezi klientem OPC UA a serverem. Omezuje, který klient může komunikovat s tímto serverem. Úložiště privátních klíčů a podepisování certifikátů je zajištěno Azure Key Vault, které podporuje hardwarové zabezpečení (HSM).

## <a name="industrial-iot-components"></a>Komponenty pro průmyslový IoT

Řešení Azure IIoT jsou založená na konkrétních součástech. Mezi ně patří následující:

- **Aspoň jeden IoT Hub Azure.**
- **IoT Edge zařízení.**
- **Moduly průmyslových Edge.**

### <a name="iot-hub"></a>IoT Hub
[IoT Hub Azure] ( https://azure.microsoft.com/services/iot-hub/ slouží jako centrální Centrum zpráv pro zabezpečenou obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Je to otevřená a flexibilní cloudová platforma jako služba (PaaS), která podporuje open source sady SDK a několik protokolů. 

Shromažďování průmyslových a podnikových dat do IoT Hub umožňuje bezpečně ukládat data, provádět analýzy podnikání a efektivity a generovat z nich sestavy. Pro konsolidovaná data můžete také použít Microsoft Azure služby a nástroje, jako je například [Power BI](https://powerbi.microsoft.com).

### <a name="iot-edge-devices"></a>IoT Edge zařízení
[Hraniční služby](https://azure.microsoft.com/services/iot-edge/) se implementují jako Azure IoT Edge moduly a běží místně. Cloudové mikroslužby jsou implementovány jako mikroslužby ASP.NET s rozhraním REST a běží na spravovaných službách Azure Kubernetes nebo samostatně na Azure App Service. U hraničních i cloudových služeb jsme na Microsoft Container Registry (MCR) zavedli předem připravené kontejnery Docker a odebrali jsme tento krok pro zákazníka. Hraniční a cloudové služby se vzájemně využívají a musí se používat společně. K dispozici jsme také snadno použitelné skripty nasazení, které umožňují nasadit celou platformu jediným příkazem.

Zařízení IoT Edge se skládá z modulů runtime a Edge.
- **Hraniční moduly** jsou kontejnery Docker, které jsou nejmenší jednotkou výpočtu, jako je OPC Publisher a OPC dvojitá. 
- **Hraniční zařízení** slouží k nasazení takových modulů, které fungují jako prostředníci mezi serverem OPC UA a IoT Hub v cloudu.

### <a name="industrial-edge-modules"></a>Moduly průmyslových okrajů
- **Vydavatel OPC**: Vydavatel OPC se spouští v rámci IoT Edge. Připojuje se k serverům OPC UA a publikuje data telemetrie kódovaná pomocí JSON z těchto serverů ve formátu OPC UA "pub/sub" do Azure IoT Hub. Můžete použít všechny transportní protokoly podporované klientskou sadou SDK pro Azure IoT Hub, například HTTPS, AMQP a MQTT.
- **OPC s dvojitou** délkou: OPC se skládá z mikroslužeb, které používají Azure IoT Edge a IoT Hub k propojení cloudu a sítě továrny. OPC vlákna poskytuje zjišťování, registraci a vzdálené řízení průmyslových zařízení prostřednictvím rozhraní REST API. NeOPCá vlákna nevyžaduje sadu SDK OPC UA (OPC Unified Architecture). Programovací jazyk je nezávislá a může být součástí pracovního postupu bez serveru.
- **Zjišťování**: modul zjišťování reprezentovaný zjistitelnou identitou poskytuje služby zjišťování na hranici, která zahrnuje zjišťování serveru OPC UA. Pokud je zjišťování nakonfigurované a povolené, modul odešle výsledky testu kontroly prostřednictvím IoT Edge a IoT Hub cestu telemetrie do služby připojování. Služba zpracovává výsledky a aktualizuje všechny související identity v registru.

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, co je průmyslový IoT, si můžete přečíst o platformě pro průmyslové IoT a vydavateli OPC:

> [!div class="nextstepaction"]
> [Co je Vydavatel OPC?](overview-what-is-opc-publisher.md)