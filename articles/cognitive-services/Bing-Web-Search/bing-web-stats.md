---
title: Přidání analýzy do rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Statistika Bingu poskytuje analýzy rozhraní API pro vyhledávání obrázků Bingu. Analytics zahrnují objem volání, řetězce špičkových dotazů, geografické rozložení a další.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882785"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Přidání analýz y do vyhledávacích api Služby Bing

Statistika Bingu poskytuje analýzy pro vyhledávací api Bingu. Tyto analýzy zahrnují objem volání, řetězce horních dotazů, geografické rozdělení a další. Statistiky Bingu můžete povolit na [webu Azure Portal](https://ms.portal.azure.com) tak, že přejdete na svůj prostředek Azure a kliknete na **Povolit statistiky Bingu**.

> [!IMPORTANT]
> * Statistika Bingu není k dispozici s bezplatnými `F0` zkušebními předplatnými nebo prostředky na bezplatné cenové úrovni.
> * K vytváření aplikací pro distribuci třetím stranám nesmíte používat žádná data dostupná prostřednictvím řídicího panelu Statistika Bingu.
> * Povolení statistiky Bing mírně zvyšuje míru předplatného. Podrobnosti najdete v [tématu ceny.](https://aka.ms/bingstatisticspricing)


Následující obrázek znázorňuje dostupné analýzy pro každý koncový bod rozhraní API pro vyhledávání Bing.

![Matice podpory distribuce podle koncového bodu](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Přístup k vašim analýzám

Bing aktualizuje analytická data každých 24 hodin a udržuje historii až 13 měsíců, ke které máte přístup z [řídicího panelu analýzy](https://bingapistatistics.com). Ujistěte se, že jste přihlášení pomocí stejného účtu Microsoft (MSA), který jste použili k registraci ke statistice Bingu.

> [!NOTE]  
> * Může trvat až 24 hodin, než se metriky objeví na řídicím panelu. Řídicí panel zobrazuje datum a čas poslední aktualizace dat.  
> * Metriky jsou k dispozici od okamžiku, kdy povolíte doplněk Statistika Bingu.

## <a name="filter-the-data"></a>Filtrování dat

Ve výchozím nastavení grafy a grafy zobrazují všechny metriky a data, ke kterým máte přístup. Data zobrazená v grafech a grafech můžete filtrovat výběrem zdrojů, trhů, koncových bodů a období vykazování, které vás zajímají. Můžete změnit následující filtry:

- **ID prostředku:** Jedinečné ID prostředku, které identifikuje vaše předplatné Azure. Seznam obsahuje více ID, pokud se přihlásíte k odběru více než jedné úrovně rozhraní API pro vyhledávání Bingu. Ve výchozím nastavení jsou vybrány všechny prostředky.  
  
- **Trhy**: Trhy, z nichž pocházejí výsledky. Například en-us (angličtina, Spojené státy). Ve výchozím nastavení jsou vybrány všechny trhy. Trh `en-WW` je trh, který Bing používá, pokud volání neurčuje trh a Bing není schopen určit trh uživatele.  
  
- **Koncové body**: Koncové body rozhraní API pro vyhledávání Bingu. Seznam obsahuje všechny koncové body, pro které máte placené předplatné. Ve výchozím nastavení jsou vybrány všechny koncové body.  

- **Časový rámec**: Vykazované období. Můžete zadat:
  - **Vše**: Zahrnuje údaje v hodnotě až 13 měsíců  
  - **Uplynulých 24 hodin**: Zahrnuje analýzy za posledních 24 hodin  
  - **Minulý týden**: Zahrnuje analýzy z předchozích sedmi dnů  
  - **Minulý měsíc**: Zahrnuje analýzy z předchozích 30 dnů  
  - **Vlastní rozsah dat**: Zahrnuje analýzy ze zadaného časového období, pokud je k dispozici  

## <a name="charts-and-graphs"></a>Grafy a grafy

Řídicí panel zobrazuje grafy a grafy metrik dostupných pro vybraný koncový bod. Ne všechny metriky jsou k dispozici pro všechny koncové body. Grafy a grafy pro každý koncový bod jsou statické (nemusí být možné vybrat grafy a grafy, které chcete zobrazit). Řídicí panel zobrazuje pouze grafy a grafy, pro které jsou k dispozici data.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Následují možné metriky a omezení koncového bodu.

- **Hlasitost volání**: Zobrazuje počet hovorů uskutečněných během vykazovaného období. Pokud je vykazované období pro jeden den, graf zobrazuje počet uskutečněných hovorů za hodinu. V opačném případě graf zobrazuje počet volání uskutečněných za den vykazovaného období.  
  
  > [!NOTE]
  > Objem volání se může lišit od přehledů fakturace, které obvykle zahrnují pouze úspěšná volání.

- **Hlavní dotazy**: Zobrazuje hlavní dotazy a počet výskytů každého dotazu během období vykazování. Můžete nakonfigurovat počet zobrazených dotazů. Můžete například zobrazit 25, 50 nebo 75 nejvyšších dotazů. Hlavní dotazy nejsou k dispozici pro následující koncové body:  

  - /images/trendy
  - /obrázky/podrobnosti
  - /obrázky/vizuální vyhledávání
  - /videa/trendy
  - /videa/podrobnosti
  - /novinky
  - /novinky/trendytémata
  - /návrhy  
  
  > [!NOTE]  
  > Některé termíny dotazu mohou být potlačeny, aby se odstranily důvěrné informace, jako jsou e-maily, telefonní čísla, SSN atd.

- **Geografické rozložení**: Trhy, na kterých pocházejí výsledky vyhledávání. Například `en-us` (angličtina, Spojené státy). Bing používá `mkt` parametr dotazu k určení trhu, pokud je zadán. V opačném případě Bing používá signály, jako je ip adresa volajícího k určení trhu.

- **Distribuce kódu odpovědi**: Stavové kódy HTTP všech volání během vykazovaného období.

- **Call Origin Distribution**: Typy prohlížečů používaných uživateli. Například Microsoft Edge, Chrome, Safari a FireFox. Hovory provedené mimo prohlížeč (například roboty, pošťák nebo pomocí zvlnění z konzolové aplikace) se seskupují v části Knihovny. Původ je určen pomocí požadavku User-Agent hlavičky hodnotu. Pokud požadavek neobsahuje hlavičku User-Agent, Bing se pokusí odvodit původ z jiných signálů.  

- **Bezpečné vyhledávání Distribuce**: Rozdělení hodnot bezpečného vyhledávání. Například vypnuto, středně nebo striktně. Parametr `safeSearch` dotazu obsahuje hodnotu, pokud je zadána. V opačném případě Bing výchozí hodnotu moderovat.  

- **Požadované odpovědi na distribuci**: Rozhraní API `responseFilter` pro vyhledávání na webu odpovídá na odpovědi, které jste požadovali v parametru dotazu.  

- **Vrácené odpovědi distribuce**: Odpovědi, které rozhraní API pro vyhledávání na webu vrátil v odpovědi.

- **Distribuce serveru odpovědí**: Aplikační server, který obsluhoval vaše požadavky rozhraní API. Možné hodnoty jsou Bing.com (pro provoz obsluhovaný ze stolních a přenosných zařízení) a Bing.com-mobile (pro provoz obsluhovaný z mobilních zařízení). Server je určen pomocí hodnoty hlavičky user-agent a požadavku. Pokud požadavek neobsahuje hlavičku User-Agent, Bing se pokusí odvodit server z jiných signálů.

## <a name="next-steps"></a>Další kroky

* [Co jsou vyhledávací api Bingu?](bing-api-comparison.md)
* [Požadavky rozhraní API pro vyhledávání Bingu na zobrazení a použití](use-display-requirements.md)
