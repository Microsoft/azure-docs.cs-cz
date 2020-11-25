---
title: Azure CDN z funkcí modulu pravidel Premium Verizon
description: Referenční dokumentace pro Azure CDN z funkcí modulu pravidel Premium Verizon
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020411"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN z funkcí modulu pravidel Premium Verizon

V tomto článku jsou uvedené podrobné popisy dostupných funkcí pro [modul pravidel](cdn-verizon-premium-rules-engine.md)služby Azure Content DELIVERY Network (CDN).

Třetí část pravidla je funkce. Funkce definuje typ akce, která je použita na typ požadavku, který je identifikován sadou podmínek shody.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Referenční informace o funkcích stroje Verizon Premium Rules Azure CDN

Dostupné typy funkcí jsou:

* [Přístup](#access)
* [Ukládání do mezipaměti](#caching)
* [Komentář](#comment)
* [Hlavičky](#headers)
* [Protokoly](#logs)
* [Optimalizace](#optimize)
* [Zdroji](#origin)
* [Specializace](#specialty)
* [Adresa URL](#url)
* [Firewall webových aplikací](#waf)

### <a name="access"></a><a name="access"></a>Access

Tyto funkce jsou navržené pro řízení přístupu k obsahu.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Odepřít přístup (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Určuje, zda jsou všechny požadavky odmítnuty pomocí odpovědi 403 Forbidden. |
| [Ověření tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Určuje, zda bude pro požadavek použito ověřování Token-Based. |
| [Kód pro odepření ověření tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Určuje typ odpovědi, která bude vrácena uživateli, pokud je žádost zamítnuta z důvodu Token-Basedho ověřování. |
| [Ignorovat velikost písmen URL pro ověření tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Určuje, zda se při porovnávání adres URL provedených ověřováním Token-Based rozlišují velká a malá písmena. |
| [Parametr ověření tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Určuje, zda má být přejmenován parametr řetězce dotazu Token-Based Authentication. |

**[Zpět na začátek](#top)**

### <a name="caching"></a><a name="caching"></a>Ukládání do mezipaměti

Tyto funkce jsou navržené tak, aby se přizpůsobily, kdy a jak se obsah ukládá do mezipaměti.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Parametry šířky pásma](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Určuje, zda budou aktivní parametry omezení šířky pásma (tj. ec_rate a ec_prebuf). |
| [Omezení šířky pásma](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Omezuje šířku pásma pro odpověď poskytovanou našimi hraničními servery. |
| [Vynechat mezipaměť](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Určuje, jestli může požadavek využívat technologii pro ukládání do mezipaměti. |
| [Zpracování hlaviček Cache-Control](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Řídí generování hlaviček Cache-Control serverem Edge, pokud je aktivní funkce externích Max-Age. |
| [Řetězec dotazu na klíč v mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Určuje, zda bude **mezipaměť-Key** _ zahrnout nebo vyloučit parametry řetězce dotazu přidružené k žádosti. <br> __ Relativní cesta, která jednoznačně identifikuje prostředek pro účely ukládání do mezipaměti.  Naše hraniční servery používají tuto relativní cestu při kontrole obsahu v mezipaměti.  Ve výchozím nastavení nebude klíč mezipaměti obsahovat řetězec dotazu parameters._ |
| [Přepsání klíče do mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Přepíše klíč mezipaměti přidružený k žádosti. |
| [Vyplnit celou mezipaměť](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Určuje, co se stane, když požadavek způsobí částečnou Neúspěšné přístupy do mezipaměti na hraničním serveru. |
| [Komprimovat typy souborů](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Definuje formáty souborů, které budou zkomprimovány na serveru. | 
| [Výchozí interní maximální stáří](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Určuje výchozí maximální dobu platnosti hraničního serveru pro opětovné ověření mezipaměti serveru. |
| [Konec platnosti hlaviček](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Řídí generování hlaviček vypršení platnosti serverem hraničního serveru, pokud je funkce externí Max-Age aktivní. |
| [Externí maximum – stáří](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Určuje maximální stáří pro opětovné ověření platnosti mezipaměti na hraničním serveru v prohlížeči. |
| [Vynutit interní maximum – stáří](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Určuje maximální stáří pro opětovné ověření platnosti mezipaměti serveru hraničního serveru. |
| [H. 264 podpora (progresivní stahování HTTP)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Určuje typy formátů souborů H. 264, které mohou být použity pro streamování obsahu. |
| [H. 264 podporují parametry hledání videí.](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Přepíše názvy přiřazené parametrům, které řídí hledání prostřednictvím média H. 264 při použití progresivního stahování HTTP. |
| [Akceptovat No-Cache požadavek](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Určuje, zda budou do zdrojového serveru předány požadavky na ukládání do mezipaměti klienta HTTP. |
| [Ignorovat původ – mezipaměť](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Určuje, jestli naše CDN bude ignorovat některé direktivy poskytované ze zdrojového serveru. |
| [Ignorovat nevyhovující rozsahy](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Určuje odpověď, která bude vrácena klientům, pokud požadavek vygeneruje 416 požadovaný rozsah nevyhovující stavový kód. |
| [Interní Max – zastaralé](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Určuje, jak dlouho po normální době vypršení platnosti může být prostředek uložený v mezipaměti obsluhován z hraničního serveru, když hraniční Server nemůže znovu ověřit prostředek uložený v mezipaměti se zdrojovým serverem. |
| [Částečné sdílení mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Určuje, zda může požadavek vygenerovat částečně uložený obsah v mezipaměti. |
| [Předběžně ověřit obsah v mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Určuje, jestli bude obsah uložený v mezipaměti způsobilý k předčasnému revalidu před vypršením platnosti TTL. |
| [Aktualizovat soubory mezipaměti Zero-Byte](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Určuje, jakým způsobem server Edge zpracovává požadavek klienta HTTP na předaný prostředek mezipaměti o velikosti 0 bajtů. |
| [Nastavit stavové kódy pro ukládání do mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Definuje sadu stavových kódů, které mohou mít za následek obsah uložený v mezipaměti. |
| [Zastaralé doručování obsahu při chybě](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Určuje, zda bude doručen obsah uložený v mezipaměti, když dojde k chybě během revalidu mezipaměti nebo při načítání požadovaného obsahu ze serveru zákazníka. | 
| [Zastaralé během Revalidace](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Zvyšuje výkon tím, že umožňuje našim hraničním serverům zajišťovat zastaralého klienta žadateli, zatímco probíhá resynchronizace. |

**[Zpět na začátek](#top)**

### <a name="comment"></a><a name="comment"></a>Komentář

Funkce komentáře umožňuje přidat poznámku v rámci pravidla.

**[Zpět na začátek](#top)**

### <a name="headers"></a><a name="headers"></a>Hlavičky

Tyto funkce jsou navržené tak, aby je bylo možné přidat, upravit nebo odstranit v žádosti nebo odpovědi.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Hlavička doby odezvy](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Určuje, zda bude v odpovědi odeslané do žadatele zahrnutá hlavička věkové odpovědi. |
| [Hlavičky odpovědí mezipaměti ladění](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Určuje, zda odpověď může obsahovat [hlavičku odpovědi X-EC-Debug](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) , která poskytuje informace o zásadách mezipaměti pro požadovaný prostředek. |
| [Upravit hlavičku žádosti klienta](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Přepíše, připojí nebo odstraní hlavičku z požadavku. |
| [Upravit hlavičku odpovědi klienta](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Přepíše, připojí nebo odstraní hlavičku z odpovědi. |
| [Nastavit vlastní hlavičku IP adresy klienta](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Umožňuje přidat IP adresu žádajícího klienta do žádosti jako vlastní hlavičku požadavku. |

**[Zpět na začátek](#top)**

### <a name="logs"></a><a name="logs"></a>Protokoly

Tyto funkce jsou navržené k přizpůsobení dat uložených v nezpracovaných souborech protokolů.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Vlastní pole protokolu 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Určuje formát a obsah, který bude přiřazen k poli vlastního protokolu v nezpracovaném souboru protokolu. |
| [Řetězec dotazu protokolu](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Určuje, zda bude řetězec dotazu uložen spolu s adresou URL v protokolech přístupu. |

**[Zpět na začátek](#top)**

### <a name="optimize"></a><a name="optimize"></a>Optimalizace

Tyto funkce určují, jestli se požadavek bude podrobit optimalizacím poskytovaným optimalizátorem Edge.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Optimalizátor Edge](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Určuje, zda lze pro požadavek použít Optimalizátor okrajů. |
| [Optimalizátor Edge – vytvoření instance konfigurace](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Vytvoří nebo aktivuje konfiguraci optimalizace Edge přidruženou k lokalitě. |

**[Zpět na začátek](#top)**

### <a name="origin"></a><a name="origin"></a>Zdroji

Tyto funkce jsou navržené tak, aby bylo možné řídit, jak CDN komunikuje se zdrojovým serverem.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Maximální počet žádostí Keep-Alive](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Definuje maximální počet požadavků pro Keep-Alive připojení před jeho zavřením. |
| [Speciální hlavičky proxy serveru](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Definuje sadu [hlaviček žádostí specifických pro CDN](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) , které budou předány z hraničního serveru na zdrojový server. |

**[Zpět na začátek](#top)**

### <a name="specialty"></a><a name="specialty"></a>Specializace

Tyto funkce poskytují pokročilé funkce, které by měly používat jenom pokročilí uživatelé.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Metody HTTP, které je možné ukládat do mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Určuje sadu dalších metod HTTP, které lze uložit do mezipaměti v naší síti. |
| [Velikost textu žádosti v mezipaměti](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Definuje prahovou hodnotu pro určení, zda lze odpověď POST ukládat do mezipaměti. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Určuje, jestli bude klient informován o tom, že služba CDN podporuje QUIC. |
| [Optimalizace streamování](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Vyladí konfiguraci ukládání do mezipaměti a optimalizuje výkon pro živé streamy a snižuje zatížení na zdrojovém serveru. |
| [Uživatelská proměnná](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Přiřadí hodnotu proměnné definované uživatelem, která je předána vašemu řešení zpracování provozu Bespoke. |

**[Zpět na začátek](#top)**

### <a name="url"></a><a name="url"></a>URL

Tyto funkce umožňují přesměrování nebo přepsání požadavku na jinou adresu URL.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Sledovat přesměrování](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Určuje, zda mohou být požadavky přesměrovány na název hostitele definovaný v hlavičce umístění, kterou vrátí server původu zákazníka. |
| [Přesměrování adresy URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Přesměruje požadavky přes hlavičku umístění. |
| [Přepsání adresy URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Přepíše adresu URL požadavku. |

**[Zpět na začátek](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall webových aplikací

Funkce [Firewall webových aplikací](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) určuje, jestli se požadavek bude monitorovat přes firewall webových aplikací.

**[Zpět na začátek](#top)**

Nejnovější funkce najdete v [dokumentaci k modulům Verizon Rules](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Další kroky

- [Odkazy na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Přepsání chování HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled Azure CDN](cdn-overview.md)
