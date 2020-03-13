---
title: Škálování streamování pomocí CDN
titleSuffix: Azure Media Services
description: Přečtěte si o službě streamování, která doručuje obsah přímo do aplikace Client Player nebo do Content Delivery Network (CDN).
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
ms.openlocfilehash: e918f7ee64d4bc49d5da80bf9a3e7595555296dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203691"
---
# <a name="scaling-streaming-with-cdn"></a>Škálování streamování s využitím CDN

Azure Content Delivery Network (CDN) nabízí vývojářům globální řešení pro rychlé doručování širokopásmového obsahu uživatelům díky ukládání obsahu do mezipaměti na fyzických uzlech strategicky umístěných po celém světě.  

CDN mezipaměť obsahu streamuje z [koncového bodu streamování Media Services (počátek)](streaming-endpoint-concept.md) na jeden kodek, podle protokolu pro streamování, podle přenosové rychlosti, podle formátu kontejneru a podle šifrování/DRM. Pro každou kombinaci kodek-streamování protokolu – formát kontejneru – přenosová rychlost – šifrování bude k dispozici samostatná mezipaměť CDN. 

Oblíbený obsah bude obsluhován přímo z mezipaměti CDN, pokud je fragment videa uložen do mezipaměti. Živý obsah se pravděpodobně ukládá do mezipaměti, protože obvykle mnoho lidí sleduje přesně stejné věci. Obsah na vyžádání může být bit trickier, protože je možné, že máte oblíbený obsah, který není. Pokud máte miliony grafických prostředků, kde žádná z nich není oblíbená (jenom jeden nebo dva čtenáři v týdnu), ale máte tisíce lidí, které sledují všechna videa, bude CDN mnohem méně efektivní. 

Také je potřeba vzít v úvahu, jak funguje adaptivní streamování. Jednotlivé fragmenty videa se ukládají do mezipaměti, protože jde o vlastní entitu. Představte si například, že při prvním spuštění konkrétního videa se bude sledovat. Pokud se v prohlížeči přeskočí, jak sledovat jenom pár sekund, stačí jenom fragmenty videa spojené s tím, co osoba sledovala v mezipaměti v síti CDN. Díky adaptivnímu streamování obvykle máte 5 až 7 různých přenosů videa. Pokud jedna osoba sleduje jednu přenosovou rychlost a jiná osoba sleduje jinou přenosovou rychlost, pak se všechny ukládají do mezipaměti samostatně v síti CDN. I když dva lidé sledují stejnou přenosovou rychlost, mohly by být streamování přes různé protokoly. Každý protokol (HLS, MPEG-POMLČKa, Smooth Streaming) se ukládá do mezipaměti samostatně. Takže všechny přenosové rychlosti a protokoly se ukládají do mezipaměti odděleně a všechny požadované fragmenty videa se ukládají do mezipaměti.

Pokud se rozhodnete, jestli chcete povolit CDN na [koncovém bodu streamování](streaming-endpoint-concept.md)Media Services, zvažte počet předpokládaných prohlížečů. CDN vám pomůže jenom v případě, že očekáváte spoustu prohlížečů pro váš obsah. Pokud je maximální souběžnost prohlížeče nižší než 500, doporučuje se zakázat CDN, protože CDN má nejlepší velikost jako v souběžnosti. 

Toto téma popisuje povolení [integrace CDN](#enable-azure-cdn-integration). Vysvětluje také předběžné načítání (aktivní ukládání do mezipaměti) a koncept od počátku do služby [CDN-Assist](#origin-assist-cdn-prefetch) .

## <a name="considerations"></a>Požadavky

* [Koncový bod streamování](streaming-endpoint-concept.md) `hostname` a adresa URL streamování zůstávají stejné, bez ohledu na to, jestli povolíte CDN.
* Pokud potřebujete mít možnost testovat obsah s CDN nebo bez něj, vytvořte další koncový bod streamování, který není CDN povolený.

## <a name="enable-azure-cdn-integration"></a>Povolit integraci Azure CDN

> [!IMPORTANT]
> CDN se nedá povolit pro zkušební verze nebo účty Azure studenta.
>
> Integrace CDN je povolená ve všech datových centrech Azure s výjimkou federálních oblastí pro státní správu a Čínu.

Po zřízení koncového bodu streamování s povoleným CDN je u Media Services definovaná čekací doba, než se provede aktualizace DNS pro mapování koncového bodu streamování na koncový bod CDN.

Pokud budete chtít síť CDN později zakázat nebo povolit, musí být koncový bod streamování ve stavu **Zastaveno** . Může trvat až dvě hodiny, než se aktivuje Integrace Azure CDN a změny, které se mají aktivní napříč všemi body POP CDN. Můžete ale spustit koncový bod streamování a datový proud bez přerušení z koncového bodu streamování a po dokončení integrace se datový proud doručí ze sítě CDN. Během období zřizování bude koncový bod streamování ve **výchozím** stavu a může se stát, že dojde ke snížení výkonu.

Když se vytvoří koncový bod streamování Standard, je ve výchozím nastavení nakonfigurovaný se standardem Verizon. Pomocí rozhraní REST API můžete nakonfigurovat poskytovatele Premium Verizon nebo Standard Akamai.

Azure Media Services integrace s Azure CDN je implementovaná na **Azure CDN z Verizon** pro koncové body streamování Standard. Koncové body streamování Premium se dají nakonfigurovat pomocí všech **Azure CDN cenové úrovně a zprostředkovatelů**. 

> [!NOTE]
> Podrobnosti o Azure CDN najdete v [přehledu CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Určení, jestli se provedla změna DNS

Můžete určit, jestli se změna DNS provedla u koncového bodu streamování (provoz se směruje na Azure CDN) pomocí https://www.digwebinterface.com. Pokud se ve výsledcích zobrazí azureedge.net názvy domén, provoz se teď nasměruje na CDN.

## <a name="origin-assist-cdn-prefetch"></a>Počátek – pomoc CDN – předběžné načtení

Mezipaměť CDN je reaktivní proces. Pokud CDN dokáže předpovědět, co bude požadovat další objekt, CDN může aktivně požádat o a uložit další objekt do mezipaměti. V tomto procesu můžete dosáhnout přístupů do mezipaměti pro všechny (nebo většinu) objektů, a tím zvýšit výkon.

Koncept předběžného načítání se snaží umístit objekty na hranici Internetu, ve kterém se předpokládá, že je přehrávač požádán o bezprostřední, čímž se zkrátí doba doručování tohoto objektu do přehrávače.

Aby bylo možné dosáhnout tohoto cíle, musí koncový bod streamování (Origin) a CDN fungovat rukou: 

- Aby bylo možné informovat CDN o dalším objektu k předběžnému navýšení, Media Services původu musí mít "Intelligence" (Origin-Assist). 
- CDN provede předběžné načtení a ukládání do mezipaměti (část CDN-předběžného načtení). CDN musí také obsahovat "Intelligence", aby informovala o původu, ať už se jedná o předběžné načtení nebo běžné načtení, zpracování odpovědí 404 a způsob, jak se vyhnout nekonečné smyčce předběžného předběžného načtení.

### <a name="benefits"></a>Výhody

Výhody funkce *původu-asistent CDN-předběžného načtení* zahrnují:

- Předběžná navýšení zlepšuje kvalitu přehrávání videa: při přehrávání předhraničních očekávaných segmentů videa na okraji můžete snížit latenci pro prohlížeč a zlepšit dobu stahování segmentů videa. Výsledkem je rychlejší spuštění videa a snížení výskytu nedokončených ukládání do vyrovnávací paměti.
- Tento koncept platí pro obecný scénář pro CDN – počátek, nikoli jenom pro média.
- Akamai přidal tuto funkci do [Akamai (ACE) pro vložení do cloudu](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Tato funkce ještě není platná pro Akamai CDN integrovaná s koncovým bodem streamování Media Services. Je ale k dispozici pro Media Services zákazníky, kteří mají již existující smlouvu Akamai, a vyžadují vlastní integraci mezi Akamai CDN a Media Servicesm zdrojem.

### <a name="how-it-works"></a>Jak to funguje

Pro zákazníky, kteří mají přímý kontrakt s Akamai CDN, je k dispozici podpora CDN pro *původní hlavičky předběžného načtení obsahu CDN* (pro streamování živě i video na vyžádání). Tato funkce zahrnuje následující výměny hlaviček protokolu HTTP mezi Akamai CDN a Media Servicesm počátkem:

|Hlavička protokolu HTTP|Hodnoty|Odesílatel|Příjemce|Účel|
| ---- | ---- | ---- | ---- | ----- |
|CDN – počátek – pomoc – předběžné načtení – povoleno | 1 (výchozí) nebo 0 |CDN|Počátek|K označení CDN je povolené předběžné načtení|
|CDN – počátek-asistence – předběžné načtení-cesta| Příklad: <br/>Fragmenty (video = 1400000000, Format = MPD-Time-CMAF)|Počátek|CDN|Zadání cesty předběžného načtení do sítě CDN|
|CDN – počátek-asistence – předběžné načtení – požadavek|1 (požadavek na předběžné načtení) nebo 0 (pravidelný požadavek)|CDN|Počátek|Pro indikaci, že žádost z CDN je předběžné načtení|

Pokud se chcete podívat na část výměny hlaviček v akci, můžete vyzkoušet následující postup:

1. Použijte metodu post nebo kudrlinkou k vydání žádosti o Media Services původ pro zvukový nebo segment nebo fragment videa. Ujistěte se, že jste v žádosti přidali hlavičku CDN-počátek-asistenci-pomoc-předběžné načtení – povoleno: 1.
2. V odpovědi byste měli vidět hlavičku CDN-Origin-Assist-předběžného umístění s relativní cestou jako hodnotou.

### <a name="supported-streaming-protocols"></a>Podporované protokoly streamování 

Funkce pro vynechání služby *CDN-Assist od počátku* do provozu podporuje následující protokoly streamování pro živé streamování a streamování na vyžádání:

* HLS V3
* HLS v4
* HLS CMAF
* POMLČKA (CSF)
* POMLČKA (CMAF)
* Hladké streamování

### <a name="faqs"></a>Nejčastější dotazy

* Co když adresa URL cesty předběžného načtení není platná, takže předběžné načtení CDN získá 404? 

    CDN bude ukládat do mezipaměti odpověď 404 jenom na 10 sekund (nebo na jinou konfigurovanou hodnotu).
* Předpokládejme, že máte video na vyžádání. Pokud je povoleno předběžné načtení obsahu CDN (CDN-), znamená to, že jakmile si klient vyžádá první segment videa, předběžného načítání spustí smyčku k předběžnému navýšení všech dalších segmentů videa na stejnou rychlost? 

    Ne, CDN – předběžné načtení se provádí až po žádosti nebo odpovědi iniciované klientem. CDN – předběžné načtení není nikdy aktivované předplatným, aby se zabránilo cyklu předběžného načtení. 
* Je funkce vynechání od počátku k pro CDN? Jak je možné ji zapnout nebo vypnout? 

    Tato funkce je ve výchozím nastavení vypnuta. Zákazníci je musí zapnout přes rozhraní Akamai API.
* V případě živého streamování, co by se stalo s původem – pomoc, pokud další segment nebo fragment ještě není k dispozici? 

    V takovém případě Media Services původu neposkytne hlavičku CDN-Origin-Assist-předběžného načtení cesty a CDN – k předběžnému načtení nebude dojít.
* Jak funguje *nahlašování obsahu CDN ze zdroje na pomoc* s dynamickými filtry manifestů? 

    Tato funkce funguje nezávisle na filtru manifestu. Pokud je další fragment mimo okno filtru, jeho adresa URL bude i nadále umístěna tak, že se zobrazí nezpracovaný manifest klienta a pak se vrátí jako hlavička odpovědi předběžného načtení CDN. To znamená, že CDN získá adresu URL fragmentu, který se vyfiltruje z nepřerušovaného/HLS/hladkého manifestu. Přehrávač ale nikdy neodešle požadavek GET do sítě CDN k načtení tohoto fragmentu, protože tento fragment není zahrnutý v manifestu POMLČKy/HLS/hladký, který je držený přehrávačem (přehrávač neví, že fragment není existence).
* Je možné, že se předběžnému načtení manifestu HLS/vyhladit

    V hlavičce předběžného načtení není přidána žádná, POMLČKa MPD, hlavní seznam testů HLS, seznam HLS variant nebo vyhlazená adresa URL manifestu.
* Jsou předběžné adresy URL předběžného nebo absolutního. 

    I když Akamai CDN umožňuje, Media Services původu poskytuje jenom relativní adresy URL pro cestu předběžného načtení, protože použití absolutních adres URL nemá žádné zjevné výhody.
* Funguje tato funkce s obsahem chráněným pomocí DRM?

    Ano, vzhledem k tomu, že tato funkce funguje na úrovni HTTP, neprovádí dekódování ani neanalyzuje žádný segment nebo fragment. Nezáleží na tom, jestli je obsah zašifrovaný nebo ne.
* Funguje tato funkce se vkládáním na straně serveru (SSAI)?
    
    Původní/hlavní obsah (původní obsah videa před vložením služby AD) funguje, protože SSAI nemění časové razítko zdrojového obsahu z Media Services původu. Určuje, jestli tato funkce funguje s obsahem AD, záleží na tom, jestli zdroj služby AD podporuje zdroj-pomoc. Například pokud je obsah služby AD hostovaný také v Azure Media Services (stejný nebo oddělený původ), obsah služby AD bude také přednačten.
* Funguje tato funkce s obsahem UHD/HEVC?

    Ano.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

* Nezapomeňte další dokument [koncového bodu streamování (Origin)](streaming-endpoint-concept.md) .
* Ukázka [v tomto úložišti](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) ukazuje, jak spustit výchozí koncový bod streamování pomocí .NET.
