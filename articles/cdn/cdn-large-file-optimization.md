---
title: Optimalizace velkých souborů ke stažení s Azure CDN
description: Tento článek vysvětluje, jak velký soubor, soubory ke stažení, lze optimalizovat.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9793348b47763e6de10992b9a8a4606fc532cc4d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094016"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimalizace velkých souborů ke stažení s Azure CDN

Velikosti souborů obsahu doručit přes internet i nadále růst díky vylepšené funkce, vylepšení grafiky a bohatý mediální obsah. Tímto růstem vychází celá řada faktorů: širokopásmové průniku, větší levné úložných zařízení, rozšířených zvýšení vysokém videa a připojené k Internetu zařízení (IoT). Rychlé a efektivní doručování mechanismus pro velké soubory je důležité zajistit hladký a dovoluje uživatelské prostředí.

Doručování velkých souborů má několik problémů. Průměrná doba stahování velkých souborů nejdřív, může být důležité, protože aplikace nemusí stahovat všechna data postupně. V některých případech může aplikace lze stáhnout v poslední části souboru před první část. Když je požadováno pouze malé množství souboru nebo nastavení stahování, stahování může selhat. Stahování také můžou být zpožděné až po síti pro doručování obsahu (CDN), načte celý soubor ze zdrojového serveru. 

Za druhé latence mezi uživatele počítače a souboru určuje rychlost, jakou můžou si zobrazit obsah. Kromě toho přetížení a kapacitu problémy se sítí také ovlivnit propustnost. Další příležitosti pro ztrátu paketů pravděpodobnější, což snižuje kvalitu vytvořit větší vzdálenosti mezi servery a uživatele. Snížení kvality způsobené omezená propustnost a ztráta paketů větší může prodloužit dobu čekání pro stahování souborů na dokončení. 

Třetí mnoho velkých souborů nejsou doručeny v plné výši. Uživatelé mohou zrušit stahování polovinu životnosti nebo sledovat pouze prvních několik minut dlouhé video MP4. Proto software a mediálním společnostem doručení má být dodána pouze část souboru, které jsou požadovány. Efektivní distribuci požadované části snižuje výchozí přenos ze zdrojového serveru. Efektivní distribuci také snižuje požadavek paměť a vstupně-výstupních operací tlak na původním serveru. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimalizovat doručování velkých souborů pomocí Azure CDN od společnosti Microsoft

**Azure CDN Standard od společnosti Microsoft** koncové body doručování velkých souborů bez limitu velikosti souboru. Další funkce jsou zapnuté ve výchozím nastavení provádět rychlejší doručování velkých souborů.

### <a name="object-chunking"></a>Objekt dělením dat do bloků 

**Azure CDN Standard od společnosti Microsoft** pomocí techniky označované jako objekt bloků. Pokud se požaduje s velkými soubory, CDN načte menších souboru z původního zdroje. Až serveru CDN POP obdrží žádost o úplné nebo rozsah bajtů souboru, CDN edge server si soubor vyžádá ze zdroje v blocích 8 MB. 

Po bloku dat dorazí na hranici CDN, má uložené v mezipaměti a okamžitě obsluhovat uživateli. CDN pak prefetches další blok paralelně. Této předběžné načtení se zajistí, že obsah zůstane jeden blok před časem uživatele, což snižuje latence. Tento proces pokračuje, dokud se celý soubor se stáhne (je-li požadovány), všechny rozsahů bajtů jsou k dispozici (je-li požadovány), nebo klient ukončí připojení. 

Další informace o žádosti o rozsah bajtů, naleznete v tématu [RFC 7233](https://tools.ietf.org/html/rfc7233).

Síť CDN ukládá do mezipaměti všechny bloky dat po přijetí. Celý soubor nemusí být uložena do mezipaměti CDN mezipaměti. Odeslání dalších žádostí o souboru nebo bajt rozsahy se obsluhují z mezipaměti CDN. Není-li všechny bloky dat jsou uložené v mezipaměti v CDN, předběžné načtení slouží k vyžádání bloků dat z původního zdroje. Tato optimalizace se spoléhá na schopnost zdrojový server podporovat požadavky na zjištění rozsahu bajtů; Pokud zdrojový server nepodporuje požadavky na zjištění rozsahu bajtů, optimalizací není platná. 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro optimalizace velkých souborů
Optimalizace velkých souborů funkcí pro **Azure CDN Standard od společnosti Microsoft** jsou ve výchozím nastavení zapnutý při použití typu optimalizace doručení obecné webu. Neplatí žádné limity na maximální velikost souboru.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimalizovat doručování velkých souborů pomocí Azure CDN od Verizonu

**Azure CDN Standard od Verizonu** a **Azure CDN Premium od Verizonu** koncové body doručování velkých souborů bez limitu velikosti souboru. Další funkce jsou zapnuté ve výchozím nastavení provádět rychlejší doručování velkých souborů.

### <a name="complete-cache-fill"></a>Výplň kompletní mezipaměti

Funkce výchozí kompletní mezipaměti výplně umožňuje CDN pro vyžádanou souboru do mezipaměti při opuštění nebo ztráty počáteční požadavku. 

Výplň kompletní mezipaměti je zvláště užitečná pro velké prostředky. Obvykle uživatele není si je stáhnout od začátku do konce. Používají progresivní stahování. Výchozí chování způsobí, že server edge k zahájení načítání na pozadí prostředku ze zdrojového serveru. Následně prostředek je v místní mezipaměti hraničním serveru. Po úplné objektu v mezipaměti, edge server splňuje požadavky na zjištění rozsahu bajtů do sítě CDN pro objekt uložený v mezipaměti.

Výchozí chování lze zakázat pomocí stroj pravidel v **Azure CDN Premium od Verizonu**.

### <a name="peer-cache-fill-hot-filing"></a>Sdílená mezipaměť vyplnit horkou vyplňování

Výchozí sdílené mezipaměti výplně horkou vyplňování funkce používá sofistikované vlastního algoritmu. Agregace požadavků metriky, aby vyplnila požadavky klienta pro velká a velmi populární objekty a používá další edge ukládání do mezipaměti serverech v závislosti na šířce pásma. Tato funkce zabraňuje situace, ve kterém jsou odesílány velký počet dalších požadavků na server původu uživatele. 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro optimalizace velkých souborů

Optimalizace velkých souborů funkcí pro **Azure CDN Standard od Verizonu** a **Azure CDN Premium od Verizonu** jsou ve výchozím nastavení zapnutý při použití typu optimalizace doručení obecné webu. Neplatí žádné limity na maximální velikost souboru. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimalizovat doručování velkých souborů pomocí Azure CDN Standard od Akamai

**Azure CDN Standard od Akamai** koncové body profilu nabízejí funkce, která přináší velké soubory efektivně uživatelům po celém světě ve velkém měřítku. Funkci snižuje latenci, protože snižuje zatížení původních serverů.

Funkce typ optimalizace velkých souborů zapne optimalizace sítě a konfigurace k doručování velkých souborů, rychlejší a více responzivně. Obecné doručování webu s **Azure CDN Standard od Akamai** koncových bodů mezipaměti soubory pouze pod 1,8 GB a můžete soubory tunelového propojení (ne mezipaměť) až 150 GB. Mezipaměti optimalizace velkých souborů soubory až do 150 GB.

Optimalizace velkých souborů je účinné, pokud jsou splněny určité podmínky. Příkladem podmínek může být jak zdrojový server funguje a velikosti a typy souborů, které jsou požadovány. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurace koncového bodu CDN společnosti Akamai optimalizovat doručování velkých souborů

Můžete nakonfigurovat váš **Azure CDN Standard od Akamai** koncový bod pro optimalizaci doručování velkých souborů prostřednictvím portálu Azure portal. Provedete to tak můžete také použít rozhraní REST API nebo některou z klientské sady SDK. Následující kroky ukazují proces prostřednictvím webu Azure portal **Azure CDN Standard od Akamai** profilu:

1. Chcete-li přidat nový koncový bod na Akamai **profil CDN** stránce **koncový bod**.

    ![Nový koncový bod](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. V **optimalizovaná pro** rozevíracího seznamu vyberte **stahování velkých souborů**.

    ![Optimalizace velkých souborů vybrané](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Po vytvoření koncového bodu CDN, bude se vztahovat optimalizace velkých souborů pro všechny soubory, které splňují určitá kritéria. Následující část popisuje tento proces.

### <a name="object-chunking"></a>Objekt dělením dat do bloků 

Optimalizace velkých souborů s **Azure CDN Standard od Akamai** pomocí techniky označované jako objekt bloků. Pokud se požaduje s velkými soubory, CDN načte menších souboru z původního zdroje. Až serveru CDN POP obdrží žádost o úplné nebo rozsah bajtů souboru, zkontroluje, zda typ souboru se nepodporuje. Tato optimalizace. Také zkontroluje, zda tento typ souboru splňuje požadavky na velikost souboru. Pokud velikost souboru je větší než 10 MB, CDN edge server si soubor vyžádá ze zdroje v blocích 2 MB. 

Po bloku dat dorazí na hranici CDN, má uložené v mezipaměti a okamžitě obsluhovat uživateli. CDN pak prefetches další blok paralelně. Této předběžné načtení se zajistí, že obsah zůstane jeden blok před časem uživatele, což snižuje latence. Tento proces pokračuje, dokud se celý soubor se stáhne (je-li požadovány), všechny rozsahů bajtů jsou k dispozici (je-li požadovány), nebo klient ukončí připojení. 

Další informace o žádosti o rozsah bajtů, naleznete v tématu [RFC 7233](https://tools.ietf.org/html/rfc7233).

Síť CDN ukládá do mezipaměti všechny bloky dat po přijetí. Celý soubor nemusí být uložena do mezipaměti CDN mezipaměti. Odeslání dalších žádostí o souboru nebo bajt rozsahy se obsluhují z mezipaměti CDN. Není-li všechny bloky dat jsou uložené v mezipaměti v CDN, předběžné načtení slouží k vyžádání bloků dat z původního zdroje. Tato optimalizace se spoléhá na schopnost zdrojový server podporovat požadavky na zjištění rozsahu bajtů; Pokud zdrojový server nepodporuje požadavky na zjištění rozsahu bajtů, optimalizací není platná.

### <a name="caching"></a>Ukládání do mezipaměti
Optimalizace velkých souborů používá různé výchozí dobu ukládání do mezipaměti vypršení platnosti od obecné doručování webu. Rozlišuje mezi ukládání do mezipaměti kladné a záporné ukládání do mezipaměti na základě kódů odpovědí HTTP. Pokud zdrojový server určuje dobu vypršení platnosti prostřednictvím cache-control nebo expires hlaviček v odpovědi, CDN respektuje tuto hodnotu. Když soubor odpovídá podmínkám typu a velikosti pro tento typ optimalizace původ neurčuje, CDN používá výchozí hodnoty pro optimalizace velkých souborů. V opačném případě CDN používá výchozí nastavení pro obecné doručování webu.


|    | Obecné web | Optimalizace velkých souborů 
--- | --- | --- 
Ukládání do mezipaměti: pozitivní <br> HTTP 200, 203, 300, <br> 301, 302 a 410 | 7 dní |1 den  
Ukládání do mezipaměti: záporná <br> HTTP 204, 305, 404, <br> a 405 | Žádný | 1 sekunda 

### <a name="deal-with-origin-failure"></a>Řešení selhání původu

Čtení – časový limit délky původu zvyšuje ze dvou sekund pro obecné doručování webu do dvou minut, než se typ optimalizace velkých souborů. Toto zvýšení účty pro větší velikosti souborů, aby se zabránilo předčasné časový limit připojení.

Když vyprší časový limit připojení, síť CDN opakování s počtem opakování, než odešle klientovi chybu "504 – časový limit brány". 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro optimalizace velkých souborů

V následující tabulce jsou uvedeny sadu kritérií, které je třeba splnit pro optimalizace velkých souborů:

Podmínka | Hodnoty 
--- | --- 
Podporované typy souborů | 3g, 2, 3gp, amp, avi, bz2, dmg, exe, f4v, flv, <br> GZ, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> MPEG, mpg, mts, balíčku, qt, rm, swf tar, <br> TGZ, wdp, webm, webp, wma, wmv, zip  
Minimální velikost souboru | 10 MB 
Maximální velikost souboru | 150 GB 
Vlastnosti serveru původu | Musí podporovat požadavky na zjištění rozsahu bajtů 

## <a name="additional-considerations"></a>Další aspekty

Vezměte v úvahu následující další aspekty pro tento typ optimalizace:

- Proces vytváření bloků vygeneruje další požadavky na zdrojový server. Celkový objem dat od počátku je však mnohem menší. Vytváření bloků za následek lepší charakteristiky ukládání do mezipaměti v CDN.

- Paměti a přetížení vstupně-výstupních operací se snížit na počátku, protože se doručují menších souboru.

- Pro bloky dat uložené v mezipaměti v CDN neexistují žádné další požadavky na počátku až vyprší platnost obsahu nebo je odstraněn z mezipaměti.

- Mohou uživatelé provádět požadavky na zjištění rozsahu do sítě CDN, které jsou zpracovány jako jakýkoli normální soubor. Optimalizace platí jenom v případě, že je platný typ souboru a rozsah bajtů je 10 MB až 150 GB. Pokud je požadovaná velikost průměrné soubor menší než 10 MB, použijte obecné doručování webu.

