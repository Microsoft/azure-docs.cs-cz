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
ms.openlocfilehash: a8d588b186652ab86ee1e8152bd9be08f0f1ef04
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978766"
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

- **ID prostředku**: Identifikátor jedinečný prostředek, který identifikuje vašeho předplatného Azure. Seznam obsahuje víc ID, pokud se zaregistrujete k více než jednu vrstvu rozhraní API Bingu pro vyhledávání. Ve výchozím nastavení jsou vybrány všechny prostředky.  
  
- **Trhy**: trhy, odkud pochází výsledky. Například en-us (angličtina, Spojené státy). Ve výchozím nastavení jsou vybrány všech trzích, kde. Všimněte si, na trhu en TT je na trhu, Bing používá, pokud volání neurčuje vstup na trh a Bing není schopen určit trhu uživatele.  
  
- **Koncové body**: koncové body API Bingu pro vyhledávání. Seznam obsahuje všechny koncové body, pro které máte placené předplatné. Ve výchozím nastavení jsou vybrány všechny koncové body.  

- **Časový rámec**: období vytváření sestav. Můžete zadat:
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

- **Volání svazku**: ukazuje počet volání provedených během období vytváření sestav. Pokud je den období vytváření sestav, graf zobrazuje počet volání za hodinu. V opačném případě graf zobrazuje počet volání za den období vytváření sestav.  
  
  > [!NOTE]
  > Objem volání může lišit od účetních sestav, který obvykle obsahuje pouze úspěšných volání.

- **Nejčastější dotazy**: Nejčastější dotazy a počet výskytů jednotlivých dotazů se zobrazí období sestavy. Můžete nakonfigurovat počet dotazů, které jsou zobrazeny. Například můžete zobrazit dotazy horní 25 a 50, 75. Nejčastější dotazy není k dispozici pro následující koncové body:  

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

- **Geografické rozptýlení**: trhy, odkud pochází výsledky. Například en-us (angličtina, Spojené státy). Bing používá `mkt` parametr k určení na trhu dotazu, pokud zadaný. V opačném případě Bing používá signálů, jako je například IP adresy volajícího k určení na trhu.

- **Rozdělení kódů odpovědí**: stavové kódy HTTP všech volání období sestavy.

- **Distribuce původu volání**: typy prohlížečích používaných uživateli. Například Edge, Chrome, Safari a FireFox. Volání z mimo prohlížeč, jako jsou roboti, Postman nebo pomocí příkazu curl z konzoly aplikace, jsou seskupené v rámci knihovny. Počátek je určen pomocí hodnotu hlavičky uživatelského agenta žádosti. Žádost neobsahuje záhlaví User-Agent, Bingu se pokusí odvodit z jiných signálů původ.  

- **Bezpečné vyhledávání distribuce**: distribuce hodnot bezpečného hledání. Například vypnout, střední nebo strict. `safeSearch` Parametru obsahuje hodnotu, pokud zadaný dotaz. V opačném případě Bingu výchozí hodnota je hodnota, která má střední.  

- **Odpovědi na požadované distribuční**: API vyhledávání na webu požadovaného v odpovědi `responseFilter` parametr dotazu.  

- **Odpovědi vrátil distribuce**: odpovědi, které webové rozhraní API pro vyhledávání vrátila v odpovědi.

- **Rozdělení odpovědí serveru**: aplikační server, který obsluhuje žádostí o rozhraní API. Možné hodnoty jsou Bing.com (pro přenos poskytovaný stolní a přenosné zařízení) a Bing.com mobile (pro přenos poskytovaný z mobilních zařízení). Na serveru se určuje pomocí hodnotu hlavičky uživatelského agenta žádosti. Žádost neobsahuje záhlaví User-Agent, Bingu se pokusí odvodit serveru z jiných signálů.

Následuje ukázka analýzy, které jsou k dispozici pro každý koncový bod.

![Distribuce podle matice podpory koncového bodu](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
