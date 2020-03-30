---
title: Azure CDN od Verizon Premium pravidla funkce | Dokumenty společnosti Microsoft
description: Referenční dokumentace pro Azure CDN z funkcí modulu pravidel Verizon Premium.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 9177ac544c83305ae95ad681d3dc9f84ac64ea36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247576"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN od Verizon Premium pravidla funkce

Tento článek obsahuje podrobný popis dostupných funkcí pro modul [pravidel](cdn-verizon-premium-rules-engine.md)sítě azure content delivery network (CDN).

Třetí část pravidla je funkce. Funkce definuje typ akce, která se použije na typ požadavku, který je identifikován sadou podmínek shody.

## <a name="access-features"></a>Přístup k funkcím

Tyto funkce jsou navrženy tak, aby řídily přístup k obsahu.

Name (Název) | Účel
-----|--------
[Odepřít přístup (403)](#deny-access-403) | Určuje, zda jsou všechny požadavky odmítnuty s 403 Zakázanou odpovědí.
[Token ušitné](#token-auth) | Určuje, zda je u požadavku použito ověřování na základě tokenu.
[Kód zamítnutí tokenu auth](#token-auth-denial-code) | Určuje typ odpovědi, která je vrácena uživateli, když je požadavek odepřen z důvodu ověřování na základě tokenu.
[Token Auth Ignorovat url případ](#token-auth-ignore-url-case) | Určuje, zda porovnání adres URL provedené ověřováním na základě tokenu rozlišují malá a velká písmena.
[Parametr tokenu auth](#token-auth-parameter) | Určuje, zda má být přejmenován parametr řetězce dotazu ověřování na základě tokenu.

## <a name="caching-features"></a>Funkce ukládání do mezipaměti

Tyto funkce jsou navrženy tak, aby přizpůsobily, kdy a jak je obsah ukládán do mezipaměti.

Name (Název) | Účel
-----|--------
[Parametry šířky pásma](#bandwidth-parameters) | Určuje, zda jsou aktivní parametry omezení šířky pásma (například ec_rate a ec_prebuf).
[Omezení šířky pásma](#bandwidth-throttling) | Omezení šířky pásma pro odezvu poskytovanou bodem přítomnosti (POP).
[Obejít mezipaměť](#bypass-cache) | Určuje, zda má požadavek obejít ukládání do mezipaměti.
[Zpracování záhlaví řízení mezipaměti](#cache-control-header-treatment) | Řídí generování `Cache-Control` záhlaví pomocí protokolu POP, když je aktivní externí funkce maximálního stáří.
[Řetězec dotazu klíče mezipaměti](#cache-key-query-string) | Určuje, zda klíč mezipaměti obsahuje nebo vylučuje parametry řetězce dotazu přidružené k požadavku.
[Přepis klíče mezipaměti](#cache-key-rewrite) | Přepíše klíč mezipaměti přidružený k požadavku.
[Dokončit výplň mezipaměti](#complete-cache-fill) | Určuje, co se stane, když výsledkem požadavku dojde k chybě částečné mezipaměti v protokolu POP.
[Komprimovat typy souborů](#compress-file-types) | Definuje formáty souborů pro soubory, které jsou komprimovány na serveru.
[Výchozí interní maximální stáří](#default-internal-max-age) | Určuje výchozí maximální věkovou délku pro opětovné ověření mezipaměti serveru POP na původní server.
[Vyprší záhlaví ošetření](#expires-header-treatment) | Určuje generování `Expires` záhlaví pomocí protokolu POP, když je aktivní externí funkce Maximální stáří.
[Maximální stáří externího](#external-max-age) | Určuje maximální věkovou délku pro obnovení platnosti mezipaměti POP v prohlížeči.
[Vynutit vnitřní maximální stáří](#force-internal-max-age) | Určuje maximální věkovou délku pro opětovné ověření mezipaměti serveru POP na zdrojový původ.
[Podpora H.264 (http progresivní stahování)](#h264-support-http-progressive-download) | Určuje typy formátů souborů H.264, které mohou být použity k streamování obsahu.
[Požadavek na nedoletovou službu](#honor-no-cache-request) | Určuje, zda jsou požadavky klienta HTTP bez mezipaměti předány na zdrojový server.
[Ignorovat origin bez mezipaměti](#ignore-origin-no-cache) | Určuje, zda CDN ignoruje určité direktivy obsluhované ze serveru původu.
[Ignorovat nenasytné rozsahy](#ignore-unsatisfiable-ranges) | Určuje odpověď, která je vrácena klientům, když požadavek generuje 416 Požadovaný rozsah není satisfiable stavový kód.
[Interní maximální zatuchlý](#internal-max-stale) | Určuje, jak dlouho po normální době vypršení platnosti může být prostředek uložený v mezipaměti obsluhován z bodu POP, pokud protokol POP nemůže znovu ověřit datový zdroj uložený v mezipaměti se zdrojovým serverem.
[Částečné sdílení mezipaměti](#partial-cache-sharing) | Určuje, zda může požadavek generovat částečně uložený obsah v mezipaměti.
[Předběžná platnost obsahu uloženého v mezipaměti](#prevalidate-cached-content) | Určuje, zda je obsah uložený v mezipaměti způsobilý pro předčasné opětovné ověření pravosti před vypršením platnosti ttl.
[Aktualizovat soubory mezipaměti s nulovým bajtem](#refresh-zero-byte-cache-files) | Určuje, jak je požadavek klienta HTTP na prostředek mezipaměti 0 bajtů zpracován pomocí polí POP.
[Nastavení stavových kódů s mezipamětí](#set-cacheable-status-codes) | Definuje sadu stavových kódů, které mohou mít za následek obsah uložený v mezipaměti.
[Zastaralé doručování obsahu při chybě](#stale-content-delivery-on-error) | Určuje, zda je obsah uložený v mezipaměti, jehož platnost vypršela, když dojde k chybě během opětovného ověření mezipaměti nebo při načítání požadovaného obsahu ze serveru původu zákazníka.
[Zastaralé při opětovném ověření](#stale-while-revalidate) | Zlepšuje výkon tím, že umožňuje POP sloužit zastaralé klienta pro uchazeče, zatímco probíhá opětovné ověření.

## <a name="comment-feature"></a>Funkce komentáře

Tato funkce je navržena tak, aby poskytovala další informace v rámci pravidla.

Name (Název) | Účel
-----|--------
[Komentář](#comment) | Umožňuje přidání poznámky do pravidla.

## <a name="header-features"></a>Funkce záhlaví

Tyto funkce jsou navrženy tak, aby z požadavku nebo odpovědi přidávaly, upravují nebo odstraňují záhlaví.

Name (Název) | Účel
-----|--------
[Záhlaví odpovědi na věk](#age-response-header) | Určuje, zda je hlavička odpovědi Age zahrnuta v odpovědi odeslané žadateli.
[Ladění záhlaví odpovědí mezipaměti](#debug-cache-response-headers) | Určuje, zda odpověď může obsahovat hlavičku odpovědi X-EC-Debug, která poskytuje informace o zásadách mezipaměti pro požadovaný prostředek.
[Změnit hlavičku požadavku klienta](#modify-client-request-header) | Přepíše, připojí nebo odstraní záhlaví z požadavku.
[Změnit hlavičku odpovědi klienta](#modify-client-response-header) | Přepíše, připojí nebo odstraní záhlaví z odpovědi.
[Nastavení vlastní hlavičky IP klienta](#set-client-ip-custom-header) | Umožňuje přidání IP adresy žádajícího klienta do požadavku jako vlastní hlavičky požadavku.

## <a name="logging-features"></a>Funkce protokolování

Tyto funkce jsou navrženy tak, aby přizpůsobily data uložená v nezpracovaných souborech protokolu.

Name (Název) | Účel
-----|--------
[Vlastní logové pole 1](#custom-log-field-1) | Určuje formát a obsah přiřazený k vlastnímu poli protokolu v nezpracovaném souboru protokolu.
[Řetězec dotazu protokolu](#log-query-string) | Určuje, zda je řetězec dotazu uložen spolu s adresou URL v protokolech přístupu.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled


### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Funkce původu

Tyto funkce jsou navrženy tak, aby řídily způsob komunikace sítě CDN se zdrojovým serverem.

Name (Název) | Účel
-----|--------
[Maximální požadavky na udržení naživu](#maximum-keep-alive-requests) | Definuje maximální počet požadavků na připojení Keep-Alive před jeho zavřením.
[Speciální záhlaví proxy serveru](#proxy-special-headers) | Definuje sadu hlavičky požadavků specifických pro CDN, které jsou předávány z bodu POP na zdrojový server.

## <a name="specialty-features"></a>Speciální funkce

Tyto funkce poskytují rozšířené funkce pro pokročilé uživatele.

Name (Název) | Účel
-----|--------
[Metody HTTP s mezipamětí](#cacheable-http-methods) | Určuje sadu dalších metod HTTP, které lze uložit do mezipaměti v síti.
[Velikost těla požadavku uložit do mezipaměti](#cacheable-request-body-size) | Definuje prahovou hodnotu pro určení, zda lze odpověď POST uložit do mezipaměti.
[Uživatelská proměnná](#user-variable) | Pouze pro interní použití.

## <a name="url-features"></a>Funkce adresy URL

Tyto funkce umožňují přesměrovat nebo převést požadavek na jinou adresu URL.

Name (Název) | Účel
-----|--------
[Sledovat přesměrování](#follow-redirects) | Určuje, zda mohou být požadavky přesměrovány na název hostitele definovaný v hlavičce Umístění vrácené serverem původu zákazníka.
[Přesměrování adresy URL](#url-redirect) | Přesměruje požadavky prostřednictvím hlavičky Umístění.
[Přepsání adresy URL](#url-rewrite)  | Přepíše adresu URL požadavku.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN od Verizon Premium pravidla motoru obsahuje reference

---

### <a name="age-response-header"></a>Záhlaví odpovědi na věk

**Účel**: Určuje, zda je hlavička odpovědi Age zahrnuta v odpovědi odeslané žadateli.

Hodnota|Výsledek
--|--
Povoleno | Hlavička odpovědi Věk je zahrnuta v odpovědi odeslané žadateli.
Zakázáno | Hlavička odpovědi Věk je vyloučena z odpovědi odeslané žadateli.

**Výchozí chování**: Zakázáno.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Parametry šířky pásma

**Účel:** Určuje, zda jsou aktivní parametry omezení šířky pásma (například ec_rate a ec_prebuf).

Parametry omezení šířky pásma určují, zda je rychlost přenosu dat pro požadavek klienta omezena na vlastní rychlost.

Hodnota|Výsledek
--|--
Povoleno|Umožňuje popům SLEDOVÁNÍ šířky pásma respektovat požadavky na omezení šířky pásma.
Zakázáno|Způsobí, že popy ignorují parametry omezení šířky pásma. Požadovaný obsah se obsluhuje normálně (to znamená bez omezení šířky pásma).

**Výchozí chování:** Povoleno.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Omezení šířky pásma

**Účel:** Omezuje šířku pásma pro odezvu poskytovanou popy POP.

Obě následující možnosti musí být definovány správně nastavit omezení šířky pásma.

Možnost|Popis
--|--
Kbytes za sekundu|Nastavte tuto možnost na maximální šířku pásma (Kb za sekundu), která může být použita k doručení odpovědi.
Prebuf sekund|Nastavte tuto možnost na počet sekund, po které budou popy čekat, dokud nebude omezena šířka pásma. Účelem tohoto časového období neomezené šířky pásma je zabránit přehrávači médií v tom, aby došlo k problémům s koktáním nebo ukládáním do vyrovnávací paměti kvůli omezení šířky pásma.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Obejít mezipaměť

**Účel:** Určuje, zda má požadavek obejít ukládání do mezipaměti.

Hodnota|Výsledek
--|--
Povoleno|Způsobí, že všechny požadavky spadají do zdrojového serveru, i v případě, že obsah byl dříve uložen do mezipaměti na POP.
Zakázáno|Způsobí, že popy popu ukládat do mezipaměti prostředky podle zásady mezipaměti definované v hlavičkách odpovědí.

**Výchozí chování:**

- **HTTP Velké:** Zakázán

<!---
- **ADN:** Enabled

--->

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Metody HTTP s mezipamětí

**Účel:** Určuje sadu dalších metod HTTP, které lze uložit do mezipaměti v síti.

Klíčové informace:

- Tato funkce předpokládá, že get odpovědi by měly být vždy uloženy do mezipaměti. V důsledku toho get http metoda by neměla být zahrnuta při nastavování této funkce.
- Tato funkce podporuje pouze metodu POST HTTP. Povolte ukládání do mezipaměti odezvy POST nastavením této funkce na `POST`.
- Ve výchozím nastavení jsou ukládány do mezipaměti pouze požadavky, jejichž tělo je menší než 14 kb. Pomocí funkce Velikost těla požadavků na cache nastavte maximální velikost těla požadavku.

**Výchozí chování:** Pouze get odpovědi jsou uloženy do mezipaměti.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Velikost těla požadavku uložit do mezipaměti

**Účel:** Definuje prahovou hodnotu pro určení, zda lze odpověď POST uložit do mezipaměti.

Tato prahová hodnota je určena zadáním maximální velikosti těla požadavku. Požadavky, které obsahují větší tělo požadavku nejsou uloženy do mezipaměti.

Klíčové informace:

- Tato funkce je použitelná pouze v případě, že odpovědi POST jsou způsobilé pro ukládání do mezipaměti. Pomocí funkce Metody HTTP s možnostmi mezipaměti povolte ukládání požadavků POST do mezipaměti.
- Orgán žádosti se bere v úvahu pro:
    - x-www-form-urlencoded hodnoty
    - Zajištění jedinečného klíče mezipaměti
- Definování velké maximální velikost těla požadavku může mít vliv na výkon doručování dat.
    - **Doporučená hodnota:** 14 Kb
    - **Minimální hodnota:** 1 Kb

**Výchozí chování:** 14 kb

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Zpracování záhlaví řízení mezipaměti

**Účel:** Určuje generování `Cache-Control` záhlaví pomocí protokolu POP, když je aktivní externí funkce maximálního stáří.

Nejjednodušší způsob, jak dosáhnout tohoto typu konfigurace je umístit externí maximální stáří a cache control header ošetření funkce ve stejném příkazu.

Hodnota|Výsledek
--|--
Přepsat|Zajišťuje, že dojde k následujícím akcím:<br/> - Přepíše `Cache-Control` záhlaví generované zdrojovým serverem. <br/>- Přidá `Cache-Control` záhlaví vytvořené externí maximální stáří funkce odpovědi.
Projít|Zajišťuje, že `Cache-Control` záhlaví vytvořené externí maximální stáří funkce je nikdy přidán do odpovědi. <br/> Pokud zdrojový server vytvoří `Cache-Control` záhlaví, přejde ke koncovému uživateli. <br/> Pokud zdrojový server nevytváří `Cache-Control` záhlaví, pak tato možnost může způsobit `Cache-Control` záhlaví odpovědi neobsahuje záhlaví.
Přidat, pokud chybí|Pokud `Cache-Control` záhlaví nebylo přijato ze původního serveru, `Cache-Control` pak tato možnost přidá záhlaví vyrobené externí maximální stáří funkce. Tato možnost je užitečná pro zajištění, `Cache-Control` že všechny prostředky jsou přiřazeny záhlaví.
Odebrat| Tato možnost zajišťuje, `Cache-Control` že záhlaví není součástí odpovědi záhlaví. Pokud `Cache-Control` již byla hlavička přiřazena, je odebrána z odpovědi záhlaví.

**Výchozí chování:** Přepsat.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Řetězec dotazu klíče mezipaměti

**Účel:** Určuje, zda klíč mezipaměti obsahuje nebo vylučuje parametry řetězce dotazu přidružené k požadavku.

Klíčové informace:

- Zadejte jeden nebo více názvů parametrů řetězce dotazu a oddělte každý název parametru jednou mezerou.
- Tato funkce určuje, zda jsou parametry řetězce dotazu zahrnuty nebo vyloučeny z klíče mezipaměti. Další informace jsou uvedeny pro každou možnost v následující tabulce.

Typ|Popis
--|--
 Zařadit členy|  Označuje, že každý zadaný parametr by měl být zahrnut do klíče mezipaměti. Pro každý požadavek, který obsahuje jedinečnou hodnotu parametru řetězce dotazu definovaného v této funkci, je generován jedinečný klíč mezipaměti.
 Zahrnout vše  |Označuje, že pro každý požadavek na prostředek, který obsahuje jedinečný řetězec dotazu, je vytvořen jedinečný klíč mezipaměti. Tento typ konfigurace se obvykle nedoporučuje, protože může vést k malému procentu přístupů do mezipaměti. Nízký počet přístupů do mezipaměti zvyšuje zatížení na zdrojový server, protože musí sloužit více požadavků. Tato konfigurace duplikuje chování ukládání do mezipaměti známé jako "jedinečná mezipaměť" na stránce ukládání do mezipaměti dotazu.
 Exclude | Označuje, že z klíče mezipaměti je vyloučen pouze zadaný parametr(y). Všechny ostatní parametry řetězce dotazu jsou zahrnuty v klíči mezipaměti.
 Vyloučit vše  |Označuje, že všechny parametry řetězce dotazu jsou vyloučeny z klíče mezipaměti. Tato konfigurace duplikuje výchozí chování ukládání do mezipaměti "standardní mezipaměti" na stránce ukládání do mezipaměti dotazu.  

Modul pravidel umožňuje přizpůsobit způsob, jakým je implementováno ukládání řetězce dotazu do mezipaměti. Můžete například určit, že ukládání řetězce dotazu do mezipaměti se provádí pouze v určitých umístěních nebo typech souborů.

Chcete-li duplikovat chování ukládání řetězce dotazu bez mezipaměti na stránce ukládání do mezipaměti dotazu, vytvořte pravidlo, které obsahuje podmínku shody zástupných karet dotazu URL a funkci Mezipaměti bypass. Nastavte podmínku shody zástupných symbolů dotazu URL na hvězdičku (*).

>[!IMPORTANT]
> Pokud je povolena autorizace tokenu pro libovolnou cestu v tomto účtu, režim standardní mezipaměti je jediný režim, který lze použít pro ukládání řetězce dotazu do mezipaměti. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Následující ukázkové použití pro tuto funkci poskytuje ukázkový požadavek a výchozí klíč mezipaměti:

- **Ukázkový požadavek:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **Výchozí klíč mezipaměti:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Zařadit členy

Konfigurace vzorku:

- **Typ:** Zahrnout
- **Parametry:** jazyk

Tento typ konfigurace by vygeneroval následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Zahrnout vše

Konfigurace vzorku:

- **Typ:** Zahrnout vše

Tento typ konfigurace by vygeneroval následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Konfigurace vzorku:

- **Typ:** Vyloučit
- **Parametry:** sessioned userid

Tento typ konfigurace by vygeneroval následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Vyloučit vše

Konfigurace vzorku:

- **Typ:** Vyloučit vše

Tento typ konfigurace by vygeneroval následující klíč mezipaměti parametru řetězce dotazu:

    /800001/Origin/folder/asset.htm

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Přepis klíče mezipaměti

**Účel:** Přepíše klíč mezipaměti přidružený k požadavku.

Klíč mezipaměti je relativní cesta, která identifikuje prostředek pro účely ukládání do mezipaměti. Jinými slovy, servery kontrolují verzi datového zdroje uloženou v mezipaměti podle jeho cesty, jak je definována jeho klíčem mezipaměti.

Nakonfigurujte tuto funkci definováním obou následujících možností:

Možnost|Popis
--|--
Původní cesta| Definujte relativní cestu k typům požadavků, jejichž klíč mezipaměti je přepsán. Relativní cestu lze definovat výběrem cesty základního původu a definováním vzoru regulárního výrazu.
Nová cesta|Definujte relativní cestu pro nový klíč mezipaměti. Relativní cestu lze definovat výběrem cesty základního původu a definováním vzoru regulárního výrazu. Tato relativní cesta může být dynamicky vytvořena pomocí [http proměnných](cdn-http-variables.md).

**Výchozí chování:** Klíč mezipaměti požadavku je určen identifikátorem URI požadavku.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Poznámka

**Účel:** Umožňuje přidání poznámky do pravidla.

Jedním z použití této funkce je poskytnout další informace o obecném účelu pravidla nebo o tom, proč byla do pravidla přidána určitá podmínka nebo funkce shody.

Klíčové informace:

- Může být zadáno maximálně 150 znaků.
- Použijte pouze alfanumerické znaky.
- Tato funkce nemá vliv na chování pravidla. Je pouze určen k poskytnutí oblasti, kde můžete poskytnout informace pro budoucí použití nebo které mohou pomoci při řešení potíží s pravidlem.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Dokončit výplň mezipaměti

**Účel:** Určuje, co se stane, když výsledkem požadavku dojde k chybě částečné mezipaměti v protokolu POP.

Částečná chybě mezipaměti popisuje stav mezipaměti pro prostředek, který nebyl zcela stažen do protokolu POP. Pokud je datový zdroj v mezipaměti pouze částečně v popu, bude další požadavek na tento datový zdroj znovu předán na zdrojový server.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Částečné mezipaměti chybí obvykle dochází po uživatel přeruší stahování nebo pro prostředky, které jsou požadovány výhradně pomocí požadavků rozsahu HTTP. Tato funkce je nejužitečnější pro velké datové zdroje, které se obvykle nestahují od začátku do konce (například videa). V důsledku toho je tato funkce ve výchozím nastavení povolena na platformě HTTP Large. Je zakázán na všech ostatních platformách.

Zachovat výchozí konfiguraci pro platformu HTTP Large, protože snižuje zatížení serveru původu zákazníka a zvyšuje rychlost, jakou zákazníci stahují váš obsah.

Hodnota|Výsledek
--|--
Povoleno|Obnoví výchozí chování. Výchozí chování je vynutit POP zahájit načítání na pozadí datového zdroje z původního serveru. Poté bude datový zdroj v místní mezipaměti protokolu POP.
Zakázáno|Zabrání pop provádět načítání na pozadí pro prostředek. Výsledkem je, že další požadavek na tento prostředek z této oblasti způsobí, že pop požadovat ze serveru původu zákazníka.

**Výchozí chování:** Povoleno.

#### <a name="compatibility"></a>Kompatibilita

Vzhledem ke způsobu sledování nastavení mezipaměti nelze tuto funkci přidružit k následujícím podmínkám shody:

- Číslo AS
- Client IP Address
- Parametr souboru cookie
- Regex parametru souboru cookie
- Země
- Zařízení
- Microsoft Edge Cname
- Odkazující doména
- Literál hlavičky požadavku
- Hlavička požadavku Regex
- Zástupný znak záhlaví požadavku
- Request – metoda
- Schéma žádostí
- Literál dotazu URL
- Regex dotazu URL
- Zástupný znak dotazu URL
- Parametr dotazu URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Komprimovat typy souborů

**Účel:** Definuje formáty souborů pro soubory, které jsou komprimovány na serveru.

Formát souboru lze zadat pomocí typu internetového média (například Typ obsahu). Typ internetových médií je metadata nezávislá na platformě, která serverům umožňuje identifikovat formát souboru určitého datového zdroje. Níže je uveden seznam běžných typů internetových médií.

Typ internetového média|Popis
--|--
text/prostý|Soubory ve formátu prostého textu
text/html| Soubory HTML
text/css|Šablony stylů CSS
aplikace/x-javascript|JavaScript
aplikace/javascript|JavaScript

Klíčové informace:

- Určete více typů internetových médií tak, že každý z nich vymezíte jednou mezerou.
- Tato funkce komprimuje pouze datové zdroje, jejichž velikost je menší než 1 MB. Větší datové zdroje nejsou servery komprimovány.
- Některé typy obsahu, například obrázky, video a datové zdroje zvukových médií (například JPG, MP3, MP4 atd.), jsou již komprimovány. Vzhledem k tomu, že další komprese u těchto typů datových zdrojů výrazně nezmenšuje velikost souboru, doporučujeme, abyste na nich nepovolovali kompresi.
- Zástupné znaky, například hvězdičky, nejsou podporovány.
- Před přidáním této funkce do pravidla se ujistěte, že jste nastavili volbu Zakázáno kompresi na stránce Komprese pro platformu, na kterou je toto pravidlo použito.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Vlastní logové pole 1

**Účel:** Určuje formát a obsah, který bude přiřazen k vlastnímu poli protokolu v nezpracovaném souboru protokolu.

Toto vlastní pole umožňuje určit, které hodnoty hlavičky požadavku a odpovědi jsou uloženy v souborech protokolu.

Ve výchozím nastavení se vlastní pole protokolu nazývá "x-ec_custom-1". Název tohoto pole lze přizpůsobit na stránce Nastavení nezpracovaného protokolu.

Formát pro určení hlavičky požadavku a odpovědi je definován takto:

Typ záhlaví|Formát|Příklady
-|-|-
Hlavička požadavku|`%{[RequestHeader]()}[i]()` | %{Přijmout kódování}i <br/> {Doporučující osoba}i <br/> %{Autorizace}i
Hlavička odpovědi|`%{[ResponseHeader]()}[o]()`| %{Věk}o <br/> %{Typ obsahu}o <br/> %{Soubor cookie}o

Klíčové informace:

- Vlastní pole protokolu může obsahovat libovolnou kombinaci polí záhlaví a prostého textu.
- Platné znaky pro toto pole jsou následující: alfanumerické (0-9, a-z a A-Z), pomlčky, dvojtečky, střední střeva, apostrofy, čárky, tečky, podtržítka, rovnítka, závorky, závorky a mezery. Symbol procenta a složené závorky jsou povoleny pouze v případě, že se používá k určení pole záhlaví.
- Pravopis pro každé zadané pole záhlaví se musí shodovat s požadovaným názvem hlavičky požadavku a odpovědi.
- Pokud chcete zadat více záhlaví, použijte oddělovač k označení každé hlavičky. Pro každé záhlaví můžete například použít zkratku:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Výchozí hodnota:** -

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Ladění záhlaví odpovědí mezipaměti

**Účel:** Určuje, zda odpověď může obsahovat [hlavičky odpovědí X-EC-Debug](cdn-http-debug-headers.md), které poskytují informace o zásadách mezipaměti pro požadovaný datový zdroj.

Ladění hlavičky odpovědí mezipaměti budou zahrnuty do odpovědi, pokud jsou splněny obě následující:

- Funkce Hlavičky odpovědí ladicí mezipaměti byla povolena na zadaný požadavek.
- Zadaný požadavek definuje sadu ladicích hlaviček odpovědí mezipaměti, které budou zahrnuty do odpovědi.

Ladění hlavičky odpovědi mezipaměti může být požadováno zahrnutím následující hlavičky a zadané direktivy v požadavku:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Příklad:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Hodnota|Výsledek
-|-
Povoleno|Požadavky na ladění hlavičky odpovědi mezipaměti vrátí odpověď, která obsahuje hlavičku X-EC-Ladění.
Zakázáno|Hlavička odpovědi X-EC-Debug bude z odpovědi vyloučena.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Výchozí interní maximální stáří

**Účel:** Určuje výchozí maximální věkovou délku pro opětovné ověření mezipaměti serveru POP na původní server. Jinými slovy, doba, která uplyne před bodem POP, zkontroluje, zda se datový zdroj uložený v mezipaměti shoduje s datovým zdrojem uloženým na zdrojovém serveru.

Klíčové informace:

- Tato akce se uskuteční pouze pro odpovědi ze serveru původu, který nepřiřadil označení maximálnístáří v záhlaví `Cache-Control` nebo. `Expires`
- Tato akce se neuskuteční u prostředků, které nejsou považovány za cacheable.
- Tato akce nemá vliv na obnovení mezipaměti PROTOKOLU POP prohlížeče. Tyto typy revalidation jsou `Cache-Control` určeny `Expires` nebo záhlaví odeslané do prohlížeče, které lze přizpůsobit pomocí externí funkce maximální stáří.
- Výsledky této akce nemají pozorovatelný vliv na hlavičky odpovědí a obsah vrácený z popů pro váš obsah, ale může mít vliv na množství přenosů revalidace odeslaných z popů na původní server.
- Tuto funkci nakonfigurujte takto:
    - Výběr stavového kódu, pro který lze použít výchozí interní maximální stáří.
    - Zadání celé hodnoty a výběr požadované časové jednotky (například sekundy, minuty, hodiny atd.). Tato hodnota definuje výchozí interní maximální věkový interval.

- Nastavení časové jednotky na "Vypnuto" přiřadí výchozí interní maximální věkový interval 7 dní pro požadavky, `Cache-Control` `Expires` kterým nebyla přiřazena indikace maximálního věku v jejich záhlaví nebo záhlaví.

**Výchozí hodnota:** 7 dní

#### <a name="compatibility"></a>Kompatibilita

Vzhledem ke způsobu sledování nastavení mezipaměti nelze tuto funkci přidružit k následujícím podmínkám shody:
- Číslo AS
- Client IP Address
- Parametr souboru cookie
- Regex parametru souboru cookie
- Země
- Zařízení
- Název hrany C
- Odkazující doména
- Literál hlavičky požadavku
- Hlavička požadavku Regex
- Zástupný znak záhlaví požadavku
- Request – metoda
- Schéma žádostí
- Literál dotazu URL
- Regex dotazu URL
- Zástupný znak dotazu URL
- Parametr dotazu URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Odepřít přístup (403)

**Účel**: Určuje, zda jsou všechny požadavky odmítnuty s 403 Zakázanou odpovědí.

Hodnota | Výsledek
------|-------
Povoleno| Způsobí, že všechny požadavky, které splňují kritéria přiřazování, budou odmítnuty pomocí zakázané odpovědi 403.
Zakázáno| Obnoví výchozí chování. Výchozí chování je umožnit zdrojový server určit typ odpovědi, která bude vrácena.

**Výchozí chování**: Zakázáno

> [!TIP]
   > Jedním z možných použití této funkce je přidružit ji k podmínce shody záhlaví požadavku a zablokovat tak přístup k odkazujícím na protokol HTTP, které používají vtextové odkazy na váš obsah.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Vyprší záhlaví ošetření

**Účel:** Určuje generování `Expires` záhlaví pomocí protokolu POP, když je aktivní externí funkce Maximální stáří.

Nejjednodušší způsob, jak dosáhnout tohoto typu konfigurace je umístit externí maximální stáří a vyprší záhlaví ošetření funkce ve stejném příkazu.

Hodnota|Výsledek
--|--
Přepsat|Zajišťuje, že budou probíhat následující akce:<br/>- Přepíše `Expires` záhlaví generované zdrojovým serverem.<br/>- Přidá `Expires` záhlaví vytvořené externí maximální stáří funkce odpovědi.
Projít|Zajišťuje, že `Expires` záhlaví vytvořené externí maximální stáří funkce je nikdy přidán do odpovědi. <br/> Pokud zdrojový server vytvoří `Expires` záhlaví, bude předávat koncovému uživateli. <br/>Pokud zdrojový server nevytváří `Expires` záhlaví, pak tato možnost může způsobit `Expires` záhlaví odpovědi neobsahuje záhlaví.
Přidat, pokud chybí| Pokud `Expires` záhlaví nebylo přijato ze původního serveru, `Expires` pak tato možnost přidá záhlaví vyrobené externí maximální stáří funkce. Tato možnost je užitečná pro zajištění toho, aby všem aktivům byla přiřazena hlavička. `Expires`
Odebrat| Zajišťuje, že `Expires` záhlaví není součástí odpovědi záhlaví. Pokud `Expires` již byla hlavička přiřazena, je odebrána z odpovědi záhlaví.

**Výchozí chování:** Přepsat

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Maximální stáří externího

**Účel:** Určuje maximální věkovou délku pro obnovení platnosti mezipaměti POP v prohlížeči. Jinými slovy, doba, která uplyne, než prohlížeč může zkontrolovat novou verzi datového zdroje z protokolu POP.

Povolení této funkce `Cache-Control: max-age` `Expires` vygeneruje a záhlaví z POP a odešle je klientovi HTTP. Ve výchozím nastavení tato záhlaví přepíší záhlaví vytvořená zdrojovým serverem. Však cache control header treatment a vyprší záhlaví ošetření funkce mohou být použity ke změně tohoto chování.

Klíčové informace:

- Tato akce nemá vliv na pop na původní mezipaměti serveru revalidations. Tyto typy revalidation jsou `Cache-Control` určeny `Expires` a záhlaví přijatá ze původního serveru a lze je přizpůsobit pomocí výchozího interního maximálního stáří a funkcí Force Internal Max-Age.
- Nakonfigurujte tuto funkci zadáním celé hodnoty a výběrem požadované časové jednotky (například sekundy, minuty, hodiny atd.).
- Nastavení této funkce na zápornou hodnotu `Cache-Control: no-cache` způsobí, že popy odešlou čas a `Expires` čas, který je nastaven v minulosti s každou odpovědí do prohlížeče. Přestože klient HTTP odpověď neuloží do mezipaměti, toto nastavení neovlivní schopnost popů ukládat odpověď do mezipaměti ze původního serveru.
- Nastavení časové jednotky na "Vypnuto" tuto funkci zakáže. `Cache-Control` Hlavičky `Expires` a hlavičky uložené v mezipaměti s odpovědí původního serveru budou přecházet do prohlížeče.

**Výchozí chování:** Vypnuto

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Sledovat přesměrování

**Účel:** Určuje, zda mohou být požadavky přesměrovány na název hostitele definovaný v hlavičce Umístění vrácené serverem původu zákazníka.

Klíčové informace:

- Požadavky lze přesměrovat pouze na hraniční CNAMEs, které odpovídají stejné platformě.

Hodnota|Výsledek
-|-
Povoleno|Požadavky lze přesměrovat.
Zakázáno|Požadavky nebudou přesměrovány.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Vynutit vnitřní maximální stáří

**Účel:** Určuje maximální věkovou délku pro opětovné ověření mezipaměti serveru POP na zdrojový původ. Jinými slovy, doba, která uplyne před pop můžete zkontrolovat, zda prostředek uložený v mezipaměti odpovídá prostředek uložený na zdrojovém serveru.

Klíčové informace:

- Tato funkce přepíše maximální věkovou `Cache-Control` přestávku definovanou v aplikaci nebo `Expires` záhlaví generovaná ze serveru původu.
- Tato funkce nemá vliv na obnovení mezipaměti PROTOKOLU POP prohlížeče. Tyto typy prodloužení platnosti jsou `Cache-Control` `Expires` určeny nebo záhlaví odeslané do prohlížeče.
- Tato funkce nemá pozorovatelný vliv na odpověď dodanou pop žadateli. Může však mít vliv na množství přenosů pro opětovné ověření odeslaných z pole POP na zdrojový server.
- Tuto funkci nakonfigurujte takto:
    - Výběr stavového kódu, pro který bude použit interní maximální stáří.
    - Zadání celé hodnoty a výběr požadované časové jednotky (například sekundy, minuty, hodiny atd.). Tato hodnota definuje maximální věkový interval požadavku.

- Nastavení časové jednotky na "Vypnuto" tuto funkci zakáže. K požadovanému majetku nebude přiřazen interní interval maximálního věku. Pokud původní záhlaví neobsahuje pokyny pro ukládání do mezipaměti, bude datový zdroj uložen do mezipaměti podle aktivního nastavení ve funkci Výchozí vnitřní maximální stáří.

**Výchozí chování:** Vypnuto

#### <a name="compatibility"></a>Kompatibilita

Vzhledem ke způsobu sledování nastavení mezipaměti nelze tuto funkci přidružit k následujícím podmínkám shody:
- Číslo AS
- Client IP Address
- Parametr souboru cookie
- Regex parametru souboru cookie
- Země
- Zařízení
- Název hrany C
- Odkazující doména
- Literál hlavičky požadavku
- Hlavička požadavku Regex
- Zástupný znak záhlaví požadavku
- Request – metoda
- Schéma žádostí
- Literál dotazu URL
- Regex dotazu URL
- Zástupný znak dotazu URL
- Parametr dotazu URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>Podpora H.264 (http progresivní stahování)

**Účel:** Určuje typy formátů souborů H.264, které mohou být použity k streamování obsahu.

Klíčové informace:

- V možnosti Přípony souborů definujte sadu povolených přípon názvů souborů H.264. Možnost Přípony souborů přepíše výchozí chování. Udržujte podporu MP4 a F4V zahrnutím těchto přípon názvů souborů při nastavování této možnosti.
- Při zadávání každé přípony názvu souboru (například _.mp4_, _.f4v)_ zahrňte tečku.

**Výchozí chování:** Http Progressive Download ve výchozím nastavení podporuje média MP4 a F4V.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Požadavek na nedoletovou službu

**Účel:** Určuje, zda budou požadavky klienta HTTP bez mezipaměti předány na zdrojový server.

Požadavek bez mezipaměti nastane, když `Cache-Control: no-cache` klient `Pragma: no-cache` HTTP odešle a/nebo záhlaví v požadavku HTTP.

Hodnota|Výsledek
--|--
Povoleno|Umožňuje předávání požadavků klienta bez mezipaměti klienta HTTP na zdrojový server a zdrojový server vrátí hlavičky odpovědí a tělo prostřednictvím protokolu POP zpět klientovi HTTP.
Zakázáno|Obnoví výchozí chování. Výchozí chování je zabránit žádné mezipaměti požadavky předávané na zdrojový server.

Pro všechny produkční provoz, důrazně doporučujeme ponechat tuto funkci ve výchozím zakázaném stavu. V opačném případě nebudou servery původu chráněny před koncovými uživateli, kteří mohou neúmyslně spustit mnoho požadavků bez mezipaměti při aktualizaci webových stránek, nebo před mnoha populárními přehrávači médií, které jsou kódovány tak, aby při každém požadavku na video odeslaly hlavičku bez mezipaměti. Tato funkce však může být užitečná pro použití v určitých neprodukčních pracovních nebo testovacích adresářích, aby bylo možné získat nový obsah na vyžádání ze zdrojového serveru.

Stav mezipaměti, který je hlášen pro požadavek, který může být `TCP_Client_Refresh_Miss`předán na zdrojový server z důvodu této funkce, je . Sestava Stavy mezipaměti, která je k dispozici v modulu vykazování jádra, poskytuje statistické informace podle stavu mezipaměti. Tato sestava umožňuje sledovat počet a procento požadavků, které jsou předávány na zdrojový server z důvodu této funkce.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignorovat origin bez mezipaměti

**Účel:** Určuje, zda bude cdn ignorovat následující direktivy obsluhované ze zdrojového serveru:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Klíčové informace:

- Nakonfigurujte tuto funkci definováním seznamu stavových kódů oddělených mezerami, pro které budou výše uvedené direktivy ignorovány.
- Sada platných stavových kódů pro tuto funkci je: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 a 505.
- Tuto funkci zakažte nastavením na prázdnou hodnotu.

**Výchozí chování:** Výchozí chování je ctít výše uvedené direktivy.

#### <a name="compatibility"></a>Kompatibilita

Vzhledem ke způsobu sledování nastavení mezipaměti nelze tuto funkci přidružit k následujícím podmínkám shody:
- Číslo AS
- Client IP Address
- Parametr souboru cookie
- Regex parametru souboru cookie
- Země
- Zařízení
- Název hrany C
- Odkazující doména
- Literál hlavičky požadavku
- Hlavička požadavku Regex
- Zástupný znak záhlaví požadavku
- Request – metoda
- Schéma žádostí
- Literál dotazu URL
- Regex dotazu URL
- Zástupný znak dotazu URL
- Parametr dotazu URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignorovat nenasytné rozsahy

**Účel:** Určuje odpověď, která bude vrácena klientům, když požadavek vygeneruje 416 Požadovaný rozsah není satisfiable stavový kód.

Ve výchozím nastavení je tento stavový kód vrácen, pokud zadaný požadavek na rozsah bajtů nemůže být splněn protokolem POP a nebylo zadáno pole hlavičky požadavku If-Range.

Hodnota|Výsledek
-|-
Povoleno|Zabrání popům reagovat na neplatný požadavek na rozsah bajtů pomocí stavového kódu 416 Requested Range Not Satisable. Místo toho servery dodají požadovaný datový zdroj a vrátí klientovi 200 OK.
Zakázáno|Obnoví výchozí chování. Výchozí chování je respektovat 416 Požadovaný rozsah není satisfiable stavový kód.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Interní maximální zatuchlý

**Účel:** Určuje, jak dlouho po normální době vypršení platnosti může být prostředek uložený v mezipaměti obsluhován z bodu POP, pokud protokol POP nemůže znovu ověřit datový zdroj uložený v mezipaměti se zdrojovým serverem.

Za normálních okolností, když vyprší maximální stáří datového zdroje, pop odešle žádost o prodloužení platnosti na zdrojový server. Zdrojový server pak odpoví buď 304 Nezměněno, aby pop nový zapůjčení na prostředek uložené v mezipaměti, nebo jinak s 200 OK poskytnout POP s aktualizovanou verzi prostředku uloženého v mezipaměti.

Pokud protokol POP nemůže navázat spojení se zdrojovým serverem při pokusu o takové opětovné ověření, pak tato funkce Internal Max-Stale určuje, zda a na jak dlouho může protokol POP nadále obsluhovat nyní zastaralý prostředek.

Všimněte si, že tento časový interval začíná, když vyprší maximální stáří datového zdroje, nikoli při selhání opětovného ověření. Maximální doba, po kterou může být majetek doručen bez úspěšného prodloužení platnosti, je tedy doba určená kombinací maximálního stáří plus maximálního zatuchlého majetku. Pokud byl například datový zdroj uložen do mezipaměti v 9:00 s maximálním stářím 30 minut a maximálním zatuchlým 15 minutem, pak neúspěšný pokus o prodloužení platnosti v 9:44 by vedl k tomu, že by koncový uživatel obdržel zastaralý prostředek uložený v mezipaměti, zatímco neúspěšný pokus o opětovné ověření v 9:46 by vedl k selhání koncového uživatele, který obdrží časový výtok brány 504.

Každá hodnota nakonfigurovaná pro `Cache-Control: must-revalidate` tuto `Cache-Control: proxy-revalidate` funkci je nahrazena záhlavími přijatými z původního serveru. Pokud je některá z těchto hlaviček přijata ze zdrojového serveru při počátečním ukládání datového zdroje do mezipaměti, nebude protokol POP sloužit zastaralému prostředku uloženému v mezipaměti. V takovém případě pokud pop není schopen znovu ověřit s původem po vypršení maximálního stáří datového zdroje, pop vrátí chybu vypršení časového limitu brány 504.

Klíčové informace:

- Tuto funkci nakonfigurujte takto:
    - Výběr stavového kódu, pro který bude použit maximální zatuchlý kód.
    - Zadání celé hodnoty a výběr požadované časové jednotky (například sekundy, minuty, hodiny atd.). Tato hodnota definuje vnitřní maximální zastaralou hodnotu, která bude použita.

- Nastavení časové jednotky na "Vypnuto" tuto funkci zakáže. Prostředek uložený v mezipaměti nebude doručen po jeho normální době vypršení platnosti.

**Výchozí chování:** Dvě minuty

#### <a name="compatibility"></a>Kompatibilita

Vzhledem ke způsobu sledování nastavení mezipaměti nelze tuto funkci přidružit k následujícím podmínkám shody:
- Číslo AS
- Client IP Address
- Parametr souboru cookie
- Regex parametru souboru cookie
- Země
- Zařízení
- Název hrany C
- Odkazující doména
- Literál hlavičky požadavku
- Hlavička požadavku Regex
- Zástupný znak záhlaví požadavku
- Request – metoda
- Schéma žádostí
- Literál dotazu URL
- Regex dotazu URL
- Zástupný znak dotazu URL
- Parametr dotazu URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Řetězec dotazu protokolu

**Účel:** Určuje, zda bude řetězec dotazu uložen spolu s adresou URL v protokolech přístupu.

Hodnota|Výsledek
-|-
Povoleno|Umožňuje ukládání řetězců dotazů při zaznamenávání adres URL v protokolu přístupu. Pokud adresa URL neobsahuje řetězec dotazu, nebude mít tato možnost vliv.
Zakázáno|Obnoví výchozí chování. Výchozí chování je ignorovat řetězce dotazu při nahrávání adres URL v protokolu přístupu.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximální požadavky na udržení naživu

**Účel:** Definuje maximální počet požadavků na připojení Keep-Alive před jeho zavřením.

Nastavení maximálního počtu požadavků na nízkou hodnotu se nedoporučuje a může vést ke snížení výkonu.

Klíčové informace:

- Zadejte tuto hodnotu jako celé celé číslo.
- Do zadané hodnoty nezahrnte čárky ani tečky.

**Výchozí hodnota:** 10 000 požadavků

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Změnit hlavičku požadavku klienta

**Účel:** Každý požadavek obsahuje sadu hlaviček požadavků, které jej popisují. Tato funkce může buď:

- Připojit nebo přepsat hodnotu přiřazenou záhlaví požadavku. Pokud zadaná hlavička požadavku neexistuje, tato funkce ji do něj přidá.
- Odstraňte hlavičku požadavku z požadavku.

Požadavky, které jsou předány na zdrojový server, budou odrážet změny provedené touto funkcí.

V hlavičce požadavku lze provést jednu z následujících akcí:

Možnost|Popis|Příklad
-|-|-
Připojit|Zadaná hodnota bude přidána na konec existující hodnoty hlavičky požadavku.|**Hodnota hlavičky požadavku (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky požadavku (modul pravidel):**<br/>Hodnota2 <br/>**Nová hodnota hlavičky požadavku:** <br/>Hodnota1Value2
Přepsat|Hodnota hlavičky požadavku bude nastavena na zadanou hodnotu.|**Hodnota hlavičky požadavku (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky požadavku (modul pravidel):**<br/>Hodnota2<br/>**Nová hodnota hlavičky požadavku:**<br/> Hodnota2 <br/>
Odstranění|Odstraní zadanou hlavičku požadavku.|**Hodnota hlavičky požadavku (klient):**<br/>Hodnota1<br/>**Změna konfigurace hlavičky požadavku klienta:**<br/>Odstraňte dotyčnou hlavičku požadavku.<br/>**Výsledek:**<br/>Zadaná hlavička požadavku nebude předána na zdrojový server.

Klíčové informace:

- Ujistěte se, že hodnota zadaná v name možnost je přesná shoda pro hlavičku požadavku požadovaný.
- Případ se nebere v úvahu pro účely identifikace záhlaví. K jeho identifikaci lze například `Cache-Control` použít některou z následujících variant názvu záhlaví:
    - řízení mezipaměti
    - ŘÍZENÍ MEZIPAMĚTI
    - cachE-Control
- Při zadávání názvu záhlaví používejte pouze alfanumerické znaky, pomlčky nebo podtržítka.
- Odstranění záhlaví zabrání jeho předání na zdrojový server pomocí pole POP.
- Následující záhlaví jsou vyhrazena a nelze je touto funkcí změnit:
    - Předány
    - host
    - Via
    - upozornění
    - x-forwarded-for
    - Všechny názvy záhlaví, které začínají "x-ec", jsou vyhrazeny.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Změnit hlavičku odpovědi klienta

Každá odpověď obsahuje sadu hlaviček odpovědí, které ji popisují. Tato funkce může buď:

- Připojit nebo přepsat hodnotu přiřazenou záhlaví odpovědi. Pokud zadaná hlavička odpovědi neexistuje, tato funkce ji přidá do odpovědi.
- Odstraňte z odpovědi hlavičku odpovědi.

Ve výchozím nastavení jsou hodnoty záhlaví odpovědi definovány zdrojovým serverem a popy POP.

V hlavičce odpovědi lze provést jednu z následujících akcí:

Možnost|Popis|Příklad
-|-|-
Připojit|Zadaná hodnota bude přidána na konec existující hodnoty hlavičky odpovědi.|**Hodnota hlavičky odpovědi (klient):**<br />Hodnota1<br/>**Hodnota hlavičky odpovědi (modul pravidel):**<br/>Hodnota2<br/>**Nová hodnota hlavičky odpovědi:**<br/>Hodnota1Value2
Přepsat|Hodnota hlavičky odpovědi bude nastavena na zadanou hodnotu.|**Hodnota hlavičky odpovědi (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky odpovědi (modul pravidel):**<br/>Hodnota2 <br/>**Nová hodnota hlavičky odpovědi:**<br/>Hodnota2 <br/>
Odstranění|Odstraní zadanou hlavičku odpovědi.|**Hodnota hlavičky odpovědi (klient):**<br/>Hodnota1<br/>**Změna konfigurace hlavičky odpovědi klienta:**<br/>Odstraňte dotyčnou hlavičku odpovědi.<br/>**Výsledek:**<br/>Zadaná hlavička odpovědi nebude předána žadateli.

Klíčové informace:

- Ujistěte se, že hodnota zadaná v name možnost je přesná shoda pro požadovanou hlavičku odpovědi.
- Případ se nebere v úvahu pro účely identifikace záhlaví. K jeho identifikaci lze například `Cache-Control` použít některou z následujících variant názvu záhlaví:
    - řízení mezipaměti
    - ŘÍZENÍ MEZIPAMĚTI
    - cachE-Control
- Odstranění záhlaví zabrání jeho předání žadateli.
- Následující záhlaví jsou vyhrazena a nelze je touto funkcí změnit:
    - přijmout kódování
    - věk
    - připojení
    - kódování obsahu
    - délka obsahu
    - obsah-rozsah
    - date
    - server
    - Přívěs
    - kódování přenosu
    - Upgrade
    - Lišit
    - Via
    - upozornění
    - Všechny názvy záhlaví, které začínají "x-ec", jsou vyhrazeny.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Částečné sdílení mezipaměti

**Účel:** Určuje, zda může požadavek generovat částečně uložený obsah v mezipaměti.

Tato částečná mezipaměť pak může být použita ke splnění nových požadavků pro tento obsah, dokud nebude požadovaný obsah plně uložen do mezipaměti.

Hodnota|Výsledek
-|-
Povoleno|Požadavky mohou generovat částečně uložený obsah v mezipaměti.
Zakázáno|Požadavky mohou generovat pouze plně uloženou verzi požadovaného obsahu v mezipaměti.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Předběžná platnost obsahu uloženého v mezipaměti

**Účel:** Určuje, zda bude obsah uložený v mezipaměti způsobilý k předčasnému opětovnému ověření platnosti před vypršením platnosti ttl.

Definujte dobu před vypršením platnosti ttl požadovaného obsahu, během které bude mít nárok na předčasné prodloužení platnosti.

Klíčové informace:

- Výběr "Vypnuto" jako časové jednotky vyžaduje opětovné ověření, aby se uskutečnilo po vypršení ttl obsahu uloženého v mezipaměti. Čas by neměl být zadán a je ignorován.

**Výchozí chování:** Vypnuto. K opětovnému ověření může dojít až po vypršení platnosti ttl obsahu uloženého v mezipaměti.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Speciální záhlaví proxy serveru

**Účel:** Definuje sadu [hlavičky požadavků HTTP specifické pro společnost Verizon,](cdn-verizon-http-headers.md) které budou předány z bodu POP na zdrojový server.

Klíčové informace:

- Každá hlavička požadavku specifické pro CDN definovaná v této funkci je předána na zdrojový server. Vyloučená záhlaví nejsou předána dál.
- Chcete-li zabránit předání hlavičky požadavku specifické pro CDN, odeberte ji ze seznamu oddělených místa v poli seznamu záhlaví.

Následující hlavičky PROTOKOLU HTTP jsou zahrnuty ve výchozím seznamu:
- Via
- X-Forwarded-Pro
- X-Forwarded-Proto
- X-Hostitel
- X-Středový průnik
- Seznam brány X
- Název X-EC
- Hostitel

**Výchozí chování:** Všechny hlavičky požadavků specifických pro CDN budou předány na zdrojový server.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Aktualizovat soubory mezipaměti s nulovým bajtem

**Účel:** Určuje, jak je požadavek klienta HTTP na prostředek mezipaměti 0 bajtů zpracován pomocí polí POP.

Platné hodnoty jsou:

Hodnota|Výsledek
--|--
Povoleno|Způsobí, že pop znovu načíst prostředek z původního serveru.
Zakázáno|Obnoví výchozí chování. Výchozí chování je sloužit až platné mezipaměti prostředky na požádání.

Tato funkce není vyžadována pro správné ukládání do mezipaměti a doručování obsahu, ale může být užitečné jako řešení. Například generátory dynamického obsahu na zdrojových serverech mohou neúmyslně způsobit, že 0bajtové odpovědi budou odeslány do polí POP. Tyto typy odpovědí jsou obvykle ukládány do mezipaměti v polích POP. Pokud víte, že 0bajtová odpověď není nikdy platnou odpovědí pro takový obsah, může tato funkce zabránit tomu, aby byly tyto typy datových zdrojů obsluhovány vašim klientům.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Nastavení stavových kódů s mezipamětí

**Účel:** Definuje sadu stavových kódů, které mohou mít za následek obsah uložený v mezipaměti.

Ve výchozím nastavení je ukládání do mezipaměti povoleno pouze pro 200 odpovědí OK.

Definujte sadu požadovaných stavových kódů oddělených mezerami.

Klíčové informace:

- Povolte funkci Ignorovat origin bez mezipaměti. Pokud tato funkce není povolena, nemusí být odpovědi BEZ 200 OK uloženy do mezipaměti.
- Sada platných stavových kódů pro tuto funkci je: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 a 505.
- Tuto funkci nelze použít k zakázání ukládání do mezipaměti pro odpovědi, které generují stavový kód 200 OK.

**Výchozí chování:** Ukládání do mezipaměti je povoleno pouze pro odpovědi, které generují stavový kód 200 OK.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Nastavení vlastní hlavičky IP klienta

**Účel:** Přidá vlastní záhlaví, které identifikuje žádající klienta podle IP adresy k požadavku.

Možnost Název záhlaví definuje název hlavičky vlastního požadavku, kde je uložena IP adresa klienta.

Tato funkce umožňuje serveru původu zákazníka zjistit ip adresy klienta prostřednictvím vlastní hlavičky požadavku. Pokud je požadavek obsluhován z mezipaměti, zdrojový server nebude informován o IP adrese klienta. Proto se doporučuje, aby tato funkce byla použita s prostředky, které nejsou uloženy v mezipaměti.

Ujistěte se, že zadaný název záhlaví neodpovídá žádnému z následujících názvů:

- Standardní názvy hlaviček požadavku. Seznam standardních názvů hlaviček naleznete v [souboru RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Vyhrazené názvy hlaviček:
    - předáno
    - host
    - Lišit
    - Via
    - upozornění
    - x-forwarded-for
    - Všechny názvy záhlaví, které začínají "x-ec", jsou vyhrazeny.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Zastaralé doručování obsahu při chybě

**Účel:** Určuje, zda bude obsah uložený v mezipaměti, jehož platnost vypršela, pokud dojde k chybě během opětovného ověření mezipaměti nebo při načítání požadovaného obsahu ze serveru původu zákazníka.

Hodnota|Výsledek
-|-
Povoleno|Zastaralý obsah se žadateli zobrazí, když dojde k chybě během připojení k zdrojovému serveru.
Zakázáno|Chyba původního serveru je předána žadateli.

**Výchozí chování:** Zakázán

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Zastaralé při opětovném ověření

**Účel:** Zlepšuje výkon tím, že umožňuje POP sloužit zastaralý obsah žadateli, zatímco probíhá opětovné ověření.

Klíčové informace:

- Chování této funkce se liší v závislosti na vybrané časové jednotce.
    - **Časová jednotka:** Zadejte dobu času a vyberte časovou jednotku (například Sekundy, Minuty, Hodiny atd.), abyste povolili doručování zastaralého obsahu. Tento typ instalace umožňuje síti CDN prodloužit dobu, po kterou může poskytovat obsah, než bude vyžadovat ověření podle následujícího vzorce: **TTL** + **Stale While Revalidate Time**
    - **Vypnuto:** Vyberte "Vypnuto", chcete-li vyžadovat prodloužení platnosti, než může být doručena žádost o zastaralý obsah.
        - Nezadávejte dobu, protože je nepoužitelná a bude ignorována.

**Výchozí chování:** Vypnuto. Prodloužení platnosti musí proběhnut dříve, než může být požadovaný obsah doručen.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token ušitné

**Účel:** Určuje, zda bude u požadavku použito ověřování na základě tokenu.

Pokud je povoleno ověřování na základě tokenu, budou dodrženy pouze požadavky, které poskytují šifrovaný token a splňují požadavky určené tímto tokenem.

Šifrovací klíč, který se používá k šifrování a dešifrování hodnot tokenů, je určen možnostmi Primární klíč a Záložní klíč na stránce Token Auth. Mějte na paměti, že šifrovací klíče jsou specifické pro platformu.

**Výchozí chování:** Zakázán.

Tato funkce má přednost před většinou funkcí s výjimkou funkce přepisu adresy URL.

Hodnota | Výsledek
------|---------
Povoleno | Chrání požadovaný obsah pomocí ověřování na základě tokenu. Budou respektovány pouze požadavky od klientů, kteří poskytují platný token a splňují jeho požadavky. Transakce FTP jsou vyloučeny z ověřování na základě tokenu.
Zakázáno| Obnoví výchozí chování. Výchozí chování je umožnit konfiguraci ověřování na základě tokenu k určení, zda bude požadavek zabezpečen.

#### <a name="compatibility"></a>Kompatibilita

Nepoužívejte token ušitý s vždy odpovídat podmínku.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Kód zamítnutí tokenu auth

**Účel:** Určuje typ odpovědi, která bude vrácena uživateli, když je požadavek odepřen z důvodu ověřování na základě tokenu.

Dostupné kódy odpovědí jsou uvedeny v následující tabulce.

Kód odpovědi|Název odpovědi|Popis
-------------|-------------|--------
301|Trvale přesunuto|Tento stavový kód přesměruje neoprávněné uživatele na adresu URL zadanou v hlavičce Umístění.
302|Found|Tento stavový kód přesměruje neoprávněné uživatele na adresu URL zadanou v hlavičce Umístění. Tento stavový kód je standardní metoda provádění přesměrování.
307|Dočasné přesměrování|Tento stavový kód přesměruje neoprávněné uživatele na adresu URL zadanou v hlavičce Umístění.
401|Neautorizováno|Kombinace tohoto stavového kódu s hlavičkou odpovědi WWW-Authenticate umožňuje vyzvat uživatele k ověření.
403|Forbidden|Tato zpráva je standardní zpráva 403 Zakázaný stav, kterou se neoprávněný uživatel zobrazí při pokusu o přístup k chráněnému obsahu.
404|Soubor nebyl nalezen.|Tento stavový kód označuje, že klient HTTP byl schopen komunikovat se serverem, ale požadovaný obsah nebyl nalezen.

#### <a name="compatibility"></a>Kompatibilita

Nepoužívejte kód odmítnutí tokenu s podmínkou Vždy shodovat. Místo toho použijte **oddíl Vlastní zpracování odmítnutí** na stránce Token **Auth** na portálu **Pro správu.** Další informace naleznete [v tématu Zabezpečení prostředků Azure CDN pomocí ověřování tokenů](cdn-token-auth.md).

#### <a name="url-redirection"></a>Přesměrování adresy URL

Tato funkce podporuje přesměrování adresy URL na uživatelem definovanou adresu URL, pokud je nakonfigurována tak, aby vrátila stavový kód 3xx. Tuto uživatelem definovanou adresu URL lze zadat provedením následujících kroků:

1. Vyberte kód odpovědi 3xx pro funkci Token Auth Denial Code.
2. Z možnosti Název volitelné hlavičky vyberte možnost Umístění.
3. Nastavte možnost Volitelná hodnota záhlaví na požadovanou adresu URL.

Pokud adresa URL není definována pro stavový kód 3xx, bude uživateli vrácena standardní stránka odpovědi pro stavový kód 3xx.

Přesměrování adresy URL je použitelné pouze pro kódy odpovědí 3xx.

Volba Volitelná hodnota záhlaví podporuje alfanumerické znaky, uvozovky a mezery.

#### <a name="authentication"></a>Ověřování

Tato funkce podporuje možnost zahrnout hlavičku WWW-Authenticate při odpovídání na neoprávněný požadavek na obsah chráněný ověřováním na základě tokenu. Pokud byla hlavička WWW-Authenticate v konfiguraci nastavena na "základní", bude neoprávněný uživatel vyzván k zadání pověření účtu.

Výše uvedené konfigurace lze dosáhnout provedením následujících kroků:

1. Vyberte "401" jako kód odpovědi pro funkci Token Auth Denial Code.
2. Z možnosti Název volitelné hlavičky vyberte možnost WWW-Authenticate.
3. Nastavte možnost Volitelná hodnota záhlaví na "základní".

Hlavička WWW-Authenticate je použitelná pouze pro kódy odpovědí 401.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Token Auth Ignorovat url případ

**Účel:** Určuje, zda porovnání adres URL provedené ověřováním na základě tokenu rozlišují malá a velká písmena.

Parametry ovlivněné touto funkcí jsou:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Platné hodnoty jsou:

Hodnota|Výsledek
---|----
Povoleno|Způsobí, že pop ignorovat případ při porovnávání adres URL pro parametry ověřování na základě tokenu.
Zakázáno|Obnoví výchozí chování. Výchozí chování je pro porovnání adres URL pro ověřování tokenu, které mají být malá a velká písmena.

**Výchozí chování:** Zakázán.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parametr tokenu auth

**Účel:** Určuje, zda má být přejmenován parametr řetězce dotazu ověřování na základě tokenu.

Klíčové informace:

- Možnost Hodnota definuje název parametru řetězce dotazu, jehož prostřednictvím může být zadán token.
- Možnost Hodnota nelze nastavit na "ec_token".
- Ujistěte se, že název definovaný v možnosti Hodnota obsahuje pouze platné znaky adresy URL.

Hodnota|Výsledek
----|----
Povoleno|Možnost Hodnota definuje název parametru řetězce dotazu, jehož prostřednictvím by měly být definovány tokeny.
Zakázáno|Token může být zadán jako nedefinovaný parametr řetězce dotazu v adrese URL požadavku.

**Výchozí chování:** Zakázán. Token může být zadán jako nedefinovaný parametr řetězce dotazu v adrese URL požadavku.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Přesměrování adresy URL

**Účel:** Přesměruje požadavky prostřednictvím hlavičky Umístění.

Konfigurace této funkce vyžaduje nastavení následujících možností:

Možnost|Popis
-|-
kód|Vyberte kód odpovědi, který bude vrácen žadateli.
Vzor & zdroje| Tato nastavení definují vzor IDENTIFIKÁTORU URI požadavku, který identifikuje typ požadavků, které mohou být přesměrovány. Budou přesměrovány pouze požadavky, jejichž adresa URL splňuje obě následující kritéria: <br/> <br/> **Zdroj (nebo přístupový bod obsahu):** Vyberte relativní cestu, která identifikuje zdrojový server. Tato cesta je _oddíl /XXXX/_ a název koncového bodu. <br/><br/> **Zdroj (vzor):** Musí být definován vzor, který identifikuje požadavky podle relativní cesty. Tento vzor regulárního výrazu musí definovat cestu, která začíná přímo za dříve vybraným přístupovým bodem obsahu (viz výše). <br/> - Ujistěte se, že dříve definovaná kritéria identifikátoru URI požadavku (tj. vzor zdroje &) nejsou v konfliktu s podmínkami shody definovanými pro tuto funkci. <br/> - Zadejte vzor; pokud jako vzorek použijete prázdnou hodnotu, budou všechny řetězce spárovány.
Cíl| Definujte adresu URL, na kterou budou výše uvedené požadavky přesměrovány. <br/><br/> Dynamicky vytvářet tuto adresu URL pomocí: <br/> - Vzor regulárního výrazu <br/>- [Proměnné HTTP](cdn-http-variables.md) <br/><br/> Nahraďte hodnoty zachycené ve zdrojovém vzoru do cílového vzoru pomocí $_n,_ kde _n_ identifikuje hodnotu podle pořadí, ve kterém byla zachycena. Například $1 představuje první hodnotu zachycenou ve zdrojovém vzoru, zatímco $2 představuje druhou hodnotu. <br/>

Důrazně doporučujeme použít absolutní adresu URL. Použití relativní adresy URL může přesměrovat adresy URL CDN na neplatnou cestu.

**Ukázkový scénář**

Tento příklad ukazuje, jak přesměrovat adresu URL CNAME edge, která se\/překládá na tuto základní adresu URL CDN: http: /marketing.azureedge.net/brochures

Kvalifikační požadavky budou přesměrovány na tuto základní okraj\/ovou adresu URL CNAME: http: /cdn.mydomain.com/resources

Tohoto přesměrování adresy URL lze dosáhnout ![prostřednictvím následující konfigurace: Přesměrování adresy URL](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Klíčové body:**

- Funkce Přesměrování adresy URL definuje adresy URL požadavků, které budou přesměrovány. V důsledku toho nejsou vyžadovány další podmínky shody. Přestože podmínka shody byla definována jako "Vždy", budou přesměrovány pouze požadavky, které odkazují na složku "brožury" v původu zákazníka "marketing".
- Všechny odpovídající požadavky budou přesměrovány na hraniční adresu URL CNAME definovanou v možnosti Cíl.
    - Ukázkový scénář #1:
        - Ukázkový požadavek (CDN\/URL): http: /marketing.azureedge.net/brochures/widgets.pdf
        - Url požadavku (po přesměrování):\/http: /cdn.mydomain.com/resources/widgets.pdf  
    - Ukázkový scénář #2:
        - Ukázkový požadavek (adresa URL\/Edge CNAME): http: /marketing.mydomain.com/brochures/widgets.pdf
        - Adresa URL požadavku (po\/přesměrování): http: /cdn.mydomain.com/resources/widgets.pdf Ukázkový scénář
    - Ukázkový scénář #3:
        - Ukázkový požadavek (adresa URL\/Edge CNAME): http: /brochures.mydomain.com/campaignA/final/productC.ppt
        - Url požadavku (po přesměrování):\/http: /cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- Proměnná Schéma požadavků (%{scheme}) je využita v možnosti Cíl, která zajišťuje, že schéma požadavku zůstane po přesměrování nezměněno.
- Segmenty adresy URL, které byly zachyceny z požadavku, jsou připojeny k nové adrese URL prostřednictvím "$1".

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>Přepsání adresy URL

**Účel:** Přepíše adresu URL požadavku.

Klíčové informace:

- Konfigurace této funkce vyžaduje nastavení následujících možností:

Možnost|Popis
-|-
 Vzor & zdroje | Tato nastavení definují vzor IDENTIFIKÁTORU URI požadavku, který identifikuje typ požadavků, které mohou být přepsány. Přepíší se pouze požadavky, jejichž adresa URL splňuje obě následující kritéria: <br/><br/>  - **Zdroj (nebo přístupový bod obsahu):** Vyberte relativní cestu, která identifikuje zdrojový server. Tato cesta je _oddíl /XXXX/_ a název koncového bodu. <br/><br/> - **Zdroj (vzor):** Musí být definován vzor, který identifikuje požadavky podle relativní cesty. Tento vzor regulárního výrazu musí definovat cestu, která začíná přímo za dříve vybraným přístupovým bodem obsahu (viz výše). <br/> Ověřte, zda dříve definovaná kritéria identifikátoru URI požadavku (tj. vzor zdroje &) nejsou v konfliktu s žádnou z podmínek shody definovaných pro tuto funkci. Určete vzorek; pokud jako vzorek použijete prázdnou hodnotu, budou všechny řetězce spárovány.
 Cíl  |Definujte relativní adresu URL, na kterou budou výše uvedené požadavky přepsány: <br/>    1. Výběr přístupového bodu obsahu, který identifikuje zdrojový server. <br/>    2. Definování relativní cesty pomocí: <br/>        - Vzor regulárního výrazu <br/>        - [Proměnné HTTP](cdn-http-variables.md) <br/> <br/> Nahraďte hodnoty zachycené ve zdrojovém vzoru do cílového vzoru pomocí $_n,_ kde _n_ identifikuje hodnotu podle pořadí, ve kterém byla zachycena. Například $1 představuje první hodnotu zachycenou ve zdrojovém vzoru, zatímco $2 představuje druhou hodnotu.

 Tato funkce umožňuje popům přepsání adresy URL bez provedení tradičního přesměrování. To znamená, že žadatel obdrží stejný kód odpovědi, jako kdyby byla požadována přepsaná adresa URL.

**Vzorový scénář 1**

Tento příklad ukazuje, jak přesměrovat adresu URL CNAME edge, která se\/překládá na tuto základní adresu URL CDN: http: /marketing.azureedge.net/brochures/

Kvalifikační požadavky budou přesměrovány na tuto základní okraj\/ovou adresu URL CNAME: http: /MyOrigin.azureedge.net/resources/

Tohoto přesměrování adresy URL lze dosáhnout ![prostřednictvím následující konfigurace: Přesměrování adresy URL](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Vzorový scénář 2**

Tento příklad ukazuje, jak přesměrovat okraj CNAME URL z velkých písmen na malá písmena pomocí regulárních výrazů.

Tohoto přesměrování adresy URL lze dosáhnout ![prostřednictvím následující konfigurace: Přesměrování adresy URL](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Klíčové body:**

- Funkce přepisování adres URL definuje adresy URL požadavků, které budou přepsány. V důsledku toho nejsou vyžadovány další podmínky shody. Přestože podmínka shody byla definována jako "Vždy", budou přepsány pouze požadavky, které odkazují na složku "brožury" v původu zákazníka "marketing".

- Segmenty adresy URL, které byly zachyceny z požadavku, jsou připojeny k nové adrese URL prostřednictvím "$1".

#### <a name="compatibility"></a>Kompatibilita

Tato funkce obsahuje odpovídající kritéria, která musí být splněna, aby mohla být použita na požadavek. Aby se zabránilo nastavení konfliktních kritérií shody, není tato funkce kompatibilní s následujícími podmínkami shody:

- Číslo AS
- Původ CDN
- Client IP Address
- Původ zákazníka
- Schéma žádostí
- Adresář cesty URL
- Rozšíření cesty URL
- Název souboru cesty URL
- Literál cesty URL
- Regex cesty URL
- Zástupný znak cesty URL
- Literál dotazu URL
- Parametr dotazu URL
- Regex dotazu URL
- Zástupný znak dotazu URL

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Uživatelská proměnná

**Účel:** Pouze pro vnitřní použití.

[Zpět na začátek](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Další kroky

- [Odkaz na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmíněné výrazy modulu pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Pravidla, které jsou v souladu s podmínkami motoru](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Přepsat chování PROTOKOLU HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled azure cdn](cdn-overview.md)