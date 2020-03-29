---
title: Streamování obsahu s integrací CDN
titleSuffix: Azure Media Services
description: Získejte informace o streamování obsahu s integrací CDN, stejně jako předběžné načítání a origin-assist CDN-Prefetch.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128068"
---
# <a name="stream-content-with-cdn-integration"></a>Streamování obsahu s integrací CDN

Azure Content Delivery Network (CDN) nabízí vývojářům globální řešení pro rychlé doručování širokopásmového obsahu uživatelům díky ukládání obsahu do mezipaměti na fyzických uzlech strategicky umístěných po celém světě.  

CDN ukládá do mezipaměti obsah streamovaný z koncového bodu streamování mediálních služeb [(origin)](streaming-endpoint-concept.md) na kodek, protokol pro streamování, datový tok, formát kontejneru a na šifrování/DRM. Pro každou kombinaci kodeku-streaming protokolu-kontejner formát-bitrate-šifrování, bude existovat samostatná mezipaměť CDN.

Populární obsah bude doručen přímo z mezipaměti CDN, pokud je fragment videa uložen do mezipaměti. Živý obsah bude pravděpodobně uložen do mezipaměti, protože obvykle mnoho lidí sleduje přesně stejnou věc. Obsah na vyžádání může být trochu složitější, protože můžete mít nějaký obsah, který je populární a některé, které nejsou. Pokud máte miliony video datových zdrojů, kde žádný z nich není populární (pouze jeden nebo dva diváci týdně), ale máte tisíce lidí, kteří sledují všechna různá videa, CDN se stává mnohem méně efektivní.

Musíte také zvážit, jak adaptivní streamování funguje. Každý jednotlivý fragment videa je uložen do mezipaměti jako jeho vlastní entita. Představte si například, že se poprvé zhlédne určité video. Pokud divák přeskočí a sleduje jen několik sekund sem a tam, pouze video fragmenty spojené s tím, co osoba sledovala dostat cache v CDN. S adaptivnístreamování, obvykle máte 5 až 7 různých datových proudů videa. Pokud jedna osoba sleduje jeden přenosový tok a jiná osoba sleduje jiný přenosový tok, pak jsou každý ukládá do mezipaměti samostatně v CDN. I když dva lidé sledují stejný datový tok, mohou streamovat přes různé protokoly. Každý protokol (HLS, MPEG-DASH, Smooth Streaming) je ukládán do mezipaměti samostatně. Takže každý přenosový tok a protokol jsou ukládány do mezipaměti samostatně a pouze ty fragmenty videa, které byly požadovány, jsou uloženy do mezipaměti.

Při rozhodování, zda povolit CDN na [koncový bod streamování](streaming-endpoint-concept.md)media services , zvažte počet očekávaných diváků. CDN pomáhá pouze v případě, že očekáváte mnoho diváků pro váš obsah. Pokud maximální souběžnost prohlížeče je nižší než 500, je doporučeno zakázat CDN, protože CDN váhy nejlépe souběžnosti.

Toto téma popisuje povolení [integrace CDN](#enable-azure-cdn-integration). Vysvětluje také předběžné načítání (aktivní ukládání do mezipaměti) a koncept [CDN-Prefetch origin-assist.](#origin-assist-cdn-prefetch)

## <a name="considerations"></a>Požadavky

* [Koncový bod](streaming-endpoint-concept.md) `hostname` streamování a adresa URL streamování zůstávají stejné bez ohledu na to, zda povolíte cdn.
* Pokud potřebujete možnost otestovat obsah s nebo bez CDN, vytvořte jiný koncový bod streamování, který není povolen CDN.

## <a name="enable-azure-cdn-integration"></a>Povolení integrace Azure CDN

> [!IMPORTANT]
> CdN nelze povolit pro zkušební nebo studentské účty Azure.
>
> Integrace CDN je povolená ve všech datových centrech Azure s výjimkou oblastí federální vlády a Číny.

Po streamování koncový bod je zřízena s cdn povoleno, je definovaná čekací doba na Media Services před aktualizací DNS se provádí mapování koncového bodu streamování na koncový bod CDN.

Pokud později chcete zakázat nebo povolit CDN, koncový bod streamování musí být v **zastaveném** stavu. Může trvat až dvě hodiny, než se integrace Azure CDN aktivuje a změny budou aktivní ve všech přístupových psách CDN. Můžete však spustit koncový bod streamování a datový proud bez přerušení z koncového bodu streamování. Po dokončení integrace je datový proud doručován z CDN. Během období zřizování koncový bod streamování bude ve **výchozím** stavu a můžete sledovat snížený výkon.

Když je vytvořen koncový bod standardního streamování, je ve výchozím nastavení nakonfigurován pomocí standardu Verizon. Můžete nakonfigurovat Poskytovatele Premium Verizon nebo Standard Akamai pomocí rozhraní REST API.

Integrace Azure Media Services s Azure CDN se implementuje na **Azure CDN od Verizonu** pro standardní koncové body streamování. Koncové body streamování Premium lze nakonfigurovat pomocí všech **cenových úrovní Azure CDN a poskytovatelů**.

> [!NOTE]
> Podrobnosti o Azure CDN najdete v přehledu [CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Zjištění, zda byla provedena změna DNS

Můžete určit, pokud byla provedena změna DNS na koncovém bodu streamování (provoz <https://www.digwebinterface.com>je směrován na Azure CDN) pomocí . Pokud se ve výsledcích zobrazí azureedge.net názvy domén, je nyní přenos směrován na cdn.

## <a name="origin-assist-cdn-prefetch"></a>Původ-Assist CDN-Prefetch

Ukládání CDN do mezipaměti je reaktivní proces. Pokud CDN může předpovědět, co další objekt bude požadováno, CDN můžete proaktivně požadovat a mezipaměti další objekt. Pomocí tohoto procesu můžete dosáhnout přístupů do mezipaměti pro všechny (nebo většinu) objektů, což zlepšuje výkon.

Koncept prefetching se snaží umístit objekty na "okraji internetu" v očekávání, že tyto budou požadovány hráčem bezprostředně, čímž se zkrátí čas dodat, že objekt na hráče.

K dosažení tohoto cíle musí koncový bod streamování (původ) a CDN pracovat ruku v ruce několika způsoby:

- Původ mediálních služeb musí mít "inteligence" (Origin-Assist) informovat CDN další objekt předběžného načtení.
- CDN provádí předběžné načítání a ukládání do mezipaměti (část CDN-prefetch). CDN také musí mít "inteligenci" informovat původ, zda je to prefetch nebo pravidelné fetch, zpracování 404 odpovědí, a způsob, jak se vyhnout nekonečné prefetch smyčky.

### <a name="benefits"></a>Výhody

Výhody funkce *Origin-Assist CDN-Prefetch* zahrnují:

- Předběžné načtení zlepšuje kvalitu přehrávání videa tím, že předem umístí očekávané segmenty videa na hraniční chod během přehrávání, sníží latenci diváka a zlepší dobu stahování segmentů videí. Výsledkem je rychlejší spuštění videa a nižší výskyty rebuffering.
- Tento koncept je použitelný pro obecný scénář původu CDN a není omezen na média.
- Akamai přidal tuto funkci do [akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Tato funkce se zatím nevztahuje na koncový bod streamování Akamai CDN integrovaný s koncovým bodem služby Media Services. Je však k dispozici pro zákazníky mediálních služeb, kteří mají již existující smlouvu Akamai a vyžadují vlastní integraci mezi Akamai CDN a původem mediálních služeb.

### <a name="how-it-works"></a>Jak to funguje

Podpora CDN `Origin-Assist CDN-Prefetch` pro záhlaví (pro živé i video streamování na vyžádání) je k dispozici zákazníkům, kteří mají přímou smlouvu s Akamai CDN. Tato funkce zahrnuje následující výměny hlaviček PROTOKOLU HTTP mezi akamai CDN a původem mediálních služeb:

|Hlavička protokolu HTTP|Hodnoty|Odesílatel|Příjemce|Účel|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (výchozí) nebo 0 |CDN|Zdroj|Chcete-li označit CDN je povoleno předběžné načtení.|
|`CDN-Origin-Assist-Prefetch-Path`| Příklad: <br/>Fragmenty(video=14000000000,format=mpd-time-cmaf)|Zdroj|CDN|Chcete-li poskytnout cestu předběžného načtení do sítě CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (žádost o předběžné načtení) nebo 0 (pravidelný požadavek)|CDN|Zdroj|Chcete-li označit požadavek z CDN je předběžné načtení.|

Chcete-li zobrazit část výměny záhlaví v akci, můžete zkusit následující kroky:

1. Pomocí postman nebo cURL vydat požadavek na původ Media Services pro audio nebo video segmentu nebo fragmentu. Nezapomeňte přidat záhlaví `CDN-Origin-Assist-Prefetch-Enabled: 1` v žádosti.
2. V odpovědi byste měli vidět `CDN-Origin-Assist-Prefetch-Path` záhlaví s relativní cestu jako jeho hodnotu.

### <a name="supported-streaming-protocols"></a>Podporované protokoly streamování

Tato `Origin-Assist CDN-Prefetch` funkce podporuje následující protokoly streamování pro živé a on-demand streamování:

* HLS v3
* HLS v4
* HLS CMAF
* Dash (CSF)
* POMLČKA (CMAF)
* Plynulé streamování

### <a name="faqs"></a>Nejčastější dotazy

* Co když je adresa URL cesty předběžného načtení neplatná, takže předběžné načtení CDN získá 404?

    CDN uloží odpověď 404 do mezipaměti pouze po dobu 10 sekund (nebo jiné nakonfigurované hodnoty).

* Předpokládejme, že máte video na vyžádání. Pokud cdn-prefetch je povolena, znamená tato funkce, že jakmile klient požádá o první segment videa, předběžné načtení spustí smyčku pro předběžné načtení všech následných segmentů videa při stejném přenosovém toku?

    Ne, předběžné načtení CDN se provádí pouze po požadavku/odpovědi iniciovaném klientem. CDN-prefetch se nikdy neaktivuje předběžné načtení, aby se zabránilo prefetch smyčky.

* Je funkce Origin-Assist CDN-Prefetch vždy zapnutá? Jak jej lze zapnout /vypnout?

    Tato funkce je ve výchozím nastavení vypnutá. Zákazníci ji musí zapnout prostřednictvím rozhraní Akamai API.

* Co by se stalo s Origin-Assist, pokud další segment nebo fragment ještě není k dispozici?

    V takovém případě původ mediálních služeb `CDN-Origin-Assist-Prefetch-Path` nebude poskytovat záhlaví a CDN-prefetch nedojde.

* Jak `Origin-Assist CDN-Prefetch` funguje práce s dynamickými filtry manifestu?

    Tato funkce funguje nezávisle na filtru manifestu. Když je další fragment mimo okno filtru, jeho adresa URL bude stále umístěna tak, že se vydívá do nezpracovaného klientského manifestu a pak bude vrácena jako hlavička odpovědi cdn předběžného načtení. Takže CDN dostane URL fragmentu, který je odfiltrován z DASH / HLS / Smooth manifestu. Přehrávač však nikdy nepodá žádost GET na CDN, aby načítaný fragment, protože tento fragment není součástí manifestu DASH/HLS/Smooth, který hráč drží (přehrávač neví o existenci tohoto fragmentu).

* Může být dash MPD/HLS playlist/Smooth manifest přednačován?

    Ne, DASH MPD, hlavní seznam stop HLS, seznam stop variant HLS nebo hladká adresa URL manifestu se do hlavičky předběžného načtení nepřidá.

* Jsou adresy URL předběžného načtení relativní nebo absolutní?

    Zatímco Akamai CDN umožňuje obojí, původ mediálních služeb poskytuje pouze relativní adresy URL pro cestu předběžného načtení, protože při používání absolutních adres URL není žádný zjevný přínos.

* Funguje tato funkce s obsahem chráněným proti DRM?

    Ano, protože tato funkce funguje na úrovni PROTOKOLU HTTP, nedekóduje ani neanalyzuje žádný segment nebo fragment. Je jedno, zda je obsah zašifrován nebo ne.

* Funguje tato funkce s vkládáním reklam na straně serveru (SSAI)?
    
    Funguje tak pro původní/hlavní obsah (původní obsah videa před vložením reklamy), protože SSAI nemění časové razítko zdrojového obsahu z počátku mediálních služeb. To, zda tato funkce funguje s obsahem reklamy, závisí na tom, zda původ reklamy podporuje Origin-Assist. Pokud jsou například obsah reklamy hostované také ve službě Azure Media Services (stejný nebo samostatný původ), bude obsah reklam y také předem načten.

* Funguje tato funkce s obsahem UHD/HEVC?

    Ano.

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

* Zkontrolujte dokument [Koncového bodu streamování (původu).](streaming-endpoint-concept.md)
* Ukázka [v tomto úložišti](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) ukazuje, jak spustit výchozí koncový bod streamování pomocí rozhraní .NET.
