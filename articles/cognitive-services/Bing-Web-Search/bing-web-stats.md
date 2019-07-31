---
title: Přidání analýzy do rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Statistika Bingu poskytuje analýzy rozhraní API Bingu pro vyhledávání obrázků. Analýzy zahrnují objem volání, nejčastější řetězce dotazů, geografické distribuce a další.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 40a5e03f4149381f096f71243361eacbdc7c16c2
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667609"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Přidání analýzy do rozhraní API pro vyhledávání Bingu

Statistika Bingu poskytuje analýzy pro rozhraní API pro vyhledávání Bingu. Tyto analýzy zahrnují objem volání, nejčastější řetězce dotazů, geografické distribuce a další. Statistiku Bingu můžete v [Azure Portal](https://ms.portal.azure.com) povolit tak, že přejdete do svého prostředku Azure a kliknete na **Povolit statistiku Bingu**.

> [!IMPORTANT]
> * Statistika Bingu není k dispozici v rámci bezplatné zkušební verze předplatného `F0` nebo prostředků na cenové úrovni Free.
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

- **ID prostředku**: Jedinečné ID prostředku, které identifikuje vaše předplatné Azure. Seznam obsahuje více ID, pokud se přihlásíte k odběru více než jedné vrstvy rozhraní API Vyhledávání Bingu. Ve výchozím nastavení jsou vybrány všechny prostředky.  
  
- **Trhy**: Trhy, ze kterých pocházejí výsledky. Například en-US (angličtina, USA). Ve výchozím nastavení jsou vybrané všechny trhy. Na `en-WW` trhu je trh, který Bing používá v případě, že volání neuvádí trh a Bing nedokáže určit trh uživatele.  
  
- **Koncové body**: Koncové body rozhraní API Vyhledávání Bingu Seznam obsahuje všechny koncové body, pro které máte placené předplatné. Ve výchozím nastavení jsou vybrány všechny koncové body.  

- **Časový rámec**: Období generování sestav. Můžete zadat:
  - **Vše**: Zahrnuje data o hodnotě až 13 měsíců.  
  - **Posledních 24 hodin**: Zahrnuje analýzy za posledních 24 hodin.  
  - **Minulý týden**: Zahrnuje analýzy z předchozích sedmi dnů.  
  - **Minulý měsíc**: Zahrnuje analýzu z předchozích 30 dnů.  
  - **Vlastní rozsah kalendářních dat**: Zahrnuje analýzu ze zadaného rozsahu kalendářních dat, pokud je k dispozici.  

## <a name="charts-and-graphs"></a>Grafy a grafy

Řídicí panel zobrazuje grafy a grafy metrik, které jsou k dispozici pro vybraný koncový bod. Ne všechny metriky jsou k dispozici pro všechny koncové body. Grafy a grafy pro každý koncový bod jsou statické (nemusíte vybírat grafy a grafy, které se mají zobrazit). Řídicí panel zobrazuje pouze grafy a grafy, pro které jsou k dispozici data.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Níže jsou uvedené možné metriky a omezení koncových bodů.

- **Svazek volání**: Zobrazuje počet volání provedených během období generování sestav. Pokud je období generování sestav za den, graf znázorňuje počet volání provedených za hodinu. V opačném případě graf znázorňuje počet volání prováděných za den v období generování sestav.  
  
  > [!NOTE]
  > Svazek volání se může lišit od fakturačních sestav, které obvykle zahrnují pouze úspěšná volání.

- **Nejčastější dotazy**: Zobrazuje horní dotazy a počet výskytů každého dotazu během období generování sestav. Můžete nakonfigurovat počet zobrazených dotazů. Můžete například zobrazit prvních 25, 50 nebo 75 dotazů. Horní dotazy nejsou k dispozici pro následující koncové body:  

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

- **Geografická distribuce**: Trhy, kde pocházejí výsledky hledání Například `en-us` (Čeština, USA). Bing používá `mkt` parametr dotazu k určení trhu, pokud je zadaný. V opačném případě Bing používá ke zjištění trhu signály, jako je IP adresa volajícího.

- **Distribuce kódu odezvy**: Stavové kódy HTTP všech volání během období generování sestav.

- **Distribuce zdroje volání**: Typy prohlížečů používaných uživateli. Například Microsoft Edge, Chrome, Safari a FireFox. V rámci knihoven se seskupují volání z mimo prohlížeč (například roboty, post nebo using kudrlinkou z konzolové aplikace). Počátek se určuje pomocí hodnoty hlavičky User-Agent. Pokud žádost neobsahuje hlavičku User-Agent, Bing se pokusí odvodit původ z jiných signálů.  

- **Distribuce bezpečného vyhledávání**: Distribuce bezpečných vyhledávacích hodnot. Například off, mírná nebo Strict. Parametr `safeSearch` dotazu obsahuje hodnotu, je-li zadán. V opačném případě Bing nastaví hodnotu na střední.  

- **Odpovědi požadovaly distribuci**: Vyhledávání na webu odpovědi rozhraní API, které jste požadovali `responseFilter` v parametru dotazu.  

- **Odpověď vrátila distribuci**: Odpovědi, které Vyhledávání na webu rozhraní API vráceného v odpovědi

- **Distribuce serveru odpovědí**: Aplikační server, který zpracovává vaše požadavky na rozhraní API. Možné hodnoty jsou Bing.com (pro přenosy poskytované z desktopových a přenosných zařízení) a Bing.com-Mobile (pro přenos dat poskytovaných z mobilních zařízení). Server je určen pomocí hodnoty hlavičky User-Agent daného požadavku. Pokud žádost neobsahuje hlavičku User-Agent, Bing se pokusí odvodit Server z jiných signálů.

## <a name="next-steps"></a>Další postup

* [Co je rozhraní API pro vyhledávání Bingu?](bing-api-comparison.md)
* [Vyhledávání Bingu požadavky na použití rozhraní API a zobrazení](use-display-requirements.md)
