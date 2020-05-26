---
title: Rozhraní příkazového řádku Azure Speech
titleSuffix: Azure Cognitive Services
description: Rozpoznávání řeči je nástroj příkazového řádku pro používání služby Speech bez psaní kódu. Rozhraní příkazového řádku pro rozpoznávání řeči vyžaduje minimální nastavení a je snadné začít experimentovat se základními funkcemi služby Speech Service, aby bylo možné zjistit, jestli se vaše případy použití můžou splnit.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: 3fb0b71cbb82b3b9acad1d1ce093baa86c700a51
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800342"
---
# <a name="what-is-the-speech-cli"></a>Co je to funkce Speech CLI?

Rozpoznávání řeči je nástroj příkazového řádku pro používání služby Speech bez psaní kódu. Rozhraní příkazového řádku pro rozpoznávání řeči vyžaduje minimální nastavení a je snadné začít experimentovat se základními funkcemi služby Speech Service, abyste viděli, jestli se můžou vaše případy použití splnit. Během několika minut můžete spustit jednoduché testovací pracovní postupy, jako je rozpoznávání řeči Batch z adresáře souborů, nebo převod textu na řeč pro kolekci řetězců ze souboru. Mimo jednoduché pracovní postupy je rozpoznávání řeči pro rozpoznávání řeči připravené k provozu a dá se škálovat až ke spouštění větších procesů pomocí automatizovaných `.bat` skriptů nebo skriptů prostředí.

Většina primárních funkcí v sadě Speech SDK je k dispozici v rozhraní příkazového řádku, ale v rozhraní příkazového řádku je zjednodušeno několik pokročilých funkcí a úprav. Při rozhodování, kdy používat rozpoznávání řeči nebo sadu Speech SDK, vezměte v úvahu následující pokyny.

Rozhraní příkazového řádku pro rozpoznávání řeči použijte v těchto případech:
* Chcete experimentovat s funkcemi služby Speech s minimálním nastavením a bez kódu
* Používání služby Speech je poměrně jednoduché požadavky na produkční aplikaci.

Sadu Speech SDK použijte v těchto případech:
* Chcete integrovat funkce hlasové služby v konkrétním jazyce nebo platformě (např. C#, Python, C++)
* Máte složité požadavky, které mohou vyžadovat pokročilé žádosti o služby nebo vývoj vlastního chování včetně streamování odpovědí.

## <a name="core-features"></a>Základní funkce

* Rozpoznávání řeči – převod řeči na text buď ze zvukových souborů, nebo přímo z mikrofonu, nebo přepisovat zaznamenané konverzace.

* Syntéza řeči – převod textu na řeč pomocí vstupu z textových souborů nebo zadání přímo z příkazového řádku Přizpůsobte charakteristiky výstupů řeči pomocí [konfigurací SSML](speech-synthesis-markup.md)a buď [standardních, nebo neuronové hlasů](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Překlad řeči – přeloží zvuk ve zdrojovém jazyce na text v cílovém jazyce.

* Spuštění na výpočetních prostředcích Azure – odeslání příkazů SPX pro spuštění na vzdáleném výpočetním prostředku Azure pomocí `spx webjob` .

## <a name="get-started"></a>Začínáme

Pokud chcete začít pracovat s rozhraním příkazového řádku, přečtěte si [článek Základy](spx-basics.md). V tomto článku se dozvíte, jak spustit některé základní příkazy s protokolem SPX, a navíc se zobrazuje ještě mírně pokročilejší příkazy pro spouštění operací Batch pro převod řeči na text a převod textu na řeč. Po přečtení článku Základy byste měli mít dost znalostí syntaxe SPX, aby bylo možné začít psát některé vlastní příkazy nebo automatizovat jednoduché operace řeči.

## <a name="next-steps"></a>Další kroky

- [Základy rozpoznávání řeči pro rozhraní příkazového řádku](spx-basics.md)
- Pokud je váš případ použití složitější, [Získejte sadu Speech SDK](speech-sdk.md) .
