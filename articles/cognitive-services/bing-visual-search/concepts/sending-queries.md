---
title: Odesílání vyhledávacích dotazů na rozhraní API pro vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje parametry a atributy požadavků odeslaných do rozhraní API pro vizuální vyhledávání Bingu a také objekt Response.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 37d9352b6384ee2b5e95903f35d531bd672b25b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490971"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Odesílání vyhledávacích dotazů na rozhraní API pro vizuální vyhledávání Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Tento článek popisuje parametry a atributy požadavků odeslaných do rozhraní API pro vizuální vyhledávání Bingu a také objekt Response. 

Přehledy o imagi můžete získat třemi způsoby:

- Použití tokenu Insights, který dostanete z obrázku v předchozím volání do jednoho z [rozhraní API Bingu pro vyhledávání obrázkůch](/rest/api/cognitiveservices/bing-images-api-v7-reference) koncových bodů.
- Odesílá se adresa URL obrázku.
- Nahrává se obrázek (v binárním formátu).

## <a name="bing-visual-search-requests"></a>Vizuální vyhledávání Bingu žádosti

Pokud odešlete Vizuální vyhledávání tokenu obrázku nebo adresu URL, následující fragment kódu zobrazí objekt JSON, který musíte zahrnout do těla příspěvku:

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

Objekt `imageInfo` musí obsahovat buď pole `url`, nebo pole `imageInsightsToken`, ale ne obě. Nastavte `url` pole na adresu URL image přístupné z Internetu. Maximální podporovaná velikost obrázku je 1 MB.

`imageInsightsToken` musí být nastaveno na token přehledů. Token přehledů získáte voláním rozhraní API Obrázků Bingu. Odpověď obsahuje seznam objektů `Image`. Každý objekt `Image` obsahuje pole `imageInsightsToken`, které obsahuje token.

Pole `cropArea` je nepovinné. Oblast oříznutí určuje levý horní roh a pravý dolní roh oblasti zájmu. Hodnoty zadejte v rozmezí 0,0 až 1,0. Hodnoty jsou procentním podílem celkové šířky nebo výšky. Například výše uvedený příklad určuje jako oblast zájmu pravou polovinu obrázku. Přidejte ho, pokud chcete požadavek přehledů omezit jenom na oblast zájmu.

Objekt `filters` obsahuje filtr webů (viz pole `site`), pomocí kterého můžete omezit výsledky podobných obrázků a podobných produktů na konkrétní doménu. Například pokud je obrázek v Surface, můžete nastavit `site` na `www.microsoft.com` .

Pokud chcete získat přehledy o místní kopii obrázku, nahrajte obrázek jako binární data.

Podrobnosti o zahrnutí těchto možností do těla POST najdete v [Typech formulářů obsahu](#content-form-types).

### <a name="search-endpoint"></a>Hledat koncový bod

Koncový bod vizuálního vyhledávání je: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Požadavky se musí zasílat jenom jako požadavky HTTP POST.

### <a name="query-parameters"></a>Parametry dotazů

Následují parametry dotazu, které váš požadavek musí obsahovat. Měli byste aspoň zadat `mkt` parametr dotazu:

| Name | Hodnota | Typ | Vyžadováno |
| --- | --- | --- | --- |
| <a name="cc"></a>cc  | Kód země se dvěma znaky, který představuje místo, odkud pocházejí výsledky.<br /><br /> Pokud tento parametr nastavíte, musíte zadat také hlavičku [Accept-Language](#acceptlanguage). Bing použije první podporovaný jazyk, který najde v seznamu jazyků, a zkombinuje ho se zadaným kódem země, aby určil trh, ze kterého má vrátit výsledky. Pokud seznam jazyků podporovaný jazyk neobsahuje, najde Bing nejbližší jazyk a trh, který požadavek podporuje. Nebo může místo zadaného trhu pro výsledky použít trh agregovaný nebo výchozí.<br /><br /> Tento parametr dotazu a parametr dotazu `Accept-Language` byste měli použít jenom v případě, že zadáte více jazyků. Jinak byste měli použít parametry dotazu `mkt` a `setLang`.<br /><br /> Tento parametr a parametr dotazu [mkt](#mkt) se vzájemně vylučují &mdash; nezadávejte oba. | Řetězec | No       |
| <a name="mkt"></a>mkt   | Trh, odkud pochází výsledky. <br /><br /> **Poznámka:** Měli byste vždycky zadat trh, pokud je známý. Určení trhu pomáhá Bingu směrovat požadavek a vrátit odpovídající a optimální odpověď.<br /><br /> Tento parametr a parametr dotazu [cc](#cc) se vzájemně vylučují &mdash; nezadávejte oba. | Řetězec | Yes      |
| <a name="safesearch"></a>safeSearch | Filtr pro obsah určený pro dospělé Následují možné hodnoty filtru s rozlišováním velkých a malých písmen.<br /><ul><li>Off (Vypnuto) &mdash; vrátit webové stránky s textem nebo obrázky pro dospělé.<br /><br/></li><li>Moderate (Střední) &mdash; vrátit webové stránky s textem pro dospělé, ale ne s obrázky pro dospělé.<br /><br/></li><li>Strict (Přísné) &mdash; nevracet webové stránky s textem nebo obrázky pro dospělé.</li></ul><br /> Výchozí hodnota je Moderate.<br /><br /> **POZNÁMKA:** Pokud požadavek pochází z trhu, kde zásady Bingu pro obsah pro dospělé vyžadují, aby hodnota `safeSearch` byl nastavená na Strict, Bing hodnotu `safeSearch` ignoruje a použije hodnotu Strict.<br/><br/>**Poznámka:** Při použití `site:` operátoru dotazu může odpověď obsahovat obsah pro dospělé bez ohledu na to, jaký `safeSearch` parametr dotazu je nastaven na hodnotu. `site:` použijte jenom v případě, že znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.  | Řetězec | No       |
| <a name="setlang"></a>setLang  | Jazyk, který se má použít pro řetězce uživatelského rozhraní. Určete jazyk pomocí kódu jazyka dvou písmen ISO 639-1. Například kód jazyka pro češtinu je CS. Výchozí hodnota je EN (angličtina).<br /><br /> I když je jazyk volitelný, měli byste ho vždy zadat. Obvykle se `setLang` nastavuje na stejný jazyk, jaký určuje parametr `mkt`, pokud uživatel nechce řetězce uživatelského rozhraní zobrazené v jiném jazyce.<br /><br /> Tento parametr a hlavička [Accept-Language](#acceptlanguage) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí také všechny odkazy na vlastnosti Bing.com v objektech odpovědi. | Řetězec | No   |

## <a name="headers"></a>Hlavičky

Následují hlavičky dotazu, které váš požadavek musí obsahovat. `Content-Type`Záhlaví a `Ocp-Apim-Subscription-Key` jsou jediná požadovaná záhlaví, ale měli byste také zahrnout,, `User-Agent` `X-MSEdge-ClientID` `X-MSEdge-ClientIP` a `X-Search-Location` .

| Hlavička | Description |
| --- | --- |
| <a name="acceptlanguage"></a>Accept-Language  | Nepovinná hlavička požadavku.<br /><br /> Čárkami oddělený seznam jazyků pro řetězce uživatelského rozhraní. Seznam je v sestupném pořadí podle priority. Další informace včetně očekávaného formátu najdete v [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tato hlavička a parametr dotazu [setLang](#setlang) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Pokud tuto hlavičku nastavíte, musíte zadat také parametr dotazu [cc](#cc). K určení trhu, pro který se mají vracet výsledky, Bing použije první podporovaný jazyk, který najde v seznamu, a zkombinuje ho s hodnotou parametru `cc`. Pokud seznam jazyků podporovaný jazyk neobsahuje, Bing najde nejbližší jazyk a trh, který požadavek podporuje, nebo pro výsledky použije agregovaný nebo výchozí trh. Chcete-li zjistit, jaký trh Bing používal, přečtěte si `BingAPIs-Market` hlavičku.<br /><br /> Tuto hlavičku a parametr dotazu `cc` použijte jenom v případě, že zadáte více jazyků. Jinak použijte parametry dotazu [mkt](#mkt) a [setLang](#setlang).<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí všechny odkazy na vlastnosti Bing.com v objektech odpovědi.  |
| <a name="contenttype"></a>Content-Type  |     |
| <a name="market"></a>BingAPIs-Market    | Hlavička odpovědi.<br /><br /> Trh používaný požadavkem. Formulář je \<languageCode\> - \<countryCode\> . Například cs-CZ.  |
| <a name="traceid"></a>BingAPIs-TraceId  | Hlavička odpovědi.<br /><br /> ID položky protokolu obsahující podrobnosti požadavku. Pokud dojde k chybě, toto ID zachyťte. Pokud problém nedokážete určit a vyřešit, uveďte toto ID spolu s dalšími informacemi, které poskytnete týmu podpory. |
| <a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key | Povinná hlavička požadavku.<br /><br /> Klíč předplatného, který jste dostali při registraci k této službě v [Cognitive Services](https://www.microsoft.com/cognitive-services/). |
| <a name="pragma"></a>Pragma |   |
| <a name="useragent"></a>User-Agent  | Nepovinná hlavička požadavku.<br /><br /> Uživatelský agent, ze kterého požadavek pochází. Bing používá uživatelského agenta k poskytnutí optimalizovaného prostředí pro mobilní uživatele. I když je tato hlavička nepovinná, doporučujeme ji vždy zadat.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odesílá kterýkoli běžně používaný prohlížeč. Informace o uživatelských agentech najdete v [dokumentu RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid"></a>X-MSEdge-ClientID  | Nepovinná hlavička požadavku a odpovědi<br /><br /> Bing tuto hlavičku používá k tomu, aby uživatelům poskytoval konzistentní chování napříč voláními rozhraní API Bingu. Bing často testuje nové funkce a vylepšení a ID klienta používá jako klíč pro přiřazení provozu v různých testovacích verzích. Pokud nepoužíváte stejné ID klienta pro uživatele napříč více požadavky, pak může Bing uživatele přiřadit k více konfliktním testovacím verzím. Přiřazení k více konfliktním testovacím verzím může vést k nekonzistentnímu prostředí pro uživatele. Pokud třeba druhý požadavek má jiné přiřazení k testovací verzi než prví, může být prostředí neočekávané. Bing také může ID klienta použít pro přizpůsobení webových výsledků historii hledání daného ID klienta. Uživatel tak získá bohatší prostředí.<br /><br /> Bing také tuto hlavičku používá ke zlepšování hodnocení výsledků díky analýze aktivity generované tímto ID klienta. Zlepšení relevance pomáhá zlepšit kvalitu výsledků poskytovaných rozhraním API Bingu a stejně tak umožňuje vyšší míru prokliků uživatele rozhraní API.<br /><br /> **DŮLEŽITÉ:** I když je tato hlavička nepovinná, měli byste ji považovat za povinnou. Zachování ID klienta mezi více požadavky pro stejnou kombinaci koncového uživatele a zařízení umožňuje 1) aby uživatel rozhraní API získával konzistentní uživatelské prostředí a 2) vyšší míry prokliků díky kvalitnějším výsledkům z rozhraní API Bingu.<br /><br /> Následují základní pravidla používání, která se na tuto hlavičku vztahují.<br /><ul><li>Každý uživatel, který používá vaši aplikaci na zařízení, musí mít jedinečné ID klienta vygenerované Bingem.<br /><br/>Pokud tuto hlavičku do požadavku nezahrnete, Bing vygeneruje ID a vrátí ho v hlavičce odpovědi X-MSEdge ClientID. Tuto hlavičku byste v požadavku zahrnout NEMĚLI jenom v případě, kdy uživatel aplikaci na zařízení používá poprvé.<br /><br/></li><li>**POZOR:** Musíte zajistit, aby toto ID klienta nebylo spojitelné s jakýmikoli informacemi účtu ověřeného uživatele.</li><li>Použijte ID klienta pro každý požadavek rozhraní API Bingu, který vaše aplikace provede pro tohoto uživatele na zařízení.<br /><br/></li><li>ID klienta zachovejte. Pokud chcete zachovat ID v aplikaci prohlížeče, použijte trvalý soubor cookie HTTP, aby se zaručilo, že se ID použije ve všech relacích. Nepoužívejte soubor cookie relace. Pro jiné aplikace, jako jsou mobilní aplikace, použijte k zachování ID trvalé úložiště zařízení.<br /><br/>Když uživatel aplikaci na zařízení příště použije, získejte ID klienta, které jste zachovali.</li></ul><br /> **POZNÁMKA:** Odpovědi Bingu tuto hlavičku mohou nebo nemusí obsahovat. Pokud odpověď tuto hlavičku obsahuje, ID klienta zachyťte a použijte pro všechny následné požadavky Bingu pro uživatele na tomto zařízení.<br /><br /> **POZNÁMKA:** Pokud zahrnete X-MSEdge-ClientID, nesmíte do požadavku zahrnout soubory cookie. |
| <a name="clientip"></a>X-MSEdge-ClientIP   | Nepovinná hlavička požadavku.<br /><br /> Adresa IPv4 nebo IPv6 klientského zařízení. IP adresa se používá ke zjištění polohy uživatele. Bing informace o poloze používá k určení chování bezpečného hledání.<br /><br /> **POZNÁMKA:** I když je tato hlavička nepovinná, doporučujeme ji vždy zadat, stejně jako hlavičku X-Search-Location.<br /><br /> Neprovádějte obfuskaci adresy (například změnou posledního oktetu na 0). Obfuskace adresy vede k tomu, že poloha nebude blízko skutečné polohy zařízení. Bing pak může dodávat chybné výsledky. |
| <a name="location"></a>X-Search-Location   | Nepovinná hlavička požadavku.<br /><br /> Středníky oddělený seznam párů klíč/hodnota, které popisují zeměpisnou polohu klienta. Bing informace o poloze používá k určení chování bezpečného hledání a vracení relevantního místního obsahu. Zadejte dvojici klíč/hodnota jako \<key\> : \<value\> . Následují klíče, které se používají k určení polohy uživatele.<br /><br /><ul><li>lat &mdash; Povinné. Zeměpisná šířka polohy klienta ve stupních. Zeměpisná šířka musí být větší nebo rovná -90,0 a menší nebo rovná +90,0. Záporné hodnoty značí jižní šířku a kladné hodnoty značí severní šířku.<br /><br /></li><li>long &mdash; Povinné. Zeměpisná délka polohy klienta ve stupních. Zeměpisná délka musí být větší nebo rovná -180,0 a menší nebo rovná +180,0. Záporné hodnoty značí západní délku a kladné hodnoty značí východní délku.<br /><br /></li><li>re &mdash; Povinné. Poloměr v metrech, který určuje vodorovnou přesnost souřadnic. Předejte hodnotu vrácenou službou zjišťování polohy zařízení. Typické hodnoty mohou být 22 m pro GPS/Wi-Fi, 380 m pro buněčné věž na konci a 18 000 m pro zpětné vyhledávání IP adres.<br /><br /></li><li>ts &mdash; Nepovinné. Časové razítko UTC UNIX, kdy se klient na daném místě nacházel. (Časové razítko UNIX je počet sekund od 1. ledna 1970.)<br /><br /></li><li>head &mdash; Nepovinné. Relativní směr pohybu klienta. Zadejte směr pohybu ve stupních od 0 do 360 ve směru hodinových ručiček vzhledem k severu. Tento klíč zadejte jenom tehdy, když je klíč `sp` nenulový.<br /><br /></li><li>sp &mdash; Nepovinné. Vodorovná rychlost v metrech za sekundu, kterou se klientské zařízení pohybuje.<br /><br /></li><li>alt &mdash; Nepovinné. Nadmořská výška klientského zařízení v metrech.<br /><br /></li><li>are &mdash; Nepovinné. Poloměr v metrech, který určuje svislou přesnost souřadnic. Tento klíč zadejte jenom tehdy, když zadáte klíč `alt`.<br /><br /></li></ul> **POZNÁMKA:** I když jsou mnohé klíče nepovinné, čím více informací zadáte, tím přesnější budou výsledky určení polohy.<br /><br /> **POZNÁMKA:** I když je geografická poloha uživatele nepovinná, doporučujeme ji vždy zadat. Poskytnutí polohy je zvlášť důležité, pokud IP adresa klienta přesně neodráží fyzickou polohu uživatele (třeba pokud klient používá síť VPN). Pro dosažení optimálních výsledků byste měli zahrnout tuto hlavičku a `X-MSEdge-ClientIP` hlavičku, ale minimálně byste měli zahrnout tuto hlavičku.       |

> [!NOTE]
> Pamatujte, že [požadavky vyhledávání Bingu na použití a zobrazení rozhraní API](../../bing-web-search/use-display-requirements.md) vyžadují dodržování předpisů se všemi platnými zákony, včetně použití těchto hlaviček. Například v určitých jurisdikcích, třeba v Evropě, se před umístěním určitých sledovacích zařízení do zařízení uživatelů požaduje získání souhlasu uživatele.

<a name="content-form-types"></a>

### <a name="content-form-types"></a>Typy formulářů obsahu

Každý požadavek musí obsahovat `Content-Type` hlavičku. Záhlaví musí být nastavené na: `multipart/form-data; boundary=\<boundary string\>` , kde \<boundary string\> je jedinečný a neprůhledný řetězec, který určuje hranici dat formuláře. Například, `boundary=boundary_1234-abcd`.

Pokud odešlete Vizuální vyhledávání tokenu obrázku nebo adresu URL, následující fragment kódu zobrazí data formuláře, která musíte zahrnout do těla příspěvku. Data formuláře musí obsahovat `Content-Disposition` hlavičku a je nutné nastavit její `name` parametr na hodnotu "knowledgeRequest". Podrobnosti o `imageInfo` objektu naleznete v žádosti.

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

Volitelně můžete nastavit `enableEntityData` atribut v záhlaví na `true` pro podrobné informace o hlavní entitě v imagi, kterou nahráváte, včetně odkazů na web a informace o přidělení. Toto pole je `false` ve výchozím nastavení.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

Pokud nahrajete místní obrázek, následující fragment kódu zobrazuje data formuláře, která musíte zahrnout do těla příspěvku. Data formuláře musí obsahovat `Content-Disposition` hlavičku. Jeho parametr `name` musí být nastavený na "image" a parametr `filename` může být nastavený na libovolný řetězec. `Content-Type`Záhlaví může být nastaveno na libovolný běžně používaný obrázek typu MIME. Obsahem formuláře jsou binární data obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB. Největší šířka nebo výška musí být 1 500 pixelů nebo méně.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Následující fragment kódu ukazuje, jak určit oblast zájmu nahrané Image:

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

### <a name="example-request"></a>Příklad požadavku

Následující fragment kódu ukazuje kompletní požadavek na Image Insights, který projde tokenem obrázku a oblastí zájmu. Získáte token Insights z předchozího volání/images/Search:

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

## <a name="bing-visual-search-responses"></a>Vizuální vyhledávání Bingu odezvy


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

Pokud jsou k dispozici přehledy o obrázku, odpověď obsahuje jeden nebo více `tags`, které obsahují přehledy. `image`Pole obsahuje token Insights pro vstupní Image:

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

Pole `tags` obsahuje zobrazovaný název a seznam akcí (přehledy). Jedna značka obsahuje pole `displayName`, které je nastavené na prázdný řetězec. Tato značka obsahuje výchozí přehledy, jako jsou webové stránky, které obrázek obsahují, vizuálně podobné obrázky a nákupního zdroje pro položky na obrázku. Vzhledem k tomu, že celý obrázek je zajímavou, výchozí značka Insights neobsahuje ohraničovací rámečky pro oblasti zájmu:

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

Seznam výchozích přehledů najdete v tématu [výchozí značka Insights](../default-insights-tag.md).

Zbývající značky obsahují další přehledy, které můžou uživatele zajímat. Pokud třeba obrázek obsahuje text, může jedna značka obsahovat přehled TextResults, který obsahuje rozpoznaný text. Nebo pokud Bing rozpoznává entitu (to znamená, že v obrázku je to kultura, na které se nachází, místo nebo věc), jedna z značek může entitu identifikovat. Vizuální vyhledávání vrátí také rozmanitou sadu termínů (značek) odvozených od zadaného obrázku. Tyto značky umožňují uživatelům prozkoumat koncepty, které najdete v imagi. Pokud je například na zadaném obrázku známý sportovec, může být jedna značka Sport, která obsahuje odkazy na obrázky sportu.

Každá značka obsahuje zobrazovaný název, který můžete použít ke kategorizaci přehledů, ohraničující rámeček identifikující oblast zájmu, které se přehled týká, samotné přehledy a miniaturu obrázku. Pokud je třeba na obrázku osoba ve sportovním dresu, může jedna značka obsahovat ohraničující rámeček, který ohraničuje dres a obsahuje přehledy VisualSearch (vizuální vyhledávání) a ProductVisualSearch (vizuální vyhledávání produktů). Další značka pak může zahrnovat přehled ImageResults (výsledky obrázků), který obsahuje adresu URL pro požadavek rozhraní API /images/search k získání obrázků, které tematicky souvisejí, nebo adresu URL vyhledávání Bing.com, která uživatele přenese na výsledky hledání obrázků Bing.com.

Všechny značky kromě výchozí značky přehledů zahrnují ohraničující rámečky, které identifikují oblasti zájmu na obrázku. Pokud třeba obrázek obsahuje více rozpoznaných osob, můžou značky obsahovat ohraničující rámečky pro každou osobu. Nebo pokud obrázek obsahuje rozpoznané kusy oblečení, můžou značky obsahovat ohraničující rámečky pro každý rozpoznaný kus oblečení. Pomocí ohraničujících rámečků můžete vytvořit aktivní body na obrázku, které při kliknutí poskytnou podrobnosti o obsahu v příslušné oblasti obrázku. Na obrázku byste neměli vyznačovat aktivní body pro ohraničující rámečky, které identifikují celý obrázek.

### <a name="text-recognition"></a>Rozpoznávání textu

Pokud obrázek obsahuje text, který služba rozpozná, jedna ze značek bude obsahovat přehled (akci) TextResults. Přehled `displayName` obsahuje rozpoznaný text:

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

Protože pole `displayName` značky obsahuje ##TextRecognition, nepoužívejte ho jako název kategorie v uživatelském prostředí. To platí pro všechny zobrazované názvy, které začínají na ##. Místo toho použijte zobrazované jméno akce.

Rozpoznávání textu může také rozpoznat kontaktní údaje na vizitkách, třeba telefonní čísla a e-mailové adresy. Ohraničující rámeček identifikuje umístění kontaktních údajů na vizitce.

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

Pokud image obsahuje rozpoznanou entitu, například kulturní známou nebo oblíbenou osobu, místo nebo věc, může jedna z značek zahrnovat přehled entit. `mainEntity`Pole a `data` jsou k dispozici pouze v případě, že `enableEntityData` atribut v `Content-Type` hlavičce je nastaven na hodnotu `true` .

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
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>Viz také

- [Co je rozhraní API pro vizuální vyhledávání Bingu?](../overview.md)
- [Kurz: Vytvoření webové aplikace Vizuální vyhledávání jednostránkového stránkování](../tutorial-bing-visual-search-single-page-app.md)