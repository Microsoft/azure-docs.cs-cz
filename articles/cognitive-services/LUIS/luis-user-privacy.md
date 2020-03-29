---
title: Export & odstranění dat – LUIS
titleSuffix: Azure Cognitive Services
description: Máte plnou kontrolu nad zobrazením, exportem a odstraněním jejich dat. Odstraňte zákaznická data, abyste zajistili ochranu osobních údajů a dodržování předpisů.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273368"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Export a odstranění zákaznických dat v jazyce Language Understanding (LUIS) ve službách Cognitive Services

Odstraňte zákaznická data, abyste zajistili ochranu osobních údajů a dodržování předpisů.

## <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavků na zákaznická data
Jazyk porozumění inteligentní služby (LUIS) zachovává obsah zákazníka pro provoz služby, ale uživatel LUIS má plnou kontrolu nad prohlížení, exportu a odstranění jejich dat. To lze provést prostřednictvím webového [portálu](luis-reference-regions.md) LUIS nebo [luis authoring (označované také jako programatické) API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Obsah zákazníků se ukládá šifrovaný v místním úložišti Microsoft Azure a zahrnuje:

- Obsah uživatelského účtu shromážděný při registraci
- Údaje o školení potřebné k sestavení modelů
- Protokolované uživatelské dotazy používané [aktivním učením](luis-concept-review-endpoint-utterances.md) ke zlepšení modelu
  - Uživatelé mohou vypnout protokolování dotazů připojením `&log=false` k požadavku, podrobnosti [zde](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Odstranění dat zákazníků
Uživatelé služby LUIS mají plnou kontrolu nad odstraněním veškerého uživatelského obsahu, a to buď prostřednictvím webového portálu LUIS, nebo pomocí rozhraní API pro vytváření luis (označované také jako programatická). V následující tabulce jsou uvedeny odkazy, které pomáhají s oběma:

| | **Uživatelský účet** | **Aplikace** | **Příklad utterance(s)** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Portál** | [Odkaz](luis-concept-data-storage.md#delete-an-account) | [Odkaz](luis-how-to-start-new-app.md#delete-app) | [Odkaz](luis-concept-data-storage.md#utterances-in-an-intent) | [Projevy aktivního učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Protokolované projevy](luis-concept-data-storage.md#disable-logging-utterances) |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Export dat zákazníků
Uživatelé služby LUIS mají úplnou kontrolu nad zobrazením dat na portálu, ale musí být exportována prostřednictvím api pro vytváření luis (označované také jako programatická). V následující tabulce jsou uvedeny odkazy, které pomáhají s exportem dat prostřednictvím api pro vytváření luis (označované také jako programatická):

| | **Uživatelský účet** | **Aplikace** | **Utterance (y)** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Umístění aktivního učení

Chcete-li povolit [aktivní učení](luis-how-to-review-endpoint-utterances.md#enable-active-learning), protokolované projevy uživatelů přijaté na publikovaných koncových bodech LUIS jsou uloženy v následujících geografických oblastech Azure:

* [Evropa](#europe)
* [Austrálie](#australia)
* [Spojené státy](#united-states)

S výjimkou aktivních dat učení (podrobně popsáno níže) se LUIS řídí [postupy ukládání dat pro regionální služby](https://azuredatacentermap.azurewebsites.net/).

### <a name="europe"></a>Evropa

Portál [eu.luis.ai](https://eu.luis.ai) a vytváření europe (označované také jako programatická api) jsou hostované v geografii Azure v Evropě. Portál eu.luis.ai a vytváření (označované také jako programová api) podporují nasazení koncových bodů do následujících zeměpisných oblastí Azure:

* Evropa
* Francie
* Spojené království

Při nasazování do těchto geografických oblastí Azure se projevy přijaté koncovým bodem od koncových uživatelů vaší aplikace uloží do geografie Azure v Evropě pro aktivní učení. Můžete zakázat aktivní učení, viz [Zakázat aktivní učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Chcete-li spravovat uložené projevy, naleznete [v tématu Odstranění utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Austrálie

Portál [au.luis.ai](https://au.luis.ai) a uživatelská zařízení v Austrálii (označované také jako programatická api) jsou hostované v geografii Azure v Austrálii. Portál au.luis.ai a uživatelská zařízení v Austrálii (označovaná také jako programová api) podporují nasazení koncových bodů do následujících zeměpisných oblastí Azure:

* Austrálie

Při nasazování do těchto geografických oblastí Azure se projevy přijaté koncovým bodem od koncových uživatelů vaší aplikace uloží do zeměpisu Azure v Austrálii pro aktivní učení. Můžete zakázat aktivní učení, viz [Zakázat aktivní učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Chcete-li spravovat uložené projevy, naleznete [v tématu Odstranění utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>Spojené státy

Portál [luis.ai](https://www.luis.ai) a vytváření ve Spojených státech (označované také jako programatická api) jsou hostované v geografii Azure ve Spojených státech. Portál luis.ai a vytváření ve Spojených státech (označované také jako programová api) podporují nasazení koncových bodů do následujících geografických oblastí Azure:

* Geografické oblasti Azure, které nejsou podporované oblastmi vytváření V Evropě nebo Austrálii

Při nasazování do těchto geografických oblastí Azure se projevy přijaté koncovým bodem od koncových uživatelů vaší aplikace uloží do geografie Azure ve Spojených státech pro aktivní učení. Můžete zakázat aktivní učení, viz [Zakázat aktivní učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Chcete-li spravovat uložené projevy, naleznete [v tématu Odstranění utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Odkaz na oblasti LUIS](./luis-reference-regions.md)
