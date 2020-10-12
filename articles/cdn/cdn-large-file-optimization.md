---
title: Optimalizace stahování velkých souborů pomocí Azure CDN
description: Přečtěte si, jak je možné optimalizovat stahování souborů v Azure Content Delivery Network. Tento článek obsahuje několik scénářů.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 6258baf37d00d35da3b7c95519caabdfcaa34b2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88192634"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimalizace stahování velkých souborů pomocí Azure CDN

Velikost souboru obsahu doručeného přes Internet se i nadále rozrůstá kvůli rozšířeným funkcím, vylepšeným grafikám a bohatě formátovanému mediálnímu obsahu. Tento nárůst je založený na mnoha faktorech: průniky do širokopásmového připojení, větší levné paměťové zařízení, rozsáhlá zvýšení videa s vysokým rozlišením a zařízení připojená k Internetu (IoT). Rychlý a efektivní mechanismus doručování velkých souborů je důležitý, aby se zajistilo hladké a pohodlnější uživatelské prostředí.

Doručení velkých souborů má několik výzev. Za prvé, Průměrná doba stahování velkého souboru může být významná, protože aplikace nemusí stahovat všechna data sekvenčně. V některých případech mohou aplikace Stáhnout poslední část souboru před první částí. Pokud je požadováno pouze malé množství souboru nebo uživatel pozastaví stahování, stahování může selhat. Stahování je také možné zpozdit, dokud síť Content Delivery Network (CDN) nenačte celý soubor ze zdrojového serveru. 

Za druhé, latence mezi počítačem uživatele a souborem určuje rychlost, s jakou mohou zobrazovat obsah. Kromě toho propustnost sítě a problémy s kapacitou ovlivňují taky propustnost. Větší vzdálenosti mezi servery a uživateli vytvářejí další příležitosti pro ztrátu paketů, což snižuje kvalitu. Snížení kvality způsobené omezením propustnosti a vyšší ztrátou paketů může prodloužit dobu čekání na dokončení stahování souboru. 

Třetí, mnoho velkých souborů se nedoručuje v celém rozsahu. Uživatelé mohou zrušit stahování v polovině nebo sledovat pouze prvních pár minut dlouhého videa MP4. Proto společnosti pro doručování softwaru a médií chtějí doručovat pouze část požadovaného souboru. Efektivní distribuce požadovaných částí snižuje přenos dat ze zdrojového serveru. Efektivní distribuce také snižuje zatížení paměti a vstupně-výstupních operací na zdrojovém serveru. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimalizace pro doručování velkých souborů s Azure CDN od Microsoftu

**Azure CDN Standard od** koncových bodů Microsoftu dodávání velkých souborů bez Cap k velikosti souborů. Další funkce jsou ve výchozím nastavení zapnuté, aby bylo možné rychlejší doručování velkých souborů.

### <a name="object-chunking"></a>Vytváření bloků objektů 

**Azure CDN Standard od Microsoftu** používá techniku nazvanou bloků dat objektů. Když je požadován velký soubor, CDN načte menší části souboru od počátku. Až CDN server POP obdrží úplný požadavek nebo soubor v bajtovém rozsahu, požádá server CDN Edge o soubor od počátku v blocích po 8 MB. 

Jakmile se blok dorazí na hraniční síť CDN, uloží se do mezipaměti a okamžitě se dodrží uživateli. CDN pak znovu načte další blok. Tento předběžný postup zajistí, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud nebude stažen celý soubor (Pokud se požaduje), jsou dostupné všechny rozsahy bajtů (Pokud se požaduje), nebo klient připojení ukončí. 

Další informace o požadavku na rozsah bajtů najdete v [dokumentu RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN ukládá do mezipaměti všechny bloky dat, jak jsou přijaty. Celý soubor není nutné ukládat do mezipaměti v mezipaměti CDN. Následné požadavky na rozsahy souborů nebo bajtů jsou obsluhovány z mezipaměti CDN. Pokud nejsou všechny bloky dat v síti CDN uložené v mezipaměti, použije se k vypsání bloků od počátku předběžné načtení. Tato optimalizace spoléhá na to, jestli má zdrojový server podporovat požadavky na rozsah bajtů. Pokud zdrojový server nepodporuje požadavky na rozsah bajtů, tato optimalizace se neprojeví. 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro optimalizaci velkých souborů
Funkce optimalizace velkých souborů pro **Azure CDN Standard od Microsoftu** jsou standardně zapnuté, když použijete obecný typ optimalizace webového doručování. Neexistují žádná omezení pro maximální velikost souboru.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimalizace pro doručování velkých souborů s Azure CDN z Verizon

**Azure CDN Standard z Verizon** a **Azure CDN Premium z** koncových bodů Verizon dodávání velkých souborů bez Cap velikosti souboru. Další funkce jsou ve výchozím nastavení zapnuté, aby bylo možné rychlejší doručování velkých souborů.

### <a name="complete-cache-fill"></a>Vyplnit celou mezipaměť

Funkce vyplnit výchozí kompletní mezipaměť umožňuje, aby CDN při opuštění nebo ztrátě počátečního požadavku načetl do mezipaměti soubor. 

Doplňování mezipaměti je pro velké prostředky nejužitečnější. Obvykle je uživatelé nestahují od začátku do konce. Používají progresivní stahování. Výchozí chování vynutí hraniční Server, aby zahájil načtení assetu ze zdrojového serveru na pozadí. Následně se Asset nachází v místní mezipaměti hraničního serveru. Po dokončení celého objektu v mezipaměti hraniční Server vyplní požadavky bajtového rozsahu do CDN pro objekt v mezipaměti.

Výchozí chování je možné zakázat prostřednictvím modulu pravidel v **Azure CDN Premium z Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Doplňování mezipaměti sdílené mezipaměti v Hot-profilace

Výchozí funkce pro vyplňování mezipaměti sdílené mezipaměti používá sofistikovaný proprietární algoritmus. Používá další servery pro ukládání hran do mezipaměti založené na šířce pásma a agregované metriky požadavků pro splnění požadavků klientů pro velké, vysoce oblíbené objekty. Tato funkce zabrání situaci, kdy se do zdrojového serveru uživatele odesílají velké počty dalších požadavků. 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro optimalizaci velkých souborů

Funkce optimalizace velkých souborů pro **Azure CDN Standard od Verizon** a **Azure CDN Premium z Verizon** jsou ve výchozím nastavení zapnuté, když použijete obecný typ optimalizace webového doručování. Neexistují žádná omezení pro maximální velikost souboru. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimalizace pro doručování velkých souborů pomocí Azure CDN Standard z Akamai

**Azure CDN Standard z** koncových bodů profilu Akamai nabízí funkci, která efektivně doručuje velké soubory uživatelům po celém světě. Funkce snižuje latenci, protože snižuje zatížení na počátečních serverech.

Funkce typu optimalizace velkých souborů zapne optimalizace sítě a konfigurace, aby poskytovala velké soubory rychleji a rychleji reagovat. Obecné doručování webu pomocí **Azure CDN Standard od** koncových bodů Akamai ukládá soubory jenom pod 1,8 GB a může tunelové soubory (nikoli mezipaměť) ukládat do 150 GB. Optimalizace velkých souborů ukládá soubory do mezipaměti až do 150 GB.

Optimalizace velkých souborů je účinná, když jsou splněné určité podmínky. Mezi podmínky patří způsob fungování zdrojového serveru a velikosti a typy souborů, které jsou požadovány. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurace koncového bodu CDN Akamai pro optimalizaci doručování velkých souborů

**Azure CDN Standard** můžete nakonfigurovat z koncového bodu Akamai k optimalizaci doručování velkých souborů přes Azure Portal. K tomu můžete použít také rozhraní REST API nebo libovolné klientské sady SDK. Následující kroky ukazují proces prostřednictvím Azure Portal pro **Azure CDN Standard z profilu Akamai** :

1. Pokud chcete přidat nový koncový bod, na stránce **profilu CDN** Akamai vyberte **koncový bod**.

    ![Nový koncový bod](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. V rozevíracím seznamu **optimalizované pro** vyberte **velký soubor ke stažení**.

    ![Vybraná optimalizace velkých souborů](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Po vytvoření koncového bodu CDN se použijí optimalizace velkých souborů pro všechny soubory, které splňují určitá kritéria. Tento proces je popsán v následující části.

### <a name="object-chunking"></a>Vytváření bloků objektů 

Optimalizace velkých souborů s **Azure CDN standardem z Akamai** používá techniku nazývanou bloky objektů. Když je požadován velký soubor, CDN načte menší části souboru od počátku. Až CDN server POP obdrží úplný požadavek nebo soubor v bajtovém rozsahu, zkontroluje, jestli je tento typ souboru pro tuto optimalizaci podporovaný. Také kontroluje, zda typ souboru splňuje požadavky na velikost souboru. Pokud je velikost souboru větší než 10 MB, server CDN Edge požaduje soubor od počátku v blocích po 2 MB. 

Jakmile se blok dorazí na hraniční síť CDN, uloží se do mezipaměti a okamžitě se dodrží uživateli. CDN pak znovu načte další blok. Tento předběžný postup zajistí, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud nebude stažen celý soubor (Pokud se požaduje), jsou dostupné všechny rozsahy bajtů (Pokud se požaduje), nebo klient připojení ukončí. 

Další informace o požadavku na rozsah bajtů najdete v [dokumentu RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN ukládá do mezipaměti všechny bloky dat, jak jsou přijaty. Celý soubor není nutné ukládat do mezipaměti v mezipaměti CDN. Následné požadavky na rozsahy souborů nebo bajtů jsou obsluhovány z mezipaměti CDN. Pokud nejsou všechny bloky dat v síti CDN uložené v mezipaměti, použije se k vypsání bloků od počátku předběžné načtení. Tato optimalizace spoléhá na to, jestli má zdrojový server podporovat požadavky na rozsah bajtů. Pokud zdrojový server nepodporuje požadavky na rozsah bajtů, tato optimalizace se neprojeví.

### <a name="caching"></a>Ukládání do mezipaměti
Optimalizace velkých souborů používá jiné výchozí doby vypršení platnosti pro ukládání do mezipaměti z obecného webového doručování. Rozlišuje kladné ukládání do mezipaměti a negativně ukládání do mezipaměti na základě kódů odpovědí HTTP. Pokud na zdrojovém serveru určíte čas vypršení platnosti prostřednictvím hlavičky Cache-Control nebo Expires v odpovědi, CDN tuto hodnotu respektuje. Pokud počátek neurčí a soubor se shoduje s typem a velikostí pro tento typ optimalizace, CDN používá výchozí hodnoty pro optimalizaci velkých souborů. V opačném případě CDN používá pro obecné doručování webu výchozí nastavení.

| Ukládání do mezipaměti  | Obecné webové | Optimalizace velkých souborů 
--- | --- | --- 
Ukládání do mezipaměti: kladné <br> HTTP 200, 203, 300, <br> 301, 302 a 410 | 7 dní |1 den  
Ukládání do mezipaměti: záporné <br> HTTP 204, 305, 404, <br> a 405 | Žádné | 1 sekunda 

### <a name="deal-with-origin-failure"></a>Zabývat se selháním původu

Délka počátečního časového limitu pro čtení se zvyšuje od dvou sekund pro obecné doručování webu do dvou minut pro typ optimalizace velkých souborů. To zvyšuje účty větší velikosti souborů, aby se zabránilo předčasnému připojení s časovým limitem.

Když se připojení vyprší, služba CDN se pokusy opakuje několikrát, než pošle klientovi chybu "504-Gateway timeout". 

### <a name="conditions-for-large-file-optimization"></a>Podmínky pro optimalizaci velkých souborů

Následující tabulka uvádí sadu kritérií, která se mají splnit pro optimalizaci velkých souborů:

Stav | Hodnoty 
--- | --- 
Podporované typy souborů | 3g2, 3GP, ASF, AVI, bz2, dmg, exe, F4V, FLV, <br> GZ, HDP, ISO, jxr, M4V, MKV, MOV, MP4, <br> MPEG, MPG, MTS, pkg, QT, RM, SWF, tar, <br> TGZ, WDP, webm, WEBP, WMA, WMV, zip  
Minimální velikost souboru | 10 MB 
Maximální velikost souboru | 150 GB 
Charakteristiky zdrojového serveru | Musí podporovat požadavky na rozsah bajtů. 

## <a name="additional-considerations"></a>Další aspekty

Vezměte v úvahu následující další aspekty tohoto typu optimalizace:

- Proces vytváření bloků dat generuje další požadavky na zdrojový server. Celkový objem dat dodaných od počátku je ale mnohem menší. Výsledkem bloků je lepší charakteristiky ukládání do mezipaměti v CDN.

- Zatížení paměti a vstupně-výstupních operací se sníží na začátku, protože se doručí menší části souboru.

- U bloků uložených v mezipaměti v CDN neexistují žádné další požadavky na původ, dokud nevyprší platnost obsahu nebo dokud nebude vyřazení z mezipaměti.

- Uživatelé můžou do sítě CDN vytvářet požadavky na rozsah, které se považují za běžné soubory. Optimalizace platí jenom v případě, že se jedná o platný typ souboru a rozsah bajtů je mezi 10 MB a 150 GB. Pokud je požadovaná Průměrná velikost souboru menší než 10 MB, použijte místo toho obecné doručování webu.

