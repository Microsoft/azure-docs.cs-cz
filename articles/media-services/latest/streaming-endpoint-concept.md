---
title: Koncové body streamování (počátek)
titleSuffix: Azure Media Services
description: Přečtěte si o streamovacích koncových bodech (Origin), dynamické službě balení a streamování, která doručuje obsah přímo do aplikace pro klientské hráče nebo do sítě pro doručování obsahu (CDN).
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
ms.openlocfilehash: 72cfdf172e4524e302ef2e22826d4f78ce32daf0
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582733"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streamování koncových bodů (Origin) ve službě Azure Media Services

Ve službě Microsoft Azure Media Services představuje [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) dynamickou službu balení (just-in-time) a službu původu, která může doručovat váš živý obsah a obsah na vyžádání přímo do aplikace klientského přehrávače pomocí jednoho ze společných protokolů datových proudů médií (HLS nebo DASH). Koncový bod **streamování** navíc poskytuje dynamické šifrování (just-in-time) pro špičkové driody DRM. 

Při vytváření účtu Služby Media Services je pro vás v zastaveném stavu vytvořen **výchozí** koncový bod streamování. **Výchozí** koncový bod streamování nelze odstranit. Další koncové body streamování lze vytvořit v rámci účtu (viz [Kvóty a limity).](limits-quotas-constraints.md)

> [!NOTE]
> Chcete-li spustit streamování videí, musíte spustit **koncový bod streamování,** ze kterého chcete streamovat video.
>
> Fakturuje se vám jenom v případě, že je koncový bod streamování ve spuštěném stavu.

Nezapomeňte si také prohlédnout téma [dynamického balení.](dynamic-packaging-overview.md) 

## <a name="naming-convention"></a>Konvence

Formát názvu hostitele adresy URL `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`streamování `servicename` je: , kde = název koncového bodu streamování nebo název živé události.

Při použití výchozího koncového bodu streamování `servicename` je `{accountname}-{regionname}.streaming.azure.net`vynechán, takže adresa URL je: .

### <a name="limitations"></a>Omezení

* Název koncového bodu streamování má maximální hodnotu 24 znaků.
* Název by měl následovat tento `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`vzor [regulárního výrazu:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

## <a name="types"></a>Typy

Existují dva typy **koncových bodů streamování:** **Standard** (náhled) a **Premium**. Typ je definován počtem jednotek`scaleUnits`měřítka ( ) přidělovaných pro koncový bod datového proudu.

Popis těchto typů najdete v následující tabulce:

|Typ|Jednotky škálování|Popis|
|--------|--------|--------|  
|**Standard**|0|Výchozí koncový bod streamování je **standardní** typ – lze jej změnit `scaleUnits`na typ Premium úpravou .|
|**Premium**|> 0|**Prémiové pojistné** Koncové body streamování jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Na typ **Premium** se přesunete úpravou `scaleUnits` (jednotky streamování). `scaleUnits`poskytují vyhrazenou výstupní kapacitu, kterou lze zakoupit v krocích po 200 Mb/s. Při použití typu **Premium** poskytuje každá povolená jednotka aplikaci další kapacitu šířky pásma. |

> [!NOTE]
> Zákazníkům, kteří chtějí doručovat obsah velkým internetovým cílovým skupinám, doporučujeme povolit cdn v koncovém bodu streamování.

Informace o sla naleznete v [tématu Ceny a SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Porovnání typů datových proudů

Funkce|Standard|Premium
---|---|---
Propustnost |Až 600 Mb/s a může poskytnout mnohem vyšší efektivní propustnost při použití CDN.|200 Mb/s na streamovací jednotku (SU). Může poskytnout mnohem vyšší efektivní propustnost při použití CDN.
CDN|Azure CDN, CDN jiného výrobce nebo žádné CDN.|Azure CDN, CDN jiného výrobce nebo žádné CDN.
Fakturace je poměrně| denně|denně
Dynamické šifrování|Ano|Ano
Dynamické balení|Ano|Ano
Měřítko|Automatické škálování až na cílovou propustnost.|Další sus
Filtrování IP/G20/Vlastní hostitel <sup>1</sup>|Ano|Ano
Postupné stahování|Ano|Ano
Doporučené použití |Doporučeno pro drtivou většinu scénářů streamování.|Profesionální použití.

<sup>1</sup> Používá se pouze přímo na koncovém bodu streamování, pokud není v koncovém bodě povolena síť CDN.<br/>

## <a name="streaming-endpoint-properties"></a>Vlastnosti koncového bodu streamování

Tato část obsahuje podrobnosti o některých vlastnostech koncového bodu streamování. Příklady, jak vytvořit nový koncový bod streamování a popisy všech vlastností, naleznete v [tématu Koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: Slouží ke konfiguraci následujících nastavení zabezpečení pro tento koncový bod streamování: Akamai Podpis header ověřování klíče a IP adresy, které jsou povoleny pro připojení k tomuto koncovému bodu. Tuto vlastnost lze nastavit `cdnEnabled` pouze v případě, že je nastavena na false.

- `cdnEnabled`: Označuje, pokud je povolena integrace Azure CDN pro tento koncový bod streamování (ve výchozím nastavení zakázána). Pokud nastavíte `cdnEnabled` hodnotu true, budou `customHostNames` `accessControl`zakázány následující konfigurace: a .

    Ne všechna datová centra podporují integraci Azure CDN. Pokud chcete zkontrolovat, jestli vaše datové centrum nemá k dispozici integraci Azure CDN, postupujte takto:

  - Pokuste se `cdnEnabled` nastavit na true.
  - Zkontrolujte vrácený `HTTP Error Code 412` výsledek (PreconditionFailed) se zprávou "Streamování koncového bodu CdnEnabled vlastnost nelze nastavit na hodnotu true jako funkce CDN není k dispozici v aktuální oblasti."

    Pokud se zobrazí tato chyba, datové centrum ji nepodporuje. Zkuste jiné datové centrum.

- `cdnProfile`: `cdnEnabled` Když je nastavena na `cdnProfile` hodnotu true, můžete také předat hodnoty. `cdnProfile`je název profilu CDN, kde bude vytvořen bod koncového bodu CDN. Můžete zadat existující cdnProfile nebo použít nový. Pokud je hodnota `cdnEnabled` NULL a je true, výchozí hodnota "AzureMediaStreamingPlatformCdnProfile" se používá. Pokud již `cdnProfile` existuje zadaný, je pod ním vytvořen koncový bod. Pokud profil neexistuje, automaticky se vytvoří nový profil.
- `cdnProvider`: Pokud je povolena síť `cdnProvider` CDN, můžete také předat hodnoty. `cdnProvider`který z nich bude použit. V současné době jsou podporovány tři hodnoty: "StandardVerizon", "PremiumVerizon" a "StandardAkamai". Pokud není k dispozici `cdnEnabled` žádná hodnota a je true, "StandardVerizon" se používá (to je výchozí hodnota).
- `crossSiteAccessPolicies`: Slouží k určení zásad přístupu mezi sítěmi pro různé klienty. Další informace naleznete v [tématech Specifikace souboru zásad mezi doménami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) a [Zpřístupnění služby přes hranice domény](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). Nastavení platí pouze pro plynulé streamování.
- `customHostNames`: Slouží ke konfiguraci koncového bodu streamování tak, aby přijímal přenosy směrované na vlastní název hostitele. Tato vlastnost je platná pro koncové body standardního `cdnEnabled`a prémiového streamování a lze ji nastavit, když : false.

    Vlastnictví názvu domény musí být potvrzeno službou Media Services. Media Services ověří vlastnictví názvu domény `CName` tím, že vyžaduje záznam obsahující ID účtu Mediální služby jako součást, která má být přidána do domény, která je používána. Jako příklad pro "sports.contoso.com", které mají být použity jako vlastní název `<accountId>.contoso.com` hostitele pro koncový bod streamování, musí být záznam pro nakonfigurován tak, aby ukazoval na jeden z názvů hostitelů ověřování služby Media Services. Název ověřovacího hostitele se skládá z verifydns. \<mediaservices-dns-zone>.

    Níže jsou očekávané zóny DNS, které mají být použity v záznamu ověření pro různé oblasti Azure.
  
  - Severní Amerika, Evropa, Singapur, Hongkong – zvláštní správní oblast, Japonsko:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Čína:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Například `CName` záznam, který mapuje "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" na "verifydns.media.azure.net" dokazuje, že ID mediálních služeb 945a4c4e-28ea-45cd-8ccb-a519f6b700ad má vlastnictví domény contoso.com, což umožňuje libovolný název pod contoso.com, který má být použit jako vlastní název hostitele pro koncový bod streamování pod tímto účtem. Pokud chcete najít hodnotu ID služby Media, přejděte na [portál Azure](https://portal.azure.com/) a vyberte účet mediální služby. **ID účtu** se zobrazí v pravém horním rohu stránky.

    Pokud dojde k pokusu o nastavení vlastního názvu hostitele `CName` bez řádného ověření záznamu, odpověď DNS se nezdaří a poté bude na určitou dobu uložena do mezipaměti. Jakmile je správný záznam na místě, může chvíli trvat, než bude odpověď uložená v mezipaměti znovu ověřena. V závislosti na poskytovateli DNS pro vlastní doménu trvá obnovení platnosti záznamu od několika minut do hodiny.

    Kromě `CName` aplikace, která `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>`se mapuje `CName` na , je nutné vytvořit `sports.contoso.com`jiný název, který mapuje vlastní název hostitele `amstest-usea.streaming.media.azure.net`(například) na název hostitele koncového bodu datových proudů médií (například ).

    > [!NOTE]
    > Koncové body streamování umístěné ve stejném datovém centru nemohou sdílet stejný vlastní název hostitele.

    V současné době media services nepodporuje TLS s vlastními doménami.

- `maxCacheAge`- Přepíše výchozí hlavičku ovládacího prvku http cache s maximálním stářím nastavenou koncovým bodem streamování na fragmentech médií a manifestech na vyžádání. Hodnota je nastavena v sekundách.
- `resourceState` -

    - Zastaveno: počáteční stav koncového bodu streamování po vytvoření
    - Spuštění: přechází do spuštěného stavu
    - Běh: je schopen streamovat obsah do klientů
    - Škálování: jednotky měřítka se zvyšují nebo snižují
    - Zastavení: přechází do zastaveného stavu.
    - Odstranění: je odstraněno

- `scaleUnits`: Poskytněte vyhrazenou výstupní kapacitu, kterou lze zakoupit v krocích po 200 Mb/s. Pokud potřebujete přejít na typ `scaleUnits` **Premium,** upravte .

## <a name="why-use-multiple-streaming-endpoints"></a>Proč používat více koncových bodů streamování?

Jeden koncový bod streamování může streamovat videa živé i na vyžádání a většina zákazníků používá pouze jeden koncový bod streamování. Tato část uvádí některé příklady, proč může být nutné použít více koncových bodů streamování.

* Každá rezervovaná jednotka umožňuje šířku pásma 200 Mb/s. Pokud potřebujete šířku pásma více než 2 000 Mb/s (2 Gb/s), můžete použít druhý koncový bod streamování a vyvážení zatížení, který vám poskytne další šířku pásma.

    CDN je však nejlepší způsob, jak dosáhnout horizontálního navýšení kapacity pro streamování obsahu, ale pokud dodáváte tolik obsahu, že CDN táhne více než 2 Gb/s, můžete přidat další koncové body streamování (počátky). V takovém případě budete muset rozdávat adresy URL obsahu, které jsou vyvážené mezi dvěma koncovými body streamování. Tento přístup poskytuje lepší ukládání do mezipaměti než pokus o odesílání požadavků na každý původ náhodně (například prostřednictvím správce provozu). 
    
    > [!TIP]
    > Obvykle v případě, že CDN je tahání více než 2 Gbps pak něco může být nesprávně nakonfigurován (například žádný původ stínění).
    
* Vyrovnávání zatížení různých zprostředkovatelů CDN. Můžete například nastavit výchozí koncový bod streamování pro použití verizoncdn a vytvořit druhý pro použití Akamai. Pak přidejte nějaké vyrovnávání zatížení mezi těmito dvěma k dosažení multi-CDN vyvažování. 

    Zákazník však často provést vyrovnávání zatížení mezi více zprostředkovatelů CDN pomocí jednoho původu.
* Streamování smíšeného obsahu: Živé vysílání a video na vyžádání. 

    Vzory přístupu pro živý a na vyžádání obsahu jsou velmi odlišné. Živý obsah má tendenci získat velkou poptávku po stejném obsahu najednou. Obsah videa na vyžádání (například obsah archivu s dlouhým ocasem) má nízké využití stejného obsahu. Tak caching funguje velmi dobře na živý obsah, ale ne tak dobře na dlouhý ocas obsah.

    Zvažte scénář, ve kterém vaši zákazníci sledují hlavně živý obsah, ale jen občas sledují obsah na vyžádání a obsluhuje se ze stejného koncového bodu streamování. Nízké využití obsahu na vyžádání by zabíralo místo v mezipaměti, které by bylo lépe uloženo pro živý obsah. V tomto scénáři doporučujeme obsluhovat živý obsah z jednoho koncového bodu streamování a dlouhý koncový obsah z jiného koncového bodu streamování. Tím se zlepší výkon obsahu živých událostí.
    
## <a name="scaling-streaming-with-cdn"></a>Škálování streamování s využitím CDN

Viz následující články:

- [Přehled sítě CDN](../../cdn/cdn-overview.md)
- [Škálování streamování s využitím CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Ptejte se a získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="see-also"></a>Viz také

[Dynamické balení](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Další kroky

[Správa koncových bodů streamování](manage-streaming-endpoints-howto.md)
