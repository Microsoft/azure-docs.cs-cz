---
title: Academic Knowledge API pro Microsoft Academic Graph | Microsoft Docs
description: Academic Knowledge API pomůže interpretovat uživatelských dotazů a načítání informací o bohaté z Academic grafu v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: mvorvoreanu
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: mivorvor
ms.openlocfilehash: e241f9a87cd58b62eafd754bd3cb4283aa0a1e92
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342685"
---
# <a name="academic-knowledge-api"></a>Rozhraní Academic Knowledge API

Vítá vás Academic Knowledge API. Pomocí této služby můžete interpretovat dotazy uživatelů z akademického pohledu a načítat bohaté informace ze služby Microsoft Academic Graph (MAG). Znalostní báze MAG je graf heterogenní entity webové škálování skládá z entity, které provádějí vědeckou aktivity modelu: pole studii, autora, instituce, papíru, místo a události. 

MAG data je zaminovaná z indexu webové služby Bing, jakož i interní báze knowledge base ze služby Bing. V důsledku probíhající Bing indexování, bude toto rozhraní API obsahovat čerstvé informace z webu následující vyhledávání a indexování podle Bing. Na základě pro tuto datovou sadu, Academic Knowledge API umožňuje řízené znalostní báze, interaktivní dialog, který bezproblémově kombinuje reaktivní vyhledávání s proaktivní návrhu prostředí, výsledky hledání grafu bohaté research dokumentu a distribuce histogram hodnoty atributů pro sadu dokumenty Paper a související entity.

Další informace o Microsoft Academic Graph najdete v tématu [ http://aka.ms/academicgraph ](http://aka.ms/academicgraph).

Academic Knowledge API přesunula z kognitivní verze Preview služby k testovacímu prostředí kognitivní služby. Nové domovské stránky projektu je: [ https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge ](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Existující klíč rozhraní API bude pokračovat v práci až 24th může 2018. Po tomto datu vygenerujte nový klíč rozhraní API. Upozorňujeme, že placené preview už bude k dispozici po vypršení platnosti existujícího klíče. Pokud úroveň free rozhraní API není dostatečná pro vaše záměry, obraťte se na náš tým. 

## <a name="features"></a>Funkce
Academic Knowledge API se skládá ze čtyř související koncové body REST:  
  1. **interpretace** – interpretuje řetězec dotazu přirozeného jazyka uživatele. Vrátí anotované interpretace, které ve vyhledávacím poli zajistí bohaté možnosti automatického dokončování a předvídají, jaký text uživatel zadává.  
  2. **vyhodnocení** – vyhodnotí výraz dotazu a vrátí výsledky Academic Knowledge entity.  
  3. **calchistogram** – vypočítá histogram rozdělení hodnoty atributu pro academic entity vrácené výrazu dotazu, jako je například distribuce citace podle roku pro danou autora.  
  4. **Graf vyhledávání** – hledá daným grafem vzor a vrátí výsledky odpovídající entity.

Tyto metody rozhraní API se použijí společně, umožňují vytvořit prostředí bohaté sémantického vyhledávání. Zadaný řetězec dotazu uživatele **interpretovat** metoda vám poskytne poznámkou verzi dotaz a výraz strukturovaných dotazů při dokončování volitelně uživatele dotaz založený na sémantika základní academic data. Například, pokud uživatel zadá řetězec *latentní s*, **interpretovat** metoda může poskytnout sadu seřazený interpretace, které naznačují, že uživatel může být hledání pole studie  *latentní sémantického analysis*, papír *latentní struktura analysis*, nebo jiné entity výrazy počínaje *latentní s*. Tyto informace lze rychle Průvodce uživatele požadovaných výsledků hledání.

**Vyhodnotit** metoda slouží k načtení sady odpovídající entity dokumentu ze znalostní báze academic a **calchistogram** metoda slouží k výpočtu distribuci hodnot atributů pro sadu entit dokumentu, které se dají použít pro další filtrování výsledků hledání.        

**Grafu vyhledávání** metoda obsahuje dva režimy: *json* a *lambda*. *Json* režim můžete provádět grafu pro porovnávání podle grafu schémat, zadat objekt JSON. *Lambda* režim můžete provádět výpočty serverové během traversals grafu podle výrazy lambda zadaného uživatelem.

## <a name="getting-started"></a>Začínáme 
Najdete v další části v levém pro podrobnou dokumentaci.  Všimněte si, že ke zlepšení čitelnosti příkladů, volání rozhraní REST API obsahovat znaky (například prostory), které nebyly kódovaná adresou URL.  Váš kód bude nutné použít příslušné adresy URL kódování.
