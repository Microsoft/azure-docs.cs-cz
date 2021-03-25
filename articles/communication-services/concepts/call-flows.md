---
title: Toky volání v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o tocích volání v komunikačních službách Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8f7bfd63d858fb409286268c318c9f66474e3d53
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110912"
---
# <a name="call-flow-basics"></a>Základy toků volání

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Následující část obsahuje přehled toků volání v komunikačních službách Azure. Signalizace a toky médií závisí na typech volání, která vaši uživatelé provádějí. Mezi příklady typů volání patří VoIP, One-to-One pro veřejné telefonní sítě a volání skupin obsahující kombinaci účastníků připojení VoIP a PSTN. Zkontrolujte [typy volání](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>O signalizacích a mediálních protokolech

Když vytvoříte volání skupiny peer-to-peer nebo Group, použijí se za vás dva protokoly na pozadí – HTTP (REST) pro signalizaci a SRTP pro média.

Signalizace mezi sadami SDK nebo mezi sadami SDK a řadiči komunikačních služeb se zpracovává pomocí protokolu HTTP REST (TLS). Pro Real-Time přenosů médií (RTP) je upřednostňovaným protokolem UDP (User Datagram Protocol). Pokud brána firewall zabrání použití protokolu UDP, SDK použije pro médium protokol TCP (Transmission Control Protocol).

Pojďme se podívat na signály a protokoly médií v různých scénářích.

## <a name="call-flow-cases"></a>Případy toku volání

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Případ 1: VoIP, kde je možné přímé připojení mezi dvěma zařízeními

V případě volání VoIP nebo videa s jedním z nich upřednostňuje přenos nejvíce přímé cesty. "Přímá cesta" znamená, že pokud se k sobě přímo můžou připojit dvě sady SDK, naváže se přímé připojení. To je obvykle možné v případě, že dvě sady SDK jsou ve stejné podsíti (například v podsíti 192.168.1.0/24) nebo dvě v případě, že se zařízení každý provoz nacházejí v podsítích, které vidí navzájem (sady SDK v podsíti 10.10.0.0/16 a 192.168.1.0/24, může vzájemně kontaktovat).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagram znázorňující přímé volání VOIP mezi uživateli a komunikačními službami.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Případ 2: VoIP, kde přímé připojení mezi zařízeními není možné, ale v případě možného připojení mezi zařízeními NAT

Pokud se v podsítích nacházejí dvě zařízení, která se nemůžou vzájemně navázat (například Alice funguje z kavárny a Bob funguje z domova), ale připojení mezi zařízeními NAT navede, sady SDK na straně klienta naváže připojení prostřednictvím zařízení NAT.

V případě Alice se jedná o překlad adres (NAT) pro kavárnu a pro Bob bude to překlad adres (NAT) doma. Zařízení Alice pošle externí adresu svého překladu adres (NAT) a Bob se bude shodovat. Sady SDK se učí externí adresy ze služby STUN (Nástroj pro procházení relace pro NAT), které poskytují bezplatné služby Azure Communications. Logika, která zpracovává signalizaci mezi Alicí a Bobem, je vložená do sad SDK služby Azure Communication Services. (Nepotřebujete žádnou další konfiguraci.)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagram znázorňující volání VOIP, které využívá připojení STUN":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Případ 3: VoIP, když není možné přímé připojení k překladu adres ani NAT

Pokud jsou jedno nebo obě klientské zařízení za symetrickým překladem adres (NAT), vyžaduje se samostatná cloudová služba, která bude přenášet médium mezi těmito sadami SDK. Tato služba se nazývá zapínání (přecházení přes překlad adres (NAT)) a poskytuje také komunikační služby. Komunikační služby, které volá sadu SDK, automaticky využívají funkci zapnout služby na základě zjištěných síťových podmínek. Používání služby společnosti Microsoft pro zapínání se účtuje samostatně.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagram znázorňující volání VOIP, které využívá připojení k zapnutí":::

### <a name="case-4-group-calls-with-pstn"></a>Případ 4: volání skupin pomocí veřejné telefonní sítě

Jak signalizace, tak volání na veřejné telefonní subsystém využívají prostředek služby Azure Communication Services. Tento prostředek je propojen s dalšími provozovateli.

Provoz multimédií v PSTN prostřednictvím komponenty s názvem Media Processor.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagram znázorňující volání skupiny veřejné telefonní služby se službami Communications.":::

> [!NOTE]
> Pro uživatele, kteří se seznámili s zpracováním médií, je náš multimediální procesor také zpětným uživatelským agentem, jak je definováno v [dokumentu RFC 3261 SIP: protokol inicializace relace](https://tools.ietf.org/html/rfc3261), což znamená, že dokáže překládat kodeky při zpracování volání mezi sítěmi Microsoftu a dopravce. Kontroler signalizace v rámci služby Azure Communications Services je implementace proxy serveru SIP od Microsoftu na stejný RFC.

Pro volání skupin se přes back-end služby Azure Communication Services vždy přesměruje média a signalizace. Zvuk a video ze všech účastníků se míchají do komponenty procesoru multimédií. Všichni členové volání skupiny odesílají své zvukové a video streamy do procesoru médií, který vrací smíšené mediální datové proudy.

Výchozím protokolem RTP (Real-Time Protocol) pro volání skupin je protokol UDP (User Datagram Protocol).

> [!NOTE]
> Procesor médií může fungovat jako jednotka řízení systému MultiPoint (MCU) nebo jednotka pro selektivní předávání (SFU).

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagram znázorňující tok procesu multimédií UDP v rámci komunikačních služeb":::

Pokud sada SDK nedokáže pro média z důvodu omezení brány firewall použít UDP, bude proveden pokus o použití protokolu TCP (Transmission Control Protocol). Všimněte si, že komponenta procesoru médií vyžaduje protokol UDP, takže pokud k tomu dojde, služba Communications zapnete službu a přidá se k volání skupiny, aby se přeložilo TCP na UDP. V takovém případě budou v takovém případě zaúčtovány poplatky, pokud nejsou možnosti vypnutí ručně zakázány.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagram znázorňující tok procesu multimédií TCP v rámci komunikačních služeb.":::

### <a name="case-5-communication-services-sdk-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>Případ 5: komunikační služby SDK a Microsoft Teams v rámci schůze plánovaných týmů

Signalizace toků prostřednictvím kontroleru signalizace. Multimediální toky přes procesor médií. Řadič signalizace a procesor médií se sdílí mezi komunikačními službami a Microsoft Teams.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="Diagram znázorňující, že klient služby Communications Services SDK a týmy v rámci schůzky naplánovaných týmů":::



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s voláním](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Následující dokumenty můžou být zajímavé:

- Další informace o [typech volání](../concepts/voice-video-calling/about-call-types.md)
- Informace o [architektuře klient-server](./client-and-server-architecture.md)
- Další informace o [topologiích toku volání](./detailed-call-flows.md)
