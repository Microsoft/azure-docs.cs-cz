---
title: O převod textu na řeč | Dokumentace Microsoftu
description: Přehled možností převod textu na řeč.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: d7ec8648a8428558264c9bfd4d923523b90cce07
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855936"
---
# <a name="about-the-text-to-speech-api"></a>O převod textu na řeč rozhraní API

**Převod textu na řeč** Speech service rozhraní API (převod textu na ŘEČ) převede vstupní text na řeč přirozeného to nezní (také nazývané *syntézu řeči*).

Pro vygenerování řeči, vaše aplikace odešle požadavky HTTP POST do služby řeči. Tady je text syntetizovat do mluvené řeči lidských to nezní a vrátí jako zvukový soubor. Širokou škálu hlasy a jazyky jsou podporovány.

Mezi scénáře, v které řeči syntézu přijali patří:

* *Vylepšení usnadnění přístupu:* **převod textu na řeč** technologie umožňuje vlastníkům obsahu a vydavatelé reagovat uživatelům různými způsoby interakce s jeho obsahem. Lidé s zrakově nebo vyřešit potíže se čtení nebudou moct využívat obsah aurally si cení toho. Hlasový výstup také usnadňuje uživatelům využívat textový obsah, jako je například novinových článcích nebo blogy na mobilních zařízeních při jste na cestách nebo práv vyplývajících z.

* *Reagovat ve scénářích multitaskingu:* **převod textu na řeč** umožňuje uživatelům chránit před důležité informace rychle a pohodlně při zvyšování nebo jinak mimo pohodlnou čtení prostředí. Navigace je běžné aplikace v této oblasti. 

* *Vylepšení učení s několika režimy:* různí lidé další nejlepší různými způsoby. Online výuka odborníků ukázalo, že poskytuje pro hlasové hovory a textové společně vám pomohou lépe informace o další informace a zachovat.

* *Poskytuje intuitivní roboti nebo Asistenti:* schopnost komunikovat může být nedílnou součástí inteligentní chatovací robot nebo virtuální Pomocníka s nastavením. Více společností vyvíjíte chatovací roboty k poskytování poutavé prostředí služby pro své zákazníky. Hlasové přidá jinou dimenzi tím, že odpovědi bodu robotů také přijímat aurally (například telefonní).

## <a name="voice-support"></a>Podpora hlasu

Microsoft **převod textu na řeč** služba nabízí více než 75 hlasy ve více než 45 jazyků a národních prostředí. Pokud chcete použít tyto standardní "hlasová písma", stačí zadat název hlas s několika dalších parametrů při volání rozhraní REST API služby. Podrobnosti hlasy podporované, najdete v článku [podporované jazyky](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Pokud chcete jedinečný hlas pro vaši aplikaci, můžete vytvořit [vlastní hlasové písmo](how-to-customize-voice-font.md) z vlastní ukázky řeči.

## <a name="next-steps"></a>Další postup

* [Získejte zkušební verzi předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
