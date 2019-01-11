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
ms.openlocfilehash: 6016b13fe7d3e1f3b673bd2446d2f68b04878cd6
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193350"
---
Statistika Bingu poskytuje analýzy pro rozhraní API Bingu pro vyhledávání. Analytics zahrnuje objemy volání, řetězce dotazu top, distribuce a další. Pokud chcete povolit Statistika Bingu ve vyhledávání Bingu placené předplatné, přejděte do vaší [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), vyberte své placené předplatné a klikněte na tlačítko Povolit Statistika Bingu. Povolení Statistika Bingu se mírně zvýší vaše míra přihlášení odběru (viz [ceny](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Statistika Bingu je dostupné pouze na placená předplatná – není k dispozici u bezplatných zkušebních verzí předplatného.

> [!NOTE]
> Žádná data k dispozici prostřednictvím řídicího panelu Statistika Bingu nesmíte používat k vytváření aplikací pro distribuci třetím stranám.

Bing aktualizuje analytics data každých 24 hodin a udržuje až do 13 měsíců za historie.

## <a name="accessing-your-analytics"></a>Přístup k vaší analýzy

Pokud chcete získat přístup k řídicím panelu analýz, přejděte na https://bingapistatistics.com. Ujistěte se, že jste přihlášeni pomocí stejného účtu Microsoft (MSA) můžete použít k získání placené předplatné.

## <a name="filtering-the-data"></a>Filtrování dat

Ve výchozím nastavení tabulky a grafy zahrnují všechna data metrik, které mají přístup k. Můžete filtrovat data zobrazená v tabulky a grafy tak, že vyberete prostředky, trhy, koncové body a vytváření sestav období máte zájem. Tabulky a grafy změnit tak, aby odrážely filtry, které použijete. Následující části popisují filtry, které mohou změnit.

- **ID prostředku**: ID jedinečný prostředek, který identifikuje vašeho předplatného Azure. Seznam obsahuje víc ID, pokud se zaregistrujete k více než jednu vrstvu rozhraní API Bingu pro vyhledávání. Ve výchozím nastavení jsou vybrány všechny prostředky.  
  
- **Trhy**: Trhy, odkud pochází výsledky. Například en-us (angličtina, Spojené státy). Ve výchozím nastavení jsou vybrány všech trzích, kde. Všimněte si, na trhu en TT je na trhu, Bing používá, pokud volání neurčuje vstup na trh a Bing není schopen určit trhu uživatele.  
  
- **Koncové body**: Koncové body rozhraní API Bingu pro vyhledávání. Seznam obsahuje všechny koncové body, pro které máte placené předplatné. Ve výchozím nastavení jsou vybrány všechny koncové body.  

- **Časový rámec**: Období vytváření sestav. Můžete zadat:
  - Všechny&mdash;obsahuje až do data za 13 měsíců  
  - Za posledních 24 hodin&mdash;zahrnuje analýzu z posledních 24 hodin  
  - Poslední týden&mdash;zahrnuje analýzu z posledních sedmi dnů  
  - Minulý měsíc&mdash;zahrnuje analýzu z posledních 30 dnů  
  - Vlastní rozsah&mdash;zahrnuje analýzu z na určité časové období, pokud je k dispozici  

  > [!NOTE]  
  > Může trvat až 24 hodin pro metriky k poskytování na řídicím panelu. Řídicím panelu zobrazuje datum a čas poslední aktualizace dat.  

  > [!NOTE]  
  > Metriky jsou k dispozici od doby, kdy povolit doplněk Statistika Bingu.

## <a name="charts-and-graphs"></a>Tabulky a grafy

Na řídicím panelu zobrazuje tabulky a grafy metrik dostupné pro vybraný koncový bod. Některé metriky jsou k dispozici pro všechny koncové body. Tabulky a grafy pro každý koncový bod jsou statické (nelze vybrat tabulky a grafy pro zobrazení). Na řídicím panelu zobrazuje pouze tabulky a grafy, u kterých jsou data.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Tady jsou možné metriky. Každý koncový bod omezení metriky poznámky.

- **Volání svazku**: Zobrazí počet volání provedených během období vytváření sestav. Pokud je den období vytváření sestav, graf zobrazuje počet volání za hodinu. V opačném případě graf zobrazuje počet volání za den období vytváření sestav.  
  
  > [!NOTE]
  > Objem volání může lišit od účetních sestav, který obvykle obsahuje pouze úspěšných volání.

- **Nejčastější dotazy**: Zobrazí nejčastější dotazy a počet výskytů jednotlivých dotazů během období vytváření sestav. Můžete nakonfigurovat počet dotazů, které jsou zobrazeny. Například můžete zobrazit dotazy horní 25 a 50, 75. Nejčastější dotazy není k dispozici pro následující koncové body:  

  - /Images/trending
  - / imagí a podrobnosti
  - / Image/visualsearch
  - /videos/trending
  - / videa a podrobnosti
  - /News
  - / news/trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Některé výrazy mohou potlačit odebrat důvěrné informace, jako je e-mailů, telefonní čísla, číslo sociálního pojištění, atd.

- **Geografické rozptýlení**: Trhy, odkud pochází výsledky. Například en-us (angličtina, Spojené státy). Bing používá `mkt` parametr k určení na trhu dotazu, pokud zadaný. V opačném případě Bing používá signálů, jako je například IP adresy volajícího k určení na trhu.

- **Rozdělení kódů odpovědí**: Stavové kódy HTTP všech volání období sestavy.

- **Distribuce původu volání**: Typy prohlížečích používaných uživateli. Například Microsoft Edge, Chrome, Safari a FireFox. Volání z mimo prohlížeč, jako jsou roboti, Postman nebo pomocí příkazu curl z konzoly aplikace, jsou seskupené v rámci knihovny. Počátek je určen pomocí hodnotu hlavičky uživatelského agenta žádosti. Žádost neobsahuje záhlaví User-Agent, Bingu se pokusí odvodit z jiných signálů původ.  

- **Bezpečné vyhledávání distribuce**: Distribuce hodnot bezpečného hledání. Například vypnout, střední nebo strict. `safeSearch` Parametru obsahuje hodnotu, pokud zadaný dotaz. V opačném případě Bingu výchozí hodnota je hodnota, která má střední.  

- **Odpovědi požadované distribuční**: Webové rozhraní API hledání požadovaného v odpovědi `responseFilter` parametr dotazu.  

- **Odpovědi vrátil distribuce**: Odpovědi, které webové rozhraní API pro vyhledávání vrátila v odpovědi.

- **Rozdělení odpovědí serveru**: Aplikační server, který obsluhuje žádostí o rozhraní API. Možné hodnoty jsou Bing.com (pro přenos poskytovaný stolní a přenosné zařízení) a Bing.com mobile (pro přenos poskytovaný z mobilních zařízení). Na serveru se určuje pomocí hodnotu hlavičky uživatelského agenta žádosti. Žádost neobsahuje záhlaví User-Agent, Bingu se pokusí odvodit serveru z jiných signálů.

Následuje ukázka analýzy, které jsou k dispozici pro každý koncový bod.

![Distribuce podle matice podpory koncového bodu](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
