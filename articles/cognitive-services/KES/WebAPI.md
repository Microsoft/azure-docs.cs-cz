---
title: Webové rozhraní API – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Pomocí webového rozhraní API pro vytváření bohatě vybaveným a sémantické vyhledávání v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 5be39e8dce6aeeef32d20273c56650620d6fe986
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122021"
---
# <a name="web-api-interface"></a>Webové rozhraní API

Model souborů sestavených Knowledge Exploration Service můžete hostované a získávat přístup k sadu webových rozhraní API.  Rozhraní API může být hostované na místním počítači pomocí [ `host_service` ](CommandLine.md#host_service-command) příkaz, nebo jde nasadit do cloudu Azure pomocí služby [ `deploy_service` ](CommandLine.md#deploy_service-command) příkazu.  Obě tyto metody vystavit následující koncové body rozhraní API:

* [*interpretace* ](interpretMethod.md) – interpretuje řetězec dotazu v přirozeném jazyce. Vrátí anotované interpretace, které ve vyhledávacím poli zajistí bohaté možnosti automatického dokončování a předvídají, jaký text uživatel zadává.
* [*vyhodnocení* ](evaluateMethod.md) – Evaluates a vrátí její výstup výrazu strukturovaných dotazů.
* [*calchistogram* ](calchistogramMethod.md) – vypočte histogram hodnot atributů pro objekty, které vrátil výraz strukturovaných dotazů.

Použijí společně, tyto metody rozhraní API umožňují vytvářet výkonné sémantické vyhledávání.  Zadaný řetězec dotazu přirozeného jazyka *interpretovat* metoda poskytuje s poznámkami verze vstupní dotaz s výrazy strukturovaných dotazů založené na podkladová data gramatiky a index.  *Vyhodnotit* metoda vyhodnotí výraz strukturovaných dotazů a vrátí odpovídající objekty index pro zobrazení.  *Calchistogram* metoda vypočítá distribuce hodnotu atributu umožňující filtrování a upřesnění.

**Příklad**

V doméně academic publikací, pokud uživatel zadá řetězec "latentní s" *interpretovat* může metoda poskytnout sadu seřazený interpretace navrhuje, že uživatel může vyhledávat – klíčové slovo "latentní sémantické analýzy" název "analýza latentní struktury", nebo dalších výrazů od "latentní s".  Tyto informace slouží k rychlé Průvodce uživatele požadovaných výsledků hledání.

Pro tuto doménu *vyhodnotit* metodu je možné načíst sadu odpovídajících publikace z akademického indexu a *calchistogram* metody slouží k výpočtu distribuce atribut hodnoty pro odpovídající publikace, které můžete použít pro další filtrování a upřesnění výsledků hledání.

Všimněte si, že ke zlepšení čitelnosti z příkladů, volání rozhraní REST API obsahovat znaky (například mezery), které nebyly kódovaná adresou URL. Váš kód bude nutné použít odpovídající kódování URL.
