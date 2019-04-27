---
title: Exportovat a odstranit data
titleSuffix: Azure Cognitive Services
description: Odstraníte zákaznická data k zajištění ochrany osobních údajů a dodržování předpisů.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597101"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportovat a odstraňovat vaše zákaznická data v Language Understanding (LUIS) ve službě Cognitive Services

Odstraníte zákaznická data k zajištění ochrany osobních údajů a dodržování předpisů. 

## <a name="summary-of-customer-data-request-features"></a>Souhrnné informace o zákaznická data žádosti o funkce
Zachová obsah zákazníka pro provoz služby Language Understanding Intelligent Service (LUIS), ale má plnou kontrolu nad zobrazení, exportu a odstranění jejich dat uživatel LUIS. To lze provést prostřednictvím webové služby LUIS [portál](luis-reference-regions.md) nebo [vytváření LUIS (označované také jako programových) rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zákazník obsah je uložen zašifrovaný ve službě Microsoft Azure storage místní a zahrnuje:

- Uživatelský účet obsah shromážděné při registraci
- Trénovací data potřebné k sestavení modelů
- Přihlášení uživatele dotazy použitými [aktivně učit](luis-concept-review-endpoint-utterances.md) jí pomohou zlepšit modelu
  - Uživatele můžete vypnout protokolování dotazu přidáním `&log=false` požadavku, podrobností [zde](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data
Služba LUIS uživatelé mají plnou kontrolu nad odstranit všechny uživatele obsahu prostřednictvím webového portálu služby LUIS nebo rozhraním API LUIS vytváření (označované také jako programátorské). Pomoc s oběma odkazy v následující tabulce:

| | **Uživatelský účet** | **Aplikace** | **Příklad Utterance(s)** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Azure Portal** | [Odkaz](luis-concept-data-storage.md#delete-an-account) | [Odkaz](luis-how-to-start-new-app.md#delete-app) | [Odkaz](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktivní učení projevy](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Zaznamenané projevy](luis-concept-data-storage.md#disable-logging-utterances) |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Export zákaznických dat
LUIS uživatelé mít plnou kontrolu pro zobrazení dat na portálu, ale musí být exportován prostřednictvím rozhraní API (označované také jako prostřednictvím kódu programu) pro vytváření LUIS. Následující tabulka obsahuje odkazy asistence s exporty data prostřednictvím rozhraní API (označované také jako prostřednictvím kódu programu) pro vytváření LUIS:

| | **Uživatelský účet** | **Aplikace** | **Utterance(s)** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Umístění aktivní učení

Chcete-li povolit [aktivně učit](luis-how-to-review-endpoint-utterances.md#enable-active-learning), zaznamenané projevy uživatelů, paket publikované koncové body služby LUIS, jsou uloženy v následujících zeměpisných oblastech Azure:

* [Evropa](#europe)
* [Austrálie](#australia)
* [Spojené státy americké](#united-states)

S výjimkou aktivně učit dat (podrobnosti jsou dole), následuje LUIS [postupů úložiště dat pro regionální služby](http://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Evropa

[Eu.luis.ai](https://eu.luis.ai) portál a Evropě vytváření (označované také jako programových rozhraní API) jsou hostované v zeměpisné oblasti Azure v Evropě. Portál eu.luis.ai a Evropa (označované také jako programové rozhraní API) pro vytváření podporují nasazení koncových bodů do následujících zeměpisných oblastech Azure:

* Evropa
* Francie
* Spojené království

Při nasazování do těchto zeměpisných oblastech Azure, projevy přijatých koncový bod koncovým uživatelům vaší aplikace se uloží v zeměpisné oblasti Evropa Azure pro active learning. Můžete zakázat active learning, naleznete v tématu [zakázat aktivně učit](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Správa uložených projevy, naleznete v tématu [odstranit utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Austrálie

[Au.luis.ai](https://au.luis.ai) portál a Austrálie pro tvorbu (označované také jako programových rozhraní API) jsou hostované v zeměpisné oblasti Austrálie pro Azure. Portál au.luis.ai a Austrálie (označované také jako programové rozhraní API) pro vytváření podporují nasazení koncových bodů do následujících zeměpisných oblastech Azure:

* Austrálie

Při nasazování do těchto zeměpisných oblastech Azure, projevy přijatých koncový bod koncovým uživatelům vaší aplikace se uloží v zeměpisné oblasti Austrálie pro Azure pro active learning. Můžete zakázat active learning, naleznete v tématu [zakázat aktivně učit](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Správa uložených projevy, naleznete v tématu [odstranit utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Spojené státy

[Luis.ai](https://www.luis.ai) portál a vytváření Spojené státy (označované také jako programových rozhraní API) jsou hostované v zeměpisné oblasti Azure v USA. Portál luis.ai a vytváření Spojené státy (označované také jako programových rozhraní API) podporují nasazení koncových bodů do následujících zeměpisných oblastech Azure:

* Zeměpisné oblasti Azure nepodporuje Evropa nebo Austrálie pro vytváření oblastí

Při nasazování do těchto zeměpisných oblastech Azure, projevy přijatých koncový bod koncovým uživatelům vaší aplikace se uloží v Azure USA zeměpisné oblasti za aktivně učit. Můžete zakázat active learning, naleznete v tématu [zakázat aktivně učit](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Správa uložených projevy, naleznete v tématu [odstranit utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Služba LUIS oblastech odkaz](./luis-reference-regions.md)
