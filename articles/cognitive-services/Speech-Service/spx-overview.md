---
title: Rozhraní příkazového řádku Azure Speech
titleSuffix: Azure Cognitive Services
description: Rozpoznávání řeči je nástroj příkazového řádku pro používání služby Speech bez psaní kódu. Rozhraní příkazového řádku pro rozpoznávání řeči vyžaduje minimální nastavení a je snadné začít experimentovat se základními funkcemi služby Speech Service, abyste viděli, jestli se můžou vaše případy použití splnit.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8f1e5f38e97a1b51a2d919deebbdc452e9daf993
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539765"
---
# <a name="what-is-the-speech-cli"></a>Co je CLI služby Speech?

Rozpoznávání řeči je nástroj příkazového řádku pro používání služby Speech bez psaní kódu. Rozhraní příkazového řádku pro rozpoznávání řeči vyžaduje minimální nastavení a je snadné začít experimentovat se základními funkcemi služby Speech Service, abyste viděli, jestli se můžou vaše případy použití splnit. Během několika minut můžete spustit jednoduché testovací pracovní postupy, jako je rozpoznávání řeči Batch z adresáře souborů, nebo převod textu na řeč pro kolekci řetězců ze souboru. Mimo jednoduché pracovní postupy je rozpoznávání řeči pro rozpoznávání řeči připravené k provozu a dá se škálovat až ke spouštění větších procesů pomocí automatizovaných `.bat` skriptů nebo skriptů prostředí.

Většina funkcí v sadě Speech SDK je k dispozici v rozhraní příkazového řádku CLI a některé pokročilé funkce a přizpůsobení jsou zjednodušeny v rozhraní příkazového řádku pro rozpoznávání řeči. Při rozhodování, kdy používat rozpoznávání řeči nebo sadu Speech SDK, vezměte v úvahu následující pokyny.

Rozhraní příkazového řádku pro rozpoznávání řeči použijte v těchto případech:
* Chcete experimentovat s funkcemi služby Speech s minimálním nastavením a bez kódu
* Používání služby Speech je poměrně jednoduché požadavky na produkční aplikaci.

Sadu Speech SDK použijte v těchto případech:
* Chcete integrovat funkce hlasové služby v konkrétním jazyce nebo platformě (například C#, Python, C++)
* Máte složité požadavky, které mohou vyžadovat pokročilé žádosti o služby nebo vývoj vlastního chování včetně streamování odpovědí.

## <a name="core-features"></a>Základní funkce

* Rozpoznávání řeči – převod řeči na text buď ze zvukových souborů, nebo přímo z mikrofonu, nebo přepisovat zaznamenané konverzace.

* Syntéza řeči – převod textu na řeč pomocí vstupu z textových souborů nebo zadání přímo z příkazového řádku Přizpůsobte charakteristiky výstupů řeči pomocí [konfigurací SSML](speech-synthesis-markup.md)a buď [standardních, nebo neuronové hlasů](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Překlad řeči – přeloží zvuk ve zdrojovém jazyce na text nebo zvuk v cílovém jazyce.

* Spuštění na výpočetních prostředcích Azure – odeslání příkazů rozhraní příkazového řádku pro spuštění na vzdáleném výpočetním prostředku Azure pomocí `spx webjob` .

## <a name="get-started"></a>Začínáme

Pokud chcete začít s rozhraním příkazového řádku, přečtěte si [rychlý Start](spx-basics.md). V tomto článku se dozvíte, jak spustit některé základní příkazy, a také o dalších pokročilejších příkazech pro spouštění operací Batch pro převod řeči na text a převod textu na řeč. Po přečtení článku Základy byste měli mít dost znalostí syntaxe, abyste mohli začít psát některé vlastní příkazy nebo automatizovat jednoduché operace hlasové služby.

## <a name="next-steps"></a>Další kroky

- Začněte s [rychlým startem pro rozpoznávání řeči](spx-basics.md)
- [Konfigurace úložiště dat](./spx-data-store-configuration.md)
- Naučte se [spouštět operace Batch pomocí rozhraní příkazového řádku pro rozpoznávání řeči](./spx-batch-operations.md)
