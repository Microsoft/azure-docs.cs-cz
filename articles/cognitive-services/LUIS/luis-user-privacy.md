---
title: Export & odstranění dat – LUIS
titleSuffix: Azure Cognitive Services
description: Máte plnou kontrolu nad zobrazením, exportem a odstraněním jejich dat. Odstraňte zákaznická data a zajistěte tak ochranu osobních údajů a dodržování předpisů.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 061f019fe36e4d5495a41fc81e56d9673ad595fc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953462"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportovat a odstraňovat vaše zákaznická data v Language Understanding (LUIS) ve službě Cognitive Services

Odstraňte zákaznická data a zajistěte tak ochranu osobních údajů a dodržování předpisů. 

## <a name="summary-of-customer-data-request-features"></a>Souhrnné informace o zákaznická data žádosti o funkce
Zachová obsah zákazníka pro provoz služby Language Understanding Intelligent Service (LUIS), ale má plnou kontrolu nad zobrazení, exportu a odstranění jejich dat uživatel LUIS. To se dá udělat prostřednictvím webového [portálu](luis-reference-regions.md) Luis nebo pomocí [rozhraní API pro vytváření Luis (označovaných také jako programové)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zákazník obsah je uložen zašifrovaný ve službě Microsoft Azure storage místní a zahrnuje:

- Uživatelský účet obsah shromážděné při registraci
- Školicí data potřebná k sestavení modelů
- Přihlášené uživatelské dotazy používané [aktivním učením](luis-concept-review-endpoint-utterances.md) k lepšímu zlepšení modelu
  - Uživatele můžete vypnout protokolování dotazu přidáním `&log=false` požadavku, podrobností [zde](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data
LUIS uživatelé mají plnou kontrolu nad tím, že budou odstraňovat obsahy uživatelů, a to buď prostřednictvím webového portálu LUIS, nebo pomocí rozhraní API pro vytváření LUIS (označovaných také jako program). Pomoc s oběma odkazy v následující tabulce:

| | **Uživatelský účet** | **Aplikace** | **Příklad utterance** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Azure Portal** | [Odkaz](luis-concept-data-storage.md#delete-an-account) | [Odkaz](luis-how-to-start-new-app.md#delete-app) | [Odkaz](luis-concept-data-storage.md#utterances-in-an-intent) | [Projevy Active Learning](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Protokol projevy](luis-concept-data-storage.md#disable-logging-utterances) |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Export zákaznických dat
LUIS uživatelé mají úplné řízení pro zobrazení dat na portálu, ale musí se exportovat prostřednictvím rozhraní API vytváření LUIS (označovaných také jako programové). V následující tabulce jsou uvedeny odkazy, které pomáhají s exporty dat prostřednictvím rozhraní API pro vytváření LUIS (označovaných také jako programové):

| | **Uživatelský účet** | **Aplikace** | **Utterance(s)** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Umístění aktivního učení

Aby bylo možné povolit [aktivní učení](luis-how-to-review-endpoint-utterances.md#enable-active-learning), ukládají se do projevych koncových bodů uživatele v následujících zeměpisných oblastech Azure:

* [Evropa](#europe)
* [Austrálie](#australia)
* [USA](#united-states)

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

### <a name="united-states"></a>Spojené státy

Portál [Luis.AI](https://www.luis.ai) a vytváření USA (označované také jako programové rozhraní API) se hostují v USA geografie Azure. Portál luis.ai a vytváření USA (označované také jako programové rozhraní API) podporují nasazení koncových bodů do následujících geografických oblastí Azure:

* Geografické oblasti Azure, které nepodporují oblasti tvorby Evropy nebo Austrálie

Při nasazování do těchto geografických oblastí Azure bude projevy přijatý koncovým uživatelem vaší aplikace uložený v oblasti Azure USA geografie pro aktivní učení. Aktivní učení můžete zakázat. Přečtěte si téma [zakázání aktivního učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Pokud chcete spravovat uložené projevy, přečtěte si téma [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Služba LUIS oblastech odkaz](./luis-reference-regions.md)
