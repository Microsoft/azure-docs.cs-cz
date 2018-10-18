---
title: Koncové body streamování ve Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou koncové body streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 5ba80df26731f9687ec4518d7f12f3c2e3cb0665
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379174"
---
# <a name="streaming-endpoints"></a>Koncové body streamování

V Microsoft Azure Media Services (AMS) [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) entity reprezentuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content Delivery Network (CDN) pro další distribuce. Výstupní datový proud z koncového bodu streamování služby může být živý stream nebo Asset videa na vyžádání ve vašem účtu Media Services. Při vytváření účtu Azure Media Services, **výchozí** koncový bod streamování je vytvořen v zastaveném stavu. Nelze odstranit výchozí koncový bod streamování. Další koncové body streamování se dají vytvořit v rámci účtu. Pokud chcete spustit streamování videí, budete muset spustit koncový bod streamování. 

## <a name="streamingendpoint-types"></a>Typy StreamingEndpoint  

Existují dva **StreamingEndpoint** typy: **standardní** a **Premium**. Typ je definovaný počet jednotek škálování (`scaleUnits`) přidělit pro koncový bod streamování. 

Tabulka popisuje typy:  

|Typ|Jednotky škálování|Popis|
|--------|--------|--------|  
|**Koncový bod streamování standard** (doporučeno)|0|**Standardní** typ možnost se doporučuje pro téměř všechny scénáře datových proudů a cílové skupiny. **Standardní** typ automaticky škáluje šířku odchozího pásma. <br/>Pro zákazníky s velmi vysokými požadavky na Media Services nabízí **Premium** streamování koncových bodů, které je možné horizontálně navyšovat kapacitu pro největší publikum internet. Pokud neočekáváte velké cílové skupiny a souběžné prohlížeče, kontaktujte nás na adrese amsstreaming@microsoft.com pokyny ohledně toho, jestli budete muset přesunout **Premium** typu. |
|**Koncový bod streamování Premium**|>0|Koncové body streamování **Premium** jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Přejdete **Premium** typ úpravou `scaleUnits`. `scaleUnits` poskytují vyhrazený odchozího přenosu dat kapacity, který lze dokupovat v jednotkách po 200 MB/s. Při použití **Premium** typ, každá povolená jednotka poskytuje další kapacitu šířky pásma aplikace. |

## <a name="working-with-cdn"></a>Práce s CDN

Ve většině případů byste měli mít povolila se síť CDN. Ale pokud jsou předvídání maximální souběžnosti menší než 500 prohlížeče pak, doporučuje se zakázat CDN, protože CDN škáluje se souběžností nejlepší.

### <a name="detailed-explanation-of-how-caching-works"></a>Podrobné vysvětlení funguje jak ukládání do mezipaměti

Neexistuje žádná hodnota s konkrétní šířkou pásma při přidávání CDN, protože koncový bod streamování povolená šířku pásma, který je nezbytný pro síť CDN se liší. Mnoho závisí na typu obsahu, jak oblíbená je, přenosových rychlostí a protokoly. CDN je pouze ukládání do mezipaměti co jsou požadovány. To znamená, že oblíbeného obsahu bude obsluhovat přímo CDN – tak dlouho, dokud se uloží do mezipaměti videa fragment. Živý obsah je pravděpodobně ukládat do mezipaměti, protože obvykle mají mnoho diváci přesně stejnou věc. Vzhledem k tomu, že můžete mít nějaký obsah, který je Oblíbené a některé, které není, může být trochu trickier obsahu na vyžádání. Pokud máte milionů videa prostředků, pokud žádná z nich jsou oblíbené (pouze 1 nebo 2 prohlížeče v týdnu) ale máte tisíce lidí všechny různé videích CDN stane mnohem méně účinné. S touto mezipamětí výpadky, zvýšit zatížení na koncový bod streamování.
 
Také je potřeba zvážit funguje jak adaptivního streamování. Každé jednotlivé fragment videa se uloží do mezipaměti, protože se jedná o vlastní entity. Například pokud okamžiku, kdy je sledována určité videa, osoba přeskočí kolem sledování jenom pár sekund tu a tam pouze videa fragmenty, které jsou přidružené k co osoby sledovali vysílání televizní získat uložené v mezipaměti v CDN. Pomocí adaptivního streamování mají obvykle různých přenosových rychlostí 5 až 7 videa. Pokud jedna osoba sleduje jeden s přenosovou rychlostí a jinou osobu sleduje různé přenosové rychlosti, pak jejich jsou každý v mezipaměti samostatně v CDN. I v případě, že dva lidé sledují stejné přenosové rychlosti může být datové proudy přes různé protokoly. Každý protokol (HLS, MPEG-DASH, Smooth Streaming) je samostatně do mezipaměti. Takže každý s přenosovou rychlostí a protokolu jsou uložené v mezipaměti samostatně a pouze video fragmenty, které byly požadovány jsou uložené v mezipaměti.
 
## <a name="streamingendpoint-properties"></a>Vlastnosti StreamingEndpoint 

Tato část obsahuje podrobnosti o některé vlastnosti StreamingEndpoint. Příklady toho, jak vytvořit nový koncový bod streamování a popisy všech vlastností, najdete v článku [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

|Vlastnost|Popis|  
|--------------|----------|
|`accessControl`|Slouží ke konfiguraci následujících nastavení zabezpečení pro tento koncový bod streamování: Akamai podpis záhlaví ověřovací klíče a IP adresy, které jsou povolené pro připojení k tomuto koncovému bodu.<br />Tuto vlastnost můžete nastavit při `cdnEnabled`"" je nastavena na hodnotu false.|  
|`cdnEnabled`|Určuje, zda je integrace Azure CDN pro tento koncový bod streamování povolená (zakázané ve výchozím nastavení.)<br /><br /> Pokud nastavíte `cdnEnabled` na hodnotu true, následující konfigurace zakázán: `customHostNames` a `accessControl`.<br /><br />Ne všechna datová centra nepodporují integraci s Azure CDN. Chcete-li zkontrolovat, jestli má vaše datové centrum Azure CDN k dispozici integrace takto:<br /><br /> -Pokusu o nastavení `cdnEnabled` na hodnotu true.<br /><br /> – Zkontrolujte vrácený výsledek pro `HTTP Error Code 412` (PreconditionFailed) se zprávou "Vlastnost CdnEnabled koncový bod streamování nelze nastavit na hodnotu true, protože možnost CDN není v aktuální oblasti k dispozici."<br /><br /> Pokud se zobrazí tato chyba, datového centra se nepodporuje. Měli byste se pokusit jiného datového centra.|  
|`cdnProfile`|Když `cdnEnabled` je nastavena na hodnotu true, můžete také předat `cdnProfile` hodnoty. `cdnProfile` je název profilu CDN, kde budou vytvářeny koncového bodu CDN. Můžete zadat existující cdnProfile nebo použít nový. Pokud je hodnota NULL a `cdnEnabled` je true, výchozí hodnota používaná "AzureMediaStreamingPlatformCdnProfile". Pokud zadaný `cdnProfile` již existuje, je vytvořen koncový bod je pod ním. Pokud se profil, který neexistuje, se vytvoří automaticky nový profil.|
|`cdnProvider`|Když je povolené CDN, můžete také předat `cdnProvider` hodnoty. `cdnProvider` ovládací prvky, které poskytovatel se použije. V současné době jsou podporovány tři hodnoty: "StandardVerizon", "PremiumVerizon" a "StandardAkamai". Pokud se nezadá žádná hodnota a `cdnEnabled` má hodnotu true, "StandardVerizon" se používá (tj. výchozí hodnota.)|
|`crossSiteAccessPolicies`|Slouží k určení zásady přístupu mezi weby pro různé klienty. Další informace najdete v tématu [specifikace souboru zásady mezi doménami](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) a [zpřístupnění služby k dispozici napříč hranicemi domén](http://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).|  
|`customHostNames`|Slouží ke konfiguraci koncový bod streamování, tak, aby přijímal přenosy směrované na vlastním názvem hostitele. To umožňuje jednodušší konfigurace správy provozu prostřednictvím globální Traffic Manageru (GTM) a také pro názvy domén brandingem má být použit jako název koncového bodu streamování.<br /><br /> Azure Media Services musí potvrdit vlastnictví názvu domény. Azure Media Services ověří vlastnictví názvu domény tak, že vyžaduje `CName` záznam obsahující ID účtu Azure Media Services jako součást přidávaného do domény používá. Jako příklad pro "sports.contoso.com", který se použije jako vlastním názvem hostitele pro koncový bod streamování, záznam pro `<accountId>.contoso.com` musí být nakonfigurované tak, aby odkazoval na jeden z názvů hostitelů ověření Media Services. Ověření názvu hostitele se skládá z verifydns. \<zónu dns mediaservices >. Následující tabulka obsahuje očekávané zóny DNS pro použití v záznamu o ověření pro různé oblasti Azure.<br /><br /> Severní Amerika, Evropa, Singapur, Hongkong, Japonska:<br /><br /> -mediaservices.windows.net<br /><br /> -verifydns.mediaservices.windows.net<br /><br /> Čína:<br /><br /> -mediaservices.chinacloudapi.cn<br /><br /> -verifydns.mediaservices.chinacloudapi.cn<br /><br /> Například `CName` záznam, který mapuje "945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com" k "verifydns.mediaservices.windows.net" prokáže, že má Azure Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad vlastnictví doména Contoso.com, což umožní jakýkoli název v rámci contoso.com má být použit jako vlastním názvem hostitele pro koncový bod streamování pod tímto účtem.<br /><br /> Hodnota ID služby média, najdete [webu Azure portal](https://portal.azure.com/) a vyberte svůj účet mediálních služeb. ID MULTIMÉDIA služby se zobrazí na pravé straně stránky řídicí panel.<br /><br /> **Upozornění**: Pokud se pokus o nastavení vlastním názvem hostitele bez řádné ověření `CName` záznam, odpověď DNS se nezdaří a poté uložit do mezipaměti po určitou dobu. Jakmile správný záznam v místě, může nějakou dobu, dokud se ověřit odpověď uložená v mezipaměti. V závislosti na poskytovateli DNS pro vlastní doménu jejich může trvat pár minut až jednu hodinu na znovu ověřit záznam.<br /><br /> Kromě `CName` , která se mapuje `<accountId>.<parent domain>` k `verifydns.<mediaservices-dns-zone>`, musíte vytvořit jiný `CName` , který mapuje název vlastního hostitele (například `sports.contoso.com`) pro název hostitele vaší služby StreamingEndpont média (například `amstest.streaming.mediaservices.windows.net`).<br /><br /> **Poznámka:**: umístěný ve stejném datovém centru, koncové body streamování nemůže mít stejný název vlastního hostitele.<br /> Tato vlastnost je platný pro Standard a premium koncové body streamování a můžete nastavit, když "cdnEnabled": false<br/><br/> V současné době nepodporuje AMS SSL s použitím vlastních domén.  |  
|`maxCacheAge`|Přepíše výchozí max-age HTTP záhlaví mezipaměti ovládacího nastavil na fragmenty médií na vyžádání manifestů a koncový bod streamování. Hodnota je nastavena v řádu sekund.|
|`resourceState`|Hodnoty pro vlastnost:<br /><br /> -Zastavena. Počáteční stav koncového bodu streamování po jeho vytvoření.<br /><br /> – Spouštění. Koncový bod streamování přechází do stavu spuštěno.<br /><br /> – Spouštění. Koncový bod streamování je moct Streamovat obsah do klientů.<br /><br /> -Škálování. Jednotky škálování se zvýší nebo sníží.<br /><br /> -Je ukončován. Koncový bod streamování přechází do stavu ukončeno.<br/><br/> -Odstranění. Koncový bod streamování se odstraňuje.|
|`scaleUnits `|scaleUnits poskytují vyhrazený odchozího přenosu dat kapacity, který lze dokupovat v jednotkách po 200 MB/s. Pokud potřebujete přesunout **Premium** zadejte, upravte `scaleUnits`. |

## <a name="next-steps"></a>Další postup

Ukázka [v tomto úložišti](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) ukazuje, jak spustit výchozí koncový bod streamování s .NET.

