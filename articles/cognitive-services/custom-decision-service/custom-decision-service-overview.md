---
title: Co je služba rozhodnutí vlastní? -Služby azure kognitivní | Microsoft Docs
description: Tento článek přehledy Azure vlastní rozhodnutí Service rozhraní API založené na cloudu kontextové rozhodnutí, které zvýší prostředí.
services: cognitive-services
author: alekh
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 774467446513dcd7ade7255d998b11f41824cafe
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "35343972"
---
# <a name="what-is-custom-decision-service"></a>Co je služba rozhodnutí vlastní?

V typické webové nebo mobilních aplikací, titulní stránky odkazy na několik článků nebo jiné typy obsahu. Jako předního stránka načte, může požádat o službu vlastní rozhodnutí rank článků, které jsou zahrnuty na této stránce front. Ano, když uživatel vybere kliknutím na článek, může být druhý požadavek odeslán do služby vlastní rozhodnutí, která bude protokolovat výsledek tohoto uživatele rozhodnutí.

Vlastní rozhodnutí služba je snadno použitelný, jak to vyžaduje pouze informačního kanálu RSS pro svůj obsah a zadání několika řádků JavaScript přidávaného do vaší aplikace.

Vlastní služba rozhodnutí převede obsah na funkce pro machine learning. Systém používá tyto funkce zjistit svůj obsah z hlediska text, obrázky, videa a celkovou představu. Používá několik dalších [kognitivní služby Microsoft](https://www.microsoft.com/cognitive-services), například [propojení Entity](../entitylinking/home.md), [Analýza textu](../text-analytics/overview.md), [rozpoznávání emocí úrovně](../emotion/home.md)a [Počítače vize](../computer-vision/home.md).

Některé běžné-případy použití pro službu rozhodnutí vlastní patří:

* Přizpůsobení články na webu zprávy
* Přizpůsobení obsahu videa na portálu média
* Optimalizace umísťováním ad nebo webové stránky, které by je odkazovalo ad
* Hodnocení doporučuje položky na nákupní webu.

Vlastní rozhodnutí služba je aktuálně v *bezplatnou verzi public preview*. Je můžete přizpůsobit seznam články na webu nebo aplikaci. Extrakce funkce funguje nejlépe ve anglických obsahu. [Omezenou funkčnost](../text-analytics/overview.md) se nabízí pro jiné jazyky, jako je španělština, francouzština, němčina, portugalština a japonštině. Tato dokumentace bude změněn, jakmile k dispozici nové funkce.

Nejde použít vlastní službu rozhodnutí v aplikacích, které nejsou v doméně obsahu individuální nastavení. Tyto aplikace může být vhodné pro vlastní verzi preview. [Kontaktujte nás](https://azure.microsoft.com/overview/sales-number/) Další informace.

## <a name="api-usage-modes"></a>Režimy využití rozhraní API

Vlastní rozhodnutí služba je použít pro webové stránky a mobilní aplikace. Rozhraní API je možné volat z prohlížeče nebo aplikace. Využití rozhraní API se podobá na obou, ale některé podrobnosti se liší.

## <a name="glossary-of-terms"></a>Slovník pojmů

V této dokumentaci dojít k často několik podmínek:

* **Akce set**: sadu obsahu položky služby vlastní rozhodnutí pro pořadí. Tato sada lze zadat jako *RSS* nebo *Atom* koncový bod.
* **Řazení**: každý požadavek pro vlastní rozhodnutí služby určuje jeden nebo více sad akce. Systém odpoví výběrem všech možností obsahu z tyto sady a vrátí je ve seřazený pořadí.
* **Funkce zpětného volání**: Tato funkce, které zadáte, vykreslí obsah v uživatelském rozhraní. Obsah je seřazené podle pořadí řazení vrácené službou vlastního rozhodnutí.
* **Potřebu**: míru jak uživatel odpověděl vykreslené obsah. Vlastní služba rozhodnutí měří odpověď uživatele pomocí kliknutí. Kliknutí jsou hlášeny systému pomocí vlastního kódu vložení do aplikace.

## <a name="next-steps"></a>Další postup

* [Registrace vaší aplikace](custom-decision-service-get-started-register.md) službou vlastní rozhodnutí
* Začínáme za účelem optimalizace [webové stránky](custom-decision-service-get-started-browser.md) nebo [aplikace pro smartphone](custom-decision-service-get-started-app.md).
* Obrátit [referenční dokumentace rozhraní API](custom-decision-service-api-reference.md) Další informace o funkci zadané.