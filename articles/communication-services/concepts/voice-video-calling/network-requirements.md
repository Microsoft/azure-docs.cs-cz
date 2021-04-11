---
title: Příprava sítě vaší organizace na komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s požadavky na síť pro volání hlasu a videa služby Azure Communication Services
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22bf8af89771e57621a0d8851ca31bb750b7cda2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108345"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Zajištění vysoce kvalitních médií v komunikačních službách Azure

Tento dokument poskytuje přehled o faktorech a osvědčených postupech, které byste měli zvážit při vytváření vysoce kvalitních multimediálních komunikačních prostředí s využitím komunikačních služeb Azure.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Faktory ovlivňující kvalitu a spolehlivost multimédií

K dispozici je celá řada různých faktorů, které přispívají ke kvalitě médií v reálném čase (zvuk, video a sdílení aplikací) ve službě Azure Communications. Mezi ně patří kvalita sítě, Šířka pásma, brána firewall, konfigurace hostitele a zařízení.


### <a name="network-quality"></a>Kvalita sítě

Kvalita média v reálném čase prostřednictvím protokolu IP je významně ovlivněna kvalitou základního připojení k síti, ale zejména podle množství:
* **Latence**. Toto je čas potřebný k získání paketu IP od bodu A až do bodu B v síti. Tato prodleva šíření sítě se určuje na základě fyzické vzdálenosti mezi dvěma body a dalšími nároky, které vznikají na zařízeních, do kterých tok dat pracuje. Latence se měří jako doba odezvy (jednosměrná nebo cyklická doba odezvy).
* **Ztráta paketů**. Procento paketů, které jsou v daném časovém intervalu ztraceny. Ztráta paketů má přímý vliv na kvalitu zvuku – od malých po ztracených paketů, které mají skoro žádný dopad, na back-výstupní ztráty, které způsobují kompletní vyjmutí zvuku.
* **Kolísání nebo pouhá kolísání příchodu mezi pakety**. Toto je průměrná změna v prodlevě mezi po sobě jdoucích paketů. Komunikační služby Azure se můžou přizpůsobit na určitou úroveň kolísání přes ukládání do vyrovnávací paměti. Je pouze v případě, že kolísání překračuje velikost vyrovnávací paměti, kterou účastník zaznamená jeho účinky.

### <a name="network-bandwidth"></a>Šířka pásma sítě

Ujistěte se, že je vaše síť nakonfigurovaná tak, aby podporovala šířku pásma vyžadovanou souběžnými pracovními relacemi Azure Communication Services a dalšími podnikovými aplikacemi. Testování kompletní síťové cesty pro kritické body šířky pásma je zásadní pro úspěšné nasazení vašich řešení multimediálních komunikačních služeb.

Níže jsou uvedeny požadavky na šířku pásma pro sady SDK pro JavaScript:

|Šířka pásma|Scénáře|
|:--|:--|
|40 kB/s|Volání zvuku peer-to-peer|
|500 kB/s|Volání zvukového volání a sdílení obrazovky peer-to-peer|
|500 kB/s|Video o kvalitě peer-to-peer s voláním 360p na 30fps|
|1,2 MB/s|Video o peer-to-peer s rozlišením HD volání s rozlišením HD 720p na 30fps|
|500 kB/s|Seskupit video s voláním 360p na 30fps|
|1,2 MB/s|Video skupiny HD volání s rozlišením HD 720p na 30fps| 

Níže jsou uvedené požadavky na šířku pásma pro nativní sady SDK pro Android a iOS:

|Šířka pásma|Scénáře|
|:--|:--|
|30 kB/s|Volání zvuku peer-to-peer |
|130 KB/s|Volání zvukového volání a sdílení obrazovky peer-to-peer|
|500 kB/s|Video o kvalitě peer-to-peer s voláním 360p na 30fps|
|1,2 MB/s|Video o peer-to-peer s rozlišením HD volání s rozlišením HD 720p na 30fps|
|1,5 MB/s|Video o kvalitě peer-to-peer s rozlišením HD 1080p na 30fps |
|500kbps/1Mbps|Seskupit volání videa|
|1Mbps/2Mbps|Audiovizuální volání ve skupině HD (540p videa na obrazovce 1080p)|

### <a name="firewalls-configuration"></a>Konfigurace bran firewall

Připojení ke službě Azure Communication Services vyžadují připojení k Internetu pro konkrétní porty a IP adresy, aby bylo možné doručovat vysoce kvalitní multimediální prostředí. Bez přístupu k těmto portům a IP adresám můžou komunikační služby Azure fungovat i nadále. Optimální prostředí je však k dispozici v případě, že jsou otevřeny Doporučené porty a rozsahy IP adres.

| Kategorie | Rozsahy IP adres nebo plně kvalifikovaný název domény | Porty | 
| :-- | :-- | :-- |
| Přenos multimédií | [Rozsah IP adres veřejného cloudu Azure](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 až 3481, porty TCP 443 |
| Signalizace, telemetrie, registrace| *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Optimalizace sítě

Následující úkoly jsou volitelné a nejsou nutné k provozu služeb Azure Communication Services. Tyto pokyny použijte k optimalizaci výkonu sítě a služby Azure Communication Services nebo pokud víte, že máte nějaká omezení sítě.
Možná budete chtít optimalizovat, pokud:
* Komunikační služby Azure běží pomalu (možná máte nedostatečnou šířku pásma).
* Volání, která se chovají (můžou být způsobená bránou firewall nebo blokování proxy serveru)
* Volání mají statické a vyjmuté zvuky nebo hlasy, jako je robot (může být kolísání nebo ztráta paketů).

| Úloha optimalizace sítě | Podrobnosti |
| :-- | :-- |
| Naplánování sítě | V této dokumentaci můžete najít minimální požadavky pro volání do vaší sítě. [Pro plánování vaší sítě](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) se podívejte na příklad týmů. |
| Překlad externích názvů | Ujistěte se, že všechny počítače, na kterých běží sady SDK komunikačních služeb Azure, můžou přeložit externí dotazy DNS na zjišťování služeb poskytovaných službou Azure Communication Servicers a že brány firewall nebrání přístupu. Ujistěte se prosím, že sady SDK můžou přeložit adresy *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.IO, *..  |
| Zachovat trvalost relace | Ujistěte se, že brána firewall nemění mapované adresy nebo porty protokolu NAT (Network Address Translation) pro protokol UDP.
Ověřit velikost fondu překladu adres | Ověřte, jestli je velikost fondu NAT (Network Address Translation) požadovaná pro připojení uživatele. Když má více uživatelů a zařízení přístup ke komunikačním službám Azure pomocí [překladu adres (NAT) nebo překladu adres portů (Pat)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365), zajistěte, aby zařízení skrytá za každou veřejnou IP adresou nepřesáhla podporované číslo. Zajistěte, aby fondy NAT přiřadily odpovídající veřejné IP adresy, aby se zabránilo vyčerpání portů. Vyčerpání portů přispěje interním uživatelům a zařízením, kteří se nebudou moct připojit ke komunikačním službám Azure. |
| Pokyny k detekci vniknutí a prevence | Pokud má vaše prostředí [zjišťování neoprávněných vniknutí](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) nebo systém prevence (ID/IP) nasazený pro dodatečnou vrstvu zabezpečení pro odchozí připojení, povolte všechny adresy URL služby Azure Communication Services. |
| Konfigurace sítě VPN s děleným tunelovým propojením | Doporučujeme zadat alternativní cestu pro provoz týmů, který obchází virtuální privátní síť (VPN), která se běžně označuje jako [tunelová síť VPN s děleným tunelem](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing). Dělené tunelové propojení znamená, že provoz pro komunikační služby Azure neprojde přes síť VPN, ale místo toho přejde přímo do Azure. Obcházení sítě VPN bude mít kladný dopad na kvalitu multimédií a snižuje zatížení ze zařízení VPN a ze sítě organizace. Pokud chcete implementovat síť VPN s děleným tunelovým propojením, pracujte s vaším dodavatelem sítě VPN. Další důvody, proč doporučujeme obejít síť VPN: <ul><li> Sítě VPN nejsou většinou navržené ani nakonfigurované pro podporu médií v reálném čase.</li><li> Sítě VPN také nemusí podporovat protokol UDP (což je vyžadováno pro komunikační služby Azure).</li><li>Sítě VPN také zavádí další vrstvu šifrování nad provozem médií, který je už zašifrovaný.</li><li>Připojení ke komunikačním službám Azure nemusí být efektivní kvůli přenosu vlasů prostřednictvím zařízení VPN.</li></ul>|
| Implementace technologie QoS | [Pomocí technologie QoS (Quality of Service)](https://docs.microsoft.com/microsoftteams/qos-in-teams) nakonfigurujte stanovení priorit paketů. Tím se vylepší kvalita volání a pomůže vám monitorovat a řešit potíže s kvalitou volání. Technologie QoS by měla být implementovaná na všech segmentech spravované sítě. I v případě, že se síť dostatečně zřídila pro šířku pásma, technologie QoS zajišťuje zmírnění rizik v případě neočekávaných událostí sítě. Díky technologii QoS je hlasový provoz nastaven na prioritu, aby tyto neočekávané události nenegativně ovlivnily kvalitu. | 
| Optimalizace Wi-Fi | Podobně jako u sítě VPN nejsou sítě Wi-Fi nutně navržené nebo nakonfigurované pro podporu médií v reálném čase. Plánování a optimalizace sítě Wi-Fi pro podporu komunikačních služeb Azure je důležitým aspektem pro vysoce kvalitní nasazení. Vezměte v úvahu tyto faktory: <ul><li>Implementujte technologie QoS nebo WiFi (Wi-Fi), abyste zajistili, že provoz médií v sítích Wi-Fi má odpovídající prioritu.</li><li>Naplánujte a optimalizujte pásma Wi-Fi a umístění přístupového bodu. Rozsah 2,4 GHz může poskytovat vhodné prostředí v závislosti na umístění přístupového bodu, ale přístup k bodům často ovlivňují jiná zákaznická zařízení, která provozují v daném rozsahu. Rozsah 5 GHz je lépe vhodný pro média v reálném čase, které je v důsledku jeho zúženého rozsahu, ale vyžaduje více přístupových bodů, aby bylo možné dosáhnout dostatečné pokrytí. Koncové body musí také podporovat tento rozsah a musí být nakonfigurovány tak, aby tyto pruhy využívaly odpovídajícím způsobem.</li><li>Pokud používáte sítě Wi-Band, zvažte implementaci řízení pásma. Řízení pásem je technika implementovaná dodavateli Wi-Fi, která umožňuje používat rozsah 5 GHz pro klienty s více procesory.</li><li>Pokud jsou přístupové body stejného kanálu příliš blízko dohromady, může dojít k překrytí signálu a neúmyslnému konkurenčnímu přístupu, což vede k degradování uživatelského prostředí. Ujistěte se, že přístupové body, které jsou vedle sebe navzájem, jsou na kanálech, které se nepřekrývají.</li></ul> Každý dodavatel bezdrátové sítě má vlastní doporučení pro nasazení jeho bezdrátového řešení. Konkrétní pokyny vám poskytne dodavatel Wi-Fi.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>Operační systém a prohlížeče (pro sady JavaScript SDK)

Sady SDK pro hlasové a video služby Azure Communications podporují určité operační systémy a prohlížeče.
Přečtěte si o operačních systémech a prohlížečích, které volající sady SDK podporují v rámci [volání koncepční dokumentace](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

## <a name="next-steps"></a>Další kroky

Následující dokumenty můžou být zajímavé:

- Další informace o [volání knihoven](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- Informace o [architektuře klient-server](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- Další informace o [topologiích toku volání](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)
