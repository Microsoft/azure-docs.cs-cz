---
title: Základní sestavy z Verizon | Microsoft Docs
description: Naučte se, jak získat přístup k základním sestavám Verizon prostřednictvím portálu pro správu profilů Verizon a jak je zobrazit.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5353fa70be4705b28dab9350fcdf7819b22faf54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985533"
---
# <a name="core-reports-from-verizon"></a>Základní sestavy od Verizonu

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Pomocí základních sestav Verizon prostřednictvím portálu spravovat portál pro profily Verizon můžete zobrazit vzorce používání pro CDN pomocí následujících sestav:

* Šířka pásma
* Přenesená data
* Hits
* Stavy mezipaměti
* Poměr přístupů do mezipaměti
* Přenesená data protokolu IPV4/IPV6

## <a name="accessing-verizon-core-reports"></a>Přístup k sestavám Verizon Core
1. V okně profilu CDN klikněte na tlačítko **Spravovat** .
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte myší na kartu **Analýza** a potom najeďte myší na informační rámeček **základních sestav** . V nabídce klikněte na sestavu.
   
    ![Portál pro správu CDN – nabídka základních sestav](./media/cdn-reports/cdn-core-reports.png)

3. U každé sestavy vyberte rozsah kalendářních dat ze seznamu **rozsah dat** . Můžete buď vybrat předem definovaný rozsah dat, například **dnešní den** nebo **Tento týden**, nebo můžete vybrat **vlastní** a ručně zadat rozsah dat kliknutím na ikony kalendáře. 

4. Po výběru rozsahu kalendářních dat kliknutím na tlačítko **Přejít** vygenerujte sestavu. 

4. Pokud chcete exportovat data ve formátu aplikace Excel, klikněte na ikonu aplikace Excel nad tlačítkem **Přejít** .

## <a name="bandwidth"></a>Šířka pásma
Sestava šířky pásma se skládá z tabulky grafu a dat, která indikuje využití šířky pásma CDN pro protokol HTTP a HTTPS v určitém časovém období v MB/s. Můžete zobrazit využití šířky pásma ve všech seznamech POP nebo pro konkrétní bod POP. Tato sestava vám umožní zobrazit špičky provozu a distribuci bodů POP.

V seznamu **hraniční uzly** vyberte **všechny hraniční uzly** , abyste viděli provoz ze všech uzlů, nebo vyberte konkrétní oblast.

Sestava se aktualizuje každých pět minut.

![Sestava šířky pásma](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Přenesená data
Tato sestava se skládá z tabulky grafu a dat, která indikuje využití provozu v síti CDN pro HTTP a HTTPS v určitém časovém období v GB. Využití provozu můžete zobrazit napříč všemi body POP nebo pro konkrétní POP. Tato sestava vám umožní zobrazit špičky provozu a distribuci mezi body POP.

V seznamu **hraniční uzly** vyberte **všechny hraniční uzly** , abyste viděli provoz ze všech uzlů, nebo vyberte konkrétní oblast.

Sestava se aktualizuje každých pět minut.

![Sestava přenesená data](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Přístupy (stavové kódy)
Tato sestava popisuje distribuci stavových kódů požadavků pro váš obsah. Každý požadavek na obsah vygeneruje stavový kód HTTP. Stavový kód popisuje, jak hraniční body POP zpracovávají požadavek. Například stavový kód 2xx označuje, že žádost byla úspěšně zpracována klientovi, zatímco stavový kód 4xx označuje, že došlo k chybě. Další informace o stavových kódech HTTP najdete v tématu [seznam stavových kódů http](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Sestava přístupů](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stavy mezipaměti
Tato sestava popisuje distribuci přístupů do mezipaměti a neúspěšných přístupů do mezipaměti pro požadavky klientů. Vzhledem k tomu, že nejrychlejší výsledky výkonu z hlediska přístupů do mezipaměti, můžete optimalizovat rychlost doručování dat díky minimalizaci přístupů do mezipaměti a přístupů do mezipaměti s vypršenou platností. 

Pokud chcete snížit počet neúspěšných přístupů do mezipaměti, nakonfigurujte zdrojový server tak, aby minimalizoval používání následujících možností: 
 * `no-cache` hlavičky odpovědi
 * Dotaz – ukládání řetězců do mezipaměti, pokud není potřeba striktně  
 * Kódy odpovědí, které nejsou v mezipaměti

Pokud chcete snížit počet přístupů do mezipaměti s vypršenou platností, nastavte prostředek `max-age` na dlouhou dobu, aby se minimalizoval počet požadavků na zdrojový server.

![Sestava stavů mezipaměti](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Mezi stavy hlavní mezipaměti patří:
* TCP_HIT: obsluha z Edge serveru. Objekt byl v mezipaměti a nepřekročil maximální stáří.
* TCP_MISS: obsluhováno ze zdrojového serveru. Objekt nebyl v mezipaměti a odpověď byla vrácena zpět na počátek.
* TCP_EXPIRED _MISS: obsluhováno ze zdrojového serveru po revalidu s počátkem. Objekt byl v mezipaměti, ale překročil maximální stáří. Výsledkem revalidu s počátkem je, že objekt mezipaměti je nahrazen novou odpovědí od počátku.
* TCP_EXPIRED _HIT: obsluhováno z Edge po revalidu s počátkem. Objekt byl v mezipaměti, ale překročil jeho maximální stáří. Došlo k revalidování se zdrojovým serverem v nezměněné objektu mezipaměti.

### <a name="full-list-of-cache-statuses"></a>Úplný seznam stavů mezipaměti
* TCP_HIT – tento stav je hlášen, když je požadavek obsluhován přímo z místního klienta. Asset se hned dokončí z POP, pokud je uložený v mezipaměti na bodu POP, který je nejbližší klientovi a má platnou hodnotu TTL (Time to Live). Hodnota TTL je určena v následujících hlavičkách odpovědi:
  
  * Cache-Control: s-maxage
  * Řízení mezipaměti: max. stáří
  * Platnost vyprší
* TCP_MISS: Tento stav indikuje, že v bodu POP, který je nejbližší klientovi, nebyla nalezena verze požadovaného prostředku uložená v mezipaměti. Asset se požaduje buď ze zdrojového serveru, nebo ze zdrojového stíněného serveru. Pokud zdrojový server nebo zdrojový chráněný server vrátí Asset, bude obsluhován klientovi a uložen do mezipaměti v klientském i hraničním serveru. V opačném případě se vrátí stavový kód, který není 200 (například 403 zakázán nebo 404 nebyl nalezen).
* TCP_EXPIRED_HIT: Tento stav se oznamuje, když se žádost, která cílí na prostředek s hodnotou TTL, zpracovala přímo z tohoto bodu na klienta. Například po vypršení platnosti maximálního stáří prostředku. 
  
   Požadavek na vypršení platnosti obvykle vede k replatným žádostem na zdrojový server. Aby se stav TCP_EXPIRED_HIT probíhat, musí zdrojový server indikovat, že neexistuje novější verze assetu. Tato situace obvykle vede k aktualizaci hlaviček Cache-Control a vypršení platnosti assetu.
* TCP_EXPIRED_MISS: Tento stav je hlášen, když je do klienta obsluhována novější verze prostředku v mezipaměti s vypršenou platností. K tomuto stavu dochází, pokud vypršela platnost hodnoty TTL pro prostředek v mezipaměti (například max. stáří) a zdrojový server vrátí novější verzi tohoto prostředku. Tato nová verze assetu se obsluhuje z klienta namísto verze v mezipaměti. Navíc je uložen do mezipaměti na hraničním serveru a klientovi.
* CONFIG_NOCACHE: Tento stav indikuje konfiguraci specifickou pro konkrétního zákazníka, po které se bod POP Edge zabránil v ukládání prostředku do mezipaměti.
* ŽÁDNÉ – tento stav označuje, že nebyla provedena kontrolu aktuálnosti obsahu mezipaměti.
* TCP_CLIENT_REFRESH_MISS: Tento stav se oznamuje, když klient HTTP, jako je například prohlížeč, vynutí, aby bod POP načetl novou verzi zastaralého prostředku ze zdrojového serveru. Ve výchozím nastavení servery brání klientovi protokolu HTTP v vynucování hraničních serverů, aby načetla novou verzi assetu ze zdrojového serveru.
* TCP_PARTIAL_HIT: Tento stav se oznamuje, když požadavek na rozsah bajtů vychází z důvodu dosažení částečně uloženého prostředku v mezipaměti. Požadovaný rozsah bajtů je okamžitě obsluhován z bodu POP klientovi.
* Nedá se Uložit do mezipaměti: Tento stav se oznamuje, když se v `Cache-Control` `Expires` záhlaví a hlavičkách assetu označuje, že by neměl být uložený v mezipaměti na POP nebo klientovi HTTP. Tyto typy požadavků jsou obsluhovány ze zdrojového serveru.

## <a name="cache-hit-ratio"></a>Poměr přístupů do mezipaměti
Tato sestava indikuje procento požadavků uložených v mezipaměti, které byly obsluhovány přímo z mezipaměti.

Tato sestava poskytuje následující podrobnosti:

* Požadovaný obsah byl uložen do mezipaměti v bodu POP, který je nejbližší žadateli.
* Žádost byla obsluhována přímo od okraje naší sítě.
* Požadavek nevyžadoval opětovné ověření u zdrojového serveru.

Sestava neobsahuje:

* Žádosti, které jsou zamítnuté z důvodu možností filtrování země/oblasti
* Žádosti o prostředky, jejichž záhlaví označují, že by neměly být ukládány do mezipaměti Například `Cache-Control: private` `Cache-Control: no-cache` `Pragma: no-cache` záhlaví zabraňují ukládání prostředku do mezipaměti.
* Počet požadavků na rozsah bajtů pro částečně uložený obsah v mezipaměti.

Vzorec je: (TCP_ úspěšný/(TCP_ PŘÍSTUPŮ + TCP_MISS)) * 100

![Sestava poměru přístupů do mezipaměti](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Přenesená data protokolu IPV4/IPV6
Tato sestava zobrazuje distribuci využití provozu v protokolu IPV4 vs IPV6.

![Přenesená data protokolu IPV4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Důležité informace
Sestavy se dají vygenerovat jenom během posledních 18 měsíců.

