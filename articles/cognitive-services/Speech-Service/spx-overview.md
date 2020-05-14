---
title: SPX – Speech Service
titleSuffix: Azure Cognitive Services
description: SPX je nástroj příkazového řádku pro používání služby Speech bez psaní kódu. Protokol SPX vyžaduje minimální nastavení a je snadné začít experimentovat se základními funkcemi služby Speech Service, aby bylo možné zjistit, jestli vaše případy použití můžou být splněné.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: b473bdc516c59b55eeb44f227352497142a4383b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202279"
---
# <a name="what-is-spx"></a>Co je SPX?

SPX je nástroj příkazového řádku pro používání služby Speech bez psaní kódu. SPX vyžaduje minimální nastavení a je snadné začít experimentovat se základními funkcemi služby Speech Service, abyste viděli, jestli se můžou vaše případy použití splnit. Během několika minut můžete spustit jednoduché testovací pracovní postupy, jako je rozpoznávání řeči Batch z adresáře souborů, nebo převod textu na řeč pro kolekci řetězců ze souboru. Kromě jednoduchých pracovních postupů je SPX připravené k provozu a dá se škálovat tak, aby se spouštěly větší procesy pomocí automatizovaných `.bat` skriptů nebo skriptů prostředí.

Většina primárních funkcí v sadě Speech SDK je dostupná v SPX, ale některé pokročilé funkce a přizpůsobení se v SPX zjednodušují. Při rozhodování o použití SPX nebo sady SDK Vezměte v úvahu následující pokyny.

Použijte SPX v těchto případech:
* Chcete experimentovat s funkcemi služby Speech s minimálním nastavením a bez kódu
* Používání služby Speech je poměrně jednoduché požadavky na produkční aplikaci.

SDK použijte v těchto případech:
* Chcete integrovat funkce hlasové služby v konkrétním jazyce nebo platformě (např. C#, Python, C++)
* Máte složité požadavky, které mohou vyžadovat pokročilé žádosti o služby nebo vývoj vlastního chování včetně streamování odpovědí.

## <a name="core-features"></a>Základní funkce

* Rozpoznávání řeči – převod řeči na text buď ze zvukových souborů, nebo přímo z mikrofonu, nebo přepisovat zaznamenané konverzace.

* Syntéza řeči – převod textu na řeč pomocí vstupu z textových souborů nebo zadání přímo z příkazového řádku Přizpůsobte charakteristiky výstupů řeči pomocí [konfigurací SSML](speech-synthesis-markup.md)a buď [standardních, nebo neuronové hlasů](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Překlad řeči – přeloží zvuk ve zdrojovém jazyce na text v cílovém jazyce.

* Spuštění na výpočetních prostředcích Azure – odeslání příkazů SPX pro spuštění na vzdáleném výpočetním prostředku Azure pomocí `spx webjob` .

## <a name="get-started"></a>Začínáme

Pokud chcete začít s SPX, přečtěte si [článek Základy](spx-basics.md). V tomto článku se dozvíte, jak spustit některé základní příkazy v SPX, a taky se zobrazuje ještě mírně pokročilejší příkazy pro spouštění operací Batch pro převod řeči na text a převod textu na řeč. Po přečtení článku Základy byste měli mít dost znalostí syntaxe SPX, aby bylo možné začít psát některé vlastní příkazy nebo automatizovat jednoduché operace řeči.

## <a name="next-steps"></a>Další kroky

- [Základy SPX](spx-basics.md)
- Pokud je váš případ použití složitější, [Získejte sadu Speech SDK](speech-sdk.md) .
