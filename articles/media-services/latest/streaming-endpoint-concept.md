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
ms.date: 03/30/2019
ms.author: juliako
ms.openlocfilehash: 8cd6a68f6593a5b746a19e42e4835deb05e112b6
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757186"
---
# <a name="streaming-endpoints"></a>Koncové body streamování

V Microsoft Azure Media Services (AMS) [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) entity reprezentuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content Delivery Network (CDN) pro další distribuce. Výstupní datový proud z **koncový bod streamování** služba může být v živém datovém proudu nebo Asset videa na vyžádání ve vašem účtu Media Services. Při vytváření účtu Azure Media Services, **výchozí** koncový bod streamování je vytvořen v zastaveném stavu. Nelze odstranit **výchozí** koncový bod streamování. Další koncové body streamování se dají vytvořit v rámci účtu. 

> [!NOTE]
> Pokud chcete spustit streamování videa, musíte spustit **koncový bod streamování** ze kterého chcete Streamovat videa. 

## <a name="naming-convention"></a>Zásady vytváření názvů

Pro výchozí koncový bod: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

Pro všechny další koncové body: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Typy  

Existují dva typy **koncových bodů streamování**: **Standard** a **Premium**. Typ je definovaný počet jednotek škálování (`scaleUnits`) přidělit pro koncový bod streamování. 

Popis těchto typů najdete v následující tabulce:  

|Type|Jednotky škálování|Popis|
|--------|--------|--------|  
|**Koncový bod streamování Standard** (doporučeno)|0|Výchozí hodnota je koncový bod streamování **standardní** typu, ale můžete změnit na typu Premium.<br/> Standardní typ možnost se doporučuje pro téměř všechny scénáře datových proudů a cílové skupiny. Typ **Standard** škáluje šířku odchozího pásma automaticky. Propustnost z tohoto typu koncový bod streamování je až 600 MB/s. Video fragmenty do mezipaměti v CDN, nepoužívejte šířky pásma koncového bodu streamování.<br/>Pro zákazníky s velmi náročnými požadavky služba Media Services nabízí koncové body streamování **Premium**, pomocí kterých je možné horizontálně rozšiřovat kapacitu i pro ty největší cílové skupiny na internetu. Pokud neočekáváte velké cílové skupiny a souběžné prohlížeče, kontaktujte nás na amsstreaming\@microsoft.com pokyny ohledně toho, jestli budete muset přesunout **Premium** typu. |
|**Koncový bod streamování Premium**|> 0|Koncové body streamování **Premium** jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Přejdete **Premium** typ úpravou `scaleUnits`. `scaleUnits` poskytují vyhrazený odchozího přenosu dat kapacity, který lze dokupovat v jednotkách po 200 MB/s. Při použití typu **Premium** každá povolená jednotka poskytuje aplikaci další kapacitu šířky pásma. |
 
## <a name="comparing-streaming-types"></a>Porovnání typů datových proudů

### <a name="features"></a>Funkce

Funkce|Standard|Premium
---|---|---
Zdarma prvních 15 dnů| Ano |Ne
Propustnost |Až 600 MB/s při Azure CDN se nepoužívá. Škálování s CDN.|200 MB/s za jednotku (SU) pro streaming. Škálování s CDN.
SLA | 99.9|99,9 (200 MB/s za SU).
CDN|Azure CDN, třetích stran CDN nebo žádné CDN.|Azure CDN, třetích stran CDN nebo žádné CDN.
Ceny jsou poměrně přepočítané| Denně|Denně
Dynamické šifrování|Ano|Ano
Dynamické balení|Ano|Ano
Měřítko|Automatické škálování až na cílové propustnosti.|Dodatečné jednotky streamování
Filtrování/G20/vlastního hostitele IP <sup>1</sup>|Ano|Ano
Progresivní stahování|Ano|Ano
Doporučené použití |Doporučuje se pro většinu scénářů streamování.|Profesionální použití.<br/>Pokud se domníváte, že máte potřebám Standard. Kontaktujte nás (amsstreaming@microsoft.com) Pokud budete chtít souběžných cílovou skupinu velikost větší než 50 000 prohlížeče.

<sup>1</sup> pouze použít přímo na koncový bod streamování, pokud síť CDN není povolené pro koncový bod.

## <a name="working-with-cdn"></a>Práce s CDN

Ve většině případů byste měli mít povolenou síť CDN. Pokud však předpokládáte, že budete mít maximálně 500 souběžných diváků, doporučujeme síť CDN zakázat, protože se nejlépe škáluje s vysokou souběžností.

> [!NOTE]
> Koncový bod streamování `hostname` a adresu URL streamování zůstala stejná, zda povolit síť CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Podrobné vysvětlení funguje jak ukládání do mezipaměti

Neexistuje žádná hodnota s konkrétní šířkou pásma při přidávání CDN, protože koncový bod streamování povolená šířku pásma, který je nezbytný pro síť CDN se liší. Mnoho závisí na typu obsahu, jak oblíbená je, přenosových rychlostí a protokoly. CDN je pouze ukládání do mezipaměti co jsou požadovány. To znamená, že oblíbeného obsahu bude obsluhovat přímo CDN – tak dlouho, dokud se uloží do mezipaměti videa fragment. Živý obsah je pravděpodobně ukládat do mezipaměti, protože obvykle mají mnoho diváci přesně stejnou věc. Vzhledem k tomu, že můžete mít nějaký obsah, který je Oblíbené a některé, které není, může být trochu trickier obsahu na vyžádání. Pokud máte milionů videa prostředků, pokud žádná z nich jsou oblíbené (pouze 1 nebo 2 prohlížeče v týdnu) ale máte tisíce lidí všechny různé videích CDN stane mnohem méně účinné. S touto mezipamětí výpadky, zvýšit zatížení na koncový bod streamování.
 
Také je potřeba zvážit funguje jak adaptivního streamování. Každé jednotlivé fragment videa se uloží do mezipaměti, protože se jedná o vlastní entity. Například pokud okamžiku, kdy je sledována určité videa, osoba přeskočí kolem sledování jenom pár sekund tu a tam pouze videa fragmenty, které jsou přidružené k co osoby sledovali vysílání televizní získat uložené v mezipaměti v CDN. Pomocí adaptivního streamování mají obvykle různých přenosových rychlostí 5 až 7 videa. Pokud jedna osoba sleduje jeden s přenosovou rychlostí a jinou osobu sleduje různé přenosové rychlosti, pak jejich jsou každý v mezipaměti samostatně v CDN. I v případě, že dva lidé sledují stejné přenosové rychlosti může být datové proudy přes různé protokoly. Každý protokol (HLS, MPEG-DASH, Smooth Streaming) je samostatně do mezipaměti. Takže každý s přenosovou rychlostí a protokolu jsou uložené v mezipaměti samostatně a pouze video fragmenty, které byly požadovány jsou uložené v mezipaměti.
 
## <a name="properties"></a>Vlastnosti 

Tato část obsahuje podrobnosti o některé vlastnosti Endpoint streamování. Příklady toho, jak vytvořit nový koncový bod streamování a popisy všech vlastností, najdete v článku [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` -Použít ke konfiguraci následujících nastavení zabezpečení pro tento koncový bod streamování: Akamai podpis záhlaví ověřovací klíče a IP adresy, které jsou povolené pro připojení k tomuto koncovému bodu.<br />Tuto vlastnost můžete nastavit při `cdnEnabled` je nastavena na hodnotu false.
- `cdnEnabled` -Určuje, zda je integrace Azure CDN pro tento koncový bod streamování povolená (zakázané ve výchozím nastavení). Pokud nastavíte `cdnEnabled` na hodnotu true, následující konfigurace zakázán: `customHostNames` a `accessControl`.
  
    Ne všechna datová centra nepodporují integraci s Azure CDN. Pokud chcete zkontrolovat, jestli vaše datové centrum má k dispozici integrace Azure CDN, postupujte takto:
 
  - Při pokusu o nastavení `cdnEnabled` na hodnotu true.
  - Podívat se na vrácený výsledek pro `HTTP Error Code 412` (PreconditionFailed) se zprávou "Vlastnost CdnEnabled koncový bod streamování nelze nastavit na hodnotu true, protože možnost CDN není v aktuální oblasti k dispozici." 

    Pokud se zobrazí tato chyba, datového centra se nepodporuje. Měli byste se pokusit jiného datového centra.
- `cdnProfile` – Když `cdnEnabled` je nastavena na hodnotu true, můžete také předat `cdnProfile` hodnoty. `cdnProfile` je název profilu CDN, kde budou vytvářeny koncového bodu CDN. Můžete zadat existující cdnProfile nebo použít nový. Pokud je hodnota NULL a `cdnEnabled` je true, výchozí hodnota používaná "AzureMediaStreamingPlatformCdnProfile". Pokud zadaný `cdnProfile` již existuje, je vytvořen koncový bod je pod ním. Pokud profil, který neexistuje, se vytvoří automaticky nový profil.
- `cdnProvider` – Když je povolené CDN, můžete také předat `cdnProvider` hodnoty. `cdnProvider` ovládací prvky, které poskytovatel se použije. V současné době jsou podporovány tři hodnoty: "StandardVerizon", "PremiumVerizon" a "StandardAkamai". Pokud se nezadá žádná hodnota a `cdnEnabled` má hodnotu true, "StandardVerizon" se používá (tj. výchozí hodnota).
- `crossSiteAccessPolicies` – Umožňuje určit zásady přístupu mezi weby pro různé klienty. Další informace najdete v tématu [specifikace souboru zásady mezi doménami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) a [zpřístupnění služby k dispozici napříč hranicemi domén](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>Nastavení platí jenom pro technologie Smooth Streaming.
- `customHostNames` – Umožňuje nakonfigurovat koncový bod streamování tak, aby přijímal přenosy směrované na vlastním názvem hostitele.  Tato vlastnost je platný pro koncové body streamování Premium a Standard a můžete nastavit, když `cdnEnabled`: false.
    
    Služba Media Services musí potvrdit vlastnictví názvu domény. Služba Media Services ověří vlastnictví názvu domény tak, že vyžaduje `CName` záznam obsahující ID účtu Media Services jako součást přidávaného do domény používá. Jako příklad pro "sports.contoso.com", který se použije jako vlastním názvem hostitele pro koncový bod streamování, záznam pro `<accountId>.contoso.com` musí být nakonfigurované tak, aby odkazoval na jeden z názvů hostitelů ověření Media Services. Ověření názvu hostitele se skládá z verifydns. \<zónu dns mediaservices >. 

    Níže jsou očekávané zóny DNS pro použití v záznamu o ověření pro různé oblasti Azure.
  
  - Severní Amerika, Evropa, Singapur, Hongkong, Japonska:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Čína:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Například `CName` záznam, který mapuje "945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com" k "verifydns.media.azure.net" prokáže, že 945a4c4e-28ea-45cd-8ccb-a519f6b700ad Media Services ID má vlastnictví domény contoso.com, tedy povolení jakýkoli název v rámci contoso.com má být použit jako vlastním názvem hostitele pro koncový bod streamování pod tímto účtem. Hodnota ID služby média, najdete [webu Azure portal](https://portal.azure.com/) a vyberte svůj účet mediálních služeb. **ID účtu** se zobrazí v horní části stránky.
        
    Pokud se pokus o nastavení vlastním názvem hostitele bez řádné ověření `CName` záznam, odpověď DNS se nezdaří a poté uložit do mezipaměti po určitou dobu. Jakmile správný záznam v místě, může nějakou dobu, dokud se ověřit odpověď uložená v mezipaměti. V závislosti na poskytovateli DNS pro vlastní doménu jejich může trvat pár minut až jednu hodinu na znovu ověřit záznam.
        
    Kromě `CName` , která se mapuje `<accountId>.<parent domain>` k `verifydns.<mediaservices-dns-zone>`, musíte vytvořit jiný `CName` , který mapuje název vlastního hostitele (například `sports.contoso.com`) k Media Services koncový bod streamování název hostitele (například `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Umístěn ve stejném datovém centru, koncové body streamování nemůže mít stejný název vlastního hostitele.

    Služba Media Services v současné době nepodporuje SSL s použitím vlastních domén. 
    
- `maxCacheAge` -Přepsání mezipaměti HTTP max-age výchozí ovládací prvek záhlaví nastavil na fragmenty médií na vyžádání manifestů a koncový bod streamování. Hodnota je nastavena v řádu sekund.
- `resourceState` -

    - Zastavený – počáteční stav koncový bod streamování se po vytvoření
    - Spouští se - přechází do stavu spuštěno
    - Spuštění – je možné ke streamování obsahu pro klienty
    - Škálování – škálovací jednotky se zvýší nebo sníží
    - Zastavování - přechází do stavu Zastaveno
    - Probíhá odstranění - odstranění
    
- `scaleUnits ` -Poskytují vyhrazený odchozího přenosu dat kapacity, který lze dokupovat v jednotkách po 200 MB/s. Pokud potřebujete přesunout **Premium** zadejte, upravte `scaleUnits`.

## <a name="next-steps"></a>Další postup

Ukázka [v tomto úložišti](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) ukazuje, jak spustit výchozí koncový bod streamování s .NET.

