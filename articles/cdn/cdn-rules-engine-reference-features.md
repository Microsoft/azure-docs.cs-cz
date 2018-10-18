---
title: Funkce stroje pravidel Azure CDN | Dokumentace Microsoftu
description: Funkce stroje pravidel referenční dokumentaci pro Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: d5be292c66a07f43b40d12e10e4939d9d91559e1
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395237"
---
# <a name="azure-cdn-rules-engine-features"></a>Funkce stroje pravidel Azure CDN
Tento článek obsahuje podrobný popis funkcí dostupných pro Azure Content Delivery Network (CDN) [stroj pravidel](cdn-rules-engine.md).

Třetí část pravidlo je funkce. Funkce definuje typ akce, která se uplatňuje na typu požadavku, která je identifikovaná sadu podmínky shody.

## <a name="access-features"></a>Získat přístup k funkcím

Tyto funkce jsou určeny k řízení přístupu k obsahu.

Název | Účel
-----|--------
[Odepřít přístup (403)](#deny-access-403) | Určuje, zda všechny požadavky byly zamítnuty 403 Zakázáno odpovědí.
[Ověření tokenu](#token-auth) | Určuje, zda je na žádost použito ověřování založené na tokenech.
[Kód s cílem odepření ověření tokenu](#token-auth-denial-code) | Určuje typ odpovědi, který je vrácen uživateli při požadavku byl odepřen v důsledku ověřování založené na tokenech.
[Ověření tokenu ignorovat velikost písmen adresy URL](#token-auth-ignore-url-case) | Určuje, zda adresa URL porovnání provedené při ověřování pomocí tokenu jsou malá a velká písmena.
[Parametr tokenu ověřování](#token-auth-parameter) | Určuje, zda by měl přejmenovat, parametru řetězce dotazu ověřování založené na tokenech.


## <a name="caching-features"></a>Ukládání do mezipaměti funkce

Tyto funkce jsou navržené tak, přizpůsobit a jak se obsah ukládá do mezipaměti.

Název | Účel
-----|--------
[Parametry šířky pásma](#bandwidth-parameters) | Určuje, zda jsou parametry omezení šířky pásma (například ec_rate a ec_prebuf) aktivní.
[Omezení šířky pásma](#bandwidth-throttling) | Omezuje šířku pásma pro odpověď v poskytnuté v point-of-presence (POP).
[Nepoužívat mezipaměť](#bypass-cache) | Určuje, zda by měl žádost nepoužívat ukládání do mezipaměti.
[Zpracování hlavičky Cache-Control](#cache-control-header-treatment) | Určuje generování `Cache-Control` záhlaví POP, když je aktivní funkce externí Max-Age.
[Řetězec dotazu klíč mezipaměti](#cache-key-query-string) | Určuje, zda klíč mezipaměti zahrnutí nebo vyloučení parametrů řetězce dotazu přidružený k požadavku.
[Přepište klíč mezipaměti](#cache-key-rewrite) | Přepíše mezipaměti – klíč přidružený k požadavku.
[Výplň kompletní mezipaměti](#complete-cache-fill) | Určuje, co se stane, když požadavek výsledkem neúspěšnému přístupu do částečné mezipaměti na místní nabídky.
[Komprese typy souborů](#compress-file-types) | Definuje formátů souborů, které jsou komprimované soubory na serveru.
[Interní výchozí Max-Age](#default-internal-max-age) | Určuje výchozí interval max-age pro POP Revalidace mezipaměti serveru původu.
[Vypršení platnosti zpracování záhlaví](#expires-header-treatment) | Určuje generování `Expires` záhlaví POP, když je aktivní funkce externí Max-Age.
[Externí Max-Age](#external-max-age) | Určuje interval max-age pro prohlížeč, aby Revalidace mezipaměti POP.
[Platnost vnitřní Max-Age](#force-internal-max-age) | Určuje interval max-age pro POP Revalidace mezipaměti serveru původu.
[Podpora H.264 (HTTP progresivního stahování)](#h264-support-http-progressive-download) | Určuje typy H.264 formátů souborů, které lze použít ke streamování obsahu.
[Dodržet No-Cache žádosti](#honor-no-cache-request) | Určuje, zda jsou předávány klienta HTTP bez mezipaměti požadavky na zdrojový server.
[Ignorovat původu No-Cache](#ignore-origin-no-cache) | Určuje, zda CDN ignoruje některé direktivy ze zdrojového serveru.
[Ignorovat Unsatisfiable rozsahy](#ignore-unsatisfiable-ranges) | Určuje, která je vrácena klientům, pokud žádost vygeneruje 416 požadovaný rozsah nelze uspokojit stavový kód odpovědi.
[Interní Nezastaralá Max](#internal-max-stale) | Ovládací prvky, jak dlouho po čase normální vypršení platnosti mezipaměti asset může obsluhovat z místní nabídky, když lokalita POP není schopen se znovu ověřit v mezipaměti asset s původním serveru.
[Sdílení částečné mezipaměti](#partial-cache-sharing) | Určuje, zda žádost lze generovat částečně uložené v mezipaměti obsahu.
[Prevalidate obsah uložený v mezipaměti](#prevalidate-cached-content) | Určuje, zda je obsah uložený v mezipaměti nárok na opětovné ověření dřívější než jeho hodnota TTL nevyprší.
[Aktualizace souborů z mezipaměti nula bajtů](#refresh-zero-byte-cache-files) | Určuje, jak se žádost klienta HTTP pro prostředek 0 bajtů mezipaměti zařizuje služba bodů POP.
[Nastavit lze uložit do mezipaměti stavové kódy](#set-cacheable-status-codes) | Definuje sadu stavové kódy, které může vést k obsahu v mezipaměti.
[Zastaralé doručování obsahu při chybě](#stale-content-delivery-on-error) | Určuje, zda vypršela platnost, že obsah uložený v mezipaměti je poskytována po dojde k chybě během Revalidace mezipaměti nebo při načítání požadovaného obsahu ze zdrojového serveru zákazníka.
[Zastaralá při Revalidate](#stale-while-revalidate) | Zvyšuje výkon tím, že bodů POP, která bude sloužit zastaralý klient žadateli během opětovné ověření.

## <a name="comment-feature"></a>Funkce komentáře

Tato funkce je určena pro poskytnutí dalších informací v rámci pravidla.

Název | Účel
-----|--------
[Komentář](#comment) | Umožňuje Poznámka k přidají v rámci pravidla.
 
## <a name="header-features"></a>Hlavička funkce

Tyto funkce slouží k přidání, úprava nebo odstranění hlaviček ze žádosti nebo odpovědi.

Název | Účel
-----|--------
[Hlavička odpovědi věk](#age-response-header) | Určuje, zda hlavičku odpovědi věk je součástí odpověď odesílanou žadateli.
[Ladění hlavičky odpovědi v mezipaměti](#debug-cache-response-headers) | Určuje, jestli odpověď může obsahovat hlavičku odpovědi X-ES-ladění, která poskytuje informace o zásady ukládání do mezipaměti pro požadovaný prostředek.
[Upravit hlavičku požadavku klienta](#modify-client-request-header) | Přepíše, připojí nebo odstraní hlavičku z požadavku.
[Upravit klienta hlavička odpovědi](#modify-client-response-header) | Přepíše, připojí nebo odstraní hlavičku z odpovědi.
[Nastavit vlastní záhlaví IP klienta](#set-client-ip-custom-header) | Umožňuje IP adresu klienta, který chcete přidat do žádosti jako hlavičku požadavku vlastní.


## <a name="logging-features"></a>Funkce protokolování

Tyto funkce slouží k přizpůsobení dat uložených v nezpracovaných souborů protokolu.

Název | Účel
-----|--------
[Protokol vlastní pole 1](#custom-log-field-1) | Určuje formát a obsah, který je přiřazen poli vlastní protokol v nezpracovaných souborů protokolu.
[Řetězec dotazu protokolu](#log-query-string) | Určuje, zda řetězec dotazu je uložen spolu s adresy URL v přístup k protokolům.


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

Tyto funkce jsou určeny k řízení, jak síť CDN komunikuje s původním serveru.

Název | Účel
-----|--------
[Maximální počet Keep-Alive požadavků](#maximum-keep-alive-requests) | Definuje maximální počet požadavků pro zachování připojení předtím, než je uzavřený.
[Speciálními záhlavími proxy](#proxy-special-headers) | Definuje sadu hlaviček požadavků specifické pro CDN, které jsou předávány z místní nabídky k serveru původu.


## <a name="specialty-features"></a>Speciální funkce

Tyto funkce umožňují pokročilé funkce pro zkušené uživatele.

Název | Účel
-----|--------
[Metody HTTP možné ukládat do mezipaměti](#cacheable-http-methods) | Určuje sadu další metody HTTP, které lze uložit do mezipaměti v síti.
[Velikost textu požadavku možné ukládat do mezipaměti](#cacheable-request-body-size) | Definuje prahovou hodnotu pro určení, jestli odpověď na příspěvek můžete uložit do mezipaměti.
[Uživatelské proměnné](#user-variable) | Pouze pro interní použití.

 
## <a name="url-features"></a>Adresa URL funkce

Tyto funkce umožňují požadavek na přesměrování nebo vzniklá jinou adresu URL.

Název | Účel
-----|--------
[Následovat přesměrování](#follow-redirects) | Určuje, zda požadavky můžete přesměrovat na název hostitele definovaná v hlavičce umístění vrátil server původu zákazníka.
[Adresa URL pro přesměrování](#url-redirect) | Přesměruje požadavky prostřednictvím hlavička umístění.
[Přepsání adresy URL](#url-rewrite)  | Přepíše adresu URL požadavku.



## <a name="azure-cdn-rules-engine-features-reference"></a>Referenční dokumentace funkcí ke stroji pravidel Azure CDN

---
### <a name="age-response-header"></a>Hlavička odpovědi věk
**Účel**: Určuje, zda je zahrnout hlavičku odpovědi stáří odpověď odesílanou žadateli.
Hodnota|Výsledek
--|--
Povoleno | Hlavička odpovědi věk je zahrnutý v odpovědi odeslané žadateli.
Zakázáno | Hlavička odpovědi věk je vyloučen z odpověď odesílanou žadateli.

**Výchozí chování**: zakázáno.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Parametry šířky pásma
**Účel:** Určuje, zda jsou parametry omezení šířky pásma (například ec_rate a ec_prebuf) aktivní.

Parametry pro omezování šířky pásma určit, jestli rychlost přenosu dat pro požadavek klienta jsou omezená na vlastní míry.

Hodnota|Výsledek
--|--
Povoleno|Umožňuje bodů POP případném dalším sdílení dodržovat požadavky omezení šířky pásma.
Zakázáno|Způsobí, že se vezme Ignorovat omezení parametry šířky pásma. Požadovaný obsah obsluhuje normálně (to znamená bez omezení šířky pásma).

**Výchozí chování:** povolena.
 
[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Omezení šířky pásma
**Účel:** omezuje šířku pásma pro odpověď v poskytované bodů POP.

Při správném nastavení omezení šířky pásma musí být definována obě z následujících možností.

Možnost|Popis
--|--
Kilobajtů za sekundu|Tuto možnost nastavte na maximální šířku pásma (Kb / s), který slouží k poskytování odpovědi.
Prebuf sekund|Tuto možnost nastavte počet sekund pro bodů POP počkat, až se omezuje šířku pásma. Účelem tohoto časového období bez omezení šířky pásma je zabránit media player problémy přerušované nebo vyrovnávací paměti z důvodu omezení šířky pásma.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Nepoužívat mezipaměť
**Účel:** Určuje, zda by měl žádost nepoužívat ukládání do mezipaměti.

Hodnota|Výsledek
--|--
Povoleno|I v případě, že obsah byl dříve uložené v mezipaměti POP způsobí, že všechny požadavky na předáno na zdrojový server.
Zakázáno|Způsobí, že POP k prostředků do mezipaměti podle zásady ukládání do mezipaměti definované v jeho hlavičkách odpovědi.

**Výchozí chování:**

- **HTTP velké:** zakázáno

<!---
- **ADN:** Enabled

--->

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Metody HTTP možné ukládat do mezipaměti
**Účel:** určuje sadu další metody HTTP, které lze uložit do mezipaměti v síti.

Informace o klíči:

- Tato funkce se předpokládá, že odpovědi GET by měl vždy uložit do mezipaměti. Metodu GET HTTP v důsledku toho by neměl být zahrnuty při nastavení této funkce.
- Tato funkce podporuje pouze metody POST protokolu HTTP. Povolit příspěvek ukládání odpovědí do mezipaměti tak, že nastavíte tuto funkci `POST`.
- Ve výchozím nastavení jsou uložené v mezipaměti pouze požadavky, jejichž text je menší než 14 Kb. Pomocí možné ukládat do mezipaměti velikost textu žádosti o funkce můžete nastavit velikost textu maximální požadavku.

**Výchozí chování:** pouze GET odpovědi jsou uložené v mezipaměti.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Velikost textu požadavku možné ukládat do mezipaměti
**Účel:** definuje prahovou hodnotu pro určení, jestli odpověď na příspěvek můžete uložit do mezipaměti.

Tato prahová hodnota je určena tak, že určíte velikost textu maximální požadavku. Požadavky, které obsahují větší textu žádosti nejsou ukládány do mezipaměti.

Informace o klíči:

- Tato funkce se vztahuje pouze při odpovědi příspěvek jsou vhodné pro ukládání do mezipaměti. Funkci lze uložit do mezipaměti HTTP metody umožňující ukládání do mezipaměti požadavek POST.
- Text požadavku je pro vzít v úvahu:
    - hodnoty x--www-form-urlencoded
    - Zajištění jedinečný klíč mezipaměti
- Definování velikost textu velké maximální požadavku může ovlivnit výkon doručování data.
    - **Doporučená hodnota:** 14 Kb
    - **Minimální hodnota:** 1 Kb

**Výchozí chování:** 14 Kb

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Zpracování hlavičky Cache-Control
**Účel:** generování Určuje `Cache-Control` záhlaví POP, když je aktivní externí funkce Max-Age.

Nejjednodušší způsob, jak dosáhnout tohoto typu konfigurace je umístit externí Max-Age a funkce zpracování hlavičky Cache-Control v jednom příkazu.

Hodnota|Výsledek
--|--
Přepsat|Zajišťuje, že dojde k následujícím akcím:<br/> -Přepíše `Cache-Control` hlaviček generovaných zdrojový server. <br/>– Přidá `Cache-Control` záhlaví vytvořený pomocí funkce externí Max-Age do odpovědi.
Průchod|Zajišťuje, `Cache-Control` záhlaví vytvořený pomocí funkce externí Max-Age se nikdy přidá do odpovědi. <br/> Pokud zdrojový server vytvoří `Cache-Control` záhlaví, prochází koncovému uživateli. <br/> Pokud zdrojový server se nevytvoří `Cache-Control` záhlaví, pak se tato možnost může způsobit, že hlavička odpovědi, aby neobsahovaly `Cache-Control` záhlaví.
Přidat, pokud chybí|Pokud `Cache-Control` hlavička nebyla přijata ze zdrojového serveru a pak tato možnost přidá `Cache-Control` záhlaví vytvořený pomocí funkce externí Max-Age. Tato možnost je užitečná pro zajištění, že všechny prostředky jsou přiřazené `Cache-Control` záhlaví.
Odebrat| Tato možnost zajistí, že `Cache-Control` záhlaví není součástí hlavičky odpovědi. Pokud `Cache-Control` hlavička již byla přiřazena, pak se odebere z hlavičky odpovědi.

**Výchozí chování:** přepsat.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Řetězec dotazu klíč mezipaměti
**Účel:** Určuje, zda klíč mezipaměti zahrnutí nebo vyloučení parametrů řetězce dotazu přidružený k požadavku.

Informace o klíči:

- Zadejte jeden nebo více názvy parametrů řetězce dotazu a název každého parametru oddělte mezerou.
- Tato funkce určuje, zda parametry řetězce dotazu jsou zahrnuty nebo vyloučeny ze klíče mezipaměti. Další informace jsou poskytovány pro jednotlivé možnosti v následující tabulce.

Typ|Popis
--|--
 Zahrnout|  Označuje, že každý zadaný parametr by měl být součástí klíče mezipaměti. Jedinečný klíč mezipaměti je vygenerována pro každý požadavek, který obsahuje jedinečná hodnota pro parametr řetězce dotazu definovaný v této funkci. 
 Zahrnout všechny  |Označuje, že je vytvořen jedinečný klíč mezipaměti pro každý požadavek na prostředek, který obsahuje řetězec dotazu jedinečný. Tento typ konfigurace se nedoporučuje obvykle, protože to může vést k malé procento přístupů k mezipaměti. Nízký počet přístupů k mezipaměti zvyšuje zatížení na původním serveru, protože ho musí poskytovat více požadavků. Tato konfigurace duplikuje chování ukládání do mezipaměti, které jsou známé jako "jedinečná mezipaměti" na stránce ukládání do mezipaměti řetězce dotazu. 
 Vyloučit | Označuje, že pouze zadané parametry je vyloučena z klíče mezipaměti. Všechny další parametry řetězce dotazu jsou součástí klíče mezipaměti. 
 Vyloučit vše  |Označuje, že všechny parametry řetězce dotazu jsou vyloučené z klíče mezipaměti. Tato konfigurace duplikuje "standard s mezipamětí" výchozí chování na stránce ukládání do mezipaměti řetězce dotazu do mezipaměti.  

Stroj pravidel umožňuje přizpůsobit způsob, ve kterém se implementuje ukládání do mezipaměti řetězce dotazu. Například můžete určit, že ukládání do mezipaměti řetězce dotazu je provedena pouze u určitých umístění nebo typy souborů.

Duplikovat řetězce dotazu "no-cache" na stránce ukládání do mezipaměti řetězce dotazu chování ukládání do mezipaměti, vytvořte pravidlo, které obsahuje funkce paměť Cache můžete přeskočit a podmínce shody zástupné adresy URL dotazu. Nastavte podmínku shody zástupné adresy URL dotazu na hvězdičku (*).

>[!IMPORTANT] 
> Pokud token autorizace je povolená pro libovolnou cestu pro tento účet, režimu mezipaměti standard je jediný režim, který slouží k ukládání do mezipaměti řetězce dotazu. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Následující příklad použití pro tuto funkci poskytuje ukázkový požadavek a klíče mezipaměti výchozí:

- **Ukázka požadavku:** http://wpc.0001.&lt; domény&gt;/800001/Origin/folder/asset.htm?sessionid=1234 & jazyk = EN & userid = 01
- **Výchozí klíč mezipaměti:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Zahrnout

Ukázková konfigurace:

- **Typ:** zahrnout
- **Parametry:** jazyka

Tento typ konfigurace vygeneruje následující řetězec parametru mezipaměti – klíč dotazu:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Zahrnout všechny

Ukázková konfigurace:

- **Typ:** zahrnují všechny

Tento typ konfigurace vygeneruje následující řetězec parametru mezipaměti – klíč dotazu:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Vyloučit

Ukázková konfigurace:

- **Typ:** vyloučit
- **Parametry:** sessionid ID uživatele

Tento typ konfigurace vygeneruje následující řetězec parametru mezipaměti – klíč dotazu:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Vyloučit vše

Ukázková konfigurace:

- **Typ:** vyřadit všechny

Tento typ konfigurace vygeneruje následující řetězec parametru mezipaměti – klíč dotazu:

    /800001/Origin/folder/asset.htm

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Přepište klíč mezipaměti
**Účel:** přepíše mezipaměti – klíč přidružený k požadavku.

Klíč mezipaměti je relativní cesta, která identifikuje prostředek pro účely ukládání do mezipaměti. Jinými slovy zkontrolujte servery pro verzi v mezipaměti prostředek podle jeho cesty podle jeho klíče mezipaměti.

Nakonfigurujte tuto funkci tak, že definujete obě z následujících možností:

Možnost|Popis
--|--
Původní cestě| Definuje relativní cestu k typy požadavků, jejichž klíč mezipaměti je přepsán. Relativní cesta lze definovat výběrem základní původní cestu a pak definuje vzor regulárního výrazu.
Nová cesta|Definuje relativní cestu pro nové klíče mezipaměti. Relativní cesta lze definovat výběrem základní původní cestu a pak definuje vzor regulárního výrazu. Tato cesta relativní můžete dynamicky vytvořený prostřednictvím [HTTP proměnné](cdn-http-variables.md).
**Výchozí chování:** mezipaměti – klíč požadavku se určuje podle identifikátoru URI požadavku.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Poznámka
**Účel:** umožňuje Poznámka k přidají v rámci pravidla.

Jedno použití k použití této funkce je poskytují další informace o pravidle nebo proč konkrétní odpovídají podmínce nebo funkce byla přidána do pravidla pro obecné účely.

Informace o klíči:

- Lze zadat maximální počet znaků je 150.
- Používejte pouze alfanumerické znaky.
- Tato funkce nemá vliv na chování pravidla. Pouze je určená k poskytování oblast, ve kterém můžete zadat informace pro budoucí použití nebo, který může pomoci při řešení potíží s pravidlo.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Výplň kompletní mezipaměti
**Účel:** Určuje, co se stane, když požadavek výsledkem neúspěšnému přístupu do částečné mezipaměti, na místní nabídky.

Neúspěšný přístup do částečné mezipaměti popisuje mezipaměti stavu pro určitý prostředek, který nebyl zcela stáhne do místní nabídky. Pokud prostředek je jen částečně uložené v mezipaměti serveru POP, pak další požadavek pro příslušný prostředek budou předány znovu na zdrojový server.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Neúspěšný přístup do částečné mezipaměti obvykle dochází, jakmile uživatel zruší stahování nebo pro prostředky, které jsou požadovány pouze pomocí protokolu HTTP rozsah požadavků. Tato funkce je zvláště užitečná pro velké prostředky, které nejsou obvykle stáhnout od začátku do konce (například videa). V důsledku toho tato funkce je povolena ve výchozím nastavení HTTP velké platformou. To je zakázáno na jiných platformách.

Zachovat výchozí konfiguraci pro platformu HTTP velké, protože snižuje zatížení serveru původu zákazníků a větší rychlost, jakou vaši zákazníci stažení vašeho obsahu.

Hodnota|Výsledek
--|--
Povoleno|Obnoví výchozí chování. Výchozí chování je vynutit POP k zahájení načítání na pozadí prostředku ze zdrojového serveru. A asset bude v místní mezipaměti serveru POP.
Zakázáno|Místní nabídky brání v provádění načítání na pozadí pro asset. Výsledkem je, že další požadavek pro tento prostředek z dané oblasti způsobí, že místní nabídky k vyžádání ze zdrojového serveru zákazníka.

**Výchozí chování:** povolena.

#### <a name="compatibility"></a>Kompatibilita
Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato funkce nemůže být přidružena následující podmínky shody: 
- JAKO číslo
- IP adresa klienta
- Parametr souboru cookie
- Soubor cookie parametr Regex
- Země
- Zařízení
- Edge Cname
- Odkaz domény
- Literál hlavičky požadavku
- Regulární výraz hlavičky požadavku
- Zástupný znak hlavičky požadavku
- Request – metoda
- Schéma požadavku
- Adresa URL dotazu literál
- Regulární výraz dotazu adresy URL
- Adresa URL dotazu zástupných znaků
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Komprese typy souborů
**Účel:** definuje formátů souborů, které jsou komprimované soubory na serveru.

Formát souboru je možné zadat pomocí jeho typ média Internetu (například Content-Type). Typ média Internet je nezávislá na platformě metadata, která umožňuje servery, které chcete určit formát příslušného prostředku. Seznam běžných typů médií Internet jsou uvedeny níže.

Typ média Internet|Popis
--|--
text/plain|Místo textových souborů
text/html| Soubory HTML
text/css|Šablony stylů CSS
Application/x-javascript|JavaScript
aplikace/javascript|JavaScript
Informace o klíči:

- Neurčujte více typů médií Internetu podle omezující každé z nich mezerou. 
- Tato funkce komprimuje jenom prostředky, jejichž velikost je menší než 1 MB. Větší prostředky nejsou komprimované servery.
- Určité typy obsahu, jako jsou obrázky, videa a zvuku mediálních materiálů (například JPG, MP3, MP4, atd.), jsou komprimované. Protože další kompresi u těchto typů prostředků není výrazně snížit velikost souboru, se doporučuje nepovolovat komprese na ně.
- Zástupné znaky, jako je například hvězdičky, nejsou podporovány.
- Předtím, než přidáte tuto funkci do pravidla, ujistěte se, nastavení komprese zakázané možnosti na stránce komprese pro platformu, pro které toto pravidlo použito.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Protokol vlastní pole 1
**Účel:** Určuje formát a obsah, který se přiřadí pole vlastní protokol v nezpracovaných souborů protokolu.

Toto vlastní pole umožňuje určit, jaké hodnoty hlavičky požadavku a odpovědi jsou uloženy v souborech protokolu.

Ve výchozím nastavení je pole vlastního protokolu s názvem "x-ec_custom-1." Název tohoto pole lze přizpůsobit z nezpracované nastavení protokolu stránky.

Formát pro zadávání hlaviček žádostí a odpovědí je definovaná následujícím způsobem:

Typ záhlaví|Formát|Příklady
-|-|-
Hlavička požadavku|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Odkazující server} mi <br/> % {Autorizace} i
Hlavička odezvy|`%{[ResponseHeader]()}[o]()`| O % {stáří} <br/> O % {content-Type} <br/> %{Cookie}o

Informace o klíči:

- Pole vlastní protokol může obsahovat libovolnou kombinaci pole hlavičky a prostý text.
- Platné znaky pro toto pole jsou následující: alfanumerické znaky (0-9, a-z a A-Z), pomlčky, použití dvojteček, středníkem, apostrofy, čárky, tečky, podtržítka, symboly rovná, závorky, hranaté závorky a mezery. Symbol procenta a složené závorky jsou povoleny pouze pokud se použije k určení pole hlavičky.
- Pravopis pro každé pole zadané záhlaví musí odpovídat názvu záhlaví požadovaného žádostí a odpovědí.
- Pokud chcete zadat více záhlaví, použijte oddělovač k označení záhlaví. Můžete například použít zkratka pro jednotlivé hlavičky:
    - AE: % {přijmout kódování} i odpověď: % {autorizace} i CT: % {Content-Type} o 

**Výchozí hodnota:** -

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Ladění hlavičky odpovědi v mezipaměti
**Účel:** Určuje, zda může obsahovat odpověď [hlavičky X-ES-Debug odpovědi](cdn-http-debug-headers.md), který poskytuje informace o zásady ukládání do mezipaměti pro požadovaný prostředek.

Ladění odpověď mezipaměti, hlavičky budou zahrnuty v odpovědi, pokud jsou splněny obě z následujících akcí:

- Funkce ladění hlavičky odpovědi mezipaměti bylo povoleno na zadaný požadavek.
- Zadaný požadavek definuje sadu hlaviček odpovědí mezipaměti ladění, které budou zahrnuty v odpovědi.

Ladění odpověď mezipaměti, které mohou být vyžádány záhlaví zahrnutím následující záhlaví a direktivy zadaný v požadavku:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Příklad:**

X-ES-ladění: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Hodnota|Výsledek
-|-
Povoleno|Požadavky pro hlavičky odpovědi mezipaměti ladění vrátí odpověď obsahující hlavičku X-ES-ladění.
Zakázáno|Hlavička X-ES-Debug odpovědi budou vyloučeny z odpovědi.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Interní výchozí Max-Age
**Účel:** Určuje výchozí interval max-age pro POP, Revalidace mezipaměti serveru původu. Jinými slovy množství času, které se předá před místní nabídky, zkontroluje, zda v mezipaměti asset odpovídá asset uložené na původním serveru.

Informace o klíči:

- Tato akce bude provádět pouze pro odpovědi ze serveru původu nepřiřadili max-age označení v `Cache-Control` nebo `Expires` záhlaví.
- Tato akce neproběhne u prostředků, které nejsou považovány za možné ukládat do mezipaměti.
- Tato akce nemá vliv na prohlížeči revalidations mezipaměti POP. Tyto druhy revalidations jsou určeny `Cache-Control` nebo `Expires` hlavičky posílané do prohlížeče, které je možné přizpůsobit pomocí funkce externí Max-Age.
- Výsledky této akce nemají pozorovatelných vliv na hlavičky odpovědi a obsah vrácená z bodů POP pro obsah, ale může mít vliv na opětovné ověření provozu odeslaný vezme váš zdrojový server.
- Nakonfigurujte tuto funkci:
    - Výběr stavový kód, který lze použít výchozí vnitřní max-age.
    - Určení celočíselnou hodnotu a pak vyberete požadovanou dobu jednotky (například několika sekund, minut, hodin, atd.). Tato hodnota určuje výchozí interval interní max-age.

- Nastavení časovou jednotku na "Off", přiřadí se výchozí vnitřní max-age intervalem 7 dní pro požadavky, které nebyly přiřazeny max-age údaj v jejich `Cache-Control` nebo `Expires` záhlaví.

**Výchozí hodnota:** 7 dní

#### <a name="compatibility"></a>Kompatibilita
Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato funkce nemůže být přidružena následující podmínky shody: 
- JAKO číslo
- IP adresa klienta
- Parametr souboru cookie
- Soubor cookie parametr Regex
- Země
- Zařízení
- Edge Cname
- Odkaz domény
- Literál hlavičky požadavku
- Regulární výraz hlavičky požadavku
- Zástupný znak hlavičky požadavku
- Request – metoda
- Schéma požadavku
- Adresa URL dotazu literál
- Regulární výraz dotazu adresy URL
- Adresa URL dotazu zástupných znaků
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Odepřít přístup (403)
**Účel**: Určuje, zda všechny požadavky byly zamítnuty 403 Zakázáno odpovědí.

Hodnota | Výsledek
------|-------
Povoleno| Způsobí, že všechny požadavky, které splňují kritéria přiřazování zamítnutí 403 Zakázáno odpovědí.
Zakázáno| Obnoví výchozí chování. Výchozí chování je povolit zdrojový server určit typ odpovědi, který bude vrácen.

**Výchozí chování**: zakázáno

> [!TIP]
   > Možné použití této funkce je pro přidružení k podmínku shody hlavičky žádosti o k blokování přístupu do odkazujících serverů protokolu HTTP, které jsou pomocí vložených odkazů na obsah.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Vypršení platnosti zpracování záhlaví
**Účel:** generování Určuje `Expires` záhlaví POP, když je aktivní funkce externí Max-Age.

Nejjednodušší způsob, jak dosáhnout tohoto typu konfigurace je umístit externí Max-Age a vyprší platnost zpracování záhlaví funkce ve stejném příkazu.

Hodnota|Výsledek
--|--
Přepsat|Zajišťuje proběhne následující akce:<br/>-Přepíše `Expires` hlaviček generovaných zdrojový server.<br/>– Přidá `Expires` záhlaví vytvořený pomocí funkce externí Max-Age do odpovědi.
Průchod|Zajišťuje, `Expires` záhlaví vytvořený pomocí funkce externí Max-Age se nikdy přidá do odpovědi. <br/> Pokud zdrojový server vytvoří `Expires` záhlaví, bude procházet pro koncového uživatele. <br/>Pokud zdrojový server nevytváří `Expires` záhlaví, pak se tato možnost může způsobit, že nebude obsahovat hlavičku odpovědi `Expires` záhlaví.
Přidat, pokud chybí| Pokud `Expires` hlavička nebyla přijata ze zdrojového serveru a pak tato možnost přidá `Expires` záhlaví vytvořený pomocí funkce externí Max-Age. Tato možnost je užitečná pro zajištění, že všechny prostředky se přiřadí `Expires` záhlaví.
Odebrat| Zajišťuje, že `Expires` záhlaví není součástí hlavičky odpovědi. Pokud `Expires` hlavička již byla přiřazena, pak se odebere z hlavičky odpovědi.

**Výchozí chování:** přepsat

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Externí Max-Age
**Účel:** Určuje interval max-age pro prohlížeč, aby Revalidace mezipaměti POP. Množství času, které se předá před prohlížeči jinými slovy, můžete zkontrolovat nové verze prostředku z místní nabídky.

Povolení této funkce bude generovat `Cache-Control: max-age` a `Expires` záhlaví z bodů POP a odeslat je do klienta protokolu HTTP. Ve výchozím nastavení přepíše tyto hlavičky těchto záhlaví vytvořené původním serveru. Ale zpracování hlavičky Cache-Control a vyprší platnost zpracování záhlaví funkce slouží ke změně tohoto chování.

Informace o klíči:

- Tato akce nemá vliv POP na původní server mezipaměti revalidations. Tyto druhy revalidations jsou určeny `Cache-Control` a `Expires` záhlaví přijatých ze zdrojového serveru a je možné přizpůsobit pomocí výchozí vnitřní Max-Age a funkce platnost vnitřní Max-Age.
- Tuto funkci konfigurovat zadáním celočíselnou hodnotou a vyberete požadovanou dobu jednotky (například několika sekund, minut, hodin, atd.).
- Nastavení této funkce na zápornou hodnotu způsobí, že bodů POP k odeslání `Cache-Control: no-cache` a `Expires` čas, který je nastavené na minulost s každou odezva do prohlížeče. I když se klientovi HTTP neukládají do mezipaměti odpovědi, toto nastavení nebude mít vliv POP možnost pro ukládání do mezipaměti odpovědi ze zdrojového serveru.
- Nastavení časovou jednotku na "Off", budou tuto funkci zakázat. `Cache-Control` a `Expires` hlavičky uložené v mezipaměti s odpovědí původním serveru předá do prohlížeče.

**Výchozí chování:** vypnuto

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Následovat přesměrování
**Účel:** Určuje, zda požadavky můžete přesměrovat na název hostitele definovaná v hlavičce umístění vrátil server původu zákazníka.

Informace o klíči:

- Požadavky můžete přesměrovat pouze do hraničních zařízení záznamů CNAME, které odpovídají stejnou platformu.

Hodnota|Výsledek
-|-
Povoleno|Požadavky můžete přesměrovat.
Zakázáno|Požadavky se nebude přesměrovávat.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Platnost vnitřní Max-Age
**Účel:** Určuje interval max-age pro POP, Revalidace mezipaměti serveru původu. Množství času, které se předá před místní nabídky jinými slovy, můžete zkontrolovat, zda v mezipaměti asset odpovídá asset uložené na původním serveru.

Informace o klíči:

- Tato funkce se přepíše max-age intervalu určeném v `Cache-Control` nebo `Expires` hlavičky generované ze zdrojového serveru.
- Tato funkce nemá vliv na prohlížeči revalidations mezipaměti POP. Tyto druhy revalidations jsou určeny `Cache-Control` nebo `Expires` hlavičky posílané do prohlížeče.
- Tato funkce nemá pozorovatelných vliv na odpověď odeslaná místní nabídky žadateli. Však může mít vliv na velikost Revalidace provoz odeslaný z bodů POP na původním serveru.
- Nakonfigurujte tuto funkci:
    - Výběr stavový kód, pro který se použije vnitřní max-age.
    - Určení celočíselnou hodnotou a vyberete požadované časové jednotky (například několika sekund, minut, hodin, atd.). Tato hodnota Určuje interval platnosti požadavku.

- Nastavení časovou jednotku na "Off" Zakáže tuto funkci. Interní max-age intervalu nesmí být přiděleno požadovaných prostředků. Pokud původní hlavička neobsahuje pokyny k ukládání do mezipaměti, pak asset bude do mezipaměti podle aktivní nastavení ve funkci výchozí vnitřní Max-Age.

**Výchozí chování:** vypnuto

#### <a name="compatibility"></a>Kompatibilita
Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato funkce nemůže být přidružena následující podmínky shody: 
- JAKO číslo
- IP adresa klienta
- Parametr souboru cookie
- Soubor cookie parametr Regex
- Země
- Zařízení
- Edge Cname
- Odkaz domény
- Literál hlavičky požadavku
- Regulární výraz hlavičky požadavku
- Zástupný znak hlavičky požadavku
- Request – metoda
- Schéma požadavku
- Adresa URL dotazu literál
- Regulární výraz dotazu adresy URL
- Adresa URL dotazu zástupných znaků
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>Podpora H.264 (HTTP progresivního stahování)
**Účel:** Určuje typy H.264 formátů souborů, které lze použít ke streamování obsahu.

Informace o klíči:

- Definujte sadu povolených H.264 přípony názvů souborů oddělená mezerami ve variantě pro přípony souborů. Přípony souborů možnost přepíše výchozí chování. Zachování podpory MP4 a F4V zahrnutím tyto přípony názvů souborů, když nastavení této možnosti. 
- Zahrnout období při zadání každou příponu názvu souboru (například _MP4_, _.f4v_).

**Výchozí chování:** progresivní stahování HTTP ve výchozím nastavení podporuje MP4 a F4V média.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Dodržet No-Cache žádosti
**Účel:** Určuje, zda je klientovi HTTP no-cache požadavky se předají ke zdrojovému serveru.

Žádost o no-cache nastane, pokud klient HTTP odešle `Cache-Control: no-cache` a/nebo `Pragma: no-cache` hlavičky v požadavku HTTP.

Hodnota|Výsledek
--|--
Povoleno|Umožňuje klienta HTTP no-cache vyžádá předávány na původním serveru, a zdrojový server vrátí hlavičky odpovědi a text přes POP zpět do klienta protokolu HTTP.
Zakázáno|Obnoví výchozí chování. Výchozí chování je zabránit no-cache požadavky předávané na zdrojový server.

Pro veškerý provoz v produkčním prostředí doporučujeme nechat tuto funkci ve svém výchozím zakázané stavu. V opačném případě nebude původních serverů stínit koncovým uživatelům, kteří můžou neúmyslně aktivovat mnoho požadavků no-cache při aktualizaci webové stránky nebo z mnoha hráči oblíbeného mediálního, které jsou naprogramovány tak, aby odesílat hlavičku no-cache při každé video žádosti. Tuto funkci však může být užitečné k platí pro určité neprodukčním bude výkon pracovního nebo testování adresářů, aby bylo možné povolit čerstvý obsah načíst na vyžádání ze zdrojového serveru.

Stav mezipaměti, který se použije v hlášení pro žádosti, která může být přeposílán zdrojový server z důvodu této funkce je `TCP_Client_Refresh_Miss`. Stavy mezipaměti sestavu, která je dostupná v Core reporting modulu, poskytuje statistické údaje podle stavu mezipaměti. Tato sestava umožňuje sledovat počet a Procento požadavků, které jsou předávány zdrojový server z důvodu této funkce.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Ignorovat původu No-Cache
**Účel:** Určuje, zda CDN bude ignorovat následující direktivy ze zdrojového serveru:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informace o klíči:

- Nakonfigurujte tuto funkci tak, že definujete mezerami oddělený seznam stavových kódů, u kterých se bude ignorovat direktivy výše.
- Sada platný stavové kódy pro tuto funkci: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 a 505.
- Tuto funkci zakážete nastavením na prázdnou hodnotu.

**Výchozí chování:** výchozí chování je případném dalším sdílení dodržovat direktivy výše.

#### <a name="compatibility"></a>Kompatibilita
Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato funkce nemůže být přidružena následující podmínky shody: 
- JAKO číslo
- IP adresa klienta
- Parametr souboru cookie
- Soubor cookie parametr Regex
- Země
- Zařízení
- Edge Cname
- Odkaz domény
- Literál hlavičky požadavku
- Regulární výraz hlavičky požadavku
- Zástupný znak hlavičky požadavku
- Request – metoda
- Schéma požadavku
- Adresa URL dotazu literál
- Regulární výraz dotazu adresy URL
- Adresa URL dotazu zástupných znaků
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ignorovat Unsatisfiable rozsahy 
**Účel:** určuje odpověď, která se vrátí ke klientům, pokud žádost vygeneruje 416 požadovaný rozsah nelze uspokojit stavový kód.

Ve výchozím nastavení tímto stavovým kódem je vrácena, pokud zadaný rozsah bajtů požadavek nelze splnit pomocí místní nabídky a nebyl zadán pole hlavičky žádosti If-Range.

Hodnota|Výsledek
-|-
Povoleno|Zabrání bodů POP reagovat na požadavek neplatný rozsah bajtů se 416 požadovaný rozsah nelze uspokojit stavovým kódem. Servery se místo toho poskytovat požadované asset a 200 OK vraťte se do klienta.
Zakázáno|Obnoví výchozí chování. Výchozí chování je případném dalším sdílení dodržovat 416 požadovaný rozsah nelze uspokojit stavový kód.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Interní Nezastaralá Max
**Účel:** ovládací prvky, jak dlouho po čase normální vypršení platnosti v mezipaměti asset může poskytovat z místní nabídky, když lokalita POP není schopen se znovu ověřit v mezipaměti asset s původním serveru.

Za normálních okolností po uplynutí této doby platnosti prostředků, lokalita POP odešle požadavek na opětovné ověření na zdrojový server. Původu serveru bude potom odpověď s buď 304 nedojde ke změně poskytnout POP nové zapůjčení v mezipaměti prostředků or else s 200 OK poskytnout POP aktualizované verze prostředku uložená v mezipaměti.

Pokud lokalita POP není schopen navázat spojení se zdrojový server při pokusu o Revalidace, pak tato interní Max-zastaralé funkce řídí, jestli a jak dlouho, lokalita POP mohou nadále sloužit asset nyní zastaralé.

Všimněte si, že tento časový interval začne, když vyprší platnost prostředku max-age, ne, při výskytu neúspěšných opětovné ověření. Maximální doba, během které je možné dodávat prostředek bez úspěšné opětovné ověření je proto množství časový limit určený parametrem kombinaci maximální stáří a maximální zastaralé. Například pokud prostředek ukládá do mezipaměti v 9:00 s maximální stáří 30 minut a maximální počet zastaralé 15 minut, pak požadavek na opětovné ověření se nezdařilo v 9:44 způsobí koncový uživatel příjem zastaralé v mezipaměti asset, zatímco požadavek na opětovné ověření se nezdařilo v 9:46 způsobí en d uživatel, který obdrží 504 Vypršel časový limit brány.

Libovolná hodnota konfigurován pro tato funkce se nahradí `Cache-Control: must-revalidate` nebo `Cache-Control: proxy-revalidate` záhlaví přijatých ze zdrojového serveru. Pokud některý z těchto záhlaví je přijata ze zdrojového serveru, pokud prostředek je zpočátku uložené v mezipaměti, lokalita POP neposkytuje zastaralé v mezipaměti asset. V takovém případě Pokud lokalita POP není schopen odhlášením původu při vypršení intervalu max-age assetu, lokalita POP vrátí chybu 504 Vypršel časový limit brány.

Informace o klíči:

- Nakonfigurujte tuto funkci:
    - Výběr stavový kód, pro který se použije maximální zastaralá.
    - Určení celočíselnou hodnotu a pak vyberete požadovanou dobu jednotky (například několika sekund, minut, hodin, atd.). Tato hodnota určuje, interní max zastaralé, která se použije.

- Nastavení časovou jednotku na "Off", budou tuto funkci zakázat. V mezipaměti asset nebude obsluhovat nad rámec doby její normální vypršení platnosti.

**Výchozí chování:** dvě minuty

#### <a name="compatibility"></a>Kompatibilita
Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato funkce nemůže být přidružena následující podmínky shody: 
- JAKO číslo
- IP adresa klienta
- Parametr souboru cookie
- Soubor cookie parametr Regex
- Země
- Zařízení
- Edge Cname
- Odkaz domény
- Literál hlavičky požadavku
- Regulární výraz hlavičky požadavku
- Zástupný znak hlavičky požadavku
- Request – metoda
- Schéma požadavku
- Adresa URL dotazu literál
- Regulární výraz dotazu adresy URL
- Adresa URL dotazu zástupných znaků
- Parametr dotazu adresy URL

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Řetězec dotazu protokolu
**Účel:** Určuje, zda řetězec dotazu se uloží spolu se adresy URL v zobrazení protokolů.

Hodnota|Výsledek
-|-
Povoleno|Při nahrávání adresy URL v protokolu přístup, umožňuje ukládání řetězce dotazu. Pokud adresa URL neobsahuje řetězec dotazu, tato možnost nebude mít vliv.
Zakázáno|Obnoví výchozí chování. Výchozí chování je při nahrávání adresy URL v protokolu přístup ignorovat řetězce dotazů.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Maximální počet Keep-Alive požadavků
**Účel:** definuje maximální počet požadavků pro zachování připojení, než je uzavřený.

Nastavení maximální počet požadavků na nízkou hodnotu, se nedoporučuje a může způsobit snížení výkonu.

Informace o klíči:

- Tuto hodnotu zadejte jako celá čísla.
- V zadané hodnotě neobsahují čárky nebo tečky.

**Výchozí hodnota:** 10 000 žádostí

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Upravit hlavičku požadavku klienta
**Účel:** každý požadavek obsahuje sadu hlaviček požadavků, které popisují ho. Tato funkce se můžou buď:

- Přidat nebo přepsat hodnoty přiřazené k hlavičce požadavku. Pokud určenou hlavičku požadavku neexistuje, tato funkce ho pak přidá k požadavku.
- Hlavička požadavku odstraňte z požadavku.

Změny provedené při této funkce bude odrážet požadavků, které jsou předávány na server původu.

V hlavičce žádosti lze provést jednu z následujících akcí:

Možnost|Popis|Příklad:
-|-|-
Připojit|Zadaná hodnota bude přidán na konec existující hodnota hlavičky žádosti.|**Hodnota hlavičky požadavku (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky požadavku (stroj pravidel):**<br/>Hodnota2 <br/>**Nová hodnota hlavičky žádosti:** <br/>Value1Value2
Přepsat|Hodnota hlavičky požadavku se nastaví na zadanou hodnotu.|**Hodnota hlavičky požadavku (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky požadavku (stroj pravidel):**<br/>Hodnota2<br/>**Nová hodnota hlavičky žádosti:**<br/> Hodnota2 <br/>
Odstranění|Odstraní určenou hlavičku požadavku.|**Hodnota hlavičky požadavku (klient):**<br/>Hodnota1<br/>**Upravte konfigurace hlaviček žádosti klienta:**<br/>Odstraňte v hlavičce požadavku.<br/>**Výsledek:**<br/>Zdrojový server se nepředají určenou hlavičku požadavku.

Informace o klíči:

- Zajistěte, aby byl hodnotu zadanou v poli Název přesná shoda pro požadované hlavičky.
- Případ nebere v úvahu za účelem identifikace záhlaví. Například některé z následujících variant `Cache-Control` název hlavičky slouží k identifikaci:
    - Parametr cache-control
    - CACHE-CONTROL
    - cachE-Control
- Při zadávání názvu záhlaví, použijte pouze alfanumerické znaky, pomlčky nebo podtržítka.
- Odstraňuje se záhlaví, nebudou moct přeposlání zdrojový server podle bodů POP.
- Následující hlavičky jsou vyhrazené a nelze ji změnit pomocí této funkce:
    - předané
    - hostitel
    - prostřednictvím
    - upozornění
    - x předané pro
    - Všechny hlavičky s názvy začínajícími "x ES", jsou vyhrazena.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Upravit klienta hlavička odpovědi
Každou odpověď obsahuje sadu hlaviček odpovědí, které popisují ho. Tato funkce se můžou buď:

- Přidat nebo přepsat hodnoty přiřazené k hlavičky odpovědi. Pokud určenou hlavičku odpovědi neexistuje, tato funkce ho pak přidá do odpovědi.
- Odstraňte hlavičky odpovědi z odpovědi.

Ve výchozím nastavení jsou definovány hodnoty hlavičky odpovědi serveru původu a bodů POP.

V hlavičce odpovědi lze provést jednu z následujících akcí:

Možnost|Popis|Příklad:
-|-|-
Připojit|Zadaná hodnota bude přidán na konec existující hodnota hlavičky odpovědi.|**Hodnota hlavičky odpovědi (klient):**<br />Hodnota1<br/>**Hodnota hlavičky odpovědi (stroj pravidel):**<br/>Hodnota2<br/>**Nová hodnota hlavičky odpovědi:**<br/>Value1Value2
Přepsat|Hodnota hlavičky odpovědi se nastaví na zadanou hodnotu.|**Hodnota hlavičky odpovědi (klient):**<br/>Hodnota1<br/>**Hodnota hlavičky odpovědi (stroj pravidel):**<br/>Hodnota2 <br/>**Nová hodnota hlavičky odpovědi:**<br/>Hodnota2 <br/>
Odstranění|Odstraní určenou hlavičku odpovědi.|**Hodnota hlavičky odpovědi (klient):**<br/>Hodnota1<br/>**Upravte Konfigurace hlavičky odpovědi klienta:**<br/>Odstraňte dotyčný hlavičky odpovědi.<br/>**Výsledek:**<br/>Žadateli se nepředají určenou hlavičku odpovědi.

Informace o klíči:

- Zajistěte, aby byl hodnotu zadanou v poli Název přesná shoda pro hlavičku požadovanou odpověď. 
- Případ nebere v úvahu za účelem identifikace záhlaví. Například některé z následujících variant `Cache-Control` název hlavičky slouží k identifikaci:
    - Parametr cache-control
    - CACHE-CONTROL
    - cachE-Control
- Odstraňuje se záhlaví brání přeposlání žadateli.
- Následující hlavičky jsou vyhrazené a nelze ji změnit pomocí této funkce:
    - přijmout kódování
    - Stáří
    - připojení
    - kódování obsahu
    - Délka obsahu
    - rozsah obsahu
    - date
    - server
    - Koncová část
    - kódování přenosu
    - upgradovat
    - se liší
    - prostřednictvím
    - upozornění
    - Všechny hlavičky s názvy začínajícími "x ES", jsou vyhrazena.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Sdílení částečné mezipaměti
**Účel:** Určuje, zda žádost lze generovat částečně uložené v mezipaměti obsahu.

Tato částečná mezipaměť lze potom splnit nové požadavky pro tento obsah, dokud se požadovaný obsah je plně ukládány do mezipaměti.

Hodnota|Výsledek
-|-
Povoleno|Požadavky můžete vygenerovat částečně uložené v mezipaměti obsahu.
Zakázáno|Požadavky můžete generovat jenom verze plně uložené v mezipaměti požadovaného obsahu.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Prevalidate obsah uložený v mezipaměti
**Účel:** Určuje, zda obsah uložený v mezipaměti nárok na opětovné ověření dřívější než jeho hodnota TTL nevyprší.

Definujte dobu před vypršením platnosti TTL požadovaný obsah, během které budou mít nárok předčasné opětovné ověření.

Informace o klíči:

- Výběr "Off" jako časovou jednotku vyžaduje opětovné ověření proběhne po obsah uložený v mezipaměti vypršela platnost hodnoty TTL. Čas by neměl být zadán a je ignorován.

**Výchozí chování:** vypnout. Opětovné ověření může provádět pouze po vypršení hodnoty TTL obsah uložený v mezipaměti.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Speciálními záhlavími proxy
**Účel:** definuje sadu [hlavičky požadavku HTTP specifické pro Verizon](cdn-verizon-http-headers.md) , které budou předány z místní nabídky k serveru původu.

Informace o klíči:

- Každá hlavička požadavku specifické pro CDN definovaný v této funkci předán zdrojový server. Vyloučené záhlaví nejsou dál.
- Abyste zabránili předávaná hlavičku požadavku specifické pro CDN, odeberte ji ze seznamu oddělených mezerami v seznamu pole hlavičky.

Následující hlavičky protokolu HTTP jsou zahrnuté ve výchozím seznamu:
- Přes
- X-předané pro
- X-Forwarded-Proto
- X-hostitel
- X-Midgress
- X-Gateway List
- X-ES Name
- Hostitel

**Výchozí chování:** všechny hlavičky požadavků specifických pro CDN se předají ke zdrojovému serveru.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Aktualizace souborů z mezipaměti nula bajtů
**Účel:** Určuje, jak je zpracována žádost klienta HTTP pro prostředek mezipaměti 0 bajtů podle bodů POP.

Platné hodnoty jsou:

Hodnota|Výsledek
--|--
Povoleno|Způsobí, že POP, znovu načíst prostředek ze zdrojového serveru.
Zakázáno|Obnoví výchozí chování. Výchozí chování je poskytovat platnou mezipaměti prostředky na vyžádání.
Tato funkce není vyžadován pro správný ukládání do mezipaměti a doručování obsahu, ale může být užitečné jako alternativní řešení. Například generátory dynamického obsahu v původních serverů může způsobit neúmyslně odesílají do bodů POP odpovědi 0 bajtů. Tyto typy odpovědí jsou obvykle ukládají do mezipaměti podle bodů POP. Pokud víte, že je 0 bajtů odpověď nikdy platné odpovědi 

pro takový obsah pak tuto funkci můžete zabránit tyto typy prostředků používané pro služby vašim klientům.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Nastavit lze uložit do mezipaměti stavové kódy
**Účel:** definuje sadu stavové kódy, které může vést k obsahu v mezipaměti.

Ve výchozím nastavení ukládání do mezipaměti je povolená jenom pro odpovědi 200 OK.

Definujte sadu požadovanou stavové kódy oddělených mezerami.

Informace o klíči:

- Povolte funkci ignorovat původu No-Cache. Pokud tato funkce není povolené, nemusí mezipaměti odpovědi 200 OK.
- Sada platný stavové kódy pro tuto funkci: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 a 505.
- Tuto funkci nelze použít k zakázání ukládání do mezipaměti pro odpovědi, které generují stavový kód 200 OK kódu.

**Výchozí chování:** ukládání do mezipaměti je povolena pouze pro odpovědi, které generují stavový kód 200 OK kódu.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Nastavit vlastní záhlaví IP klienta
**Účel:** přidá vlastní hlavičky, který identifikuje klienta podle IP adresy na požadavek.

Možnost názvu záhlaví definuje název vlastní hlavičky IP adresa klienta se mají ukládat.

Tato funkce umožňuje zákazníkovi, adresy serverů původu a zjistěte, IP adresa klienta prostřednictvím hlavičku požadavku vlastní. Pokud požadavek se načítají z mezipaměti, nebude server původu informováni o IP adresu klienta. Proto se doporučuje, aby tuto funkci použít, s prostředky, které nejsou uložené v mezipaměti.

Ujistěte se, že zadaná hlavička název neodpovídá žádnému z následujících názvů:

- Názvy záhlaví standardní požadavek. Seznam názvů standardní hlavičku nacházely v [dokumentu RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Názvy vyhrazené záhlaví:
    - předané pro
    - hostitel
    - se liší
    - prostřednictvím
    - upozornění
    - x předané pro
    - Všechny hlavičky s názvy začínajícími "x ES", jsou vyhrazena.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Zastaralé doručování obsahu při chybě
**Účel:** Určuje, zda při výskytu chyby během Revalidace mezipaměti nebo při načítání požadovaného obsahu ze zdrojového serveru zákazníků budou doručeny vypršení platnosti obsahu v mezipaměti.

Hodnota|Výsledek
-|-
Povoleno|Když dojde k chybě při připojování k serveru původu starý obsah obsluhuje žadateli.
Zakázáno|Chyba serveru původu předá žadateli.

**Výchozí chování:** zakázáno

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Zastaralá při Revalidate
**Účel:** zvyšuje výkon tím, že bodů POP pro obsluhu starý obsah žadateli během opětovné ověření.

Informace o klíči:

- Chování této funkce se liší podle vybraného časovou jednotku.
    - **Časová jednotka:** zadejte dobu a pak vyberte časovou jednotku (například několika sekund, minut, hodin, atd.) umožňující zastaralé doručování obsahu. Tento typ instalačního programu umožňuje CDN prodloužit dobu, která může doručovat obsah před vyžadování ověření podle následujícího vzorce: **TTL** + **zastaralé při odhlášením čas** 
    - **Vypnuto:** vyberte "Off" tak, aby vyžadovala opětovné ověření před žádost pro starý obsah se služba může poskytovat.
        - Nezadávejte časový úsek, protože je nepoužitelný a budou ignorovány.

**Výchozí chování:** vypnout. Opětovné ověření musí dojít předtím, než je možné dodávat požadovaný obsah.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Ověření tokenu
**Účel:** Určuje, zda ověřování na základě tokenu se použijí pro žádost.

Pokud je povoleno ověřování založené na tokenech, se uplatní pouze požadavky, které poskytují zašifrovaný token a v souladu s požadavky určené tento token.

Šifrovací klíč, který se používá k šifrování a dešifrování tokenů hodnot se určuje podle primárního klíče a možnosti zálohování klíče na stránce Token ověření. Mějte na paměti, že šifrovací klíče jsou specifické pro platformu.

**Výchozí chování:** zakázané.

Tato funkce má přednost před většinu funkcí s výjimkou funkce přepisování adres URL.

Hodnota | Výsledek
------|---------
Povoleno | Chrání požadovaný obsah pomocí ověřování založeného na tokenech. Pouze požadavky od klientů, kteří poskytují platný token a musí splňovat požadavky budou zachované. FTP transakce jsou vyloučeny z ověřování založené na tokenech.
Zakázáno| Obnoví výchozí chování. Výchozí chování je umožnit konfiguraci ověřování na základě tokenu k určení, zda budou zabezpečené žádost.

#### <a name="compatibility"></a>Kompatibilita
Nepoužívejte ověřování tokenu s podmínkou shoda vždy. 

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Kód s cílem odepření ověření tokenu
**Účel:** Určuje typ odpovědi, která se vrátí na uživatele, pokud požadavek byl odepřen v důsledku ověřování založené na tokenech.

V následující tabulce jsou uvedeny kódy odpovědí k dispozici.

Kód odezvy|Název odpovědi|Popis
-------------|-------------|--------
301|Trvale přesunuto|Tímto stavovým kódem přesměruje na adresu URL zadat v hlavičce Location neoprávnění uživatelé.
302|Nalezeno|Tímto stavovým kódem přesměruje na adresu URL zadat v hlavičce Location neoprávnění uživatelé. Tento kód stavu je standardní způsob provedení přesměrování.
307|Dočasné přesměrování|Tímto stavovým kódem přesměruje na adresu URL zadat v hlavičce Location neoprávnění uživatelé.
401|Neautorizováno|Kombinování tímto stavovým kódem se odpověď hlavičky WWW-Authenticate umožňuje zobrazit výzvu uživateli pro ověřování.
403|Zakázáno|Tato zpráva je standardní 403 Zakázáno stavová zpráva, která neoprávněný uživatel se zobrazí při pokusu o přístup k chráněnému obsahu.
404|Soubor se nenašel|Stavový kód označuje, že klient HTTP byl schopen komunikovat se serverem, ale nenašel se požadovaný obsah.

#### <a name="compatibility"></a>Kompatibilita
Nepoužívejte s podmínkou shoda vždy kód tokenu s cílem odepření vícefaktorového ověřování. Místo toho použijte **vlastní zpracování odmítnutí** tématu **tokenu ověřování** stránku **spravovat** portálu. Další informace najdete v tématu [zabezpečení Azure CDN prostředky pomocí ověřování tokenu](cdn-token-auth.md).

#### <a name="url-redirection"></a>Adresa URL přesměrování

Tato funkce podporuje adresy URL přesměrování na adresu URL definovaný uživatelem je nakonfigurovaný vrátit stavový kód 3xx. Tuto adresu URL definované uživatelem se dá nastavit pomocí následujících kroků:

1. Vyberte kód odpovědi 3xx pro funkci kód tokenu s cílem odepření vícefaktorového ověřování.
2. Vyberte "Umístění" nepovinný název hlavičky.
3. Nastavte možnost Volitelná hodnota hlavičky k požadované adrese URL.

Pokud adresu URL není definováno pro kód stavu 3xx, bude na stránce standardní odpověď pro 3xx stavový kód vrátil uživateli.

Adresa URL přesměrování jde použít jenom pro kódy odpovědí 3xx.

Volitelná hodnota hlavičky možnost podporuje alfanumerické znaky, uvozovky a mezery.

#### <a name="authentication"></a>Authentication

Tato funkce podporuje možnost zahrnout do odpovědi k neautorizovanému požadavku pro obsah chráněný pomocí ověřování na základě tokenu hlavičky WWW-Authenticate. Pokud hlavička WWW-Authenticate byla nastavena na "základní" v konfiguraci, pak neoprávněný uživatel zobrazí výzva k zadání přihlašovacích údajů účtu.

V konfiguraci uvedené výš se dá dosáhnout následujících kroků:

1. Vyberte "401" jako kód odpovědi pro funkci kód tokenu s cílem odepření vícefaktorového ověřování.
2. Vyberte možnost volitelný název hlavičky "WWW-Authenticate".
3. Volitelná hodnota hlavičky možnosti "základní".

Hlavička WWW-Authenticate jde použít jenom pro 401 kódy odpovědí.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Ověření tokenu ignorovat velikost písmen adresy URL
**Účel:** Určuje, zda adresa URL porovnání provedené při ověřování pomocí tokenu jsou malá a velká písmena.

Parametry ovlivněné touto funkcí jsou:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Platné hodnoty jsou:

Hodnota|Výsledek
---|----
Povoleno|Způsobí, že POP, ignorovat velikost písmen při porovnávání adres URL pro ověřování pomocí tokenu parametry.
Zakázáno|Obnoví výchozí chování. Výchozí chování je pro porovnání adresu URL pro ověřování pomocí tokenu být malá a velká písmena.

**Výchozí chování:** zakázané.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Parametr tokenu ověřování
**Účel:** Určuje, zda by měl přejmenovat, parametru řetězce dotazu ověřování založené na tokenech.

Informace o klíči:

- Možnost Hodnota definuje název parametru řetězce dotazu, jehož prostřednictvím může být určen token.
- Možnost hodnotu nelze nastavit na "ec_token."
- Ujistěte se, že název definovaný v rámci hodnota obsahuje jenom platné znaky adresy URL.

Hodnota|Výsledek
----|----
Povoleno|Možnost Hodnota definuje název parametru řetězce dotazu, přes který by měla být definována tokeny.
Zakázáno|Token můžete zadat jako parametr řetězec nedefinované dotazu v adrese URL požadavku.

**Výchozí chování:** zakázané. Token můžete zadat jako parametr řetězec nedefinované dotazu v adrese URL požadavku.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>Adresa URL pro přesměrování
**Účel:** přesměruje požadavky prostřednictvím hlavička umístění.

Konfigurace tato funkce vyžaduje nastavení následujících možností:

Možnost|Popis
-|-
Kód|Vyberte kód odpovědi, který bude vrácen žadateli.
Zdroje a vzoru| Tato nastavení definují, který identifikuje typ požadavků, které mohou být přesměrováno vzor identifikátoru URI požadavku. Budete přesměrováni pouze požadavky, jehož adresa URL splňují obě z následujících kritérií: <br/> <br/> **Zdroj (nebo obsahu přístupový bod):** vyberte relativní cestu, která identifikuje zdrojový server. Tato cesta je _/XXXX/_ části a název koncového bodu. <br/><br/> **Zdroj (modelu):** musí být definován vzor, který identifikuje požadavky pomocí relativní cesty. Tento vzor regulárního výrazu musí definovat cestu, která spustí přímo po dříve vybraného obsahu přístupového bodu (viz výše). <br/> – Ujistěte se, že žádost o identifikátor URI (to znamená, zdroje a vzoru) dříve definovaná kritéria není v konfliktu s všechny podmínky shody, které jsou definované pro tuto funkci. <br/> -Zadat vzor; Pokud nechcete použít prázdnou hodnotu jako vzor, všechny řetězce odpovídají.
Cíl| Definování adresy URL, na kterou se přesměruje výše uvedených požadavků. <br/><br/> Vytvoření pomocí adresy URL: <br/> -Vzor regulárního výrazu <br/>- [Proměnné protokolu HTTP](cdn-http-variables.md) <br/><br/> Nahradit hodnoty zachycení ve vzoru zdroje do cílového modelu s použitím $_n_ kde _n_ identifikuje hodnotu podle pořadí, ve kterém byla zachycena. $1 například představuje první hodnota zachycené ve vzoru zdroje, zatímco druhá hodnota představuje 2 USD. <br/> 
Důrazně doporučujeme používat absolutní adresu URL. Použití relativní adresa URL může přesměrovaly CDN na neplatnou cestu.

**Ukázkový scénář**

Tento příklad ukazuje, jak přesměrovat adresu URL CNAME, který se přeloží na tuto základní adresu URL CDN okraj: http:\//marketing.azureedge.net/brochures

Kvalifikace žádosti budete přesměrováni na tento základní edge CNAME adresy URL: http:\//cdn.mydomain.com/resources

Tato adresa URL přesměrování lze dosáhnout pomocí následující konfigurace: ![adresu URL pro přesměrování](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Klíčové body:**

- Žádost definuje funkci přesměrování URL adresy URL, které bude přesměrována. V důsledku toho podmínky další shody se nevyžadují. I když podmínka shody byl definován jako "Always", bude přesměrován pouze požadavky, které odkazují do složky "brožury" na "marketing" původu zákazníka. 
- Všechny odpovídající žádosti budete přesměrováni na hraničních zařízeních CNAME URL definované v rámci cílového. 
    - Ukázkový scénář #1: 
        - Ukázka požadavku (CDN URL): http:\//marketing.azureedge.net/brochures/widgets.pdf 
        - Adresa URL požadavku (po přesměrování): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Ukázkový scénář #2: 
        - Ukázka požadavku (CNAME Edge adresy URL): http:\//marketing.mydomain.com/brochures/widgets.pdf 
        - Adresa URL požadavku (po přesměrování): http:\//cdn.mydomain.com/resources/widgets.pdf
    - Ukázkový scénář #3: 
        - Ukázka požadavku (CNAME Edge adresy URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt 
        - Adresa URL požadavku (po přesměrování): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- Proměnná schéma požadavku (% {scheme}) se využívá v cílovém možnost, která zajistí, že schéma požadavku zůstane beze změny po přesměrování.
- Segmenty adres URL, které byly zachyceny z požadavku se připojují na novou adresu URL prostřednictvím "$1."

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>Přepsání adresy URL
**Účel:** přepíše adresu URL požadavku.

Informace o klíči:

- Konfigurace tato funkce vyžaduje nastavení následujících možností:

Možnost|Popis
-|-
 Zdroje a vzoru | Tato nastavení definují vzor identifikátoru URI požadavku, který identifikuje typ požadavků, které mohou být přepsán. Bude přepsán pouze požadavky, jehož adresa URL splňují obě z následujících kritérií: <br/><br/>  - **Zdroj (nebo obsahu přístupový bod):** vyberte relativní cestu, která identifikuje zdrojový server. Tato cesta je _/XXXX/_ části a název koncového bodu. <br/><br/> - **Zdroj (modelu):** musí být definován vzor, který identifikuje požadavky pomocí relativní cesty. Tento vzor regulárního výrazu musí definovat cestu, která spustí přímo po dříve vybraného obsahu přístupového bodu (viz výše). <br/> Ověřte, že žádost o identifikátor URI (to znamená, zdroje a vzoru) dříve definovaná kritéria není v konfliktu s všechny podmínky shody, které jsou definované pro tuto funkci. Zadejte vzorek; Pokud nechcete použít prázdnou hodnotu jako vzor, všechny řetězce odpovídají. 
 Cíl  |Definujte relativní adresu URL, na který se přepsal výše uvedených požadavků: <br/>    1. Výběr bodu přístup k obsahu, který identifikuje zdrojový server. <br/>    2. Definování typu pomocí relativní cesty: <br/>        -Vzor regulárního výrazu <br/>        - [Proměnné protokolu HTTP](cdn-http-variables.md) <br/> <br/> Nahradit hodnoty zachycení ve vzoru zdroje do cílového modelu s použitím $_n_ kde _n_ identifikuje hodnotu podle pořadí, ve kterém byla zachycena. $1 například představuje první hodnota zachycené ve vzoru zdroje, zatímco druhá hodnota představuje 2 USD. 
 Tato funkce umožňuje bodů POP pro přepsání adresy URL bez provedení tradiční přesměrování. To znamená žadatel dostane stejným kódem odpovědi jakoby požadovaly přepsaný adresy URL.

**Ukázkový scénář 1**

Tento příklad ukazuje, jak přesměrovat adresu URL CNAME, který se přeloží na tuto základní adresu URL CDN okraj: http:\//marketing.azureedge.net/brochures/

Kvalifikace žádosti budete přesměrováni na tento základní edge CNAME adresy URL: http:\//MyOrigin.azureedge.net/resources/

Tato adresa URL přesměrování lze dosáhnout pomocí následující konfigurace: ![adresu URL pro přesměrování](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Ukázkový scénář 2**

Tento příklad ukazuje, jak přesměrovat okraj CNAME adresu URL z velká písmena na malá písmena pomocí regulárních výrazů.

Tato adresa URL přesměrování lze dosáhnout pomocí následující konfigurace: ![adresu URL pro přesměrování](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Klíčové body:**

- Definuje funkci přepisování adres URL požadavek adresy URL, které bude přepsán. V důsledku toho podmínky další shody se nevyžadují. I když podmínka shody byl definován jako "Always", bude přepsán pouze požadavky, které odkazují do složky "brožury" na "marketing" původu zákazníka.

- Segmenty adres URL, které byly zachyceny z požadavku se připojují na novou adresu URL prostřednictvím "$1."

#### <a name="compatibility"></a>Kompatibilita
Tato funkce zahrnuje odpovídající kritériím, je potřeba splnit, předtím, než lze použít na požadavek. Aby nedošlo k nastavení konfliktní kritéria shody, tato funkce není kompatibilní s následující podmínky shody:

- JAKO číslo
- Zdroj CDN
- IP adresa klienta
- Zákazníka
- Schéma požadavku
- Adresář cesty URL
- Rozšíření cesty adresy URL
- Adresa URL cesta název_souboru
- Literál cestu adresy URL
- Regulární výraz cesty adresy URL
- Cesta adresy URL zástupný znak
- Adresa URL dotazu literál
- Parametr dotazu adresy URL
- Regulární výraz dotazu adresy URL
- Adresa URL dotazu zástupných znaků

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Uživatelské proměnné
**Účel:** pouze pro interní použití.

[Zpět na začátek](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Další kroky
* [Referenční informace ke stroji pravidel](cdn-rules-engine-reference.md)
* [Podmíněné výrazy stroje pravidel](cdn-rules-engine-reference-conditional-expressions.md)
* [Podmínky shody stroje pravidel](cdn-rules-engine-reference-match-conditions.md)
* [Potlačení chování HTTP pomocí stroje pravidel](cdn-rules-engine.md)
* [Přehled služby Azure CDN](cdn-overview.md)
