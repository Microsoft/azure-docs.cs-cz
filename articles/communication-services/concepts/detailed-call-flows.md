---
title: Topologie toků volání v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o topologiích toku volání v komunikačních službách Azure.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 526e3a1e4eeb6ef6a31a33498241d9a7443cca35
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490632"
---
# <a name="call-flow-topologies"></a>Topologie toků volání
Tento článek popisuje topologie toků volání komunikačních služeb Azure. Jedná se o skvělý článek, který vám umožní zkontrolovat, jestli jste zákazníkem, který integruje komunikační služby v rámci sítě, kterou spravujete. Úvod do toků volání komunikačních služeb najdete v [Koncepční dokumentaci k tokům volání](./call-flows.md).

## <a name="background"></a>Pozadí

### <a name="network-concepts"></a>Koncepty sítě

Než si prohlédnete topologie toků volání, definujeme některé výrazy, které jsou uvedené v celém dokumentu.

**Síť zákazníka** obsahuje všechny segmenty sítě, které spravujete. To může zahrnovat drátové a bezdrátové sítě v rámci kanceláře nebo mezi kancelářemi, datovými centry a poskytovateli internetových služeb.

Síť zákazníka obvykle obsahuje několik hraničních sítí s branami firewall nebo proxy servery, které vynutily zásady zabezpečení vaší organizace. Pro zajištění optimálního výkonu a kvality komunikačního řešení doporučujeme provést [komplexní hodnocení sítě](/microsoftteams/3-envision-evaluate-my-environment) .

**Síť komunikačních služeb** je segment sítě, který podporuje komunikační služby Azure. Tato síť je spravovaná Microsoftem a je distribuována po celém světě s okraji blízko k většině zákaznických sítí. Tato síť zodpovídá za přenosový přenos, zpracování médií pro volání skupin a další komponenty, které podporují multimediální komunikaci v reálném čase.

### <a name="types-of-traffic"></a>Typy přenosů

Komunikační služby jsou primárně postavené na dvou typech provozu: média a **signalizace** **v reálném** čase.

**Médium v reálném** čase je přenášeno pomocí protokolu RTP (Real-Time Transport Protocol). Tento protokol podporuje přenos dat zvuk, video a sdílení obrazovky. Tato data jsou citlivá na problémy latence sítě. I když je možné přenášet média v reálném čase pomocí protokolu TCP nebo HTTP, doporučujeme používat jako protokol transportní vrstvy protokol UDP k podpoře vysoce výkonného prostředí koncových uživatelů. Datová vytížení médií přenášená přes RTP jsou zabezpečená pomocí SRTP.

Uživatelé řešení komunikačních služeb se budou k vašim službám připojovat ze svých klientských zařízení. Komunikace mezi těmito zařízeními a vašimi servery se zpracovává pomocí **signalizace**. Například: volání iniciace a chat v reálném čase jsou podporovány signalizací mezi zařízeními a službou. Většina signálních přenosů využívá protokol HTTPS REST, i když v některých scénářích se dá protokol SIP použít jako protokol pro přenos signálů. I když tento typ provozu je méně citlivý na latenci, signalizaci s nízkou latencí, uživatelé vašeho řešení budou mít příjemný dojem koncového uživatele.

### <a name="interoperability-restrictions"></a>Omezení interoperability

Přetékání multimédií prostřednictvím komunikačních služeb se omezuje takto:

**Přenáší média třetích stran.** Vzájemná funkční spolupráce s předáváním médií třetích stran se nepodporuje. Pokud je některý z vašich koncových bodů médií komunikační služby, může vaše médium procházet jenom Microsoftem nativní přenos médií, včetně těch, které podporují Microsoft teams a Skype pro firmy.

Řadič hraničních relací třetí strany (mezipaměť softwarové sběrnice) na hranici s PSTN by měl ukončit datový proud RTP/RTCP zabezpečený pomocí SRTP a nepřenést ho do dalšího segmentu směrování. Pokud tok nanášíte do dalšího segmentu směrování, nemusí být srozumitelný.

**Proxy servery SIP třetích stran.** Dialogové okno signalizace komunikačních služeb, které se mezipaměť softwarové sběrnice a/nebo bránou jiného výrobce, může procházet Microsoft Native proxy SIP (stejně jako týmy). Vzájemná funkční spolupráce s proxy s protokolem SIP třetích stran není podporována.

**B2BUA třetí strany (nebo mezipaměť softwarové sběrnice).** Služba Communications flow (Media Flowing Services) do a ze sítě PSTN je ukončena mezipaměť softwarové sběrnice třetí strany. Interoperabilita s mezipaměť softwarové sběrnice třetí strany v rámci sítě komunikačních služeb (kde mezipaměť softwarové sběrnice třetí strany řeší dva koncové body služby Communications Services) není podporována.

### <a name="unsupported-technologies"></a>Nepodporované technologie

**Síť VPN.** Komunikační služby nepodporují přenos médií přes sítě VPN. Pokud vaši uživatelé používají klienty VPN, měl by klient rozdělit a směrovat mediální přenos přes připojení bez VPN, které je uvedené v části [Povolení médií v Lyncu pro obejít tunelu VPN.](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)

*Značte. I když nadpis indikuje Lync, vztahuje se na komunikační služby a týmy pro Azure.*

**Shapers paketů.** Výstřižky paketů, kontrola paketů nebo zařízení pro tvarování paketů nejsou podporovány a mohou výrazně snížit kvalitu.

### <a name="call-flow-principles"></a>Principy toku volání

Existují čtyři obecné principy, které využívají toky volání komunikačních služeb:

* **První účastník volání skupiny komunikačních služeb určuje oblast, ve které je volání hostováno**. Existují výjimky z tohoto pravidla v některých topologiích, které jsou popsány níže.
* **Koncový bod média, který se používá k podpoře volání komunikačních služeb, je vybraný na základě potřeb zpracování médií** a není ovlivněn počtem účastníků volání. Například volání Point-to-Point může použít koncový bod médií v cloudu ke zpracování médií pro přepis nebo záznam, zatímco volání dvou účastníků nesmí používat žádné koncové body médií. Volání skupin budou používat koncový bod médií pro kombinování a směrování. Tento koncový bod je vybrán v závislosti na oblasti, ve které je volání hostováno. Provoz multimédií odeslaný z klienta na koncový bod média se může směrovat přímo nebo může použít přenosový přenos v Azure, pokud to vyžadují omezení brány firewall sítě zákazníka.
* **Přenos médií pro volání peer-to-peer využívá nejvíce přímé trasy, která je k dispozici**. za předpokladu, že volání nepotřebuje koncový bod médií v cloudu. Upřednostňovaná trasa směřuje ke vzdálenému partnerovi (klient). Pokud není přímá trasa k dispozici, jeden nebo více přenosů přenosu bude přenášet provoz. Multimediální provoz by neměl mít příčný Server, který funguje jako shapers paketů, servery VPN nebo jiné funkce, které můžou zpozdit zpracování a snížit činnost koncového uživatele.
* **Signalizace přenosu vždy probíhá na jakémkoli serveru, který je nejblíže uživateli.**

Pokud se chcete dozvědět víc o podrobnostech o zvolené cestě k médiu, přečtěte si [Koncepční dokumentaci k tokům volání](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Toky volání v různých topologiích

### <a name="communication-services-internet"></a>Komunikační služby (Internet)

Tuto topologii používají zákazníci, kteří používají komunikační služby z cloudu bez jakéhokoli místního nasazení, jako je třeba rozhraní SIP. V této topologii přenos z provozu do a ze komunikačních služeb přes Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Topologie komunikačních služeb Azure.":::

*Obrázek 1 – topologie komunikačních služeb*

Směr šipek na výše uvedeném diagramu odráží směr zahájení komunikace, který má vliv na připojení v podnikových obvodech. V případě protokolu UDP pro médium může první paket zacházet v opačném směru. tyto pakety ale mohou být zablokovány, dokud nebudou přenášeny pakety v opačném směru.

Popisy toků:
* Flow 2 * – představuje tok iniciované uživatelem v síti zákazníka k internetu jako součást prostředí komunikačních služeb uživatele. Příklady těchto toků zahrnují přenos médií DNS a peer-to-peer.
* Flow 2 – představuje tok iniciované vzdáleným uživatelem mobilních komunikačních služeb s VPN k síti zákazníka.
* Flow 3 – představuje tok iniciované vzdáleným mobilním komunikačním službami ke koncovým bodům služby Communication Services.
* Flow 4 – představuje tok iniciující uživatelem v síti zákazníka pro komunikační služby.
* Flow 5 – představuje tok médií peer-to-peer mezi jedním uživatelem komunikačních služeb a jiným v rámci sítě zákazníka.
* Flow 6 – představuje tok médií peer-to-peer mezi vzdáleným uživatelem mobilních komunikačních služeb a jiným vzdáleným uživatelem Mobile Communication Services přes Internet.

### <a name="use-case-one-to-one"></a>Případ použití: 1:1

Volání 1:1 používají společný model, ve kterém volající získá sadu kandidátů, které se skládají z IP adres nebo portů, včetně místních, předávacích a reflexivní (veřejná IP adresa klienta, jak je vidět předáváním přenosu). Volající odesílá tyto kandidáty volané straně; Volaná strana také získá podobnou sadu kandidátů a pošle ji volajícímu. Zprávy kontroly připojení STUN slouží k tomu, aby bylo možné zjistit, které volající a které z nich jsou v cestě k médiím, a že je vybrána nejlepší pracovní cesta. Média (tj. pakety RTP/RTCP zabezpečené pomocí SRTP) se pak odesílají pomocí vybraného páru kandidátů. Přenosový přenos je nasazený jako součást komunikačních služeb Azure.

Pokud máte připojení k místní IP adrese/portu nebo reflexivní kandidáti, pak se pro médium vybere Přímá cesta mezi klienty (nebo použitím protokolu NAT). Pokud jsou klienti v síti zákazníka, měla by být vybrána Přímá cesta. To vyžaduje přímé připojení UDP v rámci sítě zákazníka. Pokud jsou klienti Nomadic v cloudových uživatelích, v závislosti na překladu adres (NAT) nebo bráně firewall můžou média používat přímé připojení.

Pokud je jeden klient interní v síti zákazníka a jeden z nich je externí (například uživatel s mobilním cloudem), je pravděpodobné, že přímé připojení mezi místními nebo reflexivnými kandidáty funguje. V tomto případě je možnost použít jedno z kandidátů přenosu přenosu od klienta (například interní klient získal kandidáta Relay z přenosového přenosu v Azure; externí klient musí být schopný odesílat pakety STUN/RTP/RTCP do přenosového přenosu). Další možností je, že interní klient odesílá do kandidáta Relay, kterou získal klient pro mobilní Cloud. I když se důrazně doporučuje připojení UDP pro médium, podporuje se protokol TCP.

**Kroky vysoké úrovně:**
1.  Služba Communications Services uživatele překládá název domény (DNS) adresy URL pomocí služby Flow 2.
2.  Uživatel A přiděluje port pro přenos médií v týmech přenosový přenos pomocí toku 4.
3.  Uživatel komunikačních služeb A pošle pozvání "Pozvánka" s kandidáty na ICE pomocí služby Flow 4 ke komunikačním službám.
4.  Komunikační služby upozorní uživatele B na používání služby Flow 4.
5.  Uživatel B přiděluje port pro přenos médií v týmech přenosový přenos pomocí toku 4.
6.  Uživatel B pošle odpověď s kandidáty na ICE pomocí toku 4, který se přesměruje zpět na uživatele A pomocí toku 4.
7.  Uživatel a a uživatel B vyvolá testy připojení ICE a je vybrána nejlepší dostupná cesta k médiu (viz diagramy níže pro různé případy použití).
8.  Oba uživatelé odesílají telemetrii do komunikačních služeb pomocí služby Flow 4.

### <a name="customer-network-intranet"></a>Síť zákazníka (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Přenosový tok v rámci sítě zákazníka.":::

*Obrázek 2 – v rámci zákaznické sítě*

V kroku 7 je vybraný Media Flow peer-to-peer 5.

Přenos tohoto média je obousměrný. Směr toku 5 znamená, že jedna strana iniciuje komunikaci z perspektivy připojení. V takovém případě nezáleží na tom, který směr se používá, protože oba koncové body jsou v rámci sítě zákazníka.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Síť zákazníka k externímu uživateli (média přenášená přenosovými týmy)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Jeden do jednoho toku volání prostřednictvím přenosu.":::

*Obrázek 3 – zákaznická síť pro externího uživatele (médium přenesené přes Azure Transport Relay)*

V kroku 7 jsou vybrány toky 4 (od sítě zákazníka až po komunikační služby) a flow 3 (od vzdáleného uživatele mobilních komunikačních služeb ke službě Azure Communication Services).

Tyto toky přenáší týmy přenosového přenosu v rámci Azure.

Přenos tohoto média je obousměrný. Směr označuje, která strana inicializuje komunikaci z perspektivy připojení. V takovém případě se tyto toky používají k signalizaci a médiím pomocí různých přenosových protokolů a adres.

### <a name="customer-network-to-external-user-direct-media"></a>Síť zákazníka k externímu uživateli (přímé médium)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Jeden k jednomu toku volání s externím uživatelem.":::

*Obrázek 4 – zákaznická síť pro externího uživatele (přímé médium)*

V kroku 7 je vybrána možnost Flow 2 (ze sítě zákazníka do partnerského uzlu klienta přes Internet).

Přímá média se vzdáleným mobilním uživatelem (bez přenosu prostřednictvím Azure) jsou volitelná. Jinými slovy, můžete zablokovat tuto cestu k vykonání cesty k médiu prostřednictvím přenosového přenosu v Azure.

Přenos tohoto média je obousměrný. Směr toku 2 ke vzdálenému mobilnímu uživateli indikuje, že jedna strana iniciuje komunikaci z perspektivy připojení.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>Uživatel VPN na interního uživatele (média přenášená přenosovými týmy)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Jeden k jednomu toku volání s uživatelem sítě VPN prostřednictvím přenosu.":::

*Obrázek 5 – uživatel sítě VPN pro interního uživatele (médium předané Azure Relay*

Signalizace mezi sítí VPN a zákaznickou sítí používá Flow 2 *. Signalizace mezi zákaznickou sítí a Azure používá službu Flow 4. Médium však síť VPN obchází a je směrováno pomocí toků 3 a 4 přes Azure Media Relay.

### <a name="vpn-user-to-internal-user-direct-media"></a>Uživatel VPN na interního uživatele (přímé médium)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Jeden k jednomu toku volání (interní uživatel) s VPN s přímým médiem":::

*Obrázek 6 – uživatel sítě VPN pro interního uživatele (přímé médium)*

Signalizace mezi sítí VPN a zákaznickou sítí používá Flow 2. Signalizace mezi zákaznickou sítí a Azure používá Flow 4. Médium ale tuto síť VPN obchází a je směrované pomocí služby Flow 2 ze sítě zákazníka na Internet.

Přenos tohoto média je obousměrný. Směr toku 2 ke vzdálenému mobilnímu uživateli indikuje, že jedna strana iniciuje komunikaci z perspektivy připojení.

### <a name="vpn-user-to-external-user-direct-media"></a>Uživatel VPN na externího uživatele (přímé médium)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Jeden do jednoho toku volání (externí uživatel) s VPN s přímým médiem":::

*Obrázek 7 – uživatel VPN s externím uživatelem (přímé médium)*

Signalizace mezi uživatelem sítě VPN a zákaznickou sítí používá Flow 2 * a flow 4 do Azure. Médium ale obchází VPN a je směrované pomocí toku 6.

Přenos tohoto média je obousměrný. Směr toku 6 ke vzdálenému mobilnímu uživateli indikuje, že jedna strana iniciuje komunikaci z perspektivy připojení.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Případ použití: klient komunikačních služeb do veřejné telefonní sítě prostřednictvím služby Communications

Komunikační služby umožňují umístit a přijímat volání z veřejné komutované telefonní sítě (PSTN). Pokud je kabel veřejné telefonní sítě připojený pomocí telefonních čísel poskytovaných komunikačními službami, pro tento případ použití nejsou k dispozici žádné speciální požadavky na připojení. Pokud chcete k komunikačním službám Azure připojit svůj vlastní místní firemní telefon, můžete použít rozhraní SIP (dostupné v CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Jedno k jednomu volání s účastníkem veřejné sítě":::

*Obrázek 8 – uživatel komunikačních služeb do veřejné telefonní sítě prostřednictvím služby Azure šacht*

V takovém případě signalizace a média ze sítě zákazníka do Azure využívají tok 4.

### <a name="use-case-communication-services-group-calls"></a>Případ použití: volání skupin komunikačních služeb

Služba VBSS (audio/video/obrazovka sdílení obrazovky) je součástí komunikačních služeb Azure. Má veřejnou IP adresu, kterou musí být dosažitelná ze sítě zákazníka a musí být dosažitelná z cloudového klienta Nomadic. Každý klient/koncový bod musí být schopný se připojit ke službě.

Interní klienti získají kandidáty na místní, reflexivní a předávání stejným způsobem, jaký je popsaný pro volání 1:1. Klienti odešlou tyto kandidáty na službu v pozvánce. Služba nepoužívá předávání, protože má veřejně dosažitelnou IP adresu, takže odpoví na svůj místní kandidát na IP adresu. Klient a služba budou kontrolovat připojení stejným způsobem, jaký je popsaný pro volání 1:1.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="Volání skupiny OACS":::

*Obrázek 9 – volání skupin komunikačních služeb*

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s voláním](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Následující dokumenty můžou být zajímavé:

- Další informace o [typech volání](../concepts/voice-video-calling/about-call-types.md)
- Informace o [architektuře klient-server](./client-and-server-architecture.md)