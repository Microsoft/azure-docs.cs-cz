---
title: Webové rozhraní API rozhraní v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Webové rozhraní API použijte k vytvoření prostředí bohatou a sémantické vyhledávání v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 16c5680eb4f249a5d37e6b90eea92cfff7090eef
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342419"
---
# <a name="web-api-interface"></a>Webové rozhraní API
Model soubory vytvořené službou zkoumání znalostní báze můžete hostované a přístupných přes sadu webových rozhraní API.  Rozhraní API může být hostovaný na místním počítači pomocí [ `host_service` ](CommandLine.md#host_service-command) příkaz nebo může nasadit do služby Azure cloud pomocí [ `deploy_service` ](CommandLine.md#deploy_service-command) příkaz.  Obě tyto metody vystavit vytvoření následujících koncových bodů rozhraní API:
* [*interpretace* ](interpretMethod.md) – interpretuje řetězec dotazu přirozeného jazyka. Vrátí anotované interpretace, které ve vyhledávacím poli zajistí bohaté možnosti automatického dokončování a předvídají, jaký text uživatel zadává.
* [*vyhodnocení* ](evaluateMethod.md) – Evaluates a vrátí její výstup výrazu strukturovaných dotazu.
* [*calchistogram* ](calchistogramMethod.md) – vypočítá histogram hodnoty atributů pro objektů vrácený výrazu strukturovaných dotazu.

Společně použít tyto metody rozhraní API umožní vytvoření prostředí bohaté sémantického vyhledávání.  Zadaný řetězec dotazu přirozeného jazyka *interpretovat* metoda poskytuje poznámkou verzích vstupní dotaz s výrazy strukturovaných dotazů, na základě základní dat gramatika a index.  *Vyhodnotit* metoda vyhodnotí výraz SQL a vrátí odpovídajících objektů index pro zobrazení.  *Calchistogram* metoda vypočítá distribuce hodnota atributu filtrování a upřesnění povolit.

**Příklad**

V doméně academic publikace, pokud uživatel zadá řetězec "latentní s" *interpretovat* metoda může poskytnout sadu seřazený interpretace, které naznačují, že uživatel může být hledání – klíčové slovo "latentní sémantického analýzy", název "latentní struktura analysis", nebo jiné výrazy počínaje "latentní s".  Tyto informace lze rychle Průvodce uživatele požadovaných výsledků hledání.

Pro tuto doménu *vyhodnotit* metoda slouží k načtení sadu odpovídajících publikace z academic indexu a *calchistogram* metoda slouží k výpočtu distribuce atributu hodnoty pro odpovídající publikace, které můžete použít pro další filtrování a výsledky hledání upřesněte.

Všimněte si, že ke zlepšení čitelnosti příkladů, volání rozhraní REST API obsahovat znaky (například prostory), které nebyly kódovaná adresou URL. Váš kód bude nutné použít příslušné adresy URL kódování.
