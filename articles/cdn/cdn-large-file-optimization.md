---
title: Optimalizace stahování velkých souborů pomocí Azure CDN
description: Tento článek vysvětluje, jak lze optimalizovat stahování velkých souborů.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 4fe72985a799595908a0ff6bceb1a73dca823c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593784"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimalizace stahování velkých souborů pomocí Azure CDN

Velikost souborů obsahu dodaného přes internet nadále roste díky vylepšeným funkcím, vylepšené grafice a bohatému mediálnímu obsahu. Tento růst je způsoben mnoha faktory: rozšířením širokopásmového připojení, většími levnými paměťovými zařízeními, rozšířeným nárůstem videa s vysokým rozlišením a zařízeními připojenými k internetu (IoT). Rychlý a efektivní mechanismus doručování velkých souborů je rozhodující pro zajištění hladkého a příjemného zážitku pro spotřebitele.

Doručování velkých souborů má několik výzev. Za prvé, průměrná doba stahování velkého souboru může být významná, protože aplikace nemusí stahovat všechna data postupně. V některých případech mohou aplikace stáhnout poslední část souboru před první částí. Pokud je požadováno pouze malé množství souboru nebo uživatel pozastaví stahování, stahování může selhat. Stahování také může být odloženo až poté, co síť pro doručování obsahu (CDN) načte celý soubor z původního serveru. 

Za druhé, latence mezi počítačem uživatele a souborem určuje rychlost, jakou mohou zobrazit obsah. Propustnost navíc ovlivňují také zahlcení sítě a problémy s kapacitou. Větší vzdálenosti mezi servery a uživateli vytvářejí další příležitosti pro ztrátu paketů, což snižuje kvalitu. Snížení kvality způsobené omezenou propustností a zvýšenou ztrátou paketů může prodloužit dobu čekání na dokončení stahování souboru. 

Za třetí, mnoho velkých souborů nejsou dodávány v plném rozsahu. Uživatelé mohou zrušit stahování v polovině nebo sledovat pouze prvních pár minut dlouhého videa MP4. Společnosti dodávací software a média proto chtějí dodat pouze část požadovaného souboru. Efektivní distribuce požadovaných částí snižuje odchozí provoz ze původního serveru. Efektivní distribuce také snižuje tlak paměti a vstupně-videa na zdrojový server. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimalizujte pro doručování velkých souborů s Azure CDN od Microsoftu

**Azure CDN Standard od koncových** bodů Microsoftu poskytuje velké soubory bez omezení velikosti souboru. Další funkce jsou ve výchozím nastavení zapnuty, aby se doručení velkých souborů urychlilo.

### <a name="object-chunking"></a>Bloků objektů 

**Azure CDN Standard od Microsoftu** používá techniku zvanou bloků objektů. Je-li požadován velký soubor, CDN načte menší části souboru z počátku. Poté, co server CDN POP obdrží úplný nebo byte-range požadavek na soubor, server EDGE CDN požaduje soubor od počátku v blocích 8 MB. 

Poté, co blok dorazí na okraj CDN, je uložen do mezipaměti a okamžitě doručena uživateli. CDN pak předem načte další blok paralelně. Toto předběžné načtení zajišťuje, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud není stažen celý soubor (pokud je požadováno), všechny rozsahy bajtů jsou k dispozici (na vyžádání) nebo klient ukončí připojení. 

Další informace o požadavku na rozsah bajtů naleznete v [tématu RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN ukládá všechny bloky, jak jsou přijaty. Celý soubor nemusí být uložen do mezipaměti CDN. Následné požadavky na soubor nebo rozsahy bajtů jsou obsluhovány z mezipaměti CDN. Pokud nejsou všechny bloky jsou uloženy v mezipaměti na CDN, předběžné načtení se používá k vyžádání bloků z počátku. Tato optimalizace závisí na schopnosti původního serveru podporovat požadavky rozsahu bajtů; Pokud zdrojový server nepodporuje požadavky na rozsah bajtů, tato optimalizace není efektivní. 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro rozsáhlou optimalizaci souborů
Funkce pro optimalizaci velkých souborů pro **Azure CDN Standard od Microsoftu** jsou ve výchozím nastavení zapnuté při použití obecného typu optimalizace webového doručování. Maximální velikost souboru není nijak limitována.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimalizujte pro doručování velkých souborů s Azure CDN od verizonu

**Azure CDN Standard od Verizonu** a **Azure CDN Premium od koncových** bodů Verizon udávají velké soubory bez omezení velikosti souboru. Další funkce jsou ve výchozím nastavení zapnuty, aby se doručení velkých souborů urychlilo.

### <a name="complete-cache-fill"></a>Kompletní vyplnění mezipaměti

Výchozí funkce úplného vyplnění mezipaměti umožňuje cdn vytáhnout soubor do mezipaměti, když je původní požadavek opuštěn nebo ztracen. 

Úplné vyplnění mezipaměti je nejužitečnější pro velké datové zdroje. Uživatelé je obvykle nestahují od začátku do konce. Používají progresivní stahování. Výchozí chování vynutí, aby hraniční server inicioval načítání datového zdroje na pozadí ze zdrojového serveru. Poté je datový zdroj v místní mezipaměti hraničního serveru. Poté, co je celý objekt v mezipaměti, hraniční server splňuje požadavky na rozsah bajtů na CDN pro objekt uložený v mezipaměti.

Výchozí chování lze zakázat prostřednictvím modulu pravidel v **Azure CDN Premium od společnosti Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Peer cache vyplnit hot-podání

Výchozí funkce vyplnění hot-filing peer cache používá sofistikovaný proprietární algoritmus. Používá další servery pro ukládání okrajů do mezipaměti založené na šířce pásma a agregační požadavky metriky splnit požadavky klientů pro velké, velmi populární objekty. Tato funkce zabraňuje situaci, kdy jsou odesílány velké množství dalších požadavků na zdrojový server uživatele. 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro rozsáhlou optimalizaci souborů

Funkce pro optimalizaci velkých souborů pro **Azure CDN Standard od Verizonu** a **Azure CDN Premium od verizonu** jsou ve výchozím nastavení zapnuté při použití obecného typu optimalizace webového doručování. Maximální velikost souboru není nijak limitována. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimalizujte pro doručování velkých souborů pomocí Azure CDN Standard z Akamai

Koncové body profilu **Azure CDN Standard od Akamai** nabízejí funkci, která uživatelům po celém světě ve velkém měřítku efektivně poskytuje velké soubory. Tato funkce snižuje latence, protože snižuje zatížení na původní servery.

Funkce velkého typu optimalizace souborů zapne optimalizace sítě a konfigurace, aby poskytovala velké soubory rychleji a lépe. Obecné doručování webu pomocí **Azure CDN Standard z koncových bodů Akamai** ukládá soubory do mezipaměti pouze pod 1,8 GB a může tunelovat (ne cache) soubory až do 150 GB. Optimalizace velkých souborů ukládá soubory až do velikosti 150 GB.

Optimalizace velkých souborů je účinná, pokud jsou splněny určité podmínky. Podmínky zahrnují fungování původního serveru a velikosti a typy požadovaných souborů. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurace koncového bodu Akamai CDN pro optimalizaci doručování velkých souborů

Azure CDN Standard můžete nakonfigurovat z koncového bodu **Akamai** a optimalizovat tak doručování pro velké soubory prostřednictvím portálu Azure. Můžete také použít rest API nebo některý z klientských sad SDK k tomu. Následující kroky ukazují proces prostřednictvím portálu Azure pro Azure CDN Standard z profilu **Akamai:**

1. Pokud chcete přidat nový koncový bod, vyberte na stránce profilu Akamai **CDN** možnost **Koncový bod**.

    ![Nový koncový bod](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. V rozevíracím seznamu **Optimalizováno pro** vyberte **možnost Stažení velkého souboru**.

    ![Byla vybrána optimalizace velkých souborů.](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Po vytvoření koncového bodu CDN použije velké optimalizace souborů pro všechny soubory, které odpovídají určitým kritériím. Následující část popisuje tento proces.

### <a name="object-chunking"></a>Bloků objektů 

Optimalizace velkých souborů s **Azure CDN Standard od Akamai** používá techniku zvanou objekt chunking. Je-li požadován velký soubor, CDN načte menší části souboru z počátku. Poté, co server CDN POP obdrží úplný nebo byte-range požadavek na soubor, zkontroluje, zda je pro tuto optimalizaci podporován typ souboru. Také zkontroluje, zda typ souboru splňuje požadavky na velikost souboru. Pokud je velikost souboru větší než 10 MB, server edge CDN požaduje soubor od počátku v blocích o velikosti 2 MB. 

Poté, co blok dorazí na okraj CDN, je uložen do mezipaměti a okamžitě doručena uživateli. CDN pak předem načte další blok paralelně. Toto předběžné načtení zajišťuje, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud není stažen celý soubor (pokud je požadováno), všechny rozsahy bajtů jsou k dispozici (na vyžádání) nebo klient ukončí připojení. 

Další informace o požadavku na rozsah bajtů naleznete v [tématu RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN ukládá všechny bloky, jak jsou přijaty. Celý soubor nemusí být uložen do mezipaměti CDN. Následné požadavky na soubor nebo rozsahy bajtů jsou obsluhovány z mezipaměti CDN. Pokud nejsou všechny bloky jsou uloženy v mezipaměti na CDN, předběžné načtení se používá k vyžádání bloků z počátku. Tato optimalizace závisí na schopnosti původního serveru podporovat požadavky rozsahu bajtů; Pokud zdrojový server nepodporuje požadavky na rozsah bajtů, tato optimalizace není efektivní.

### <a name="caching"></a>Ukládání do mezipaměti
Optimalizace velkých souborů používá různé výchozí doby ukládání do mezipaměti a vypršení platnosti z obecného doručování webu. Rozlišuje mezi pozitivní ukládání do mezipaměti a negativní ukládání do mezipaměti na základě kódů odezvy HTTP. Pokud zdrojový server určuje čas vypršení platnosti prostřednictvím ovládacího prvku mezipaměti nebo vyprší platnost záhlaví v odpovědi, CDN ctí tuto hodnotu. Pokud počátek neurčuje a soubor odpovídá podmínkám typu a velikosti pro tento typ optimalizace, cdn používá výchozí hodnoty pro rozsáhlé optimalizace souborů. V opačném případě cdn používá výchozí hodnoty pro obecné doručování na webu.


|    | Obecný web | Optimalizace velkých souborů 
--- | --- | --- 
Ukládání do mezipaměti: Pozitivní <br> HTTP 200, 203, 300, <br> 301, 302 a 410 | 7 dní |1 den  
Ukládání do mezipaměti: Negativní <br> HTTP 204, 305, 404, <br> a 405 | Žádný | 1 sekunda 

### <a name="deal-with-origin-failure"></a>Řešení selhání původu

Délka časového času při čtení původu se zvětší ze dvou sekund pro obecné doručování webu na dvě minuty pro typ optimalizace velkých souborů. Toto zvýšení účty pro větší velikosti souborů, aby se zabránilo předčasnému časovému limitu připojení.

Při výpadku časového limitu připojení CDN opakuje několikrát před odesláním chyby "504 - Gateway Timeout" klientovi. 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro rozsáhlou optimalizaci souborů

V následující tabulce je uvedena sada kritérií, která mají být splněna pro rozsáhlou optimalizaci souborů:

Podmínka | Hodnoty 
--- | --- 
Podporované typy souborů | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, dehet, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minimální velikost souboru | 10 MB 
Maximální velikost souboru | 150 GB 
Charakteristiky původního serveru | Musí podporovat požadavky na rozsah bajtů. 

## <a name="additional-considerations"></a>Další aspekty

Zvažte následující další aspekty pro tento typ optimalizace:

- Proces bloků generuje další požadavky na zdrojový server. Celkový objem dat dodaných z počátku je však mnohem menší. Chunking má za následek lepší ukládání do mezipaměti charakteristiky na CDN.

- Paměť a vstupně-v/O tlak jsou sníženy na počátku, protože menší části souboru jsou dodávány.

- Pro bloky do mezipaměti v síti CDN neexistují žádné další požadavky na původ, dokud obsah nevyprší nebo jeho vyřazení z mezipaměti.

- Uživatelé mohou provádět požadavky na rozsah cdn, které jsou považovány za každý normální soubor. Optimalizace platí pouze v případě, že se jedná o platný typ souboru a rozsah bajtů je mezi 10 MB a 150 GB. Pokud je průměrná požadovaná velikost souboru menší než 10 MB, použijte místo toho obecné webové doručení.

