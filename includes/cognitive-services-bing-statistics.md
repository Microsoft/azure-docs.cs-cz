---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 4e19c1afefdc5bcacebcb0d495193b48c7a6d724
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313706"
---
Statistiky Bing poskytuje analytics pro rozhraní API pro Bing vyhledávání. Analytics obsahuje volání na technickou podporu, řetězce dotazu top, zeměpisném rozložení a další. Chcete-li povolit Bing statistické údaje ve vyhledávání Bing placené předplatné, přejděte na vaše [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)vyberte placené předplatné a klikněte na tlačítko Povolit statistiky Bing. Povolení Bing statistiky mírně zvyšuje rychlost vaše předplatné (viz [ceny](https://aka.ms/bingstatisticspricing)).


> [!NOTE]
> Statistiky Bing je k dispozici pouze placená předplatná – není k dispozici bezplatné zkušební verze předplatného. 

> [!NOTE]
> Žádná data k dispozici prostřednictvím řídicího panelu Bing statistiky nesmíte používat k vytvoření aplikace pro distribuci třetím stranám.

Bing aktualizuje analytická data každých 24 hodin a udržuje až do 13 měsíc za historie.

## <a name="accessing-your-analytics"></a>Přístup k vaší analytics

Chcete-li získat přístup k řídicím panelu analýzy, přejděte na https://bingapistatistics.com. Ujistěte se, že jste přihlášení pomocí stejného účtu Microsoft (MSA) můžete použít k získání placené předplatné.


## <a name="filtering-the-data"></a>Filtrování dat

Ve výchozím nastavení tabulky a grafy projeví všechna data metriky, které máte přístup. Můžete filtrovat data zobrazená na tabulky a grafy výběrem prostředky, trhy, koncových bodů a vytváření sestav období vás zajímá. Tabulky a grafy změnit tak, aby odrážela filtry, které použijete. Následující části popisují filtry, které můžete změnit.

- **ID prostředku**: Identifikátor jedinečný prostředek, který identifikuje vašeho předplatného Azure. Seznam obsahuje více ID, pokud se přihlásíte k více než jednu úroveň rozhraní API služby Bing Search. Ve výchozím nastavení jsou vybrané všechny prostředky.  
  
- **Trhů**: trhy, odkud pocházejí výsledky. Například en-us (angličtina, USA). Ve výchozím nastavení jsou vybrány všechny trhů. Všimněte si, en TT trhu je na trhu, Bing používá, pokud volání neurčuje trhu a Bing se nepodařilo určit trhu uživatele.  
  
- **Koncové body**: koncové body rozhraní API pro vyhledávání Bing. Seznam obsahuje všechny koncové body, pro které máte na placené předplatné. Ve výchozím nastavení jsou vybrány všechny koncové body.  

- **Časový rámec**: období vytváření sestav. Můžete zadat:  
  
  - Všechny&mdash;zahrnuje až 13 měsíce v hodnotě dat  
  - Za posledních 24 hodin&mdash;zahrnuje analytics z posledních 24 hodin  
  - Poslední týden&mdash;zahrnuje analytics z předchozích 7 dní  
  - Poslední měsíc&mdash;zahrnuje analytics z předchozích 30 dnů  
  - Vlastní období&mdash;zahrnuje analytics z v zadaném období, pokud je k dispozici  
  
  > [!NOTE]  
  > To může trvat až 24 hodin pro metriky prezentovat na řídicím panelu. Řídicí panel se zobrazuje datum a čas poslední aktualizace dat.  
  
  > [!NOTE]  
  > Metriky jsou k dispozici od času povolíte doplněk Bing statistiky. 


## <a name="charts-and-graphs"></a>Grafy

Řídicí panel zobrazuje, tabulky a grafy metriky, které jsou k dispozici pro vybraný koncový bod. Ne všechny metriky jsou k dispozici pro všechny koncové body. Tabulky a grafy pro každý koncový body jsou statické (nemusí vybrat tabulky a grafy pro zobrazení). Řídicí panel zobrazuje pouze tabulky a grafy, pro které se nacházejí data. 

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Níže jsou uvedeny možné metriky. Každý koncový bod omezení metriky poznámky. 

- **Volání svazku**: zobrazuje počet volání období sestavy. Pokud období vytváření sestav je za den, graf zobrazuje počet volání za hodinu. V opačném graf zobrazuje počet volání za den období vytváření sestav.  
  
  > [!NOTE]
  > Volání svazku může lišit od fakturace sestavy, což obvykle zahrnuje pouze úspěšných volání. 
  
-  **TOP dotazy**: zobrazuje nejčastějších dotazů a počet výskytů každý dotaz období sestavy. Můžete konfigurovat počet dotazů vidět. Můžete například zobrazit horní 25, 50 nebo 75 dotazy. Nejčastějších dotazů není k dispozici pro vytvoření následujících koncových bodů:  
  
  - /Images/trending
  - / Image/podrobnosti
  - / Image/visualsearch
  - /videos/trending
  - / videa/podrobnosti
  - /News
  - / zprávy/trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Některé podmínky dotazu může potlačit, chcete-li odebrat důvěrné informace, jako je například e-mailů, telefonních čísel, číslo sociálního pojištění, atd.  

- **Geografické rozptýlení**: trhy, odkud pocházejí výsledky. Například en-us (angličtina, USA). Používá Bing `mkt` parametr k určení na trhu dotazu, pokud zadaný. Bing, jinak používá signály například IP adresa volajícího k určení na trhu.  
  
- **Distribuce kód odpovědi**: stavové kódy HTTP všechna volání období sestavy.  
  
- **Distribuce počátek volání**: typy prohlížečů, uživatelé. Například Edge, Chrome, Safari a FireFox. Volání z mimo prohlížeče, jako je například robotů, Postman nebo pomocí curl z konzoly aplikace, jsou seskupené v rámci knihovny. Počátek se určuje pomocí hodnotu hlavičky uživatelského agenta žádosti. Pokud žádost neobsahuje záhlaví User-Agent, pokusí se Bing odvození počátku od jiných signálů.  
  
- **Bezpečné distribuce vyhledávání**: distribuce bezpečné vyhledávání hodnoty. Například vypnutý, střední nebo strict. `safeSearch` Parametr obsahuje hodnotu, pokud zadaný dotaz. Bing jinak, výchozí hodnota, která má střední.  
  
- **Požadované distribuční odpoví**: rozhraní API pro vyhledávání webového odpovídá je požadovaný v `responseFilter` parametr dotazu.  
  
- **Přijme vrátil distribuční**: odpovědi, které vrátí vyhledávání webového rozhraní API v odpovědi.  
  
- **Odpověď serveru distribuční**: aplikační server, který zpracoval vaší žádostí o rozhraní API. Možné hodnoty jsou vyhledávače Bing.com (pro provoz z stolní a přenosné zařízení) a vyhledávače Bing.com-mobile (pro provoz z mobilních zařízení). Server je určen pomocí hodnotu hlavičky uživatelského agenta žádosti. Pokud žádost neobsahuje záhlaví User-Agent, pokusí se Bing odvozena od jiných signálů serveru.  
  


Následující obrázek znázorňuje analýzy, které jsou k dispozici pro každý koncový bod.

![Distribuce podle matici podpory koncový bod](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)


