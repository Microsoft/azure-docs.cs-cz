---
title: O převodu textu na řeč – Speech Service
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro převod textu na řeč poskytuje více než 75 hlasy ve více než 45 jazyků a národních prostředí. Použít standardní hlasová písma, stačí zadat název hlasové s několika dalších parametrů při volání služby řeči.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b413bd65582dceadd9aab912694c3b560070c4f3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251550"
---
# <a name="about-the-text-to-speech-api"></a>O rozhraní API pro převod textu na řeč

**Převod textu na řeč** rozhraní API (převod textu na ŘEČ) převede vstupní text na řeč přirozeného to nezní (také nazývané *syntézu řeči*).

Pro vygenerování řeči, vaše aplikace posílá požadavky HTTP POST do rozhraní API pro převod textu na řeč. Tady je text syntetizovat do mluvené řeči lidských to nezní a vrátí jako zvukový soubor. Širokou škálu hlasy a jazyky jsou podporovány.

Mezi scénáře, v které řeči syntézu přijali patří:

* *Vylepšení usnadnění přístupu:* **převod textu na řeč** technologie umožňuje vlastníkům obsahu a vydavatelé reagovat uživatelům různými způsoby interakce s jeho obsahem. Lidé s zrakově nebo vyřešit potíže se čtení nebudou moct využívat obsah aurally si cení toho. Hlasový výstup také usnadňuje uživatelům využívat textový obsah, jako je například novinových článcích nebo blogy na mobilních zařízeních při jste na cestách nebo práv vyplývajících z.

* *Reagovat ve scénářích multitaskingu:* **převod textu na řeč** umožňuje uživatelům chránit před důležité informace rychle a pohodlně při zvyšování nebo jinak mimo pohodlnou čtení prostředí. Navigace je běžné aplikace v této oblasti.

* *Vylepšení učení s několika způsoby:* Různí lidé další nejlepší různými způsoby. Online výuka odborníků ukázalo, že poskytuje pro hlasové hovory a textové společně vám pomohou lépe informace o další informace a zachovat.

* *Poskytuje intuitivní roboti nebo Asistenti:* Schopnost komunikovat může být nedílnou součástí inteligentní chatovací robot nebo virtuální Pomocníka s nastavením. Více společností vyvíjíte chatovací roboty k poskytování poutavé prostředí služby pro své zákazníky. Hlasové přidá jinou dimenzi tím, že odpovědi bodu robotů také přijímat aurally (například telefonní).

## <a name="voice-support"></a>Podpora hlasu

Microsoft **převod textu na řeč** služba nabízí více než 75 hlasy ve více než 45 jazyků a národních prostředí. Pokud chcete použít tyto standardní "hlasová písma", stačí zadat název hlas s několika dalších parametrů při volání rozhraní REST API služby. Další informace o podporovaných jazycích, národních prostředích a hlasy najdete v tématu [podporované jazyky](language-support.md#text-to-speech).

> [!IMPORTANT]
> Náklady se liší pro standardní, vlastní a neuronových sítí hlasy. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Neurální hlasů

Neurální převod textu na řeč umožňuje interakce s chatovacích a virtuálních asistentů přirozenější a poutavější převést digitální texty např. e knihy audiobooks a vylepšit navigaci systémy. Přirozené prosody podobnou té lidské a jasné rozdělení slov Neurální převod textu na ŘEČ významně snížila naslouchání reorganizovaly při interakci s AI systémy. Další informace o neuronových sítí hlasů, naleznete v tématu [podporované jazyky](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Vlastní hlasů

Přizpůsobení řeč umožňuje vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky: *hlasového písma.* K vytvoření hlasového písma, provedete studio nahrávání a nahrát přidružené skripty jako trénovací data. Služba potom vytvoří jedinečný hlas modelu, která je vyladěná pro nahrávání. Můžete použít jeho hlasového písma tak, aby odpovídaly řeči. Další informace najdete v tématu [vlastní hlasové písmo](how-to-customize-voice-font.md).

## <a name="api-capabilities"></a>Funkce rozhraní API

Mnoho funkcí **převod textu na řeč** rozhraní API, zejména v oblasti Vlastní nastavení, jsou k dispozici prostřednictvím REST. V následující tabulce najdete souhrn možností jednotlivých metod přístupu k rozhraní API. Úplný seznam možností a podrobnosti o rozhraní API najdete v tématu [Swagger odkaz](https://westus.cris.ai/swagger/ui/index).

| Případ použití | REST | Sady SDK |
|-----|-----|-----|----|
| Nahrání datových sad pro přizpůsobení hlasu | Ano | Ne |
| Vytvoření a správa modelů hlasového písma | Ano | Ne |
| Vytvoření a Správa nasazení hlasového písma | Ano | Ne |
| Vytvoření a správa testů hlasového písma| Ano | Ne |
| Správa předplatných | Ano | Ne |

> [!NOTE]
> Rozhraní API implementuje tohoto omezení požadavků rozhraní API 25 na 5 sekund. Záhlaví zpráv bude informovat omezení.

## <a name="next-steps"></a>Další postup

* [Pořiďte si předplatné služby zdarma řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Rychlé zprovoznění: Převést převod textu na řeč, Python](quickstart-python-text-to-speech.md)
* [Rychlé zprovoznění: Převést převod textu na řeč, .NET Core](quickstart-dotnet-text-to-speech.md)
* [REST API – referenční informace](rest-apis.md)
