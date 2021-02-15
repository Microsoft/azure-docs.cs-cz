---
title: Koncepce integrace telefonního subsystému PSTN pro komunikační služby Azure
description: Naučte se integrovat možnosti volání do veřejné sítě v aplikaci Azure Communication Services.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7c2b0168c914c836aca7b551a9c22d4718c7b6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100422445"
---
# <a name="telephony-concepts"></a>Koncepce telefonního subsystému

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Komunikační služby Azure, které volají klientské knihovny, se dají použít k přidání telefonního subsystému a sítě PSTN do vašich aplikací. Tato stránka shrnuje klíčové koncepty a možnosti telefonního subsystému. Další informace o konkrétních jazycích a funkcích klientské knihovny najdete v [knihovně volání knihovny](../../quickstarts/voice-video-calling/calling-client-samples.md) .

## <a name="overview-of-telephony"></a>Přehled telefonního subsystému
Vždycky, když vaši uživatelé komunikují s tradičním telefonním číslem, se volání usnadňují hlasovým voláním sítě PSTN (s použitím veřejné komutované telefonní sítě). Pokud chcete uskutečnit a přijímat volání do veřejné telefonní sítě, musíte do svého prostředku Azure Communications Services přidat možnosti telefonního subsystému. V takovém případě signalizace a média používají pro připojení uživatelů kombinaci technologií založených na protokolu IP a PSTN. Komunikační služby poskytují dva diskrétní způsoby, jak kontaktovat síť PSTN: volání do cloudu Azure a rozhraní SIP.

### <a name="azure-cloud-calling"></a>Volání cloudu Azure

Snadný způsob, jak přidat připojení do veřejné telefonní sítě do vaší aplikace nebo služby, v tomto případě je vaším poskytovatelem výpovědi. Čísla můžete koupit přímo od Microsoftu. Volání cloudu Azure je vše v cloudu řešení telefonního subsystému pro komunikační služby. Toto je nejjednodušší možnost, která připojuje službu ACS k veřejné komutované telefonní síti (PSTN), aby umožňovala volání landlines a mobilních telefonů po celém světě. S touto možností se Microsoft chová jako váš dopravce PSTN, jak je znázorněno v následujícím diagramu:

![Diagram volání cloudu Azure](../media/telephony-concept/azure-calling-diagram.png)

Pokud odpovíte Ano na následující, bude volání cloudu Azure pro vás nejvhodnějším řešením:
- Volání cloudu Azure je k dispozici ve vaší oblasti.
- Nemusíte zachovat svůj stávající dopravce PSTN.
- Chcete použít přístup spravovaný přes Microsoft k veřejné síti.

S touto možností:
- Získáte čísla přímo od Microsoftu a můžete volat telefony po celém světě.
- Nepotřebujete nasazení nebo údržbu místního nasazení – protože cloudové volání Azure funguje mimo komunikační služby Azure.
- Poznámka: v případě potřeby se můžete připojit k podporovanému kontroleru hraničního připojení (mezipaměť softwarové sběrnice) prostřednictvím rozhraní SIP pro interoperabilitu s PBXs, analogová zařízení třetích stran a dalšími telefonními zařízeními třetích stran, které podporuje mezipaměť softwarové sběrnice.

Tato možnost vyžaduje nepřerušované připojení ke komunikačním službám Azure.

### <a name="sip-interface"></a>Rozhraní SIP

Pomocí této možnosti můžete připojit starší verze místního telefonního subsystému a poskytovatele dle vašeho výběru ke komunikačním službám Azure. Poskytuje možnosti volání do veřejné sítě pro vaše aplikace ACS, i když není ve vaší zemi nebo oblasti dostupné cloudové volání Azure. 

![Diagram rozhraní SIP](../media/telephony-concept/sip-interface-diagram.png)

Pokud odpovíte Ano na kteroukoli z následujících otázek, pak je pro vás toto správné řešení rozhraní SIP:

- Chcete používat službu ACS s funkcemi volání po veřejné síti.
- Musíte zachovat svůj stávající dopravce PSTN.
- Chcete kombinovat směrování s některými voláními, která procházejí prostřednictvím cloudového volání Azure, od vašeho operátora.
- Musíte spolupracovat s PBXs a/nebo zařízeními třetích stran, jako jsou třeba režijní operátory, analogová zařízení atd.

S touto možností:

- K komunikačním službám Azure připojíte vlastní podporované mezipaměť softwarové sběrnice bez nutnosti dalšího místního softwaru.
- Službu ACS můžete použít pro doslova libovolného poskytovatele telefonního subsystému.
- Můžete zvolit konfiguraci a správu této možnosti, nebo ji můžete nakonfigurovat a spravovat pomocí svého operátora nebo partnera (zeptejte se, jestli váš operátor nebo partner tuto možnost poskytuje).
- Můžete nakonfigurovat interoperabilitu mezi vaším telefonním zařízením, jako jsou PBX a analogová zařízení třetí strany a služba ACS.

Tato možnost vyžaduje následující:

- Nepřerušované připojení k Azure.
- Nasazení a údržba podporovaných mezipaměť softwarové sběrnice
- Kontrakt s dopravcem třetí strany. (Pokud není nasazen jako možnost pro poskytování připojení k pobočkám PBX třetí strany, analogovému zařízení nebo jinému telefonnímu vybavení pro uživatele, kteří jsou v komunikačních službách.)

## <a name="next-steps"></a>Další kroky

### <a name="conceptual-documentation"></a>Koncepční dokumentace

- [Typy telefonních čísel v komunikačních službách Azure](./plan-solution.md)
- [Plánování rozhraní SIP](./sip-interface-infrastructure.md)
- [Ceny](../pricing.md)

### <a name="quickstarts"></a>Rychlé starty

- [Získat telefonní číslo](../../quickstarts/telephony-sms/get-phone-number.md)
- [Zavolat na telefon](../../quickstarts/voice-video-calling/pstn-call.md)
