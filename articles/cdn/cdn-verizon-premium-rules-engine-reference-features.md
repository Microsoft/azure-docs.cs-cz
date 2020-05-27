---
title: Azure CDN z funkcí modulu pravidel Premium Verizon | Microsoft Docs
description: Referenční dokumentace pro Azure CDN z funkcí modulu pravidel Premium Verizon
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: d2d4090934a940809fe75ad70e0650eb1c9353f1
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872720"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN z funkcí modulu pravidel Premium Verizon

V tomto článku jsou uvedené podrobné popisy dostupných funkcí pro [modul pravidel](cdn-verizon-premium-rules-engine.md)služby Azure Content DELIVERY Network (CDN).

Třetí část pravidla je funkce. Funkce definuje typ akce, která je použita na typ požadavku, který je identifikován sadou podmínek shody.


Nejnovější funkce najdete v [dokumentaci k modulům Verizon Rules](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).


## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Referenční informace o funkcích stroje Verizon Premium Rules Azure CDN

---

### <a name="age-response-header"></a>Hlavička doby odezvy

**Účel**: Určuje, zda je v odpovědi odeslané do žadatele zahrnutá hlavička věkové odpovědi.

Hodnota|Výsledek
--|--
Povoleno | Odpověď, která je odeslána žadateli, obsahuje hlavičku stáří.
Zakázáno | Hlavička věkové odpovědi je vyloučena z odpovědi odeslané žadateli.

**Výchozí chování**: zakázáno.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Parametry šířky pásma

**Účel:** Určuje, zda jsou aktivní parametry omezení šířky pásma (například ec_rate a ec_prebuf).

Parametry omezení šířky pásma určují, jestli je rychlost přenosu dat pro požadavek klienta omezená na vlastní sazbu.

Hodnota|Výsledek
--|--
Povoleno|Umožňuje bodům POP akceptovat požadavky na omezení šířky pásma.
Zakázáno|Způsobí, že body POP ignorují parametry omezení šířky pásma. Požadovaný obsah se zpracovává normálně (tj. bez omezení šířky pásma).

**Výchozí chování:** Umožněn.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Omezení šířky pásma

**Účel:** Omezuje šířku pásma pro odpověď poskytovanou body POP.

Aby bylo možné správně nastavit omezení šířky pásma, musí být definována obě z následujících možností.

Možnost|Popis
--|--
Kilobajtů za sekundu|Nastavte tuto možnost na maximální šířku pásma (KB/s), která se dá použít k doručení odpovědi.
Prebuf sekund|Tuto možnost nastavte na počet sekund, po které mají body POP čekat, než se omezí šířka pásma. Účelem tohoto časového období neomezené šířky pásma je zabránit přehrávači médií v stuttering nebo ukládání do vyrovnávací paměti z důvodu omezení šířky pásma.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Vynechat mezipaměť

**Účel:** Určuje, zda by požadavek měl obejít ukládání do mezipaměti.

Hodnota|Výsledek
--|--
Povoleno|Způsobí, že všechny žádosti přecházejí na zdrojový server, a to i v případě, že obsah byl dříve uložen do mezipaměti v bodu POP.
Zakázáno|Způsobí, že body POP budou ukládat prostředky do mezipaměti podle zásad mezipaměti definovaných v hlavičkách odpovědi.

**Výchozí chování:**

- **Velké http:** Zabezpečen

<!---
- **ADN:** Enabled

--->

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Metody HTTP, které je možné ukládat do mezipaměti

**Účel:** Určuje sadu dalších metod HTTP, které lze uložit do mezipaměti v síti.

Informace o klíči:

- Tato funkce předpokládá, že odpovědi GET by měly být vždy uloženy do mezipaměti. V důsledku toho by během nastavování této funkce neměla být zahrnutá metoda GET HTTP.
- Tato funkce podporuje pouze metodu POST HTTP. Nastavením této funkce na můžete povolit ukládání do mezipaměti po odeslání odpovědi `POST` .
- Ve výchozím nastavení jsou uloženy do mezipaměti pouze požadavky, jejichž tělo je menší než 14 KB. Chcete-li nastavit maximální velikost textu požadavku, použijte funkci velikost textu požadavků do mezipaměti.

**Výchozí chování:** Pouze odpovědi jsou ukládány do mezipaměti.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Velikost textu žádosti v mezipaměti

**Účel:** Definuje prahovou hodnotu pro určení, zda lze odpověď POST ukládat do mezipaměti.

Tato prahová hodnota je určena zadáním maximální velikosti textu požadavku. Žádosti, které obsahují větší text žádosti, nejsou ukládány do mezipaměti.

Informace o klíči:

- Tato funkce je platná pouze v případě, že odpovědi na POST mají nárok na ukládání do mezipaměti. Pro povolení ukládání požadavků POST do mezipaměti použijte funkci metod HTTP s mezipamětí.
- Text žádosti se vezme v úvahu pro:
    - hodnoty x-www-form-urlencoded
    - Zajištění jedinečného klíče pro ukládání do mezipaměti
- Definování vysoké maximální velikosti textu požadavku může mít vliv na výkon při doručování dat.
    - **Doporučená hodnota:** 14 KB
    - **Minimální hodnota:** 1 KB

**Výchozí chování:** 14 KB

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Zpracování hlaviček Cache-Control

**Účel:** Řídí generování `Cache-Control` hlaviček pomocí ovládacího prvku pop, pokud je aktivní externí funkce Max-stáří.

Nejjednodušším způsobem, jak dosáhnout tohoto typu konfigurace, je umístit v rámci stejného příkazu funkce pro zpracování hlaviček v rámci maximálního stáří a funkce zpracování hlaviček Cache-Control.

Hodnota|Výsledek
--|--
Přepsat|Zajistí, že dojde k následujícím akcím:<br/> – Přepíše `Cache-Control` hlavičku vygenerovanou počátečním serverem. <br/>– Přidá `Cache-Control` hlavičku vytvořenou externí funkcí maximálního stáří pro odpověď.
Předávací|Zajistí, aby se `Cache-Control` záhlaví vytvořená externí funkcí pro maximální stáří nikdy nepřidalo do odpovědi. <br/> Pokud zdrojový server vytvoří `Cache-Control` hlavičku, projde koncovým uživatelem. <br/> Pokud zdrojový Server nevytváří `Cache-Control` hlavičku, může tato možnost způsobit, že hlavička odpovědi nebude obsahovat `Cache-Control` hlavičku.
Přidat, pokud chybí|Pokud se `Cache-Control` ze zdrojového serveru nepřijala hlavička, tato možnost přidá `Cache-Control` hlavičku vytvořenou externí funkcí pro maximální stáří. Tato možnost je užitečná pro zajištění, že všechny assety mají přiřazenou `Cache-Control` hlavičku.
Odebrat| Tato možnost zajistí, že `Cache-Control` Hlavička není zahrnuta v odpovědi hlavičky. Pokud byla `Cache-Control` Hlavička již přiřazena, je odebrána z odpovědi záhlaví.

**Výchozí chování:** Přepsat.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Řetězec dotazu na klíč v mezipaměti

**Účel:** Určuje, jestli mezipaměť-Key zahrnuje nebo vylučuje parametry řetězce dotazu přidružené k žádosti.

Informace o klíči:

- Zadejte jeden nebo více názvů parametrů řetězce dotazu a jednotlivé názvy parametrů oddělte na jedno místo.
- Tato funkce určuje, zda jsou parametry řetězce dotazu zahrnuty nebo vyloučeny z klíčového klíče cache. Další informace jsou k dispozici pro každou možnost v následující tabulce.

Typ|Popis
--|--
 Zařadit členy|  Označuje, že každý zadaný parametr by měl být zahrnut do meziklíče mezipaměti. Pro každý požadavek, který obsahuje jedinečnou hodnotu parametru řetězce dotazu definovaného v této funkci, je vygenerován jedinečný klíč mezipaměti.
 Zahrnout vše  |Indikuje, že pro každý požadavek na prostředek, který obsahuje jedinečný řetězec dotazu, se vytvoří jedinečný klíč mezipaměti. Tento typ konfigurace se obvykle nedoporučuje, protože může vést k malému procentu přístupů do mezipaměti. Nízký počet přístupů do mezipaměti zvyšuje zatížení na zdrojovém serveru, protože musí obsluhovat více požadavků. Tato konfigurace duplikuje chování ukládání do mezipaměti označované jako "jedinečná mezipaměť" na stránce ukládání řetězců dotazu do mezipaměti.
 Exclude | Označuje, že z klíčového klíče cache jsou vyloučeny pouze zadané parametry. Všechny ostatní parametry řetězce dotazu jsou zahrnuty v mezipaměti – klíč.
 Vyloučit vše  |Označuje, že všechny parametry řetězce dotazu jsou vyloučené z klíčového klíče cache. Tato konfigurace duplikuje výchozí chování ukládání do mezipaměti řetězce dotazu do mezipaměti.  

Modul pravidel umožňuje přizpůsobit způsob, jakým je implementováno ukládání řetězců dotazů do mezipaměti. Můžete například určit, že ukládání řetězců dotazu do mezipaměti je provedeno pouze v určitých umístěních nebo typech souborů.

Pokud chcete duplikovat chování při ukládání řetězce dotazu "no-cache" na stránku ukládání řetězců do mezipaměti, vytvořte pravidlo, které obsahuje podmínku shody se zástupnými znaky dotazu URL a funkci vynechat mezipaměť. Nastavte podmínku shody zástupného znaku dotazu URL na hvězdičku (*).

>[!IMPORTANT]
> Pokud je povolena autorizace tokenu pro jakoukoli cestu k tomuto účtu, je režim standardní mezipaměti jediným režimem, který lze použít pro ukládání řetězců dotazu do mezipaměti. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Následující ukázkové použití této funkce poskytuje ukázkový požadavek a výchozí klíč mezipaměti:

- **Vzorový požadavek:** http://wpc.0001.&lt ;D omain &gt; /800001/Origin/Folder/Asset.htm? SessionID = 1234&Language = EN&UserID = 01
- **Výchozí mezipaměť-klíč:** /800001/Origin/Folder/Asset.htm

##### <a name="include"></a>Zařadit členy

Ukázková konfigurace:

- **Zadejte:** Připojit
- **Parametr (y):** jazyk

Tento typ konfigurace vygeneruje následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Zahrnout vše

Ukázková konfigurace:

- **Zadejte:** Zahrnout vše

Tento typ konfigurace vygeneruje následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Ukázková konfigurace:

- **Zadejte:** Slevy
- **Parametr (y):** ID uživatele relace

Tento typ konfigurace vygeneruje následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Vyloučit vše

Ukázková konfigurace:

- **Zadejte:** Vyloučit vše

Tento typ konfigurace vygeneruje následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Přepsání klíče do mezipaměti

**Účel:** Přepíše klíč mezipaměti přidružený k žádosti.

Klíč mezipaměti je relativní cesta, která identifikuje prostředek pro účely ukládání do mezipaměti. Jinými slovy, servery kontrolují verzi assetu v mezipaměti podle jeho cesty, jak je definováno jeho meziklíčovou mezipamětí.

Tuto funkci nakonfigurujte definováním obou z následujících možností:

Možnost|Popis
--|--
Původní cesta| Zadejte relativní cestu k typům požadavků, jejichž klíč mezipaměti je přepsaný. Relativní cestu lze definovat výběrem základní cesty počátku a následným definováním vzoru regulárního výrazu.
Nová cesta|Zadejte relativní cestu pro nový klíč mezipaměti. Relativní cestu lze definovat výběrem základní cesty počátku a následným definováním vzoru regulárního výrazu. Tato relativní cesta se dá dynamicky sestavit pomocí [proměnných http](cdn-http-variables.md).

**Výchozí chování:** Klíč mezipaměti požadavku je určený identifikátorem URI žádosti.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Komentář

**Účel:** Umožňuje přidat poznámku v rámci pravidla.

Jedním z těchto funkcí je poskytnout další informace o obecném účelu pravidla nebo o tom, proč se do pravidla přidal konkrétní podmínka shody nebo funkce.

Informace o klíči:

- Může být zadáno maximálně 150 znaků.
- Použijte pouze alfanumerické znaky.
- Tato funkce nemá vliv na chování pravidla. Slouží pouze k poskytnutí oblasti, kde můžete poskytnout informace pro budoucí referenci nebo které mohou při řešení potíží s pravidlem pomáhat.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Vyplnit celou mezipaměť

**Účel:** Určuje, co se stane, když požadavek způsobí chybějící částečnou mezipaměť na bodu POP.

Částečný chybějící mezipaměť popisuje stav mezipaměti pro Asset, který nebyl zcela stažen do bodu POP. Pokud je prostředek v místní nabídce POP jenom částečně uložený v mezipaměti, pak se další požadavek na tento prostředek znovu pošle na zdrojový server.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
K částečnému neúspěšnému výskytu mezipaměti dochází, když uživatel přeruší stahování nebo pro prostředky, které jsou výhradně požadovány pomocí požadavků na rozsah HTTP. Tato funkce je nejužitečnější pro velké prostředky, které se obvykle nestahují od začátku do konce (například videa). V důsledku toho je tato funkce ve výchozím nastavení povolená na velké platformě HTTP. Je zakázaná na všech ostatních platformách.

Ponechte výchozí konfiguraci pro velkou platformu HTTP, protože snižuje zatížení vašeho zákaznického serveru a zvyšuje rychlost, s jakou vaši zákazníci stahují obsah.

Hodnota|Výsledek
--|--
Povoleno|Obnoví výchozí chování. Výchozím chováním je vynutit, aby POP zahájil načítání assetu ze zdrojového serveru. V takovém případě bude Asset v místní mezipaměti POP.
Zakázáno|Zabrání v tom, aby se v rámci assetu provádělo načítání na pozadí. Výsledkem je, že další požadavek na daný Asset z této oblasti způsobí, že se bod POP vyžádá od serveru zákazníka.

**Výchozí chování:** Umožněn.

#### <a name="compatibility"></a>Kompatibilita

Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, nelze tuto funkci přidružit k následujícím podmínkám shody:

- JAKO číslo
- Client IP Address
- Parametr souboru cookie
- Regulární parametr pro soubor cookie
- Země
- Zařízení
- Microsoft Edge CNAME
- Odkazující doména
- Literál hlavičky požadavku
- Regulární výraz pro záhlaví požadavku
- Zástupný znak hlavičky žádosti
- Request – metoda
- Schéma žádosti
- Literál dotazu URL
- Regulární výraz pro dotaz na adresu URL
- Zástupný znak URL pro dotaz
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Komprimovat typy souborů

**Účel:** Definuje formáty souborů pro soubory, které jsou komprimovány na serveru.

Formát souboru lze zadat pomocí jeho typu internetového média (například Content-Type). Typ internetového média je nezávislá na platformě, která umožňuje serverům identifikovat formát souboru konkrétního prostředku. Níže je uveden seznam běžných typů internetových médií.

Typ internetového média|Popis
--|--
Text/prostý|Soubory prostého textu
text/html| Soubory HTML
text/css|Šablony stylů CSS
Application/x-JavaScript|JavaScript
aplikace/JavaScript|JavaScript

Informace o klíči:

- Určete více typů internetových médií tak, že každý z nich omezíte na jednu mezeru.
- Tato funkce komprimuje pouze prostředky, jejichž velikost je menší než 1 MB. Větší prostředky nejsou komprimovány servery.
- Určité typy obsahu, jako jsou obrázky, videa a zvukové mediální prostředky (například JPG, MP3, MP4 atd.), jsou již komprimovány. Vzhledem k tomu, že další komprese u těchto typů prostředků podstatně nezmenšuje velikost souboru, doporučujeme, abyste na nich nepovolili kompresi.
- Zástupné znaky, jako jsou hvězdičky, se nepodporují.
- Před přidáním této funkce k pravidlu se ujistěte, že jste na kompresní stránce nastavili možnost komprese vypnutá pro platformu, na kterou se toto pravidlo použije.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Vlastní pole protokolu 1

**Účel:** Určuje formát a obsah, který bude přiřazen k poli vlastního protokolu v nezpracovaném souboru protokolu.

Toto vlastní pole vám umožní určit, které hodnoty hlaviček požadavků a odpovědí se mají ukládat do souborů protokolu.

Ve výchozím nastavení se vlastní pole protokolu nazývá "x-ec_custom-1." Název tohoto pole je možné přizpůsobit na stránce nezpracovaná nastavení protokolu.

Formát pro zadání hlaviček Request a Response je definován následujícím způsobem:

Typ záhlaví|Formát|Příklady
-|-|-
Hlavička požadavku|`%{[RequestHeader]()}[i]()` | % {Accept – Encoding} <br/> {Odkazující} <br/> % {Authorization}
Hlavička odpovědi|`%{[ResponseHeader]()}[o]()`| % {Age} o <br/> % {Content-Type} o <br/> % {Cookie} o

Informace o klíči:

- Vlastní pole protokolu může obsahovat libovolnou kombinaci polí záhlaví a prostého textu.
- Platné znaky tohoto pole jsou následující: alfanumerické (0-9, a-z, a A-Z), pomlčky, dvojtečky, středníky, apostrofy, čárky, tečky, podtržítka, podtržítka, závorky, závorky a mezery. Symbol procenta a složené závorky jsou povoleny pouze v případě, že se používají k určení pole hlavičky.
- Pravopis každého zadaného pole hlavičky musí odpovídat názvu požadované hlavičky žádosti a odpovědi.
- Pokud chcete zadat více hlaviček, použijte oddělovač k označení jednotlivých záhlaví. Můžete například použít zkratku pro každou hlavičku:
    - AE:% {Accept-Encoding} i:% {Authorization} i CT:% {Content-Type} o

**Výchozí hodnota:** -

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Hlavičky odpovědí mezipaměti ladění

**Účel:** Určuje, jestli může odpověď zahrnovat [hlavičky odpovědí X-EC-Debug](cdn-http-debug-headers.md), které poskytují informace o zásadách mezipaměti pro požadovaný prostředek.

Hlavičky odpovědi mezipaměti ladění budou zahrnuty do odpovědi, pokud jsou splněny obě následující podmínky:

- V zadaném požadavku byla povolena funkce hlaviček odpovědí mezipaměti ladění.
- Zadaný požadavek definuje sadu hlaviček odpovědí mezipaměti pro ladění, které budou zahrnuty do odpovědi.

Hlavičky odpovědí mezipaměti ladění mohou být požadovány vložením následující hlavičky a zadaných direktiv v žádosti:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Případě**

X-ES-Debug: x-EC-cache, x-ES-check-Cached, x-EC-cache-Key, x-ES-cache-State

Hodnota|Výsledek
-|-
Povoleno|Požadavky na hlavičky odpovědí mezipaměti ladění vrátí odpověď, která obsahuje hlavičku X-EC-Debug.
Zakázáno|Hlavička odpovědi X-EC-Debug se z odpovědi vyloučí.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Výchozí interní maximální stáří

**Účel:** Určuje výchozí interval maximálního stáří pro revalidovou mezipaměť serveru POP na původní server. Jinými slovy, množství času, které se předá za POP, zkontroluje, jestli se Asset uložený v mezipaměti shoduje s Assetem uloženým na zdrojovém serveru.

Informace o klíči:

- Tato akce bude probíhat pouze pro odpovědi ze zdrojového serveru, který nepřiřadil údaje o maximálním stáří v `Cache-Control` `Expires` hlavičce nebo.
- Tato akce nebude provedena u prostředků, které nejsou považovány za mezipaměť.
- Tato akce nemá vliv na revalidy mezipaměti POP v prohlížeči. Tyto typy replatných jsou určeny `Cache-Control` `Expires` hlavičkou nebo odeslanými do prohlížeče, které lze přizpůsobit pomocí externí funkce pro maximální stáří.
- Výsledky této akce nemají pozorovatelný účinek na hlavičky odpovědí a obsah vrácený z bodů POP pro váš obsah, ale může to mít vliv na množství replatných přenosů odeslaných z bodů POP na zdrojový server.
- Tuto funkci konfigurujete pomocí:
    - Výběr stavového kódu, pro který lze použít výchozí interní maximální stáří.
    - Zadáním celočíselné hodnoty a následným výběrem požadované časové jednotky (například sekund, minut, hodin atd.). Tato hodnota definuje výchozí interní interval pro maximální stáří.

- Když se časová jednotka nastaví na off, přiřadí se výchozí interní interval max. 7 dní pro žádosti, u kterých se nepřiřadilo označení maximálního stáří v `Cache-Control` hlavičce nebo `Expires` .

**Výchozí hodnota:** 7 dní

#### <a name="compatibility"></a>Kompatibilita

Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, nelze tuto funkci přidružit k následujícím podmínkám shody:
- JAKO číslo
- Client IP Address
- Parametr souboru cookie
- Regulární parametr pro soubor cookie
- Země
- Zařízení
- Záznam CNAME okraje
- Odkazující doména
- Literál hlavičky požadavku
- Regulární výraz pro záhlaví požadavku
- Zástupný znak hlavičky žádosti
- Request – metoda
- Schéma žádosti
- Literál dotazu URL
- Regulární výraz pro dotaz na adresu URL
- Zástupný znak URL pro dotaz
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Odepřít přístup (403)

**Účel**: Určuje, jestli se všechny požadavky zamítly pomocí odpovědi 403 zakázané.

Hodnota | Výsledek
------|-------
Povoleno| Způsobí odmítnutí všech požadavků, které splňují kritéria pro porovnání, s 403 zakázanou odpovědí.
Zakázáno| Obnoví výchozí chování. Výchozím chováním je, aby zdrojový server mohl určit typ odpovědi, která bude vrácena.

**Výchozí chování**: zakázáno

> [!TIP]
   > Jedním z možných použití této funkce je jejich přidružení se podmínkou shody hlaviček požadavku, která zablokuje přístup k předaným objektům HTTP, které používají vložené odkazy na váš obsah.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Konec platnosti hlaviček

**Účel:** Řídí generování `Expires` záhlaví pomocí ovládacího prvku pop, pokud je aktivní externí funkce Max-stáří.

Nejjednodušším způsobem, jak dosáhnout tohoto typu konfigurace, je umístit do stejného příkazu funkce pro zpracování hlaviček v rámci maximálního stáří a vyprší platnost.

Hodnota|Výsledek
--|--
Přepsat|Zajistí, že budou provedeny následující akce:<br/>– Přepíše `Expires` hlavičku vygenerovanou počátečním serverem.<br/>– Přidá `Expires` hlavičku vytvořenou externí funkcí maximálního stáří pro odpověď.
Předávací|Zajistí, aby se `Expires` záhlaví vytvořená externí funkcí pro maximální stáří nikdy nepřidalo do odpovědi. <br/> Pokud zdrojový server vytvoří `Expires` hlavičku, předává se koncovému uživateli. <br/>Pokud zdrojový Server nevytváří `Expires` hlavičku, může tato možnost způsobit, že hlavička odpovědi nebude obsahovat `Expires` hlavičku.
Přidat, pokud chybí| Pokud se `Expires` ze zdrojového serveru nepřijala hlavička, tato možnost přidá `Expires` hlavičku vytvořenou externí funkcí pro maximální stáří. Tato možnost je užitečná pro zajištění, že se všem assetům přiřadí `Expires` záhlaví.
Odebrat| Zajistí, že `Expires` Hlavička není zahrnuta v hlavičce odpovědi. Pokud byla `Expires` Hlavička již přiřazena, je odebrána z odpovědi záhlaví.

**Výchozí chování:** Přepsat

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Externí maximum – stáří

**Účel:** Určuje interval maximálního stáří pro revalidování mezipaměti POP v prohlížeči. Jinými slovy, doba, po kterou bude prohlížeč moci vyhledat novou verzi assetu z POP.

Povolení této funkce bude generovat `Cache-Control: max-age` a `Expires` hlavičky z bodů POP a pošle je klientovi HTTP. Ve výchozím nastavení budou tato záhlaví přepsat tato záhlaví vytvořená zdrojovým serverem. Zpracování hlaviček Cache-Control a funkce pro zpracování hlaviček platnosti se ale dají použít ke změně tohoto chování.

Informace o klíči:

- Tato akce nemá vliv na opětovné ověření v mezipaměti serveru POP na původní server. Tyto typy replatných jsou určeny `Cache-Control` `Expires` hlavičkou a přijatými ze zdrojového serveru a lze je přizpůsobit pomocí výchozích vnitřních funkcí maximálního stáří a vynutit interní funkce maximálního stáří.
- Tuto funkci můžete nakonfigurovat zadáním celočíselné hodnoty a výběrem požadované časové jednotky (například sekundy, minuty, hodiny atd.).
- Nastavení této funkce na zápornou hodnotu způsobí, že body POP odesílají `Cache-Control: no-cache` a `Expires` čas, který je v minulosti nastaven s každou odpovědí do prohlížeče. I když klient protokolu HTTP nebude odpověď ukládat do mezipaměti, nebude mít toto nastavení vliv na schopnost bodů POP ukládat odpověď ze zdrojového serveru do mezipaměti.
- Když se časová jednotka nastaví na off, tato funkce se zakáže. `Cache-Control`Hlavičky a se `Expires` uloží do mezipaměti s odpovědí zdrojového serveru do prohlížeče.

**Výchozí chování:** Zaokrouhl

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Sledovat přesměrování

**Účel:** Určuje, zda mohou být požadavky přesměrovány na název hostitele definovaný v hlavičce umístění, kterou vrátí server původu zákazníka.

Informace o klíči:

- Žádosti se dají přesměrovat jenom na hraniční záznamy CNAME, které odpovídají stejné platformě.

Hodnota|Výsledek
-|-
Povoleno|Žádosti je možné přesměrovat.
Zakázáno|Žádosti nebudou přesměrovány.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Vynutit interní maximum – stáří

**Účel:** Určuje interval maximálního stáří pro revalidu mezipaměti serveru POP na původní server. Jinými slovy, doba, která se předá za POP, může ověřit, jestli se Asset uložený v mezipaměti shoduje s Assetem uloženým na zdrojovém serveru.

Informace o klíči:

- Tato funkce přepíše interval maximálního stáří definovaný v `Cache-Control` nebo `Expires` hlavičkách vygenerovaných ze zdrojového serveru.
- Tato funkce nemá vliv na revalidy mezipaměti POP v prohlížeči. Tyto typy replatných jsou určeny `Cache-Control` `Expires` hlavičkou nebo odeslanými do prohlížeče.
- Tato funkce nemá pozorovatelný účinek na odpověď doručenou prostřednictvím bodu POP žadateli. Může to ale mít vliv na množství replatných přenosů odeslaných z bodů POP na zdrojový server.
- Tuto funkci konfigurujete pomocí:
    - Výběr stavového kódu, pro který bude použito interní maximum – stáří.
    - Zadání celočíselné hodnoty a výběr požadované časové jednotky (například sekund, minut, hodin atd.). Tato hodnota definuje interval maximálního stáří žádosti.

- Když se nastaví Časová jednotka na off, tato funkce se zakáže. K požadovaným prostředkům se nepřiřazuje interní interval Max-stáří. Pokud původní hlavička neobsahuje pokyny k ukládání do mezipaměti, bude Asset uložen v mezipaměti podle aktivního nastavení ve výchozí interní funkci pro maximální stáří.

**Výchozí chování:** Zaokrouhl

#### <a name="compatibility"></a>Kompatibilita

Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, nelze tuto funkci přidružit k následujícím podmínkám shody:
- JAKO číslo
- Client IP Address
- Parametr souboru cookie
- Regulární parametr pro soubor cookie
- Země
- Zařízení
- Záznam CNAME okraje
- Odkazující doména
- Literál hlavičky požadavku
- Regulární výraz pro záhlaví požadavku
- Zástupný znak hlavičky žádosti
- Request – metoda
- Schéma žádosti
- Literál dotazu URL
- Regulární výraz pro dotaz na adresu URL
- Zástupný znak URL pro dotaz
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H. 264 podpora (progresivní stahování HTTP)

**Účel:** Určuje typy formátů souborů H. 264, které mohou být použity pro streamování obsahu.

Informace o klíči:

- V možnosti přípony souborů Definujte sadu povolených hodnot s čárkou oddělenou příponou názvů souborů H. 264. Možnost přípony souborů přepíše výchozí chování. Při nastavení této možnosti Udržujte podporu souborů MP4 a F4V zahrnutím těchto přípon názvů.
- Pokud určíte jednotlivé přípony názvů souborů (například _. mp4_, _. F4V_), zahrňte tečku.

**Výchozí chování:** Progresivní stahování HTTP podporuje ve výchozím nastavení médium MP4 a F4V.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Akceptovat požadavek bez mezipaměti

**Účel:** Určuje, zda budou do zdrojového serveru předány požadavky na ukládání do mezipaměti klienta HTTP.

V případě, že klient HTTP odešle `Cache-Control: no-cache` hlavičku a/nebo `Pragma: no-cache` v požadavku HTTP, dojde k žádosti bez mezipaměti.

Hodnota|Výsledek
--|--
Povoleno|Umožňuje přeposílání požadavků na neukládání do mezipaměti klienta HTTP na zdrojový server a zdrojový server vrátí hlavičky odpovědi a tělo prostřednictvím serveru POP zpátky do klienta HTTP.
Zakázáno|Obnoví výchozí chování. Výchozím chováním je zabránit přeposílání požadavků na neukládání do mezipaměti na zdrojový server.

U všech produkčních přenosů se důrazně doporučuje ponechat tuto funkci ve svém výchozím zakázaném stavu. Jinak se zdrojové servery nebudou chránit před koncovými uživateli, kteří můžou nechtěně aktivovat spoustu požadavků bez mezipaměti při obnovení webových stránek nebo z mnoha oblíbených přehrávačů médií, které jsou kódované tak, aby odesílali hlavičku bez mezipaměti s každou žádostí o video. Tato funkce ale může být užitečná pro použití v některých neprodukčních nebo testovacích adresářích, aby bylo možné na vyžádání začít s čerstvým obsahem ze zdrojového serveru.

Stav mezipaměti, který se oznamuje pro požadavek, který se dá přeslat na zdrojový server, protože tato funkce je `TCP_Client_Refresh_Miss` . Sestava stavů mezipaměti, která je k dispozici v základním modulu vytváření sestav, poskytuje statistické informace podle stavu mezipaměti. Tato sestava umožňuje sledovat počet a procento požadavků, které jsou předávány na zdrojovém serveru z důvodu této funkce.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignorovat původ – mezipaměť

**Účel:** Určuje, jestli CDN bude ignorovat následující direktivy poskytované ze zdrojového serveru:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informace o klíči:

- Tuto funkci nakonfigurujte tak, že definujete seznam stavových kódů oddělených mezerami, pro které se výše uvedené direktivy budou ignorovat.
- Sada platných stavových kódů pro tuto funkci: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, 505,,, a.
- Tuto funkci zakažte tak, že ji nastavíte na prázdnou hodnotu.

**Výchozí chování:** Výchozím chováním je dodržovat výše uvedené direktivy.

#### <a name="compatibility"></a>Kompatibilita

Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, nelze tuto funkci přidružit k následujícím podmínkám shody:
- JAKO číslo
- Client IP Address
- Parametr souboru cookie
- Regulární parametr pro soubor cookie
- Země
- Zařízení
- Záznam CNAME okraje
- Odkazující doména
- Literál hlavičky požadavku
- Regulární výraz pro záhlaví požadavku
- Zástupný znak hlavičky žádosti
- Request – metoda
- Schéma žádosti
- Literál dotazu URL
- Regulární výraz pro dotaz na adresu URL
- Zástupný znak URL pro dotaz
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignorovat nevyhovující rozsahy

**Účel:** Určuje odpověď, která bude vrácena klientům, pokud požadavek vygeneruje 416 požadovaný rozsah nevyhovující stavový kód.

Ve výchozím nastavení se tento stavový kód vrátí, když zadaný požadavek v bajtovém rozsahu nemůže být splněný pomocí bodu POP a polem s hlavičkou požadavku if-Range nebylo zadáno.

Hodnota|Výsledek
-|-
Povoleno|Zabraňuje bodům POP v reakci na neplatnou žádost o bajtovém rozsahu s 416 požadovaným rozsahem, který nesplňuje stavový kód. Místo toho budou servery doručovat požadovaný prostředek a vrátí 200 OK klientovi.
Zakázáno|Obnoví výchozí chování. Výchozím chováním je respektování 416 požadovaného rozsahu, který nesplňuje stavový kód.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Interní Max – zastaralé

**Účel:** Určuje, jak dlouho po normální době vypršení platnosti může být prostředek uložený v mezipaměti obsluhován z bodu POP, pokud se POP nemůže znovu ověřit prostředek uložený v mezipaměti se zdrojovým serverem.

V případě, že čas vypršení platnosti prostředku vyprší, server POP odešle požadavek na revalid na zdrojový server. Zdrojový server pak odpoví buď 304. nezměněno, aby v případě, že má místní zapůjčení na prostředku uloženého v mezipaměti, nebo jinak s 200 OK, aby byl bod POP s aktualizovanou verzí prostředku uložený v mezipaměti.

Pokud se POP nedaří navázat spojení se zdrojovým serverem při pokusu o provedení takového opětovného ověření, pak tato interní funkce s maximálním zastaralým prvkem určuje, jestli a jak dlouho může bod POP dál obsluhovat zastaralý Asset.

Všimněte si, že tento časový interval se spustí, když vyprší platnost maximálního stáří assetu, ne v případě, že dojde k selhání replatného. Proto maximální doba, během které může být Asset obsluhován bez úspěšného replatného, je množství času určené kombinací maximálního stáří plus Max-zastaralé. Pokud by například byl prostředek uložen do mezipaměti v 9:00 s maximálním stářím 30 minut a max. 15 minut, pak pokus o revalidy, který se nezdařil v 9:44, by měl za následek, že by se při neúspěšném pokusu o opětovné ověření při 9:46 mohl koncovému uživateli přijmout časový limit služby 504.

Všechny hodnoty nakonfigurované pro tuto funkci jsou nahrazené `Cache-Control: must-revalidate` nebo `Cache-Control: proxy-revalidate` záhlavími přijatými ze zdrojového serveru. Pokud se některá z těchto hlaviček obdrží ze zdrojového serveru, když se prostředek poprvé ukládá do mezipaměti, nepoužije se v něm zastaralý prostředek uložený v mezipaměti. V takovém případě, pokud se POP nemůže znovu ověřit s počátkem, když vypršel časový limit assetu, vrátí POP chybu vypršení platnosti brány 504.

Informace o klíči:

- Tuto funkci konfigurujete pomocí:
    - Výběr stavového kódu, pro který bude použito maximum-zastaralé.
    - Zadáním celočíselné hodnoty a následným výběrem požadované časové jednotky (například sekund, minut, hodin atd.). Tato hodnota definuje interní max. zastaralou hodnotu, která bude použita.

- Když se časová jednotka nastaví na off, tato funkce se zakáže. Prostředek uložený v mezipaměti nebude zpracován po dobu jeho normálního vypršení platnosti.

**Výchozí chování:** Dvě minuty

#### <a name="compatibility"></a>Kompatibilita

Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, nelze tuto funkci přidružit k následujícím podmínkám shody:
- JAKO číslo
- Client IP Address
- Parametr souboru cookie
- Regulární parametr pro soubor cookie
- Země
- Zařízení
- Záznam CNAME okraje
- Odkazující doména
- Literál hlavičky požadavku
- Regulární výraz pro záhlaví požadavku
- Zástupný znak hlavičky žádosti
- Request – metoda
- Schéma žádosti
- Literál dotazu URL
- Regulární výraz pro dotaz na adresu URL
- Zástupný znak URL pro dotaz
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Řetězec dotazu protokolu

**Účel:** Určuje, zda bude řetězec dotazu uložen spolu s adresou URL v protokolech přístupu.

Hodnota|Výsledek
-|-
Povoleno|Povoluje ukládání řetězců dotazů při zaznamenávání adres URL v protokolu přístupu. Pokud adresa URL neobsahuje řetězec dotazu, nebude mít tato možnost žádný efekt.
Zakázáno|Obnoví výchozí chování. Výchozím chováním je ignorovat řetězce dotazů při zaznamenávání adres URL v protokolu přístupu.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximální počet požadavků Keep-Alive

**Účel:** Definuje maximální počet požadavků na připojení Keep-Alive před jeho zavřením.

Nastavení maximálního počtu požadavků na nízkou hodnotu se nedoporučuje a může způsobit snížení výkonu.

Informace o klíči:

- Tuto hodnotu zadejte jako celé celé číslo.
- Do zadané hodnoty nezahrnujte čárky ani tečky.

**Výchozí hodnota:** 10 000 požadavků

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Upravit hlavičku žádosti klienta

**Účel:** Každý požadavek obsahuje sadu hlaviček požadavků, které ho popisují. Tato funkce může:

- Umožňuje připojit nebo přepsat hodnotu přiřazenou k hlavičce požadavku. Pokud zadaná Hlavička požadavku neexistuje, bude tato funkce přidána do žádosti.
- Odstraní hlavičku požadavku z požadavku.

Požadavky, které jsou předány na zdrojový server, budou zárážet změny provedené touto funkcí.

V hlavičce požadavku lze provést jednu z následujících akcí:

Možnost|Popis|Příklad
-|-|-
Připojit|Zadaná hodnota bude přidána na konec existující hodnoty hlavičky požadavku.|**Hodnota hlavičky žádosti (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky požadavku (stroj pravidel):**<br/>Argument <br/>**Nová hodnota hlavičky žádosti:** <br/>Value1Value2
Přepsat|Hodnota hlavičky Request se nastaví na zadanou hodnotu.|**Hodnota hlavičky žádosti (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky požadavku (stroj pravidel):**<br/>Argument<br/>**Nová hodnota hlavičky žádosti:**<br/> Argument <br/>
Odstranit|Odstraní určenou hlavičku požadavku.|**Hodnota hlavičky žádosti (klient):**<br/>Hodnota1<br/>**Upravit konfiguraci hlavičky žádosti klienta:**<br/>Odstraňte příslušnou hlavičku žádosti.<br/>**Vyústit**<br/>Zadaná hlavička požadavku se nepřepošle na zdrojový server.

Informace o klíči:

- Zajistěte, aby hodnota zadaná v parametru název byla přesnou shodou pro požadovanou hlavičku požadavku.
- Případ se nebere v úvahu pro účely identifikace hlavičky. K identifikaci můžete například použít kteroukoli z následujících variant `Cache-Control` názvu záhlaví:
    - řízení mezipaměti
    - ŘÍZENÍ MEZIPAMĚTI
    - Řízení mezipaměti
- Při zadávání názvu záhlaví použijte pouze alfanumerické znaky, pomlčky nebo podtržítka.
- Odstraněním hlavičky zabráníte tomu, aby se servery POP přenesly na zdrojový server.
- Následující záhlaví jsou vyhrazena a nelze je upravit touto funkcí:
    - přesměrovaná
    - host
    - vedení
    - upozornění
    - x-předané – pro
    - Všechny názvy hlaviček začínající řetězcem "x-ES" jsou rezervované.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Upravit hlavičku odpovědi klienta

Každá odpověď obsahuje sadu hlaviček odpovědi, které ji popisují. Tato funkce může:

- Umožňuje připojit nebo přepsat hodnotu přiřazenou k hlavičce odpovědi. Pokud zadaná hlavička odpovědi neexistuje, bude tato funkce přidána do odpovědi.
- Odstraní hlavičku odpovědi z odpovědi.

Ve výchozím nastavení jsou hodnoty hlaviček odpovědí definovány na zdrojovém serveru a na serverech pop.

V hlavičce odpovědi se dá udělat jedna z následujících akcí:

Možnost|Popis|Příklad
-|-|-
Připojit|Zadaná hodnota bude přidána na konec existující hodnoty hlavičky odpovědi.|**Hodnota hlavičky odpovědi (klient):**<br />Hodnota1<br/>**Hodnota hlavičky odpovědi (stroj pravidel):**<br/>Argument<br/>**Nová hodnota hlavičky odpovědi:**<br/>Value1Value2
Přepsat|Hodnota hlavičky Response se nastaví na zadanou hodnotu.|**Hodnota hlavičky odpovědi (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky odpovědi (stroj pravidel):**<br/>Argument <br/>**Nová hodnota hlavičky odpovědi:**<br/>Argument <br/>
Odstranit|Odstraní určenou hlavičku odpovědi.|**Hodnota hlavičky odpovědi (klient):**<br/>Hodnota1<br/>**Upravit konfiguraci hlavičky odpovědi klienta:**<br/>Odstraňte příslušnou hlavičku odpovědi.<br/>**Vyústit**<br/>Zadaná hlavička odpovědi nebude postoupena žadateli.

Informace o klíči:

- Zajistěte, aby hodnota zadaná v parametru název byla přesnou shodou pro požadovanou hlavičku odpovědi.
- Případ se nebere v úvahu pro účely identifikace hlavičky. K identifikaci můžete například použít kteroukoli z následujících variant `Cache-Control` názvu záhlaví:
    - řízení mezipaměti
    - ŘÍZENÍ MEZIPAMĚTI
    - Řízení mezipaměti
- Odstranění záhlaví brání jeho přeposílání žadateli.
- Následující záhlaví jsou vyhrazena a nelze je upravit touto funkcí:
    - přijmout – kódování
    - věk
    - připojení
    - kódování obsahu
    - Content-Length
    - rozsah obsahu
    - date
    - server
    - uprostřed
    - přenos – kódování
    - Přejít
    - toho
    - vedení
    - upozornění
    - Všechny názvy hlaviček začínající řetězcem "x-ES" jsou rezervované.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Částečné sdílení mezipaměti

**Účel:** Určuje, zda může požadavek vygenerovat částečně uložený obsah v mezipaměti.

Tato částečná mezipaměť se pak může použít k plnění nových požadavků na daný obsah, dokud se požadovaný obsah plně neuloží do mezipaměti.

Hodnota|Výsledek
-|-
Povoleno|Požadavky mohou vygenerovat částečně uložený obsah v mezipaměti.
Zakázáno|Požadavky mohou vygenerovat pouze plně uloženou verzi požadovaného obsahu v mezipaměti.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Předběžně ověřit obsah v mezipaměti

**Účel:** Určuje, jestli bude obsah uložený v mezipaměti způsobilý k předčasnému revalidu před vypršením platnosti TTL.

Definujte dobu před vypršením platnosti požadovaného času TTL obsahu, během kterého bude mít nárok na předčasné opětovné ověření.

Informace o klíči:

- Výběr možnosti off (vypnuto), protože Časová jednotka vyžaduje, aby se provedlo opětovné ověření po vypršení hodnoty TTL obsahu uložené v mezipaměti. Čas by neměl být zadán a bude ignorován.

**Výchozí chování:** Zaokrouhl. Revaliding může proběhnout až po vypršení platnosti hodnoty TTL obsahu uložené v mezipaměti.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Speciální hlavičky proxy serveru

**Účel:** Definuje sadu [hlaviček požadavků HTTP specifických pro Verizon](cdn-verizon-http-headers.md) , které se předají ze serveru POP na zdrojový server.

Informace o klíči:

- Každá Hlavička požadavku specifická pro CDN definovaná v této funkci se přepošle na zdrojový server. Vyloučené hlavičky nejsou předávány.
- Chcete-li zabránit přeposílání hlavičky požadavku specifického pro CDN, odeberte ji ze seznamu odděleného mezer v poli se seznamem hlaviček.

Výchozí seznam obsahuje následující hlavičky protokolu HTTP:
- Vedení
- X-předané – pro
- X-předáno – proto
- X-host
- X – Midgress
- X-Gateway – seznam
- X-EC – název
- Hostitel

**Výchozí chování:** Všechny hlavičky žádostí specifické pro CDN se předají na zdrojový server.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Aktualizovat soubory mezipaměti s nulovou bajtů

**Účel:** Určuje, jakým způsobem se v rozhraních pop zpracovávají požadavky klienta HTTP na prostředek mezipaměti o velikosti 0 bajtů.

Platné hodnoty jsou:

Hodnota|Výsledek
--|--
Povoleno|Způsobí, že bod POP znovu načte Asset ze zdrojového serveru.
Zakázáno|Obnoví výchozí chování. Výchozím chováním je zajišťovat platné prostředky mezipaměti na vyžádání.

Tato funkce není nutná pro správné ukládání do mezipaměti a doručování obsahu, ale může být užitečná jako alternativní řešení. Například generátory dynamického obsahu na zdrojovém serveru můžou neúmyslně vést k odesílání odpovědí do bodů POP na 0 bajtů. Tyto typy odpovědí jsou obvykle ukládány do mezipaměti pomocí bodů POP. Pokud víte, že odpověď 0 bajty není nikdy platnou odezvou pro takový obsah, může tato funkce zabránit tomu, aby tyto typy prostředků byly obsluhovány klientům.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Nastavit stavové kódy pro ukládání do mezipaměti

**Účel:** Definuje sadu stavových kódů, které mohou mít za následek obsah uložený v mezipaměti.

Ve výchozím nastavení je ukládání do mezipaměti povoleno pouze v případě odpovědí 200 OK.

Definujte sadu požadovaných stavových kódů oddělených mezerami.

Informace o klíči:

- Povolí funkci ignorovat zdroj bez mezipaměti. Pokud tato funkce není povolená, nemusejí být odpovědi na non-200 OK uložené v mezipaměti.
- Sada platných stavových kódů pro tuto funkci: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, 505,,, a.
- Tato funkce se nedá použít k zákazu ukládání do mezipaměti pro odpovědi, které generují stavový kód 200 OK.

**Výchozí chování:** Ukládání do mezipaměti je povoleno pouze pro odpovědi, které generují stavový kód 200 OK.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Nastavit vlastní hlavičku IP adresy klienta

**Účel:** Přidá vlastní hlavičku identifikující žádajícího klienta podle IP adresy k žádosti.

Možnost název hlavičky definuje název vlastní hlavičky žádosti, kde je uložená IP adresa klienta.

Tato funkce umožňuje, aby zdrojový server zákazníka mohl zjistit IP adresy klientů prostřednictvím vlastní hlavičky žádosti. Pokud je požadavek obsluhován z mezipaměti, nebude zdrojový server informován o IP adrese klienta. Proto se doporučuje použít tuto funkci u prostředků, které nejsou uložené v mezipaměti.

Zajistěte, aby zadaný název záhlaví neodpovídal žádnému z následujících názvů:

- Názvy hlaviček standardních požadavků. Seznam standardních názvů hlaviček najdete v [dokumentu RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Rezervované názvy hlaviček:
    - předané – pro
    - host
    - toho
    - vedení
    - upozornění
    - x-předané – pro
    - Všechny názvy hlaviček začínající řetězcem "x-ES" jsou rezervované.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Zastaralé doručování obsahu při chybě

**Účel:** Určuje, zda bude doručen obsah uložený v mezipaměti, když dojde k chybě během revalidu mezipaměti nebo při načítání požadovaného obsahu ze serveru zákazníka.

Hodnota|Výsledek
-|-
Povoleno|Zastaralému obsahu se zajišťují žadateli, když dojde k chybě během připojení k původnímu serveru.
Zakázáno|Žadateli se přepošle chyba zdrojového serveru.

**Výchozí chování:** Zabezpečen

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Zastaralé během Revalidace

**Účel:** Zvyšuje výkon tím, že umožňuje bodům POP, aby službě žadatele při opětovném ověřování poskytovala zastaralý obsah.

Informace o klíči:

- Chování této funkce se liší v závislosti na zvolené časové jednotce.
    - **Časová jednotka:** Zadejte dobu a vyberte časovou jednotku (například sekundy, minuty, hodiny atd.), aby bylo možné doručování zastaralých obsahu. Tento typ instalace umožňuje CDN zvětšit dobu, po kterou může doručovat obsah, než se ověří podle následujícího vzorce: **hodnota TTL**  +  **zastaralá při revalidaci** .
    - **Vypnuto:** Pokud chcete před odesláním žádosti o zastaralý obsah vyžádat opětovné ověření, vyberte vypnuto.
        - Nezadávejte časovou prodlevu, protože je neplatná a bude ignorována.

**Výchozí chování:** Zaokrouhl. Aby bylo možné dodávat požadovaný obsah, musí proběhnout revalid.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Ověření tokenu

**Účel:** Určuje, zda bude pro požadavek použito ověřování založené na tokenech.

Pokud je povoleno ověřování na základě tokenu, budou přijaty pouze požadavky, které poskytují šifrovaný token a vyhovují požadavkům zadaným tímto tokenem.

Šifrovací klíč, který se používá k šifrování a dešifrování hodnot tokenu, je určený primárním klíčem a možnostmi záložního klíče na stránce ověření tokenu. Pamatujte, že šifrovací klíče jsou specifické pro platformu.

**Výchozí chování:** Zabezpečen.

Tato funkce má přednost před většinou funkcí s výjimkou funkce přepisu adresy URL.

Hodnota | Výsledek
------|---------
Povoleno | Chrání požadovaný obsah pomocí ověřování založeného na tokenech. Budou přijaty pouze požadavky od klientů, kteří poskytují platný token a splňují požadavky. Transakce FTP jsou vyloučeny z ověřování založeného na tokenech.
Zakázáno| Obnoví výchozí chování. Výchozím chováním je povolení konfigurace ověřování na základě tokenu, aby bylo možné určit, jestli bude požadavek zabezpečený.

#### <a name="compatibility"></a>Kompatibilita

Nepoužívejte ověření tokenu s podmínkou Always Match.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Kód pro odepření ověření tokenu

**Účel:** Určuje typ odpovědi, která bude vrácena uživateli, pokud je žádost zamítnuta z důvodu ověřování založeného na tokenech.

Dostupné kódy odpovědí jsou uvedené v následující tabulce.

Kód odpovědi|Název odpovědi|Popis
-------------|-------------|--------
301|Trvale přesunuto|Tento stavový kód přesměruje neautorizované uživatele na adresu URL zadanou v hlavičce umístění.
302|Found|Tento stavový kód přesměruje neautorizované uživatele na adresu URL zadanou v hlavičce umístění. Tento kód stavu je standardní způsob, jak provést přesměrování.
307|Dočasné přesměrování|Tento stavový kód přesměruje neautorizované uživatele na adresu URL zadanou v hlavičce umístění.
401|Neautorizováno|Kombinování tohoto stavového kódu s hlavičkou odpovědi WWW-Authenticate vám umožní vyzvat uživatele k ověření.
403|Forbidden|Tato zpráva je standardní 403 zakázané zprávy o stavu, které se při pokusu o přístup k chráněnému obsahu uvidí neautorizovaný uživatel.
404|Soubor se nenašel.|Tento stavový kód označuje, že klient protokolu HTTP byl schopen komunikovat se serverem, nebyl však nalezen požadovaný obsah.

#### <a name="compatibility"></a>Kompatibilita

Nepoužívejte kód pro odepření tokenu s podmínkou vždycky se shodou. Místo toho použijte **vlastní část manipulace s odepřením** na stránce **ověřování tokenu** na portálu **Spravovat** . Další informace najdete v tématu [zabezpečení Azure CDNch prostředků pomocí ověření tokenu](cdn-token-auth.md).

#### <a name="url-redirection"></a>Přesměrování adresy URL

Tato funkce podporuje přesměrování adresy URL na uživatelem definovanou adresu URL, pokud je nakonfigurována tak, aby vrátila stavový kód 3xx. Tuto adresu URL definovanou uživatelem lze zadat provedením následujících kroků:

1. Vyberte kód odpovědi 3xx pro funkci pro ověření kódu tokenu.
2. Vyberte možnost umístění z volitelného názvu záhlaví.
3. Možnost volitelné hodnoty hlavičky nastavte na požadovanou adresu URL.

Pokud adresa URL není definována pro stavový kód 3xx, bude uživateli vrácena stránka standardní odpověď pro stavový kód 3xx.

Přesměrování adresy URL je použitelné pouze pro kódy odpovědí 3xx.

Možnost volitelné hodnoty v hlavičce podporuje alfanumerické znaky, uvozovky a mezery.

#### <a name="authentication"></a>Authentication

Tato funkce podporuje možnost zahrnout hlavičku WWW-Authenticate při reakci na neautorizovaný požadavek na obsah chráněný ověřováním na základě tokenu. Pokud je záhlaví WWW-Authenticate v konfiguraci nastaveno na "základní", pak se uživateli zobrazí výzva k zadání přihlašovacích údajů k účtu.

Výše uvedenou konfiguraci je možné dosáhnout provedením následujících kroků:

1. Jako kód odpovědi pro funkci DOS Code auth vyberte "401".
2. Vyberte možnost WWW-Authenticate z volitelného názvu záhlaví.
3. Nastavte možnost volitelná hodnota hlavičky na Basic.

Záhlaví WWW-Authenticate je použitelné pouze pro kódy odpovědí 401.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Ignorovat velikost písmen URL pro ověření tokenu

**Účel:** Určuje, zda porovnávání adres URL prováděné ověřováním na základě tokenu rozlišuje velká a malá písmena.

Parametry ovlivněné touto funkcí jsou:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Platné hodnoty jsou:

Hodnota|Výsledek
---|----
Povoleno|Způsobí, že se při porovnávání adres URL pro parametry ověřování na základě tokenu ignoruje velikost písmen POP.
Zakázáno|Obnoví výchozí chování. Výchozím chováním je pro porovnání adres URL při ověřování tokenu rozlišovat velká a malá písmena.

**Výchozí chování:** Zabezpečen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parametr ověření tokenu

**Účel:** Určuje, zda má být přejmenován parametr řetězce dotazu ověřování založeného na tokenu.

Informace o klíči:

- Možnost Value definuje název parametru řetězce dotazu, pomocí kterého lze zadat token.
- Možnost value nelze nastavit na hodnotu ec_token.
- Ujistěte se, že název definovaný v možnosti hodnota obsahuje pouze platné znaky adresy URL.

Hodnota|Výsledek
----|----
Povoleno|Možnost Value definuje název parametru řetězce dotazu, prostřednictvím kterého by měly být definovány tokeny.
Zakázáno|Token se dá zadat jako nedefinovaný parametr řetězce dotazu v adrese URL požadavku.

**Výchozí chování:** Zabezpečen. Token se dá zadat jako nedefinovaný parametr řetězce dotazu v adrese URL požadavku.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Přesměrování adresy URL

**Účel:** Přesměruje požadavky přes hlavičku umístění.

Konfigurace této funkce vyžaduje nastavení následujících možností:

Možnost|Popis
-|-
Kód|Vyberte kód odpovědi, který se vrátí žadateli.
Zdrojový & – vzor| Tato nastavení definují vzor identifikátoru URI požadavku, který identifikuje typ požadavků, které mohou být přesměrovány. Budou přesměrovány pouze požadavky, jejichž adresa URL splňuje obě následující kritéria: <br/> <br/> **Zdroj (nebo přístupový bod obsahu):** Vyberte relativní cestu, která identifikuje zdrojový server. Tato cesta je oddílem _/xxxx/_ a názvem vašeho koncového bodu. <br/><br/> **Zdroj (vzorek):** Je nutné definovat vzor, který identifikuje požadavky podle relativní cesty. Tento vzor regulárního výrazu musí definovat cestu, která začíná přímo za dříve vybraným přístupovým bodem obsahu (viz výše). <br/> – Ujistěte se, že kritéria identifikátoru URI požadavku (tj. vzor zdrojové &), která byla dříve definovaná, nejsou v konfliktu se všemi podmínkami shody definovanými pro tuto funkci. <br/> -Zadat vzor; Použijete-li jako vzor prázdnou hodnotu, budou všechny řetězce spárovány.
Cíl| Zadejte adresu URL, na kterou budou přesměrovány výše uvedené požadavky. <br/><br/> Tuto adresu URL dynamicky Sestavte pomocí: <br/> – Vzor regulárního výrazu <br/>- [Proměnné HTTP](cdn-http-variables.md) <br/><br/> Nahraďte hodnoty zachycené ve zdrojovém vzoru do cílového vzoru pomocí $_n_ , kde _n_ identifikuje hodnotu v pořadí, v jakém byla zachycena. Například $1 představuje první hodnotu zachycenou ve zdrojovém vzoru, zatímco $2 představuje druhou hodnotu. <br/>

Důrazně doporučujeme použít absolutní adresu URL. Použití relativní adresy URL může přesměrovat adresy URL CDN na neplatnou cestu.

**Ukázkový scénář**

Tento příklad ukazuje, jak přesměrovat adresu URL hraničního CNAME, která se překládá na tuto základní adresu URL CDN: http: \/ /marketing.azureedge.NET/brochures

Opravňující požadavky budou přesměrovány na tuto adresu URL CNAME základní hrany: http: \/ /CDN.mydomain.com/resources

Přesměrování adresy URL můžete dosáhnout pomocí následující konfigurace: ![ přesměrování adresy URL](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Klíčové body:**

- Funkce přesměrování adresy URL definuje adresy URL požadavků, které budou přesměrovány. V důsledku toho nejsou vyžadovány další podmínky shody. I když je podmínka shody definovaná jako "Always", pouze požadavky, které odkazují na složku "brožury" na zdroji zákazníka "marketing", budou přesměrovány.
- Všechny vyhovující požadavky budou přesměrovány na adresu URL CNAME Edge definovanou v možnosti cíl.
    - Ukázkový scénář #1:
        - Vzorový požadavek (adresa URL CDN): http: \/ /marketing.azureedge.NET/brochures/widgets.PDF
        - Adresa URL požadavku (po přesměrování): http: \/ /CDN.mydomain.com/resources/widgets.PDF  
    - Ukázkový scénář #2:
        - Ukázková žádost (adresa URL hraničního CNAME): http: \/ /marketing.mydomain.com/brochures/widgets.PDF
        - Adresa URL požadavku (po přesměrování): \/ ukázkový scénář http:/CDN.mydomain.com/resources/widgets.PDF
    - Ukázkový scénář #3:
        - Ukázková žádost (adresa URL hraničního CNAME): http: \/ /brochures.mydomain.com/campaignA/Final/productC.ppt
        - Adresa URL požadavku (po přesměrování): http: \/ /CDN.mydomain.com/resources/campaignA/Final/productC.ppt 
- Proměnná schéma požadavků (% {schéma}) se využívá v možnosti cíle, která zajišťuje, že schéma žádosti zůstane po přesměrování beze změn.
- Segmenty adres URL zachycené z požadavku se připojí k nové adrese URL prostřednictvím "$1".

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>Přepsání adresy URL

**Účel:** Přepíše adresu URL požadavku.

Informace o klíči:

- Konfigurace této funkce vyžaduje nastavení následujících možností:

Možnost|Popis
-|-
 Zdrojový & – vzor | Tato nastavení definují vzor identifikátoru URI požadavku, který identifikuje typ požadavků, které mohou být přepsány. Přepíší se pouze požadavky, jejichž adresa URL splňuje obě následující kritéria: <br/><br/>  - **Zdroj (nebo přístupový bod obsahu):** Vyberte relativní cestu, která identifikuje zdrojový server. Tato cesta je oddílem _/xxxx/_ a názvem vašeho koncového bodu. <br/><br/> - **Zdroj (vzorek):** Je nutné definovat vzor, který identifikuje požadavky podle relativní cesty. Tento vzor regulárního výrazu musí definovat cestu, která začíná přímo za dříve vybraným přístupovým bodem obsahu (viz výše). <br/> Ověřte, že kritéria identifikátoru URI požadavku (tj. vzor zdrojové &) dříve definovaná nejsou v konfliktu s žádnou z podmínek shody definovaných pro tuto funkci. Zadejte vzor. Použijete-li jako vzor prázdnou hodnotu, budou všechny řetězce spárovány.
 Cíl  |Zadejte relativní adresu URL, na kterou budou přepsány výše uvedené požadavky: <br/>    1. výběr přístupového bodu obsahu, který identifikuje zdrojový server. <br/>    2. definování relativní cesty pomocí: <br/>        – Vzor regulárního výrazu <br/>        - [Proměnné HTTP](cdn-http-variables.md) <br/> <br/> Nahraďte hodnoty zachycené ve zdrojovém vzoru do cílového vzoru pomocí $_n_ , kde _n_ identifikuje hodnotu v pořadí, v jakém byla zachycena. Například $1 představuje první hodnotu zachycenou ve zdrojovém vzoru, zatímco $2 představuje druhou hodnotu.

 Tato funkce umožňuje, aby body POP přepsaly adresu URL bez provedení tradičního přesměrování. To znamená, že žadatel obdrží stejný kód odpovědi, jako kdyby byla vyžádána přepsaná adresa URL.

**Vzorový scénář 1**

Tento příklad ukazuje, jak přesměrovat adresu URL hraničního CNAME, která se překládá na tuto základní adresu URL CDN: http: \/ /marketing.azureedge.NET/brochures/

Opravňující požadavky budou přesměrovány na tuto adresu URL CNAME základní hrany: http: \/ /MyOrigin.azureedge.NET/Resources/

Přesměrování adresy URL můžete dosáhnout pomocí následující konfigurace: ![ přesměrování adresy URL](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Vzorový scénář 2**

Tento příklad ukazuje, jak přesměrovat adresu URL CNAME Edge z velkých a malých písmen pomocí regulárních výrazů.

Přesměrování adresy URL můžete dosáhnout pomocí následující konfigurace: ![ přesměrování adresy URL](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Klíčové body:**

- Funkce přepsání adresy URL definuje adresy URL požadavku, které budou přepsány. V důsledku toho nejsou vyžadovány další podmínky shody. I když je podmínka shody definovaná jako "Always", pouze požadavky, které odkazují na složku "brožury" na zdroji zákazníka "marketing", budou přepsány.

- Segmenty adres URL zachycené z požadavku se připojí k nové adrese URL prostřednictvím "$1".

#### <a name="compatibility"></a>Kompatibilita

Tato funkce zahrnuje kritéria porovnání, která musí být splněna, aby bylo možné je použít na žádost. Aby nedocházelo k nastavení konfliktních kritérií, tato funkce je nekompatibilní s následujícími podmínkami shody:

- JAKO číslo
- Zdroj CDN
- Client IP Address
- Původ zákazníka
- Schéma žádosti
- Adresář cesty URL
- Přípona cesty URL
- Název cesty URL
- Literál cesty URL
- Regulární výraz cesty URL
- Zástupný znak cesty adresy URL
- Literál dotazu URL
- Parametr dotazu adresy URL
- Regulární výraz pro dotaz na adresu URL
- Zástupný znak URL pro dotaz

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Uživatelská proměnná

**Účel:** Pouze pro interní použití.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)
## <a name="next-steps"></a>Další kroky

- [Odkazy na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Přepsání chování HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled Azure CDN](cdn-overview.md)
