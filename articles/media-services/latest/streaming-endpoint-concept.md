---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Povinná pole. Další informace najdete na aka.ms/skyeye/meta.
title: koncové body streamování (počátek): Azure Media Services popis: informace o koncových bodech streamování (Origin), dynamickém balení a službě streamování, které doručují obsah přímo do aplikace Client Player nebo do Content Delivery Network (CDN). služby: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. rebavování: MS. téma: konceptuální MS. Date: 02/13/2020 MS. Author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Koncové body streamování (počátek) v Azure Media Services

V Microsoft Azure Media Services [koncový bod streamování](/rest/api/media/streamingendpoints) představuje dynamický (just-in-time) a službu Origin Service, která může doručovat obsah živě a na vyžádání přímo do aplikace Client Playeru pomocí některého z běžných protokolů mediálního vysílání (HLS nebo pomlčky). **Koncový bod streamování** navíc poskytuje dynamické šifrování (za běhu) pro špičkové několikanásobnou. 

Když vytvoříte účet Media Services, vytvoří se **výchozí** koncový bod streamování pro vás v zastaveném stavu. V rámci účtu se dá vytvořit víc koncových bodů streamování (viz [kvóty a omezení](limits-quotas-constraints.md)).

> [!NOTE]
> Pokud chcete začít streamovat videa, musíte spustit **koncový bod streamování** , ze kterého chcete streamovat video.
>
> Fakturuje se vám jenom v případě, že je koncový bod streamování ve stavu spuštěno.

Nezapomeňte si také projít téma [dynamického balení](dynamic-packaging-overview.md) . 

## <a name="naming-convention"></a>Konvence pojmenování

Formát názvu hostitele adresy URL streamování je: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , kde `servicename` = název koncového bodu streamování nebo název živé události.

Při použití výchozího koncového bodu streamování `servicename` se vynechá Tato adresa URL: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Omezení

* Název koncového bodu streamování má maximální hodnotu 24 znaků.
* Název by měl splňovat tento vzor [regulárního výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Typy

Existují dva typy **koncových bodů streamování** : **Standard** (Preview) a **Premium**. Typ je definovaný počtem jednotek škálování ( `scaleUnits` ), které přidělíte pro koncový bod streamování.

Maximální limit jednotek streamování je obvykle 10. Pokud chcete pro svůj účet zvýšit [limit, kontaktujte](https://azure.microsoft.com/support/create-ticket/) nás.

Popis těchto typů najdete v následující tabulce:

|Typ|Jednotky škálování|Popis|
|--------|--------|--------|  
|**Standard**|0|Výchozí koncový bod streamování je **standardní** typ – dá se změnit na typ Premium úpravou `scaleUnits` .|
|**Premium**|> 0|Úroveň **Premium** Koncové body streamování jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Přesunete se na typ **Premium** úpravou `scaleUnits` (jednotky streamování). `scaleUnits` Poskytněte vyhrazenou výstupní kapacitu, kterou můžete koupit v přírůstcích po 200 MB/s. Při použití typu **Premium** poskytuje každá povolená jednotka pro aplikaci další kapacitu šířky pásma. |

> [!NOTE]
> Pro zákazníky, kteří chtějí doručovat obsah do rozsáhlých internetových cílových skupin, doporučujeme povolit CDN pro koncový bod streamování.

Informace o smlouvě SLA najdete v článku [ceny a smlouvy SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Porovnávání typů streamování

Funkce|Standard|Premium
---|---|---
Propustnost |Až 600 MB/s a při použití CDN může poskytovat mnohem vyšší efektivní propustnost.|200 MB/s na jednotku streamování (SU). Může poskytovat mnohem vyšší efektivní propustnost při použití CDN.
CDN|Azure CDN, CDN třetí strany nebo bez CDN.|Azure CDN, CDN třetí strany nebo bez CDN.
Fakturuje se poměrná hodnota| Každý den|Každý den
Dynamické šifrování|Yes|Yes
Dynamické balení|Yes|Yes
Měřítko|Automatické škálování až na cílovou propustnost.|Další služba SUs
Filtrování IP/G20/vlastní hostitel <sup>1</sup>|Yes|Yes
Progresivní stahování|Yes|Yes
Doporučené použití |Doporučuje se pro velká většina scénářů streamování.|Profesionální použití.

<sup>1</sup> používá se jenom přímo na koncovém bodu streamování, když CDN není na koncovém bodu povolený.<br/>

## <a name="streaming-endpoint-properties"></a>Vlastnosti koncového bodu streamování

V této části jsou uvedeny podrobnosti o některých vlastnostech koncového bodu streamování. Příklady, jak vytvořit nový koncový bod streamování a popisy všech vlastností, najdete v tématu [koncový bod streamování](/rest/api/media/streamingendpoints/create).

- `accessControl`: Používá se ke konfiguraci následujících nastavení zabezpečení pro tento koncový bod streamování: Akamai klíče pro ověřování a IP adresy, které se můžou připojit k tomuto koncovému bodu. Tato vlastnost může být nastavena pouze v případě, že `cdnEnabled` je nastavena na hodnotu false.

- `cdnEnabled`: Určuje, jestli je povolená Integrace Azure CDN pro tento koncový bod streamování (ve výchozím nastavení zakázaná). Pokud nastavíte `cdnEnabled` hodnotu true, následující konfigurace se zablokují: `customHostNames` a `accessControl` .

    Ne všechna datová centra podporují integraci Azure CDN. Pokud chcete zjistit, jestli je v datovém centru dostupná Azure CDN integrace, proveďte následující kroky:

  - Zkuste nastavit na `cdnEnabled` hodnotu true.
  - Ověřte vrácený výsledek pro `HTTP Error Code 412` (PreconditionFailed) se zprávou o tom, že vlastnost CdnEnabled koncového bodu streamování nelze nastavit na hodnotu true, protože funkce CDN není v aktuální oblasti k dispozici.

    Pokud se zobrazí tato chyba, datové centrum je nepodporuje. Vyzkoušejte jiné datové centrum.

- `cdnProfile`: Když `cdnEnabled` je nastavená hodnota true, můžete také předávat `cdnProfile` hodnoty. `cdnProfile` je název profilu CDN, kde se vytvoří koncový bod CDN. Můžete zadat existující cdnProfile nebo použít nový. Pokud má Value hodnotu NULL a `cdnEnabled` má hodnotu true, použije se výchozí hodnota "AzureMediaStreamingPlatformCdnProfile". Pokud již zadaná `cdnProfile` instance existuje, je pod ní vytvořen koncový bod. Pokud profil neexistuje, vytvoří se automaticky nový profil.
- `cdnProvider`: Pokud je povoleno CDN, můžete také předat `cdnProvider` hodnoty. `cdnProvider` Určuje, který zprostředkovatel bude použit. V současné době jsou podporovány tři hodnoty: "StandardVerizon", "PremiumVerizon" a "StandardAkamai". Pokud není zadána žádná hodnota a `cdnEnabled` je true, použije se "StandardVerizon" (to je výchozí hodnota).
- `crossSiteAccessPolicies`: Používá se k určení zásad přístupu mezi weby pro různé klienty. Další informace najdete v tématu [specifikace souborů zásad pro různé domény](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) a [zpřístupnění služby napříč hranicemi domén](/previous-versions/azure/azure-services/gg185950(v=azure.100)). Nastavení platí pouze pro Smooth Streaming.
- `customHostNames`: Používá se ke konfiguraci koncového bodu streamování pro příjem provozu směrovaného na vlastní název hostitele. Tato vlastnost je platná pro koncové body streamování Standard a Premium a dá se nastavit, když `cdnEnabled` : false.

    Vlastnictví názvu domény musí potvrdit Media Services. Media Services ověří vlastnictví názvu domény tím, že vyžaduje `CName` záznam obsahující Media Services ID účtu jako součást, která se má přidat do domény, která se používá. Například pro "sports.contoso.com", který se má použít jako název vlastního hostitele pro koncový bod streamování, musí být záznam pro, který `<accountId>.contoso.com` odkazuje na jeden z Media Services názvů hostitelů pro ověření. Název ověřovacího hostitele se skládá z verifydns. \<mediaservices-dns-zone> .

    Níže jsou uvedené očekávané zóny DNS, které se použijí v záznamu ověření pro různé oblasti Azure.
  
  - Severní Amerika, Evropa, Singapur, Hongkong – zvláštní správní oblast, Japonsko:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Lidov

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Například `CName` záznam, který mapuje "945a4c4e-28ea-45CD-8ccb-a519f6b700ad.contoso.com" na "verifydns.Media.Azure.NET", potvrzuje, že Media Services ID 945a4c4e-28ea-45CD-8ccb-a519f6b700ad má vlastnictví domény contoso.com, čímž se v rámci tohoto účtu povoluje použití libovolného názvu v contoso.com jako vlastního názvu hostitele pro koncový bod streamování. Pokud chcete najít hodnotu ID mediální služby, klikněte na [Azure Portal](https://portal.azure.com/) a vyberte svůj účet Media Service. **ID účtu** se zobrazí v pravém horním rohu stránky.

    Pokud se pokusíte nastavit vlastní název hostitele bez správného ověření `CName` záznamu, odpověď DNS se nezdařila a po nějaké době se uloží do mezipaměti. Jakmile je uložen správný záznam, může chvíli trvat, než se znovu ověří odpověď uložená v mezipaměti. V závislosti na poskytovateli DNS pro vlastní doménu trvá několik minut do hodiny, než se záznam znovu ověří.

    Kromě toho, `CName` který je namapován `<accountId>.<parent domain>` na `verifydns.<mediaservices-dns-zone>` , je nutné vytvořit jiný `CName` , který mapuje název vlastního hostitele (například `sports.contoso.com` ) na název hostitele koncového bodu streamování Media Services (například `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Koncové body streamování nacházející se ve stejném datovém centru nemůžou sdílet stejný vlastní název hostitele.

    V současné době Media Services nepodporuje TLS s vlastními doménami.

- `maxCacheAge` – Přepíše výchozí hlavičku řízení mezipaměti protokolu HTTP pro maximální stáří nastavenou koncovým bodem streamování na fragmentech médií a manifestech na vyžádání. Hodnota se nastaví v sekundách.
- `resourceState` -

    - Zastaveno: počáteční stav koncového bodu streamování po vytvoření
    - Spouští se: Probíhá převod do běžícího stavu.
    - Spuštění: umožňuje streamování obsahu klientům.
    - Škálování: jednotky škálování se zvyšují nebo zmenšují.
    - Zastavení: probíhá přechod do stavu Zastaveno.
    - Odstraňování: se odstraňuje.

- `scaleUnits`: Poskytněte vyhrazenou výstupní kapacitu, kterou můžete koupit v přírůstcích po 200 MB/s. Pokud potřebujete přejít na typ **Premium** , upravte `scaleUnits` .

## <a name="why-use-multiple-streaming-endpoints"></a>Proč používat více koncových bodů streamování?

Jeden koncový bod streamování může streamovat videa živě i na vyžádání a většina zákazníků používá jenom jeden koncový bod streamování. V této části jsou uvedeny některé příklady, proč možná budete muset použít několik koncových bodů streamování.

* Každá rezervovaná jednotka umožňuje šířku pásma 200 MB/s. Pokud potřebujete více než 2 000 MB/s (2 GB/s) šířky pásma, můžete použít druhý koncový bod streamování a vyrovnávání zatížení a poskytnout tak větší šířku pásma.

    CDN je ale nejlepším způsobem, jak dosáhnout horizontálního navýšení kapacity pro streamování obsahu, ale pokud dodáváte tolik obsahu, který CDN nasazuje více než 2 GB/s, můžete přidat další koncové body streamování (zdroje). V takovém případě byste potřebovali vysílat adresy URL obsahu, které jsou vyvážené napříč dvěma koncovými body streamování. Tento přístup poskytuje lepší ukládání do mezipaměti, než pokus o odeslání požadavků do jednotlivých zdrojů náhodně (například prostřednictvím Traffic Manageru). 
    
    > [!TIP]
    > V případě, že CDN vychází z více než 2 GB/s, může být něco špatně nakonfigurované (například bez ochrany zdroje).
    
* Vyrovnávání zatížení různých poskytovatelů CDN. Například můžete nastavit výchozí koncový bod streamování pro použití CDN Verizon a vytvořit druhý pro použití Akamai. Pak přidejte některé vyrovnávání zatížení mezi dvěma, abyste dosáhli Vyrovnávání více CDN. 

    Zákazník ale často vyrovnává zatížení mezi více poskytovateli CDN pomocí jednoho původu.
* Streamování smíšeného obsahu: živé a video na vyžádání. 

    Vzorce přístupu pro živý a obsah na vyžádání se velmi liší. Živý obsah představuje hodně požadavků na stejný obsah najednou. Obsah videa na vyžádání (dlouhý konec obsahu archivu pro instanci) má nízké využití na stejném obsahu. Proto ukládání do mezipaměti funguje velmi dobře na živém obsahu, ale ne i na dlouhodobém obsahu.

    Vezměte v úvahu scénář, ve kterém budou vaši zákazníci hlavně sledovat živý obsah, ale občas sledují obsah na vyžádání a je obsluhován ze stejného koncového bodu streamování. Nízké využití obsahu na vyžádání by zabíralo místo v mezipaměti, které by bylo lépe uloženo pro živý obsah. V tomto scénáři doporučujeme, abyste zasloužili živý obsah z jednoho koncového bodu streamování a z jiného koncového bodu streamování obsah Long. Tím se zvýší výkon obsahu živé události.
    
## <a name="scaling-streaming-with-cdn"></a>Škálování streamování s využitím CDN

Viz následující články:

- [Přehled CDN](../../cdn/cdn-overview.md)
- [Škálování streamování s využitím CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Klást otázky a získávat aktualizace

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="see-also"></a>Viz také

[Dynamické balení](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Další kroky

[Správa koncových bodů streamování](manage-streaming-endpoints-howto.md)
