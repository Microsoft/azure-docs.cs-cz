---
title: Koncové body streamování (počátek)
titleSuffix: Azure Media Services
description: Přečtěte si o koncových bodech streamování (Origin), dynamickém balení a službě streamování, které doručují obsah přímo do aplikace Client Player nebo do Content Delivery Network (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: c8901dccb67e91c608e999f823cf7d2e757da08b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186019"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Koncové body streamování (počátek) v Azure Media Services

V Microsoft Azure Media Services [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) představuje dynamický (just-in-time) a službu Origin Service, která může doručovat obsah živě a na vyžádání přímo do aplikace Client Playeru pomocí některého z běžných protokolů mediálního vysílání (HLS nebo pomlčky). **Koncový bod streamování** navíc poskytuje dynamické šifrování (za běhu) pro špičkové několikanásobnou.

Když vytvoříte účet Media Services, vytvoří se **výchozí** koncový bod streamování pro vás v zastaveném stavu. Nemůžete odstranit **výchozí** koncový bod streamování. V rámci účtu se dá vytvořit víc koncových bodů streamování (viz [kvóty a omezení](limits-quotas-constraints.md)).

> [!NOTE]
> Pokud chcete začít streamovat videa, musíte spustit **koncový bod streamování** , ze kterého chcete streamovat video.
>
> Fakturuje se vám jenom v případě, že je koncový bod streamování ve stavu spuštěno.

## <a name="naming-convention"></a>Konvence pojmenování

Formát názvu hostitele adresy URL streamování je: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, kde `servicename` = název koncového bodu streamování nebo název živé události.

Při použití výchozího koncového bodu streamování je `servicename` vynecháno, takže adresa URL je: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Omezení

* Název koncového bodu streamování má maximální hodnotu 24 znaků.
* Název by měl splňovat tento vzor [regulárního výrazu](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typy

Existují dva typy **koncových bodů streamování** : **Standard** (Preview) a **Premium**. Typ je definovaný počtem jednotek škálování (`scaleUnits`), které přidělíte pro koncový bod streamování.

Popis těchto typů najdete v následující tabulce:

|Typ|Jednotky škálování|Popis|
|--------|--------|--------|  
|**Standard**|0|Výchozí koncový bod streamování je **standardní** typ – dá se změnit na typ Premium úpravou `scaleUnits`.|
|**Premium**|>0|Úroveň **Premium** Koncové body streamování jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Přesunete se na typ **Premium** úpravou `scaleUnits` (jednotky streamování). `scaleUnits` poskytují vyhrazenou výstupní kapacitu, kterou můžete koupit v přírůstcích po 200 MB/s. Při použití typu **Premium** poskytuje každá povolená jednotka pro aplikaci další kapacitu šířky pásma. |

> [!NOTE]
> Pro zákazníky, kteří chtějí doručovat obsah do rozsáhlých internetových cílových skupin, doporučujeme povolit CDN pro koncový bod streamování.

Informace o smlouvě SLA najdete v článku [ceny a smlouvy SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Porovnávání typů streamování

Funkce|Standard|Premium
---|---|---
Propustnost |Až 600 MB/s a při použití CDN může poskytovat mnohem vyšší efektivní propustnost.|200 MB/s na jednotku streamování (SU). Může poskytovat mnohem vyšší efektivní propustnost při použití CDN.
CDN|Azure CDN, CDN třetí strany nebo bez CDN.|Azure CDN, CDN třetí strany nebo bez CDN.
Fakturuje se poměrná hodnota| Denně|Denně
Dynamické šifrování|Ano|Ano
Dynamické balení|Ano|Ano
Měřítko|Automatické škálování až na cílovou propustnost.|Další služba SUs
Filtrování IP/G20/vlastní hostitel <sup>1</sup>|Ano|Ano
Progresivní stahování|Ano|Ano
Doporučené použití |Doporučuje se pro velká většina scénářů streamování.|Profesionální použití.

<sup>1</sup> používá se jenom přímo na koncovém bodu streamování, když CDN není na koncovém bodu povolený.<br/>

## <a name="properties"></a>Vlastnosti

V této části jsou uvedeny podrobnosti o některých vlastnostech koncového bodu streamování. Příklady, jak vytvořit nový koncový bod streamování a popisy všech vlastností, najdete v tématu [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: používá se ke konfiguraci následujících nastavení zabezpečení pro tento koncový bod streamování: Akamai podpisové klíče a IP adresy, které se můžou připojit k tomuto koncovému bodu. Tuto vlastnost lze nastavit pouze v případě, že je parametr `cdnEnabled` nastaven na hodnotu false.

- `cdnEnabled`: Určuje, jestli je povolená Integrace Azure CDN pro tento koncový bod streamování (ve výchozím nastavení vypnuté). Pokud nastavíte `cdnEnabled` na hodnotu true, následující konfigurace se zablokují: `customHostNames` a `accessControl`.

    Ne všechna datová centra podporují integraci Azure CDN. Pokud chcete zjistit, jestli je v datovém centru dostupná Azure CDN integrace, proveďte následující kroky:

  - Zkuste nastavit `cdnEnabled` na hodnotu true.
  - Podívejte se na vrácený výsledek pro `HTTP Error Code 412` (PreconditionFailed) se zprávou, že vlastnost CdnEnabled koncového bodu streamování nelze nastavit na hodnotu true, protože funkce CDN není v aktuální oblasti k dispozici.

    Pokud se zobrazí tato chyba, datové centrum je nepodporuje. Vyzkoušejte jiné datové centrum.

- `cdnProfile`: Pokud je `cdnEnabled` nastavená na hodnotu true, můžete také předávat `cdnProfile` hodnoty. `cdnProfile` je název profilu CDN, ve kterém se vytvoří bod koncového bodu CDN. Můžete zadat existující cdnProfile nebo použít nový. Pokud je hodnota NULL a `cdnEnabled` true, použije se výchozí hodnota "AzureMediaStreamingPlatformCdnProfile". Pokud zadaný `cdnProfile` již existuje, je pod ním vytvořen koncový bod. Pokud profil neexistuje, vytvoří se automaticky nový profil.
- `cdnProvider`: když je povolený CDN, můžete taky předat `cdnProvider` hodnoty. `cdnProvider` řídí, který zprostředkovatel bude použit. V současné době jsou podporovány tři hodnoty: "StandardVerizon", "PremiumVerizon" a "StandardAkamai". Pokud není zadána žádná hodnota a `cdnEnabled` má hodnotu true, použije se "StandardVerizon" (to je výchozí hodnota).
- `crossSiteAccessPolicies`: slouží k určení zásad přístupu mezi weby pro různé klienty. Další informace najdete v tématu [specifikace souborů zásad pro různé domény](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) a [zpřístupnění služby napříč hranicemi domén](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). Nastavení platí pouze pro Smooth Streaming.
- `customHostNames`: slouží ke konfiguraci koncového bodu streamování pro příjem provozu směrovaného na vlastní název hostitele. Tato vlastnost je platná pro koncové body streamování Standard a Premium a dá se nastavit, když `cdnEnabled`: false.

    Vlastnictví názvu domény musí potvrdit Media Services. Media Services ověří vlastnictví názvu domény tím, že vyžaduje záznam `CName` obsahující Media Services ID účtu jako součást, která se má přidat do domény, která se používá. Jako příklad pro "sports.contoso.com", který se má použít jako název vlastního hostitele pro koncový bod streamování, musí být záznam pro `<accountId>.contoso.com` nakonfigurovaný tak, aby odkazoval na jeden z názvů hostitelů pro ověření Media Services. Název ověřovacího hostitele se skládá z verifydns.\<MediaServices-DNS-Zone >.

    Níže jsou uvedené očekávané zóny DNS, které se použijí v záznamu ověření pro různé oblasti Azure.
  
  - Severní Amerika, Evropa, Singapur, Hongkong – zvláštní správní oblast, Japonsko:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Lidov

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Například záznam `CName`, který mapuje "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" na "verifydns.media.azure.net", prokáže, že Media Services ID 945a4c4e-28ea-45CD-8ccb-a519f6b700ad má vlastnictví domény contoso.com, čímž se v rámci tohoto účtu povoluje použití libovolného názvu v contoso.com jako vlastního názvu hostitele pro koncový bod streamování. Pokud chcete najít hodnotu ID mediální služby, klikněte na [Azure Portal](https://portal.azure.com/) a vyberte svůj účet Media Service. **ID účtu** se zobrazí v pravém horním rohu stránky.

    Pokud se pokusíte nastavit vlastní název hostitele bez správného ověření záznamu `CName`, odpověď DNS se nezdařila a po nějaké době se uloží do mezipaměti. Jakmile je uložen správný záznam, může chvíli trvat, než se znovu ověří odpověď uložená v mezipaměti. V závislosti na poskytovateli DNS pro vlastní doménu trvá několik minut do hodiny, než se záznam znovu ověří.

    Kromě `CName`, který mapuje `<accountId>.<parent domain>` na `verifydns.<mediaservices-dns-zone>`, je nutné vytvořit další `CName`, který mapuje název vlastního hostitele (například `sports.contoso.com`) na název hostitele koncového bodu streamování Media Services (například `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Koncové body streamování nacházející se ve stejném datovém centru nemůžou sdílet stejný vlastní název hostitele.

    V současné době Media Services nepodporuje protokol SSL s vlastními doménami.

- `maxCacheAge` – přepíše výchozí hlavičku řízení mezipaměti protokolu HTTP pro maximální stáří nastavenou koncovým bodem streamování na fragmenty média a manifestech na vyžádání. Hodnota se nastaví v sekundách.
- `resourceState` -

    - Zastaveno: počáteční stav koncového bodu streamování po vytvoření
    - Spouští se: Probíhá převod do běžícího stavu.
    - Spuštění: umožňuje streamování obsahu klientům.
    - Škálování: jednotky škálování se zvyšují nebo zmenšují.
    - Zastavení: probíhá přechod do stavu Zastaveno.
    - Odstraňování: se odstraňuje.

- `scaleUnits`: poskytují vyhrazenou výstupní kapacitu, kterou můžete koupit v přírůstcích po 200 MB/s. Pokud potřebujete přejít na typ **Premium** , upravte `scaleUnits`.

## <a name="working-with-cdn"></a>Práce s CDN

Ve většině případů byste měli mít povolenou síť CDN. Pokud však očekáváte maximální souběžnost, která je nižší než 500 prohlížečů, doporučuje se zakázat CDN, protože CDN nejlépe odpovídá souběžnosti.

### <a name="considerations"></a>Požadavky

* Koncový bod streamování `hostname` a adresa URL streamování zůstávají stejné, bez ohledu na to, jestli povolíte CDN.
* Pokud potřebujete mít možnost testovat obsah s CDN nebo bez něj, vytvořte další koncový bod streamování, který není CDN povolený.

### <a name="detailed-explanation-of-how-caching-works"></a>Podrobné vysvětlení, jak funguje ukládání do mezipaměti

Při přidávání CDN není k dispozici žádná konkrétní hodnota šířky pásma, protože velikost pásma potřebná pro koncový bod streamování s povoleným CDNm se liší. Spousta záleží na typu obsahu, na tom, jak oblíbené je, přenosové rychlosti a protokoly. CDN ukládá do mezipaměti jenom to, co se požaduje. To znamená, že oblíbený obsah bude obsluhován přímo ze sítě CDN – Pokud je fragment videa uložen do mezipaměti. Živý obsah se pravděpodobně ukládá do mezipaměti, protože obvykle mnoho lidí sleduje přesně stejné věci. Obsah na vyžádání může být bit trickier, protože je možné, že máte oblíbený obsah, který není. Pokud máte miliony grafických prostředků, kde žádná z nich není oblíbená (jenom jeden nebo dva čtenáři v týdnu), ale máte tisíce lidí, které sledují všechna videa, bude CDN mnohem méně efektivní. V případě neúspěšných přístupů k této mezipaměti zvýšíte zatížení koncového bodu streamování.

Také je potřeba vzít v úvahu, jak funguje adaptivní streamování. Jednotlivé fragmenty videa se ukládají do mezipaměti, protože jde o vlastní entitu. Představte si například, že při prvním spuštění konkrétního videa se bude sledovat. Pokud se v prohlížeči přeskočí, jak sledovat jenom pár sekund, a tu tam, jenom fragmenty videa spojené s tím, co osoba sledovala, získá v mezipaměti CDN. Díky adaptivnímu streamování obvykle máte 5 až 7 různých přenosů videa. Pokud jedna osoba sleduje jednu přenosovou rychlost a jiná osoba sleduje jinou přenosovou rychlost, pak se všechny ukládají do mezipaměti samostatně v síti CDN. I když dva lidé sledují stejnou přenosovou rychlost, mohly by být streamování přes různé protokoly. Každý protokol (HLS, MPEG-POMLČKa, Smooth Streaming) se ukládá do mezipaměti samostatně. Takže všechny přenosové rychlosti a protokoly se ukládají do mezipaměti odděleně a všechny požadované fragmenty videa se ukládají do mezipaměti.

### <a name="enable-azure-cdn-integration"></a>Povolit integraci Azure CDN

Po zřízení koncového bodu streamování s povoleným CDN je u Media Services definovaná čekací doba, než se provede aktualizace DNS pro mapování koncového bodu streamování na koncový bod CDN.

Pokud budete chtít síť CDN později zakázat nebo povolit, musí být koncový bod streamování ve stavu **Zastaveno** . Může trvat až dvě hodiny, než se aktivuje Integrace Azure CDN a změny, které se mají aktivní napříč všemi body POP CDN. Můžete ale spustit koncový bod streamování a datový proud bez přerušení z koncového bodu streamování a po dokončení integrace se datový proud doručí ze sítě CDN. Během období zřizování bude koncový bod streamování ve **výchozím** stavu a může se stát, že dojde ke snížení výkonu.

Když se vytvoří koncový bod streamování Standard, je ve výchozím nastavení nakonfigurovaný se standardem Verizon. Pomocí rozhraní REST API můžete nakonfigurovat poskytovatele Premium Verizon nebo Standard Akamai.

Integrace CDN je povolená ve všech datových centrech Azure s výjimkou Číny a federálních vládních oblastí.

> [!IMPORTANT]
> Azure Media Services integrace s Azure CDN je implementovaná na **Azure CDN z Verizon** pro koncové body streamování Standard. Koncové body streamování Premium se dají nakonfigurovat pomocí všech **Azure CDN cenové úrovně a zprostředkovatelů**. Další informace o funkcích Azure CDN najdete v [přehledu CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-was-made"></a>Určení, jestli se provedla změna DNS

Můžete určit, jestli se změna DNS provedla u koncového bodu streamování (provoz se směruje na Azure CDN) pomocí https://www.digwebinterface.com. Pokud výsledky ve výsledcích obsahují azureedge.net názvy domén, provoz se teď nasměruje na CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

Ukázka [v tomto úložišti](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) ukazuje, jak spustit výchozí koncový bod streamování pomocí .NET.
