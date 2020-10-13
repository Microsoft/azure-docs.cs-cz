---
title: Export & odstranění dat – LUIS
titleSuffix: Azure Cognitive Services
description: Máte plnou kontrolu nad zobrazením, exportem a odstraněním jejich dat. Odstraňte zákaznická data a zajistěte tak ochranu osobních údajů a dodržování předpisů.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.openlocfilehash: 868443e52b7159e6ee9478f64b7b2f376a10f604
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541002"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportujte a odstraňte zákaznická data v Language Understanding (LUIS) v Cognitive Services

Odstraňte zákaznická data a zajistěte tak ochranu osobních údajů a dodržování předpisů.

## <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí pro žádosti o data zákazníků
Language Understanding Intelligent Service (LUIS) zachovává obsah zákazníka při provozu služby, ale uživatel LUIS má plnou kontrolu nad zobrazením, exportem a odstraněním jejich dat. To se dá udělat prostřednictvím webového [portálu](luis-reference-regions.md) Luis nebo pomocí [rozhraní API pro vytváření Luis (označovaných také jako programové)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Obsah zákazníka je uložen zašifrovaný v místní službě Azure Storage společnosti Microsoft a zahrnuje:

- Obsah účtu uživatele shromážděn při registraci
- Školicí data potřebná k sestavení modelů
- Přihlášené uživatelské dotazy používané [aktivním učením](luis-concept-review-endpoint-utterances.md) k lepšímu zlepšení modelu
  - Uživatelé můžou vypnout protokolování dotazů připojením `&log=false` k žádosti, podrobnosti [tady](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Odstraňují se zákaznická data
LUIS uživatelé mají plnou kontrolu nad tím, že budou odstraňovat obsahy uživatelů, a to buď prostřednictvím webového portálu LUIS, nebo pomocí rozhraní API pro vytváření LUIS (označovaných také jako program). V následující tabulce jsou uvedeny odkazy, které pomáhají s oběma:

| | **Uživatelský účet** | **Aplikace** | **Příklad utterance** | **Dotazy pro koncové uživatele** |
| --- | --- | --- | --- | --- |
| **Azure Portal** | [Propojit](luis-concept-data-storage.md#delete-an-account) | [Propojit](luis-how-to-start-new-app.md#delete-app) | [Propojit](luis-concept-data-storage.md#utterances-in-an-intent) | [Projevy Active Learning](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Protokol projevy](luis-concept-data-storage.md#disable-logging-utterances) |
| **Rozhraní API** | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Export zákaznických dat
LUIS uživatelé mají úplné řízení pro zobrazení dat na portálu, ale musí se exportovat prostřednictvím rozhraní API vytváření LUIS (označovaných také jako programové). V následující tabulce jsou uvedeny odkazy, které pomáhají s exporty dat prostřednictvím rozhraní API pro vytváření LUIS (označovaných také jako programové):

| | **Uživatelský účet** | **Aplikace** | **Utterance (celkem)** | **Dotazy pro koncové uživatele** |
| --- | --- | --- | --- | --- |
| **Rozhraní API** | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Propojit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Umístění aktivního učení

Aby bylo možné povolit [aktivní učení](luis-how-to-review-endpoint-utterances.md#enable-active-learning), ukládají se do projevych koncových bodů uživatele v následujících zeměpisných oblastech Azure:

* [Evropa](#europe)
* [Austrálie](#australia)
* [Spojené státy](#united-states)

S výjimkou aktivních výukových dat (popsaných níže) se LUIS řídí [postupy pro ukládání dat pro regionální služby](https://azuredatacentermap.azurewebsites.net/).

### <a name="europe"></a>Evropa

Portál [EU.Luis.AI](https://eu.luis.ai) a vytváření v Evropě (označované taky jako programové rozhraní API) se hostují v rámci Evropy v oblasti Azure. Portál eu.luis.ai a vytváření v Evropě (označované také jako programové rozhraní API) podporují nasazení koncových bodů do následujících geografických oblastí Azure:

* Evropa
* Francie
* Spojené království

Při nasazování do těchto geografických oblastí Azure bude projevy přijatý koncovým uživatelem vaší aplikace uložený v geografických oblastech Evropy Azure pro aktivní učení. Aktivní učení můžete zakázat. Přečtěte si téma [zakázání aktivního učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Pokud chcete spravovat uložené projevy, přečtěte si téma [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Austrálie

Portál [au.Luis.AI](https://au.luis.ai) a vytváření Austrálie (označované taky jako programové rozhraní API) se hostují v oblasti Austrálie v Azure. Portál au.luis.ai a vytváření Austrálie (označované také jako programové rozhraní API) podporují nasazení koncových bodů do následujících geografických oblastí Azure:

* Austrálie

Při nasazování do těchto geografických oblastí Azure bude projevy přijatý koncovým uživatelem vaší aplikace uložený v geografických oblastech Austrálie Azure pro aktivní učení. Aktivní učení můžete zakázat. Přečtěte si téma [zakázání aktivního učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Pokud chcete spravovat uložené projevy, přečtěte si téma [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>USA

Portál [Luis.AI](https://www.luis.ai) a vytváření USA (označované také jako programové rozhraní API) se hostují v USA geografie Azure. Portál luis.ai a vytváření USA (označované také jako programové rozhraní API) podporují nasazení koncových bodů do následujících geografických oblastí Azure:

* Geografické oblasti Azure, které nepodporují oblasti tvorby Evropy nebo Austrálie

Při nasazování do těchto geografických oblastí Azure bude projevy přijatý koncovým uživatelem vaší aplikace uložený v oblasti Azure USA geografie pro aktivní učení. Aktivní učení můžete zakázat. Přečtěte si téma [zakázání aktivního učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Pokud chcete spravovat uložené projevy, přečtěte si téma [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Referenční dokumentace oblastí LUIS](./luis-reference-regions.md)
