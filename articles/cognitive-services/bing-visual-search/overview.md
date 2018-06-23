---
title: Přehled rozhraní API vyhledávání Visual Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak získat podrobnosti nebo přehledy o bitovou kopii například podobné obrázky nebo nákupní zdroje.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 95f10d8ea7ebe1d40d45231a8ea40df81543fe8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343490"
---
# <a name="what-is-bing-visual-search-api"></a>Co je rozhraní API vyhledávání Visual Bing?

Rozhraní API vyhledávání Visual Bing poskytuje podobné zobrazený na Bing.com/images podrobnosti bitové kopie. S Visual vyhledávání můžete nahrát obrázku a získat zpět přehledy o bitovou kopii jako vizuálně podobné bitové kopie, nákupní zdroje, webové stránky, které zahrnují bitovou kopii a další. Místo odesílání bitovou kopii, můžete zadat také token statistiky, kterou můžete získat z image ve výsledcích hledání bitové kopie (viz [API bitové kopie Bingu](../bing-image-search/overview.md)).

Visual vyhledávání můžete identifikovat celebrit, památek a zajímavá, kresby, domácí vybavení, způsobem, produktů, znak rozpoznávání a další.

Níže jsou uvedeny statistiky, že Visual vyhledávání umožňuje zjistit.

- Vizuální podobné obrázky&mdash;seznam bitové kopie, které jsou vizuálně podobná vstupní image
- Vizuální podobné produkty&mdash;seznam bitové kopie, které obsahují produkty, které jsou podobné vizuálně znázorněno na obrázku vstupní produktu
- Shopping zdroje&mdash;seznam míst, kde je možné koupit položky zobrazené v vstupní image
- Související hledání&mdash;seznam související hledání provedené ostatní nebo které jsou na základě obsahu bitové kopie
- Webové stránky, které obsahují image&mdash;seznam webových stránek, které zahrnují vstupní image
- Recepty&mdash;seznam webových stránek, které zahrnují recepty pro provedení misky znázorněno na obrázku vstupní

Kromě těchto Statistika Visual vyhledávání také vrátí hodnotu různého typu podmínky (značky) odvozené ze vstupní image. Tyto značky umožňují uživatelům prozkoumat koncepty najít v bitové kopii. Například pokud je vstupní image famous sportovce, jednu z uvedených značek může být název Atlet, jiné značky může být sportu. Nebo, pokud je vstupní bitovou kopii apple kruhový, značek může být zákusků Apple kruhový, paštiky, proto uživatelé můžete prozkoumat související koncepty.

Výsledky hledání Visual také zahrnovat ohraničujícího polí pro oblasti zájmu v bitové kopii. Například pokud bitová kopie obsahuje několik celebrit, výsledky mohou obsahovat ohraničujícího políčka pro každou rozpoznaný celebrit v bitové kopii. Nebo Bing rozpozná produkt nebo oblečení v bitové kopii, výsledkem mohou být ohraničující pole pro rozpoznaný produkt nebo oblečení položky.

> [!IMPORTANT]
> Pokud použijete/Image/podrobnosti koncový bod, který [přehledné image](../bing-image-search/image-insights.md), by měl aktualizovat kód a místo toho použijte funkci vyhledávání Visual vzhledem k tomu, že poskytuje komplexní přehled.


## <a name="the-request"></a>Požadavek

Následující části jsou uvedené možnosti pro získání přehledy o bitovou kopii. 

- Odeslat token statistiky, kterou můžete získat z bitové kopie v předchozích volání mezi [API bitové kopie Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) koncové body
- Odeslat adresu URL obrázku
- Odeslat bitovou kopii (binární)


Při odesílání Visual vyhledávání tokenu bitovou kopii nebo adresa URL, na obrázku je objekt JSON, který je nutné zahrnout text příspěvku. 

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

`imageInfo` Objekt musí obsahovat buď `url` a `imageInsightsToken` pole, ale ne obojí. Nastavte `url` pole na adresu URL bitové kopie přístupné Internetu. Bitové kopie maximální podporovaná velikost je 1 MB.

`imageInsightsToken` Musí být nastavena na token statistiky. Získat token statistiky, volání rozhraní API bitové kopie Bingu. Odpověď obsahuje seznam `Image` objekty. Každý `Image` objekt obsahuje `imageInsightsToken` pole, které obsahuje tento token.

`cropArea` Pole je nepovinné. Oblast oříznutí určuje horního, levém horním a dolním, oblasti, v pravém rohu. Zadejte hodnoty v rozsahu 0,0 až 1,0. Hodnoty jsou podíl celkové šířky nebo výšky. Například výše uvedeném příkladu označí právo polovinu bitovou kopii jako oblasti, které vás zajímají. Zahrňte jej, pokud chcete omezit požadavek statistiky pro oblast zájmu.

`filters` Objekt obsahuje filtr lokality (najdete v článku `site` pole), můžete omezit na konkrétní domény podobné bitové kopie a podobné produkty výsledky. Například pokud je image Surface Book, můžete nastavit `site` k www.microsoft.com. 

Pokud chcete získat informace o místní kopie image, nahrajte image jako binární data.

Podrobnosti o v těle v příspěvku, včetně těchto možností najdete v tématu [formuláře typů obsahu](#content-form-types).


### <a name="endpoint"></a>Koncový bod

Koncový bod Visual vyhledávání: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Požadavky, musí se poslat jako pouze požadavky HTTP POST. 


### <a name="query-parameters"></a>Parametry dotazu

Níže jsou uvedeny parametry dotazu, který by měl určovat vaši žádost. Minimálně by měla obsahovat `mkt` parametr dotazu.

|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="cc" />kopie|Kód země 2 znak země, odkud pocházejí výsledky.<br /><br /> Pokud nastavíte tento parametr, musíte zadat také [Accept-Language](#acceptlanguage) záhlaví. Bing používá první podporované jazykové vyhledá ze seznamu jazyků a kombinuje jazyk kód země, který určíte k určení trhu vrácení výsledků z. Pokud seznam jazyků nezahrnuje podporovaném jazyce, Bing vyhledá nejbližší jazyk a trhu, který podporuje žádosti. Nebo může použít agregované nebo výchozí trhu pro výsledky místo zadané jeden.<br /><br /> Měli byste použít tento parametr dotazu a `Accept-Language` parametr dotazu, pouze pokud zadáte více jazyků; jinak hodnota, měli byste použít `mkt` a `setLang` parametrů dotazu.<br /><br /> Tento parametr a [mkt](#mkt) parametr dotazu se vzájemně vylučují&mdash;nezadávejte i.|Řetězec|Ne|  
|<a name="mkt" />mkt|Na trhu, odkud pocházejí výsledky. <br /><br /> **Poznámka:** se důrazně doporučujeme, aby vždycky zadat na trhu, pokud je znám. Určení trhu pomáhá Bing směrování požadavku a vrátí odpověď příslušné a optimální.<br /><br /> Tento parametr a [kopie](#cc) parametr dotazu se vzájemně vylučují&mdash;nezadávejte i.|Řetězec|Ano|  
|<a name="safesearch" />bezpečné hledání|Filtr, který se používá k filtrování obsahu pro dospělé. Jsou následující hodnoty filtru možné velká a malá písmena.<br /><ul><li>Vypnout&mdash;vrátit webové stránky s pro dospělé textu nebo obrázků.<br /><br/></li><li>Střední&mdash;vrátit webové stránky s pro dospělé text, ale není pro dospělé bitové kopie.<br /><br/></li><li>Striktní&mdash;nevrátí webové stránky s pro dospělé textu nebo obrázků.</li></ul><br /> Výchozí hodnota je střední.<br /><br /> **Poznámka:** Pokud požadavek pochází z na trhu pro dospělé zásady této Bing vyžaduje, aby `safeSearch` být nastaveno Strict, ignoruje Bing `safeSearch` hodnotu a používá Strict.<br/><br/>**Poznámka:** Pokud použijete `site:` – operátor dotazu, je pravděpodobné, že odpověď může obsahovat obsah pro dospělé bez ohledu na to, co `safeSearch` parametr dotazu je nastavena na. Použití `site:` pouze v případě, že jste si vědomi obsahu v lokalitě a váš scénář podporuje možnost obsah pro dospělé. |Řetězec|Ne|  
|<a name="setlang" />setLang|Jazyk pro použití řetězců uživatelského rozhraní. Zadejte jazyk pomocí kód ISO jazyka písmeno 2 639-1. Kód jazyka pro angličtina je například EN. Výchozí hodnota je EN (angličtina).<br /><br /> I když je volitelné, musíte vždycky zadat jazyk. Obvykle nastavíte `setLang` ve stejném jazyce určeného `mkt` Pokud uživatel chce řetězců uživatelského rozhraní, zobrazí v jiném jazyce.<br /><br /> Tento parametr a [Accept-Language](#acceptlanguage) záhlaví se vzájemně vylučují&mdash;nezadávejte i.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON nejsou několik řetězců uživatelského rozhraní. Všechny odkazy na vlastnosti vyhledávače Bing.com v objektech odpovědi vztahovat určený jazyk.|Řetězec|Ne| 

### <a name="headers"></a>Záhlaví

Následuje seznam hlaviček, které by měl určovat vaši žádost. Hlavičky Content-Type a Ocp-Apim-Subscription-Key jsou pouze požadované hlavičky, ale musí obsahovat také uživatelského agenta, X-MSEdge-ClientID, X-MSEdge-když a umístění X vyhledávání.


|Záhlaví|Popis|  
|------------|-----------------|  
|<a name="acceptlanguage" />Přijměte jazyk|Hlavička požadavku volitelné.<br /><br /> Čárkami oddělený seznam jazyků, které chcete použít pro uživatelské rozhraní řetězce. V seznamu je v sestupném pořadí podle priority. Další informace, včetně očekává se formát, najdete v části [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tuto hlavičku a [setLang](#setlang) parametr dotazu se vzájemně vylučují&mdash;nezadávejte i.<br /><br /> Pokud nastavíte tuto hlavičku, musíte zadat také [kopie](#cc) parametr dotazu. K určení trhu k vracení výsledků pro, používá Bing první podporované jazykové ho najde ze seznamu a její kombinuje `cc` hodnotu parametru. Pokud seznam neobsahuje podporovaném jazyce, Bing vyhledá nejbližší jazyk a trhu, který podporuje požadavku nebo používá agregované nebo výchozí trhu pro dané výsledky. Na trhu, který používá Bing, zjistíte v hlavičce BingAPIs trhu.<br /><br /> Tuto hlavičku používají a `cc` parametr dotazu, pouze pokud zadáte více jazyků. Jinak použijte [mkt](#mkt) a [setLang](#setlang) parametrů dotazu.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON nejsou několik řetězců uživatelského rozhraní. Všechny odkazy na vlastnosti vyhledávače Bing.com v objektech odpovědi použít určený jazyk.|  
|<a name="contenttype" />Content-Type|Hlavička požadované požadavku.<br /><br />Musí být nastavena na multipart/formulář data a obsahují parametr hranic (například multipart/formulář data; hranic =\<hranic řetězec\>). Další podrobnosti najdete v tématu [formuláře typů obsahu](#content-form-types).
|<a name="market" />BingAPIs trhu|Hlavička odpovědi.<br /><br /> Na trhu používané žádosti. Formulář je \<languageCode\>-\<countryCode\>. Například en US.|  
|<a name="traceid" />BingAPIs TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu, který obsahuje podrobnosti o žádosti. Když dojde k chybě, zachytit číslem ID této. Pokud si nejste schopni zjistit a vyřešte problém, zahrnují toto ID spolu s další informace, že zadáte tým podpory.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Hlavička požadované požadavku.<br /><br /> Klíč předplatného, který jste obdrželi při registraci pro tuto službu v [kognitivní služby](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Direktiva pragma|Hlavička volitelné požadavku<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Chcete-li Bing nedošlo k vrácení obsahu v mezipaměti, nastavte na hodnotu no cache hlavičku – direktiva Pragma (například – direktiva Pragma: Ne mezipaměti).
|<a name="useragent" />Uživatelský Agent|Hlavička požadavku volitelné.<br /><br /> Uživatelský agent pocházející žádosti. K poskytování mobilní uživatele optimalizované prostředí používá Bing uživatelský agent. I když je volitelné, vám doporučujeme, aby se vždycky zadat tuto hlavičku.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odešle jakýkoli prohlížeč pro běžně používané. Informace o Uživatelští agenti najdete v tématu [dokumentu RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilní; MSIE 10.0; Windows Phone 8.0; Trident nebo 6.0; IEMobile/10.0; ARM; Dotykové ovládání; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 sestavení nebo PERNÍK) AppleWebKit/533.1 (KHTML; prostředí Gecko, jako) verze nebo 4.0 Mobile Safari nebo 533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Procesor 6_1 iPhone operačního systému jako Mac OS X) AppleWebKit/536.26 (KHTML; prostředí Gecko, jako) mobilní nebo 10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Počítač&mdash;Mozilla/5.0 (systém Windows NT 6.3; SUBSYSTÉM WOW64; Trident/7.0; Dotykové ovládání; Rv:11.0) prostředí Gecko, jako<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 procesoru operačního systému jako Mac OS X) AppleWebKit/537.51.1 (jako jsou prostředí Gecko KHTML) verze 7.0 nebo mobilní nebo 11A465 Safari nebo 9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Volitelné hlavička požadavku a odpovědi.<br /><br /> Bing používá tuto hlavičku zajišťuje uživatelům konzistentní chování napříč volání rozhraní API služby Bing. Bing často letů nové funkce a vylepšení a jako klíč pro přiřazení provoz na jiné letech používá ID klienta. Pokud nepoužijete stejný ID klienta pro uživatele pro víc požadavků, pak Bing může přiřadit uživatele k několika lety konfliktní. Přiřazení k několika lety konfliktní může vést k nekonzistentním uživatelské prostředí. Například pokud má druhý požadavek přiřazení jiné cestě než první, prostředí může neočekávaná. Navíc můžete použít ID klienta pro přizpůsobení webové výsledky pro tohoto klienta Bing ID, která historii hledání, poskytování a pestřejší prostředí pro uživatele.<br /><br /> Tuto hlavičku Bing také používá k vylepšení výsledek hodnocení analýzou aktivitu generovanou podle ID klienta. Vylepšení relevance pomůže s lepší kvality výsledků doručeny podle rozhraní API pro Bing a zase umožňuje vyšší kliknutí sazby pro příjemce rozhraní API.<br /><br /> **Důležité:** i když je volitelné, měli byste zvážit tuto hlavičku vyžaduje. Uložením ID klienta pro víc požadavků pro stejnou koncových uživatelů a zařízení kombinace umožňuje 1) příjemce rozhraní API pro příjem konzistentní podmínky koncového uživatele a sazby 2) vyšší kliknutí prostřednictvím lepší kvalitu výsledky z rozhraní API služby Bing.<br /><br /> Níže jsou uvedeny základní informace o využití pravidla, která se vztahují k této hlavičce.<br /><ul><li>Každý uživatel, který používá vaše aplikace na tomto zařízení musí mít jedinečný, Bing vygeneruje ID klienta.<br /><br/>Pokud neuvedete této hlavičky v požadavku, Bing vygeneruje ID a vrátí ji v záhlaví X-MSEdge-ClientID odpovědi. Jenom jednou, můžete tuto hlavičku obsahovat není v požadavku je prvním uživatel používá aplikaci na daném zařízení.<br /><br/></li><li>**Upozornění:** Ujistěte se, že tohoto ID klienta není možné korelovat k žádné informace o účtu ověřeného uživatele.</li><li>Použijte ID klienta pro každého požadavku rozhraní API služby Bing, které vytváří vaše aplikace pro tohoto uživatele na zařízení.<br /><br/></li><li>Zachovat ID klienta. Udržení ID v aplikaci Prohlížeč, použijte trvalého souboru cookie HTTP k zajištění, že Identifikátor se používá napříč všechny relace. Nepoužívejte souboru cookie relace. Pro jiné aplikace, jako je například mobilní aplikace použijte trvalého úložiště v zařízení k uchování ID.<br /><br/>Při příštím uživatel používá aplikaci na zařízení, získání ID klienta, který jste jako trvalé.</li></ul><br /> **Poznámka:** Bing odpovědí může nebo nemusí obsahovat hlavičku. Pokud odpověď obsahuje hlavičku, zaznamenat ID klienta a použít jej pro všechny následné žádosti Bingu pro uživatele na tomto zařízení.<br /><br /> **Poznámka:** Pokud zahrnete X-MSEdge-ClientID, nesmí obsahovat soubory cookie v požadavku.|  
|<a name="clientip" />X-MSEdge když|Hlavička požadavku volitelné.<br /><br /> IPv4 nebo IPv6 adresa klientského zařízení. IP adresa se používá ke zjišťování umístění uživatele. Bing používá k určení chování bezpečné vyhledávání informace o umístění.<br /><br /> **Poznámka:** i když je volitelné, vám doporučujeme, aby se vždycky zadat umístění X vyhledávání a tento záhlaví.<br /><br /> Není obfuskováním adresu (například změnou poslední oktet 0). Zmatení data adresu výsledky v umístění nebude kdekoli téměř skutečné umístění zařízení, což může vést k Bing obsluhující chybné výsledky.|  
|<a name="location" />Umístění X vyhledávání|Hlavička požadavku volitelné.<br /><br /> Seznam páry klíč/hodnota, které popisují zeměpisné umístění klienta oddělené středníkem. Bing používá informace o umístění, k určení chování bezpečné vyhledávání a relevantní lokálního obsahu. Zadat dvojice klíč/hodnota jako \<klíč\>:\<hodnotu\>. Níže jsou uvedeny klíčů, které se používá k určení umístění uživatele.<br /><br /><ul><li>LAT&mdash;vyžaduje. Zeměpisná šířka umístění klienta ve stupních. Zeměpisnou musí být větší než nebo rovna hodnotě-90.0 a menší než nebo rovna +90.0. Záporné hodnoty stanovují Jižní zeměpisné šířky a kladné hodnoty stanovují severní zeměpisné šířky.<br /><br /></li><li>dlouhé&mdash;vyžaduje. Zeměpisná délka umístění klienta ve stupních. Zeměpisné délky musí být větší než nebo rovna hodnotě-180.0 a menší než nebo rovna +180.0. Záporné hodnoty stanovují western stupně zeměpisné délky a kladné hodnoty stanovují východní délka.<br /><br /></li><li>RE&mdash;vyžaduje. Hodnota úhlu, v měřidla, který určuje vodorovné přesnost souřadnice. Předáte hodnoty vrácené služby umístění zařízení. Typické hodnoty může být 22m pro GPS/Wi-Fi, 380m pro buňky věž měny a 18 000 m pro reverzního vyhledávání IP.<br /><br /></li><li>TS&mdash;volitelné. Časové razítko UTC UNIX když klient se v daném umístění. (UNIX časové razítko je počet sekund od 1. ledna 1970.)<br /><br /></li><li>HEAD&mdash;volitelné. Relativní záhlaví nebo směr cesta klienta. Zadejte směr cesta jako od 0 do 360, počítání po směru hodinových ručiček relativně k – true sever stupňů. Zadejte tento klíč jenom Pokud `sp` klíč je nenulové hodnoty.<br /><br /></li><li>SP&mdash;volitelné. Vodorovné rychlosti (rychlost), v měřidla za sekundu, které při cestě klientské zařízení.<br /><br /></li><li>ALT&mdash;volitelné. Výška klientské zařízení, v měřidla.<br /><br /></li><li>jsou&mdash;volitelné. Hodnota úhlu, v měřidla, která určuje svislé přesnost souřadnice. Zadejte tento klíč jenom v případě, že zadáte `alt` klíč.<br /><br /></li></ul> **Poznámka:** i když řadu klíče jsou volitelné, další informace, které zadáte, jsou více přesné výsledky umístění.<br /><br /> **Poznámka:** i když je volitelné, vám doporučujeme, aby se vždycky zadat zeměpisné umístění uživatele. Poskytnutí umístění je obzvláště důležité, pokud IP adresa klienta přesně neodpovídají fyzické umístění uživatele (například pokud klient používá sítě VPN). Pro dosažení optimálních výsledků by měla obsahovat této hlavičky a hlavičky X-MSEdge – když, ale minimálně by měla obsahovat hlavičku.|

> [!NOTE] 
> Mějte na paměti, že podmínky použití vyžadují kompatibilitu s všechny platné zákony, včetně týkající se použití těchto hlaviček. Například v určité oblasti jurisdikce, jako je například Evropa, existují požadavky na získat souhlas uživatele před uvedením určitých sledování zařízení na zařízení uživatelů.


<a name="content-form-types" />

### <a name="content-form-types"></a>Typy obsahu formuláře

Každý požadavek musí obsahovat hlavičku Content-Type. Záhlaví musí být nastavena na: multipart/formulář data; hranice =\<hranic řetězec\>, kde \<hranic řetězec\> je jedinečný, neprůhledný řetězec, který identifikuje hranice dat formuláře. Například hranic = boundary_1234 abcd.


Při odesílání Visual vyhledávání tokenu bitovou kopii nebo adresa URL, následující zobrazí formulář data, že je nutné zahrnout text příspěvku. Data formuláře musí obsahovat hlavičku Content-Disposition a jeho `name` parametr musí být nastaven na "knowledgeRequest". Podrobnosti o `imageInfo` objektu, najdete v části [požadavku](#the-request).


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

Odešlete obrázek místní následující zobrazí formulář data, že je nutné zahrnout text příspěvku. Data formuláře musí obsahovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr lze nastavit na libovolný řetězec. Hlavička Content-Type může být nastaven na libovolný typ mime běžně používané bitové kopie. Obsah formuláře je binární soubor bitové kopie. Maximální obrázek, který může odeslat je 1 MB. 


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Následující ukazuje, jak určit oblasti, v nahraném bitové kopie.

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

Na obrázku je požadavek Statistika dokončení bitové kopie, který předá tokenu bitové kopie a oblast zájmu. Můžete získat token statistiky z předchozího volání /images/search.


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

Pokud nejsou k dispozici pro bitovou kopii statistiky, odpověď obsahuje jeden nebo více `tags` obsahující k přehledům. `image` Pole obsahuje token statistiky pro vstupní bitovou kopii.

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

`tags` Pole obsahuje zobrazovaný název a seznam akcí (přehled). Obsahuje jednu z uvedených značek `displayName` pole, který je nastavený na prázdný řetězec. Tato značka obsahuje přehled výchozí například webové stránky, které zahrnují bitové kopie, vizuálně podobné bitové kopie a nákupní zdroje pro položek nalezených v bitovou kopii. Protože celého obrázku je zájmu, neobsahuje výchozí značka Statistika ohraničujícího polí oblastí zájmu.


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

Seznam výchozí statistiky najdete v tématu [výchozí insights](./default-insights-tag.md).



Zbývající značky obsahovat další statistiky, které mohou být zajímavé pro uživatele. Například pokud bitová kopie obsahuje text, jednu z uvedených značek mohou obsahovat TextResults vhled, který obsahuje rozpoznaný text. Nebo, pokud Bing rozpozná entity (osoby, místní nebo věcí) v bitové kopii, jednu z uvedených značek identifikují entitu. Visual vyhledávání také vrátí hodnotu různého typu podmínky (značky) odvozené ze vstupní image. Tyto značky umožňují uživatelům prozkoumat koncepty najít v bitové kopii. Například pokud je vstupní image famous sportovce, jednu z uvedených značek může být sportu, který obsahuje odkazy na obrázky sportu.

Jednotlivé značky zahrnuje zobrazovaný název, který lze použít ke kategorizaci vhled, ohraničujícího pole, které identifikují oblasti zájmu, který se vztahuje informace o, Statistika sami a miniaturu obrázku. Například pokud je bitová kopie osoby používání jersey sportu, jednu z uvedených značek mohou zahrnovat ohraničující pole bounds jersey, která obsahuje VisualSearch a ProductVisualSearch statistiky. A jiné značky mohou zahrnovat ImageResults přehledu, který obsahuje adresu URL pro požadavek /images/search rozhraní API získat bitové kopie, které se týkají aplikován nebo adresu URL vyhledávače Bing.com vyhledávání, která přebírá uživateli výsledky hledání vyhledávače Bing.com bitové kopie.

Všechny značky než výchozí značka Statistika zahrnují ohraničujícího polí, které identifikují oblasti zájmu v bitové kopii. Například pokud image obsahuje více rozpoznaný uživatelů, můžou zahrnovat značky ohraničujícího polí pro jednotlivé uživatele nebo pokud bitová kopie obsahuje položky rozpoznaný oblečení, můžou zahrnovat značky ohraničujícího políčka pro každou položku rozpoznaný oblečení. Ohraničující pole můžete použít k vytvoření aktivní body přes bitovou kopii, při kliknutí na, zadejte podrobnosti o obsahu v této oblasti obrázku. Aktivní body by neměla zahrnovat v obraze u ohraničující polí, které identifikují celého obrázku.

### <a name="text-recognition"></a>Rozpoznávání textu

Pokud bitová kopie obsahuje text, který služba rozpozná, jednu z uvedených značek bude obsahovat TextResults přehledy (akce). Přehledu `displayName` obsahuje rozpoznaný text. 

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

Protože značka `displayName` pole obsahuje ##TextRecognition nepoužívejte ho jako kategorii title v UX Že přejde k žádné zobrazení názvů, které začíná ##. Místo toho použijte akce zobrazovaný název.


Rozpoznávání textu můžete také rozpoznat kontaktní informace na kartách firmy, jako je například telefonní čísla a e-mailové adresy. Pole ohraničující identifikuje umístění kontaktní informace na kartě. 

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

Pokud bitová kopie obsahuje rozpoznaný entity, jako je osoba, místní nebo věcí, jednu z uvedených značek může zahrnovat přehledu Entity. Entity může také obsahovat trivia, jak je znázorněno v následujícím příkladu:

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
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "Trivia",
          "data" : {
            "value" : [
              {
                "name" : "Where was the cornerstone of the statue of liberty laid",
                "text" : "<the answer>",
                "hostPageUrl" : "http:\/\/contoso.com\/history\/...",
              },
              {
                "name" : "Why Is the Statue of Liberty Green",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/why-statue-of-liberty-is-green",
              },
              {
                "name" : "What is the Statue of Liberty made of",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/art-literature\/statue-liberty-made",
              }
            ]
          }
        }
      ]
    }
```



## <a name="next-steps"></a>Další postup

Abyste mohli rychle začít s první žádost, najdete v článku elementy QuickStart: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Vyzkoušejte rozhraní API. Přejděte na [Visual vyhledávání API testovací konzolu](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Seznamte se s [Visual referenční dokumentace rozhraní API vyhledávání](https://aka.ms/bingvisualsearchreferencedoc). Odkaz obsahuje seznam koncových bodů, hlavičky a parametry dotazu, které byste použili k žádosti o výsledky hledání. Zahrnuje také definice objektů odpovědi. 

Nezapomeňte si přečíst [požadavky zobrazení a použití Bing](./use-and-display-requirements.md) tak, že nemáte žádné z pravidel o používání výsledky hledání rozdělit.


