---
title: Webové rozhraní API – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Pomocí webového rozhraní API pro vytváření bohatě vybaveným a sémantické vyhledávání v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 131d8d58982b5430063aa3dc7b3d1982cd430148
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211300"
---
# <a name="web-api-interface"></a>Webové rozhraní API

Model souborů sestavených Knowledge Exploration Service můžete hostované a získávat přístup k sadu webových rozhraní API.  Rozhraní API může být hostované na místním počítači pomocí [ `host_service` ](CommandLine.md#host_service-command) příkaz, nebo jde nasadit do cloudu Azure pomocí služby [ `deploy_service` ](CommandLine.md#deploy_service-command) příkazu.  Obě tyto metody vystavit následující koncové body rozhraní API:

* [*interpretace* ](interpretMethod.md) – interpretuje řetězec dotazu v přirozeném jazyce. Vrátí anotované interpretace, které ve vyhledávacím poli zajistí bohaté možnosti automatického dokončování a předvídají, jaký text uživatel zadává.
* [*vyhodnocení* ](evaluateMethod.md) – Evaluates a vrátí její výstup výrazu strukturovaných dotazů.
* [*calchistogram* ](calchistogramMethod.md) – vypočte histogram hodnot atributů pro objekty, které vrátil výraz strukturovaných dotazů.

Použijí společně, tyto metody rozhraní API umožňují vytvářet výkonné sémantické vyhledávání.  Zadaný řetězec dotazu přirozeného jazyka *interpretovat* metoda poskytuje s poznámkami verze vstupní dotaz s výrazy strukturovaných dotazů založené na podkladová data gramatiky a index.  *Vyhodnotit* metoda vyhodnotí výraz strukturovaných dotazů a vrátí odpovídající objekty index pro zobrazení.  *Calchistogram* metoda vypočítá distribuce hodnotu atributu umožňující filtrování a upřesnění.

**Příklad**

V doméně academic publikací, pokud uživatel zadá řetězec "latentní s" *interpretovat* může metoda poskytnout sadu seřazený interpretace navrhuje, že uživatel může vyhledávat – klíčové slovo "latentní sémantické analýzy" název "analýza latentní struktury", nebo dalších výrazů od "latentní s".  Tyto informace můžete použít k rychlému přivedení uživatele k požadovaným výsledkům hledání.

Pro tuto doménu *vyhodnotit* metodu je možné načíst sadu odpovídajících publikace z akademického indexu a *calchistogram* metody slouží k výpočtu distribuce atribut hodnoty pro odpovídající publikace, které můžete použít pro další filtrování a upřesnění výsledků hledání.

Všimněte si, že ke zlepšení čitelnosti příkladů obsahuje volání rozhraní REST API znaky (například mezery), které nebyly zakódovány do adresy URL. Váš kód bude muset použít odpovídající kódování adresy URL.
