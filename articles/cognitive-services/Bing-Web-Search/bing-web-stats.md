---
title: Přidání analýzy do rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Statistika Bingu poskytuje analýzy rozhraní API Bingu pro vyhledávání obrázků. Analýzy zahrnují objem volání, nejčastější řetězce dotazů, geografické distribuce a další.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 02fea02cca5950ef8467377a866e9a765af9e2e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349550"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Přidání analýzy do rozhraní API pro vyhledávání Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Statistika Bingu poskytuje analýzy pro rozhraní API pro vyhledávání Bingu. Tyto analýzy zahrnují objem volání, nejčastější řetězce dotazů, geografické distribuce a další. Statistiku Bingu můžete v [Azure Portal](https://ms.portal.azure.com) povolit tak, že přejdete do svého prostředku Azure a kliknete na **Povolit statistiku Bingu**.

> [!IMPORTANT]
> * Statistika Bingu není k dispozici u prostředků na `F0` cenové úrovni Free.
> * Nemůžete použít žádná data, která jsou k dispozici prostřednictvím řídicího panelu statistiky Bingu k vytváření aplikací pro distribuci třetím stranám.
> * Povolením statistik Bingu se zvýší míra předplatného. Podrobnosti najdete v tématu [ceny](https://aka.ms/bingstatisticspricing) .


Na následujícím obrázku jsou znázorněny dostupné analýzy pro každý koncový bod rozhraní API Vyhledávání Bingu.

![Distribuce podle matice podpory koncových bodů](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Přístup k analýzám

Bing data Analytics aktualizuje data každých 24 hodin a udržuje až 13 měsíců s historií, ke které máte přístup z [řídicího panelu Analytics](https://bingapistatistics.com). Ujistěte se, že jste přihlášeni pomocí stejné účet Microsoft (MSA), kterou jste použili k registraci statistik Bingu.

> [!NOTE]  
> * Může trvat až 24 hodin, než se metriky na řídicím panelu dokliká na Surface. Řídicí panel zobrazuje datum a čas poslední aktualizace dat.  
> * Metriky jsou k dispozici v době, kdy jste povolili doplněk Statistika Bingu.

## <a name="filter-the-data"></a>Filtrování dat

Ve výchozím nastavení se v grafech a grafech zobrazují všechny metriky a data, ke kterým máte přístup. Data zobrazená v grafech a grafech můžete filtrovat tak, že vyberete prostředky, trhy, koncové body a období hlášení, které vás zajímá. Můžete změnit následující filtry:

- **ID prostředku**: jedinečné ID prostředku, které identifikuje vaše předplatné Azure. Seznam obsahuje více ID, pokud se přihlásíte k odběru více než jedné vrstvy rozhraní API Vyhledávání Bingu. Ve výchozím nastavení jsou vybrány všechny prostředky.  
  
- **Trhy**: trhy, ze kterých pocházejí výsledky. Například en-US (angličtina, USA). Ve výchozím nastavení jsou vybrané všechny trhy. Na `en-WW` trhu je trh, který Bing používá v případě, že volání neuvádí trh a Bing nedokáže určit trh uživatele.  
  
- **Koncové body**: koncové body rozhraní vyhledávání Bingu API. Seznam obsahuje všechny koncové body, pro které máte placené předplatné. Ve výchozím nastavení jsou vybrány všechny koncové body.  

- **Časový rámec**: období generování sestav. Můžete zadat:
  - **Vše**: zahrnuje data o hodnotě až 13 měsíců.  
  - **Posledních 24 hodin**: zahrnuje analýzu za posledních 24 hodin.  
  - **Minulý týden**: zahrnuje analýzu z předchozích 7 dnů.  
  - **Minulý měsíc**: zahrnuje analýzu z předchozích 30 dnů.  
  - **Vlastní rozsah kalendářních dat**: zahrnuje analýzu ze zadaného rozsahu kalendářních dat, pokud je k dispozici.  

## <a name="charts-and-graphs"></a>Grafy a grafy

Řídicí panel zobrazuje grafy a grafy metrik, které jsou k dispozici pro vybraný koncový bod. Ne všechny metriky jsou k dispozici pro všechny koncové body. Grafy a grafy pro každý koncový bod jsou statické (nemusíte vybírat grafy a grafy, které se mají zobrazit). Řídicí panel zobrazuje pouze grafy a grafy, pro které jsou k dispozici data.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Níže jsou uvedené možné metriky a omezení koncových bodů.

- **Svazek volání**: zobrazuje počet volání provedených během období generování sestav. Pokud je období generování sestav za den, graf znázorňuje počet volání provedených za hodinu. V opačném případě graf znázorňuje počet volání prováděných za den v období generování sestav.  
  
  > [!NOTE]
  > Svazek volání se může lišit od fakturačních sestav, které obvykle zahrnují pouze úspěšná volání.

- **Nejčastější dotazy**: zobrazuje horní dotazy a počet výskytů každého dotazu během období generování sestav. Můžete nakonfigurovat počet zobrazených dotazů. Můžete například zobrazit prvních 25, 50 nebo 75 dotazů. Horní dotazy nejsou k dispozici pro následující koncové body:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Některé výrazy dotazu je možné potlačit a odebrat tak důvěrné informace, jako jsou e-maily, telefonní čísla, rodné číslo atd.

- **Geografická distribuce**: trhy, kde pocházejí výsledky hledání. Například `en-us` (Čeština, USA). Bing používá `mkt` parametr dotazu k určení trhu, pokud je zadaný. V opačném případě Bing používá ke zjištění trhu signály, jako je IP adresa volajícího.

- **Distribuce kódu odezvy**: stavové kódy http všech volání během období generování sestav.

- **Distribuce zdroje volání**: typy prohlížečů používaných uživateli. Například Microsoft Edge, Chrome, Safari a FireFox. V rámci knihoven se seskupují volání z mimo prohlížeč (například roboty, post nebo using kudrlinkou z konzolové aplikace). Počátek se určuje pomocí hodnoty hlavičky User-Agent požadavku. Pokud žádost neobsahuje hlavičku User-Agent, Bing se pokusí odvodit původ z jiných signálů.  

- **Distribuce bezpečného vyhledávání**: distribuce bezpečných vyhledávacích hodnot. Například off, mírná nebo Strict. `safeSearch`Parametr dotazu obsahuje hodnotu, je-li zadán. V opačném případě Bing nastaví hodnotu na střední.  

- **Odpovědi požadovaly distribuci**: vyhledávání na webu odpovědi rozhraní API, které jste požadovali v `responseFilter` parametru dotazu.  

- Odpověď **vrátila distribuci**: odpovědi, které vyhledávání na webu rozhraní API vrátilo v odpovědi.

- **Distribuce serveru odpovědí**: aplikační server, který zpracovává požadavky rozhraní API. Možné hodnoty jsou Bing.com (pro přenosy poskytované z desktopových a přenosných zařízení) a Bing.com-Mobile (pro přenos dat poskytovaných z mobilních zařízení). Server je určen pomocí hodnoty hlavičky User-Agent požadavku. Pokud žádost neobsahuje hlavičku User-Agent, Bing se pokusí odvodit Server z jiných signálů.

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro vyhledávání Bingu?](bing-api-comparison.md)
* [Požadavky rozhraní API pro vyhledávání Bingu na zobrazení a použití](use-display-requirements.md)