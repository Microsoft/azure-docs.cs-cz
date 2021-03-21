---
title: Přesměrování adresy URL a přepsání adresy URL pomocí Azure front-bran Standard/Premium (Preview)
description: Tento článek vám pomůže porozumět tomu, jak přední dvířka Azure podporuje přesměrování adres URL a přepsání adresy URL pomocí sady pravidel pro službu Azure front-dveří.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099172"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Přesměrování adresy URL a přepsání adresy URL pomocí Azure front-bran Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Tento článek vám pomůže pochopit, jakým způsobem Azure front-in standard/Premium podporuje přesměrování adresy URL a přepsání adresy URL v sadě pravidel.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>Přesměrování adresy URL

Přední dvířka Azure můžou přesměrovat provoz na každou z těchto úrovní: protokol, název hostitele, cesta, řetězec dotazu a fragment. Tyto funkce se dají nakonfigurovat pro jednotlivé mikroslužby, protože přesměrování je založené na cestě. Přesměrování adresy URL vám umožní zjednodušit konfiguraci aplikace optimalizací využití prostředků a podporovat nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cest.

Přesměrování adresy URL můžete nakonfigurovat pomocí sady pravidel.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Snímek obrazovky při vytváření přesměrování adresy URL pomocí sady pravidel" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Typy přesměrování
Typ přesměrování nastaví stavový kód odpovědi pro klienty, aby porozuměl účelu přesměrování. Podporovány jsou následující typy přesměrování:

* **301 (přesunuto trvale)**: označuje, že cílovým prostředkům byl přiřazen nový trvalý identifikátor URI. Jakékoli budoucí odkazy na tento prostředek budou používat jeden z uzavřených identifikátorů URI. Pro přesměrování HTTP na HTTPS použijte stavový kód 301.
* **302 (nalezeno)**: označuje, že cílový prostředek je dočasně pod jiným identifikátorem URI. Vzhledem k tomu, že se přesměrování může v některých případech změnit, klient by měl nadále používat platný identifikátor URI žádosti pro budoucí požadavky.
* **307 (dočasné přesměrování)**: označuje, že cílový prostředek je dočasně pod jiným identifikátorem URI. Pokud má uživatelský agent automatické přesměrování na tento identifikátor URI, nesmí změnit metodu žádosti. Vzhledem k tomu, že přesměrování se může v průběhu času měnit, klient by měl pokračovat v používání původního platného identifikátoru URI žádosti pro budoucí požadavky.
* **308 (trvalé přesměrování)**: označuje, že cílový prostředek byl přiřazen k novému TRVALÉmu identifikátoru URI. Jakékoli budoucí odkazy na tento prostředek by měly používat jeden z uzavřených identifikátorů URI.

### <a name="redirection-protocol"></a>Protokol přesměrování
Můžete nastavit protokol, který se bude používat pro přesměrování. Nejběžnějšími případy použití funkce přesměrování je nastavení přesměrování HTTP na HTTPS.

* **Pouze https**: Nastavte protokol jenom na https, pokud chcete přesměrovat provoz z http na https. Přední dveře Azure doporučuje, abyste měli vždy nastavené přesměrování jenom na HTTPS.
* **Pouze http**: přesměruje příchozí požadavek na http. Tuto hodnotu použijte jenom v případě, že chcete zachovat provoz HTTP, který není šifrovaný.
* **Požadavek shody**: Tato možnost zachovává protokol používaný příchozím požadavkem. Požadavek HTTP tedy zůstane HTTP a požadavek HTTPS zůstane přesměrování post protokolu HTTPS.

### <a name="destination-host"></a>Cílový hostitel
V rámci konfigurace směrování přesměrování můžete také změnit název hostitele nebo doménu pro požadavek na přesměrování. Nastavením tohoto pole můžete změnit název hostitele v adrese URL pro přesměrování nebo jinak zachovat název hostitele z příchozího požadavku. Pomocí tohoto pole pak můžete přesměrovat všechny požadavky odeslané na `https://www.contoso.com/*` `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Cílová cesta
V případě, že chcete jako součást přesměrování nahradit segment cesty adresy URL, můžete toto pole nastavit s novou hodnotou cesty. V opačném případě můžete zvolit zachování hodnoty cesty jako součást přesměrování. Pomocí tohoto pole pak můžete přesměrovat všechny požadavky odeslané na `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Parametry řetězce dotazu
Můžete také nahradit parametry řetězce dotazu v přesměrované adrese URL. Chcete-li nahradit všechny existující řetězce dotazu z adresy URL příchozího požadavku, nastavte toto pole na hodnotu Replace a pak nastavte příslušnou hodnotu. V opačném případě můžete zachovat původní sadu dotazovacích řetězců nastavením pole na možnost zachovat. Pomocí tohoto pole můžete například přesměrovat veškerý provoz odeslaný na `https://www.contoso.com/foo/bar` do `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Fragment cíle
Cílový fragment je část adresy URL po #, kterou prohlížeč používá k pozemku v konkrétní části webové stránky. Nastavením tohoto pole můžete přidat fragment na adresu URL pro přesměrování.

## <a name="url-rewrite"></a>Přepsání adresy URL

Přední dvířka Azure podporují přepis adres URL, aby přepsaly cestu k žádosti, která je v cestě k původnímu zdroji. Přepsání adresy URL umožňuje přidat podmínky, abyste zajistili, že se adresa URL nebo zadaná záhlaví přepíší pouze v případě splnění určitých podmínek. Tyto podmínky vycházejí z informací o požadavku a odpovědi.

Pomocí této funkce můžete přesměrovat uživatele na různé zdroje založené na scénáři, typu zařízení a požadovaném typu souboru.

Přesměrování adresy URL můžete nakonfigurovat pomocí sady pravidel.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Snímek obrazovky při vytváření přepisu adresy URL se sadou pravidel" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Zdrojový vzor

Zdrojový vzor je cesta URL ve zdrojovém požadavku, která má být nahrazena. V současné době zdrojový vzor používá shodu na základě předpony. Pro vyhledání všech cest URL použijte lomítko (/) jako hodnotu zdrojového vzoru.

### <a name="destination"></a>Cíl

Můžete definovat cílovou cestu, která se má použít při přepisování. Cílová cesta přepíše zdrojový vzor.

### <a name="preserve-unmatched-path"></a>Zachovat neshodnou cestu

Zachovat neshodnou cestu umožňuje připojit zbývající cestu po zdrojovém vzoru k nové cestě.

Pokud je například nastaveno **zachovat neshodnou cestu na Ano**.
* Pokud je příchozí požadavek `www.contoso.com/sub/1.jpg` , zdrojový vzor se nastaví na `/` , cíl je nastaven na hodnotu `/foo/` a obsah se získá z `/foo/sub/1` . jpg od počátku.

* Pokud je příchozí požadavek `www.contoso.com/sub/image/1.jpg` , zdrojový vzor se nastaví na, cíl se nastaví na, obsah se získá od `/sub/` `/foo/` `/foo/image/1.jpg` počátku.

Například pokud je nastaveno **zachovat neshodnou cestu na ne**.
* Pokud je příchozí požadavek `www.contoso.com/sub/image/1.jpg` , zdrojový vzor se nastaví na `/sub/` , cílová metoda get nastaví na hodnotu `/foo/2.jpg` , obsah bude vždy obsluhován od `/foo/2.jpg` původu bez ohledu na to, na jakou cestu následovala `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [sadě pravidel Premium pro Azure front-end Standard/Premium](concept-rule-set.md).
