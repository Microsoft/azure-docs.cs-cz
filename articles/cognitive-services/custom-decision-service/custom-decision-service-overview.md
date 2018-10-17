---
title: Co je Custom Decision Service?
titlesuffix: Azure Cognitive Services
description: Tento článek obsahuje přehled služby Custom Decision Service.
services: cognitive-services
author: alekh
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 273f2965a0fcaaa729175c5232da1aba69589eec
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364201"
---
# <a name="what-is-custom-decision-service"></a>Co je Custom Decision Service?

Úvodní stránka typické webové nebo mobilní aplikace odkazuje na několik článků nebo jiné typy obsahu. Při načítání úvodní stránky může aplikace požádat Custom Decision Service o seřazení článků, které jsou na dané stránce. Když tedy uživatel kliknutím vybere článek, může být do Custom Decision Service odeslána druhá žádost, která bude zaznamenávat výsledky rozhodnutí uživatele.

Custom Decision Service se snadno používá, protože vyžaduje pouze informační kanál RSS pro váš obsah a přidání několika řádků kódu JavaScriptu do vaší aplikace.

Custom Decision Service převede váš obsah na funkce pro strojové učení. Systém používá tyto funkce k pochopení vašeho obsahu z hlediska jeho textu, obrázků, videí a celkového mínění. Využívá několik dalších služeb [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services), jako například [spojování entit](../entitylinking/home.md), [analýzu textu](../text-analytics/overview.md), [rozpoznávání emocí](../emotion/home.md) a [počítačové zpracování obrazu](../computer-vision/home.md).

Mezi případy běžného použití Custom Decision Service patří:

* Přizpůsobení článků na webu zpráv
* Přizpůsobení obsahu videa na portálu médií
* Optimalizace umístění reklam nebo webových stránek, na které reklamy odkazují
* Řazení doporučených položek na nákupním webu

Custom Decision Service je nyní ve *verzi Public Preview zdarma*. Umožní přizpůsobení seznamu článků na webu nebo v aplikaci. Nejvhodnější je extrahovat funkce z obsahu v angličtině. [Omezenou funkčnost](../text-analytics/overview.md) nabízíme i pro jiné jazyky, jako je španělština, francouzština, němčina, portugalština a japonština. Tato dokumentace bude při zpřístupnění nových funkcí revidována.

Custom Decision Service můžete použít v aplikacích, které nejsou v doméně přizpůsobení obsahu. Tyto aplikace můžou být vhodné pro vlastní náhled. [Kontaktujte nás](https://azure.microsoft.com/overview/sales-number/) a dozvíte se víc.

## <a name="api-usage-modes"></a>Režimy využití rozhraní API

Custom Decision Service můžete použít pro webové stránky i mobilní aplikace. Rozhraní API mohou být volána z prohlížeče nebo aplikace. Použití rozhraní API je podobné v obou, ale některé podrobnosti se liší.

## <a name="glossary-of-terms"></a>Glosář termínů

V této dokumentaci se často vyskytuje několik termínů:

* **Sada akcí**: Sada položek obsahu, které má Custom Decision Service řadit. Tuto sadu lze určit jako koncový bod *RSS* nebo *Atom*.
* **Řazení**: Každý požadavek na Custom Decision Service určuje jednu nebo více sad akcí. Systém reaguje výběrem všech možností obsahu z těchto sad a vrátí je seřazené.
* **Funkce zpětného volání**: Tato funkce, kterou specifikujete vy, vykreslí obsah v uživatelském rozhraní. Obsah je seřazený podle pořadí určeného Custom Decision Service.
* **Odměna**: Míra, jak uživatel reagoval na vykreslený obsah. Custom Decision Service měří odpověď uživatele podle kliknutí. Kliknutí jsou nahlášená do systému pomocí vlastního kódu vloženého do vaší aplikace.

## <a name="next-steps"></a>Další kroky

* [Zaregistrujte si vaši aplikaci](custom-decision-service-get-started-register.md) s Custom Decision Service.
* Začněte optimalizovat [webovou stránku](custom-decision-service-get-started-browser.md) nebo [aplikaci pro smartphone](custom-decision-service-get-started-app.md).
* Více informací o podporovaných funkcích se dozvíte v [referenční dokumentaci k rozhraní API](custom-decision-service-api-reference.md).