---
title: Přehled API pro vizuální vyhledávání Bingu | Dokumentace Microsoftu
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak získat informace nebo informace o obrázku například podobné obrázky nebo nákupní zdroje.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa563d89b1834f5be952f13c31a2451d809709b1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006525"
---
# <a name="what-is-bing-visual-search-api"></a>Co je rozhraní API pro vizuální vyhledávání Bingu?

API pro vizuální vyhledávání Bingu poskytuje funkce podobné zobrazený na Bing.com/images podrobnosti o imagi. Pomocí vizuálního vyhledávání můžete nahrát obrázek a získat přehledy o imagi, jako je například vizuálně podobné obrázky, nákupní zdrojů, webové stránky, které zahrnují image a další. Namísto odesílání bitovou kopii, můžete také zadat token insights, který můžete získat z image ve výsledcích hledání obrázků (viz [rozhraní API Bingu pro image](../bing-image-search/overview.md)).

Pro vizuální vyhledávání můžete identifikovat celebrit, památek a památek, uměleckých, domácí vybavení, způsobem, produkty, rozpoznávání znaků (OCR) a další.

Níže jsou takové informace, že pro vizuální vyhledávání umožňuje zjišťovat.

- Vizuálně podobné obrázky&mdash;seznam imagí, které jsou vizuálně podobné vstupního obrázku
- Vizuálně podobné produkty&mdash;seznam imagí, které obsahují produkty, které jsou vizuálně podobné produktu vstupního obrázku
- Nákupní zdroje&mdash;seznam míst, kde si můžete koupit položky zobrazené v vstupního obrázku
- Související hledání&mdash;seznam související hledání od ostatních nebo které jsou na základě obsahu obrázku
- Webové stránky, které obsahují image&mdash;seznam webové stránky, které zahrnují vstupního obrázku
- Recepty&mdash;seznam webových stránek, které obsahují recepty vznesení pokrmu vstupního obrázku

Kromě těchto přehledů pro vizuální vyhledávání také vrátí různé podmínky (značky) odvozené ze vstupního obrázku. Tato klíčová slova umožňují uživatelům Seznamte se s koncepty, které jsou součástí image. Pokud vstupního obrázku je známý athlete, jedna značka může být název sportovce, jiné značky může být sportu. Nebo vstupního obrázku je apple výsečové, značky by My jsme zvolili zákusky Apple výsečového, Pies, takže uživatelé můžou prozkoumat související koncepty.

Výsledky pro vizuální vyhledávání také zahrnovat ohraničující polí pro oblasti zájmu v bitové kopii. Například pokud image obsahuje několik celebrit, výsledky mohou zahrnovat ohraničující políčka pro každou rozpoznaný celebrit na obrázku. Nebo pokud Bingu rozpozná produktu nebo oblečení v bitové kopii, výsledek může zahrnovat ohraničující rámeček pro rozpoznaný produkt nebo oblečení položku.

> [!IMPORTANT]
> Pokud používáte/imagí/podrobnosti koncový bod, který [získat přehledy obrázků](../bing-image-search/image-insights.md), měli byste aktualizovat kódu pro vizuální vyhledávání místo toho použít, protože nabízí komplexnější Přehled.


## <a name="the-request"></a>Žádost

Následující jsou možnosti získání přehledů o bitovou kopii. 

- Odeslat insights token, který získáte z image v předchozí volání k jednomu z [rozhraní API Bingu pro obrázky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) koncových bodů
- Odeslat adresu URL obrázku
- Nahrání image (binární)


Při odesílání pro vizuální vyhledávání image token nebo adresu URL, následuje objekt JSON, který musí obsahovat v textu příspěvku. 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

`imageInfo` Objektu musí obsahovat buď `url` nebo `imageInsightsToken` pole, ale ne obojí. Nastavte `url` pole Adresa URL obrázku, přístupný na Internetu. Obrázek maximální podporovaná velikost je 1 MB.

`imageInsightsToken` Musí být nastaveno na insights token. Získat token přehledy, volání rozhraní API Bingu pro bitové kopie. Odpověď obsahuje seznam `Image` objekty. Každý `Image` obsahuje objekt `imageInsightsToken` pole, který obsahuje token.

`cropArea` Pole je volitelné. Oblast oříznutí Určuje horní, levého horního a dolního, pravém horním rohu oblasti zájmu. Zadejte hodnoty v rozmezí 0,0 až 1,0. Hodnoty jsou procentní podíl celkové šířky nebo výšky. Například výše uvedený příklad značky vpravo polovinu obrázku jako oblasti zájmu. Pokud chcete omezit insights požadavek na oblasti zájmu, zahrnují ho.

`filters` Objekt obsahuje filtr lokality (najdete v článku `site` pole), můžete použít k omezení podobné obrázky a podobné výsledky produktů ke konkrétní doméně. Například pokud image je Surface Book, můžete nastavit `site` to www.microsoft.com. 

Pokud chcete získat přehled o místní kopie image, tuto image odešlete jako binární data.

Podrobnosti o v text příspěvku, včetně těchto možností najdete v tématu [formuláře typů obsahu](#content-form-types).


### <a name="endpoint"></a>Koncový bod

Je koncový bod vizuálního vyhledávání: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Požadavky musí být odeslána jako pouze požadavky HTTP POST. 


### <a name="query-parameters"></a>Parametry dotazu

Následují parametry dotazu, které by měl určit vaši žádost. Minimálně byste měli zahrnout `mkt` parametr dotazu.

|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="cc" />Kopie|Kód země 2 znacích země, odkud pochází výsledky.<br /><br /> Pokud nastavíte tento parametr, musíte zadat také [Accept-Language](#acceptlanguage) záhlaví. Bing používá první podporované jazykové najde ze seznamu jazyků a spojuje se směrové číslo země, které určíte k určení na trhu a vracení výsledků z jazyka. Pokud seznam jazyků neobsahuje podporovaném jazyce, Bingu vyhledá nejbližší jazyka a, který podporuje žádosti. Nebo může použít agregované nebo výchozí trhu pro výsledky místo zadaného etagu.<br /><br /> Používejte tento parametr dotazu a `Accept-Language` parametr dotazu, pouze pokud zadáte více jazyků; v opačném případě byste měli použít `mkt` a `setLang` parametrů dotazu.<br /><br /> Tento parametr a [mkt](#mkt) parametr dotazu se vzájemně vylučují&mdash;nezadávejte obě.|Řetězec|Ne|  
|<a name="mkt" />mkt|Na trhu, odkud pochází výsledky. <br /><br /> **Poznámka:** vám doporučujeme, aby se vždy zadávat na trhu, pokud je známý. Určení trhu pomáhá Bingu směrování požadavku a vrací odpovídající a optimální odpověď.<br /><br /> Tento parametr a [kopie](#cc) parametr dotazu se vzájemně vylučují&mdash;nezadávejte obě.|Řetězec|Ano|  
|<a name="safesearch" />bezpečné hledání|Filtr, který se používá k filtrování obsahu pro dospělé. Jsou následující hodnoty možné filtrovat velká a malá písmena.<br /><ul><li>Vypnout&mdash;vrátit webových stránek s dospělé text nebo obrázky.<br /><br/></li><li>Střední&mdash;vrátit webových stránek s dospělé text, ale nezletilý imagí.<br /><br/></li><li>Striktní&mdash;nevrátí webových stránek s dospělé text nebo obrázky.</li></ul><br /> Výchozí hodnota je střední.<br /><br /> **Poznámka:** Pokud žádost pochází z na trhu této Bing pro dospělé zásad vyžaduje, aby `safeSearch` nastavit tak, aby Strict, Bing, ignoruje `safeSearch` hodnotu a používá Strict.<br/><br/>**Poznámka:** používáte `site:` – operátor dotazu, je pravděpodobné, že odpovědi může obsahovat obsah pro dospělé bez ohledu na to, co `safeSearch` parametr dotazu je nastaven na. Použití `site:` pouze v případě, že si uvědomujete obsah na webu a váš scénář podporuje možnost obsahu pro dospělé. |Řetězec|Ne|  
|<a name="setlang" />setLang|Jazyk, který se má použít pro uživatelské rozhraní řetězce. Zadejte jazyk pomocí kód ISO 639-1 písmeno 2 jazyka. Kód jazyka angličtina je například cs. Výchozí hodnota je EN (v angličtině).<br /><br /> I když je volitelné, musíte vždycky zadat jazyk. Obvykle nastavena `setLang` ve stejném jazyce určeném `mkt` Pokud chce uživatel řetězců uživatelského rozhraní, které jsou zobrazeny v jiném jazyce.<br /><br /> Tento parametr a [Accept-Language](#acceptlanguage) záhlaví se vzájemně vylučují&mdash;nezadávejte obě.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON je několik řetězců uživatelského rozhraní. Odkazy na vlastnosti Bing.com v objektech odpovědi platí také, zadaný jazyk.|Řetězec|Ne| 

### <a name="headers"></a>Záhlaví

Níže jsou hlavičky, která by měla určit vaši žádost. Záhlaví Content-Type a Ocp-Apim-Subscription-Key jsou pouze požadované záhlaví, ale by měl také obsahovat uživatelského agenta, X-MSEdge ClientID, X-MSEdge ClientIP a X-Search-umístění.


|Záhlaví|Popis|  
|------------|-----------------|  
|<a name="acceptlanguage" />Přijměte jazyka|Hlavička požadavku volitelné.<br /><br /> Čárkami oddělený seznam jazyků pro řetězce uživatelského rozhraní. V seznamu je v sestupném pořadí podle priority. Další informace, včetně očekávaném formátu najdete v části [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tato hlavička a [setLang](#setlang) parametr dotazu se vzájemně vylučují&mdash;nezadávejte obě.<br /><br /> Pokud nastavíte tuto hlavičku, musíte zadat také [kopie](#cc) parametr dotazu. K určení na trhu a vracení výsledků pro Bing používá první podporované jazykové vyhledá v seznamu a sloučí ji `cc` hodnotu parametru. Pokud seznam neobsahuje podporovaném jazyce, Bingu vyhledá nejbližší jazyka a, který podporuje žádosti nebo použije agregované nebo výchozí trhu pro výsledky. Pokud chcete zjistit, který používá Bing, naleznete v tématu BingAPIs trh záhlaví.<br /><br /> Tuto hlavičku používají a `cc` parametr dotazu, pouze pokud zadáte více jazyků. Jinak použijte [mkt](#mkt) a [setLang](#setlang) parametrů dotazu.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON je několik řetězců uživatelského rozhraní. Odkazy na vlastnosti Bing.com v odpovědi objektů použít zadaný jazyk.|  
|<a name="contenttype" />Content-Type|Hlavička požadavku vyžaduje.<br /><br />Musí být nastavena na multipart/formuláře data a zahrnují parametr hranice (například multipart/formulář data; boundary =\<hranice řetězec\>). Další informace najdete v tématu [formuláře typů obsahu](#content-form-types).
|<a name="market" />BingAPIs trh|Hlavička odpovědi.<br /><br /> Na trhu použít v požadavku. Formulář je \<languageCode\>-\<countryCode\>. Například en US.|  
|<a name="traceid" />BingAPIs TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu, který obsahuje podrobné informace o požadavku. Pokud dojde k chybě, zachycení číslem ID této. Pokud nejste schopni zjistit a vyřešte problém, patří toto ID spolu s dalšími informacemi, že zadáte na tým podpory.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Hlavička požadavku vyžaduje.<br /><br /> Klíč předplatného, který jste dostali při registraci pro tuto službu v [služeb Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Direktiva pragma|Volitelné hlavičky<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste zabránili Bing vrátí obsah uložený v mezipaměti, hlavičku – direktiva Pragma nastavit na hodnotu no-cache (například – direktiva Pragma: no-cache).
|<a name="useragent" />Uživatelský Agent|Hlavička požadavku volitelné.<br /><br /> Uživatelský agent původní požadavek. Bing používá uživatelský agent mobilním uživatelům poskytnout prostředí optimalizované. I když je volitelné, jsou ukončena. doporučujeme vždy zadejte toto záhlaví.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odešle všechny běžně používané prohlížeč. Informace o agentech uživatele najdete v tématu [dokumentu RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilní; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Dotykové ovládání; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 sestavení/PERNÍK) AppleWebKit/533.1 (KHTML; prostředí Gecko, jako je) mobilní verze/4.0 Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Operační systém Iphonu 6_1 procesoru jako Mac OS X) AppleWebKit/536.26 (KHTML; prostředí Gecko, jako je) iPhone4 mobilní/10B142; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Počítač&mdash;Mozilla/5.0 (Windows NT 6.3; SUBSYSTÉM WOW64; Trident/7.0; Dotykové ovládání; Rv:11.0) prostředí Gecko, jako jsou<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 procesoru operačního systému jako Mac OS X) AppleWebKit/537.51.1 (jako je prostředí gecko, jež KHTML) verze 7.0 nebo mobilní/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge ClientID|Volitelné záhlaví žádostí a odpovědí.<br /><br /> Bing používá tuto hlavičku uživatelům poskytnout konzistentní chování napříč volání rozhraní API Bingu. Bing často letů nových funkcí a vylepšení a používá ID klienta jako klíč pro přiřazení provoz na jiné lety. Pokud je velmi riskantní používat stejné ID klienta pro uživatele mezi více požadavků, pak Bingu může přiřadit uživatele k více konfliktních lety. Přiřazení na více konfliktních lety může vést k nekonzistentním uživatelské prostředí. Například pokud druhá žádost o přiřazení jiné cestě než první, prostředí může neočekávané. Navíc můžete použít ID klienta pro přizpůsobení webové výsledky pro tohoto klienta Bingu ID historie hledání, poskytuje pohodlnější a pestřejší prostředí pro uživatele.<br /><br /> Bing používá této hlavičky také jí pomohou zlepšit výsledek hodnocení díky analýze aktivitu generovanou podle ID klienta. Zlepšení relevance pomoct s lepší kvalitou výsledků přednesl rozhraní API Bingu a zase umožňuje vyšší proklikávání sazby pro rozhraní API příjemců.<br /><br /> **Důležité:** nepovinný, měli byste zvážit této hlavičky vyžaduje. Zachování ID klienta mezi více požadavků pro stejný koncový uživatel a kombinaci zařízení povolí (1) na rozhraní API příjemce pro příjem konzistentní uživatelské prostředí a (2) vyšší míru proklikávání prostřednictvím kvalitnější výsledky z rozhraní API Bingu.<br /><br /> Tady jsou základní informace o využití pravidel, které se vztahují k této hlavičce.<br /><ul><li>Každý uživatel, který používá vaše aplikace v zařízení musí mít jedinečný, Bing, vygeneruje ID klienta.<br /><br/>Pokud neuvedete této hlavičky v požadavku, Bingu vygeneruje ID a vrátí jej v hlavičce X-MSEdge ClientID odpovědi. Že by měl nezahrnuje této hlavičky v požadavku se pouze první uživatel použije aplikaci na daném zařízení.<br /><br/></li><li>**Pozor:** musíte zajistit, že toto ID klienta není propojovací informací účet ověřeného uživatele.</li><li>Použijte ID klienta pro každé rozhraní API Bingu pro žádosti, která vaše aplikace provádí pro tohoto uživatele na zařízení.<br /><br/></li><li>Zachovat ID klienta. Pokud chcete zachovat ID v aplikaci prohlížeče, ujistěte se, že ID se používá ve všech relacích pomocí trvalého souboru cookie HTTP. Nepoužívejte souboru cookie relace. Pro jiné aplikace, jako jsou například mobilní aplikace použijte k uchování ID. trvalého úložiště zařízení<br /><br/>Při příštím uživatel použije aplikaci na zařízení, získejte ID klienta, který je trvalé.</li></ul><br /> **Poznámka:** odpovědi Bingu může nebo nemusí obsahovat tato záhlaví. Pokud odpověď obsahuje tato záhlaví, ID klienta zachytit a používat ho pro všechny následné požadavky Bing pro uživatele na tomto zařízení.<br /><br /> **Poznámka:** Pokud zahrnete X MSEdge ClientID, nesmí obsahovat soubory cookie v požadavku.|  
|<a name="clientip" />X-MSEdge ClientIP|Hlavička požadavku volitelné.<br /><br /> Adresa IPv4 nebo IPv6 klienta zařízení. IP adresa se používá ke zjišťování podle umístění uživatele. Bing používá k určení chování bezpečného hledání informace o poloze.<br /><br /> **Poznámka:** nepovinný vám doporučujeme, aby se vždy zadejte toto záhlaví a záhlaví X-Search-umístění.<br /><br /> Není obfuskaci adresu (například tak, že změníte poslední oktet na hodnotu 0). Že maskuje výsledky adres v umístění nebude kdekoli v zařízení aktuální umístění, což může vést k Bingu obsluhující chybné výsledky.|  
|<a name="location" />X-Search umístění|Hlavička požadavku volitelné.<br /><br /> Seznam oddělený středníkem páry klíč/hodnota, které popisují zeměpisné umístění klienta. Bing používá informace o poloze a určit chování bezpečného hledání vrátí odpovídající místní obsah. Zadejte pár klíč/hodnota jako \<klíč\>:\<hodnota\>. Níže jsou klíče, které se používá k určení umístění uživatele.<br /><br /><ul><li>LAT&mdash;vyžaduje. Zeměpisná šířka umístění klienta ve stupních. Zeměpisná šířka musí být větší než nebo rovna hodnotě-90.0 a menší než nebo rovna +90.0. Záporné hodnoty značí Jižní zeměpisná šířka a kladné hodnoty značí severní meze.<br /><br /></li><li>dlouhé&mdash;vyžaduje. Zeměpisná délka umístění klienta ve stupních. Zeměpisná délka musí být větší než nebo rovna hodnotě-180.0 a menší než nebo rovna +180.0. Záporné hodnoty značí západní délka a kladné hodnoty značí východní délka.<br /><br /></li><li>RE&mdash;vyžaduje. Poloměr, v metrech, který určuje vodorovný přesnost souřadnic. Předejte hodnotu vrácené službou umístění zařízení. Typické hodnoty mohou být 22m pro GPS/Wi-Fi, 380m triangulace tower buňky a 18 000 m pro reverzního vyhledávání IP adresy.<br /><br /></li><li>TS&mdash;volitelné. Časové razítko UTC UNIX když klient se v umístění. (UNIXOVÉ časové razítko je počet sekund od 1. ledna 1970.)<br /><br /></li><li>hlavní&mdash;volitelné. Relativní záhlaví nebo směr cesty klienta. Určení směru cestovní jako stupních od 0 do 360, počítací po směru hodinových ručiček vzhledem k true – sever. Zadejte tento klíč pouze tehdy, pokud `sp` klíč není nenulová.<br /><br /></li><li>SP&mdash;volitelné. Vodorovné rychlost (rychlost), v metrech za sekundu, cestování klientského zařízení.<br /><br /></li><li>ALT&mdash;volitelné. Výška klientské zařízení, v metrech.<br /><br /></li><li>jsou&mdash;volitelné. Poloměr, v metrech, určující přesnost svislé souřadnice. Zadejte tento klíč jenom v případě, že zadáte `alt` klíč.<br /><br /></li></ul> **Poznámka:** i když mnohé z klíče jsou volitelné, další informace, které zadáte, jsou přesnější výsledky umístění.<br /><br /> **Poznámka:** nepovinný vám doporučujeme, aby se vždy zadat zeměpisné umístění uživatele. Poskytuje umístění je obzvláště důležité, pokud IP adresa klienta neodráží přesně podle fyzického umístění uživatele (například pokud klient používá síť VPN). Pro dosažení optimálních výsledků by měl obsahovat tato záhlaví a záhlaví X-MSEdge ClientIP, ale minimálně, měli byste zahrnout tuto hlavičku.|

> [!NOTE] 
> Mějte na paměti, podmínky použití vyžadovat dodržování předpisů s příslušnými zákony, včetně týkající se použití těchto hlaviček. Například v určitých jurisdikce, jako jsou Evropa, existují požadavky na získat souhlas uživatele před uvedením určitá sledování zařízení na zařízení uživatelů.


<a name="content-form-types" />

### <a name="content-form-types"></a>Typy obsahu formuláře

Každý požadavek musí obsahovat hlavičku Content-Type. Záhlaví musí být nastavena na: multipart /-data formuláře; hranice =\<hranice řetězec\>, kde \<hranice řetězec\> je jedinečný, neprůhledný řetězec, který určuje hranice data formuláře. Například hranice = boundary_1234 abcd.


Při odesílání pro vizuální vyhledávání image token nebo adresu URL, následuje data formuláře, že je třeba zahrnout text příspěvku. Data formuláře musí zahrnovat hlavičku Content-Disposition a jeho `name` parametr musí být nastaven na "knowledgeRequest." Podrobnosti o `imageInfo` objektu, najdete v článku [žádost](#the-request).


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Pokud odešlete místní image, následuje data formuláře musí obsahovat v textu příspěvku. Data formuláře musí zahrnovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr může být nastaven na libovolný řetězec. Hlavička Content-Type může být nastaven na libovolný typ mime běžně používaných image. Obsah formuláře je binární soubor obrázku. Maximální velikost, kterou můžete k nahrání je 1 MB. Největší šířka nebo výška musí být 1 500 pixelů nebo méně.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Následující ukazuje, jak zadat tuto oblast zájmu nahraného obrázku.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Příklad žádosti

Následuje kompletní obrázek insights požadavek, který se předává tokenu image a oblasti zájmu. Získat token přehledy z předchozího volání /images/search.


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="the-response"></a>Odpověď

Pokud nejsou k dispozici pro bitovou kopii přehledy, odpověď obsahuje jeden nebo více `tags` , které obsahují přehledy. `image` Pole obsahuje přehledy token vstupního obrázku.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

`tags` Pole obsahuje zobrazovaný název a seznam akcí (přehled). Jedna značka obsahuje `displayName` pole, který je nastaven na prázdný řetězec. Tato značka obsahuje výchozí přehledy, jako jsou webové stránky, které obsahují image, vizuálně podobné obrázky a nákupního zdroje pro položky v obrázku. Protože celého obrázku je relevantní, neobsahuje výchozí značka insights ohraničující polí pro oblasti zájmu.


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Seznam výchozích insights najdete v tématu [výchozí insights](./default-insights-tag.md).



Zbývající značky obsahují další přehledy, které mohou být zajímavé pro uživatele. Například pokud image obsahuje text, jedna značka může obsahovat TextResults přehled, který obsahuje rozpoznaný text. Nebo, pokud Bingu rozpozná entity (osobě, místě nebo věc, kterou) na obrázku, jedna značka identifikují entitu. Pro vizuální vyhledávání také vrátí různé podmínky (značky) odvozené ze vstupního obrázku. Tato klíčová slova umožňují uživatelům Seznamte se s koncepty, které jsou součástí image. Například pokud vstupního obrázku je známý athlete, jedna značka může být sportu, který obsahuje odkazy na Image sportu.

Každá značka obsahuje zobrazovaný název, který lze použít ke kategorizaci těchto přehledů ohraničující rámeček, který identifikuje oblasti zájmu, která se vztahuje na podrobnější informace o, insights, sami a miniaturu obrázku. Například pokud se image nachází osoby nosit jersey sportu, jedna značka může obsahovat ohraničujícího rámečku, která jersey za rozsahem a zahrnuje VisualSearch a ProductVisualSearch přehledy. A další značky mohou zahrnovat ImageResults přehledů, který obsahuje adresu URL pro požadavek /images/search rozhraní API k získání bitové kopie, které se týkají aplikován nebo Bing.com adresa URL pro hledání, která přebírá uživateli výsledky hledání obrázků Bing.com.

Všechny značky jiné než výchozí značka insights zahrnují ohraničující polí, které identifikují oblasti zájmu v bitové kopii. Například pokud image obsahuje více rozpoznaný uživatelů, může obsahovat značky ohraničující políčka pro každou osob, nebo pokud image obsahuje oblečení rozpoznaných položek, může obsahovat značky ohraničující políčka pro každou položku rozpoznaný oblečení. Ohraničující polí můžete použít k vytváření hotspotů přes obrázek, který po kliknutí na, obsahují podrobné informace o obsahu v dané oblasti obrázku. V obrázku pro ohraničující pole, které identifikují celého obrázku by neměla zahrnovat aktivní body.

### <a name="text-recognition"></a>Rozpoznávání textu

Pokud image obsahuje text, který služba rozpozná, jedna značka bude obsahovat přehled TextResults (akce). Požadovaný přehled `displayName` obsahuje rozpoznaný text. 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Protože značka `displayName` pole obsahuje ##TextRecognition, nepoužívejte ho jako název kategorie v uživatelské prostředí Přejde pro jakékoli zobrazovaný název, který začíná ##. Místo toho použijte zobrazovaný název akce.


Rozpoznávání textu můžete také rozpoznává kontaktní informace na kartách business, jako jsou telefonní čísla a e-mailové adresy. Ohraničujícího rámečku polohu kontaktní údaje najdete na kartě. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Pokud image obsahuje rozpoznaný entitu, jako je osobě, místě nebo věc, jedna značka může obsahovat přehled Entity. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```



## <a name="next-steps"></a>Další postup

Abyste mohli rychle začít s vaší první žádost, najdete v článku postupů rychlý start: [jazyka C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Vyzkoušejte si rozhraní API. Přejděte na [konzole testování rozhraní API pro vizuální vyhledávání](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Seznamte se s [vizuální Reference k rozhraní API hledání](https://aka.ms/bingvisualsearchreferencedoc). Tyto referenční informace obsahují seznam koncových bodů, hlaviček a parametrů dotazů, které můžete použít při odesílání požadavků na výsledky hledání. Obsahují také definice objektů odpovědi. 

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./use-and-display-requirements.md), abyste neporušili žádná pravidla používání výsledků hledání.


