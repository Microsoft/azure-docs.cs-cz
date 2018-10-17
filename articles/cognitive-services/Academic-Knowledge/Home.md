---
title: Co je rozhraní Academic Knowledge API?
titlesuffix: Azure Cognitive Services
description: Pomocí rozhraní Academic Knowledge API můžete interpretovat dotazy uživatelů a načítat velké množství dat ze služby Academic Graph.
services: cognitive-services
author: mvorvoreanu
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: overview
ms.date: 03/27/2017
ms.author: mivorvor
ms.openlocfilehash: d08cd7124b232e50365e72753eba97c6309f401c
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901206"
---
# <a name="academic-knowledge-api"></a>Rozhraní Academic Knowledge API

Vítáme vás u rozhraní Academic Knowledge API. Pomocí této služby můžete interpretovat dotazy uživatelů z akademického pohledu a načítat velké množství dat ze služby Microsoft Academic Graph (MAG). Znalostní báze MAG je webový graf heterogenních entit, který se skládá z entit, které modelují odborné aktivity: studijní obor, autor, instituce, dokument, místo a událost. 

Data MAG se dolují z indexu webu Bingu a také z interní znalostní báze z Bingu. V důsledku probíhajícího indexování Bingu bude toto rozhraní API obsahovat čerstvé informace z webu v souladu se zjišťováním a indexováním pomocí Bingu. Na základě této datové sady rozhraní Academic Knowledge API umožňuje znalostmi řízené interaktivní dialogové okno, které bezproblémově kombinuje reaktivní vyhledávání s proaktivními návrhy prostředí, bohatými výsledky vyhledávání v grafech výzkumných dokumentů a distribuci histogramů z hodnot atributů pro sadu dokladů a souvisejících entit.

Další informace o Microsoft Academic Graph naleznete na adrese [ http://aka.ms/academicgraph ](http://aka.ms/academicgraph).

Rozhraní Academic Knowledge API se přesunulo z Cognitive Services ve verzi Preview do Cognitive Services Labs. Nová domovská stránka projektu je: [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Existující klíč rozhraní API bude funkční až do 24. května 2018. Po tomto datu si vygenerujte nový klíč rozhraní API. Upozorňujeme, že placená verze Preview nebude po vypršení platnosti existujícího klíče k dispozici. Pokud rozhraní API na úrovni Free není pro vaše účely dostatečné, obraťte se na náš tým. 

## <a name="features"></a>Funkce
Rozhraní Academic Knowledge API se skládá ze čtyř souvisejících koncových bodů REST:  
  1. **interpretace** – interpretuje řetězec dotazu v přirozeném jazyku uživatele. Vrátí anotované interpretace, které ve vyhledávacím poli zajistí bohaté možnosti automatického dokončování a předvídají, jaký text uživatel zadává.  
  2. **vyhodnocení**  –vyhodnotí výraz dotazu a vrátí výsledky entit Academic Knowledge.  
  3. **výpočet histogramu** – vypočte histogram distribuce hodnot atributů pro akademické entity, které vrátil výraz dotazu, jako je třeba distribuce citací konkrétního autora v jednotlivých letech.  
  4. **hledání grafu** – hledá zadaný vzor grafu a vrátí odpovídající výsledky entit.

Při společném použití tyto metody rozhraní API umožňují vytvářet výkonné sémantické vyhledávání. S ohledem na řetězec dotazu uživatele metoda **interpretace** poskytne anotovanou verzi dotazu a strukturovaný výraz dotazu, zatímco volitelně dokončí dotaz uživatele podle sémantiky základních akademických dat. Pokud uživatel zadá například řetězec *latentní s*, může metoda **interpretace** poskytnout sadu zařazených interpretací a navrhnout, že uživatel hledá studijní obor *latentní sémantická analýza*, dokument *latentní struktura a její analýza* nebo jiné výrazy entity začínající textem *latent s*. Tyto informace můžete použít k rychlému přivedení uživatele k požadovaným výsledkům hledání.

Metodu **vyhodnocení** můžete použít k načtení sady odpovídajících dokumentů entity ze znalostní báze Academic Knowledge a metodu **výpočtu histogramu** můžete použít k výpočtu distribuce hodnot atributů pro sadu dokumentů entity, které můžete použít k dalšímu filtrování výsledků hledání.        

Metoda **hledání grafu** má dva režimy: *json* a *lambda*. Režim *Json* může provádět porovnávání vzoru grafu se vzory grafů určenými objektem JSON. Režim *Lambda* může během procházení grafu provádět výpočty na straně serveru podle uživatelem zadaných výrazů lambda.

## <a name="getting-started"></a>Začínáme 
Podrobnou dokumentaci najdete v podtématech na levé straně.  Všimněte si, že ke zlepšení čitelnosti příkladů obsahuje volání rozhraní REST API znaky (například mezery), které nebyly zakódovány do adresy URL.  Váš kód bude muset použít odpovídající kódování adresy URL.
