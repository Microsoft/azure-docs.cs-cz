---
title: Exportovat a odstranit data
titleSuffix: Azure Cognitive Services
description: Zachová obsah zákazníka pro provoz služby Language Understanding Intelligent Service (LUIS), ale má plnou kontrolu nad zobrazení, exportu a odstranění jejich dat uživatel LUIS. To lze provést prostřednictvím webového portálu služby LUIS nebo rozhraním API LUIS prostřednictvím kódu programu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0aba859fc0c5e2de64b2bbdb9c06f2434937f4e8
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140672"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportovat a odstraňovat vaše zákaznická data v Language Understanding (LUIS) ve službě Cognitive Services

## <a name="summary-of-customer-data-request-features"></a>Souhrnné informace o zákaznická data žádosti o funkce
Zachová obsah zákazníka pro provoz služby Language Understanding Intelligent Service (LUIS), ale má plnou kontrolu nad zobrazení, exportu a odstranění jejich dat uživatel LUIS. To lze provést prostřednictvím webové služby LUIS [portál](luis-reference-regions.md) nebo [programových rozhraní API LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zákazník obsah je uložen zašifrovaný ve službě Microsoft Azure storage místní a zahrnuje:

- Uživatelský účet obsah shromážděné při registraci
- Trénovací data potřebné k sestavení modelů (tj. záměr & entity)
- Dotazy uživatele zaznamenána v době běhu k vylepšení modely uživatelů
  - Uživatele můžete vypnout protokolování dotazu přidáním `&log=false` požadavku, podrobností [zde](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data
LUIS uživatelé mají plnou kontrolu nad obsah uživatelů, buď prostřednictvím webového portálu služby LUIS nebo programových rozhraní API LUIS odstraňovat. Pomoc s oběma odkazy v následující tabulce:

| | **Uživatelský účet** | **Aplikace** | **Utterance(s)** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Azure Portal** | [Odkaz](luis-how-to-account-settings.md) | [Odkaz](luis-how-to-start-new-app.md#delete-app) | [Odkaz](luis-how-to-start-new-app.md#delete-app) | [Odkaz](luis-how-to-start-new-app.md#delete-app) |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Export zákaznických dat
LUIS uživatelé mají plnou kontrolu nad zobrazení dat na portálu, ale musí být exportován prostřednictvím programových rozhraní API LUIS. Následující tabulka obsahuje odkazy asistence s exporty data prostřednictvím programových rozhraní API LUIS:

| | **Uživatelský účet** | **Aplikace** | **Utterance(s)** | **Dotazy koncových uživatelů** |
| --- | --- | --- | --- | --- |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Služba LUIS oblastech odkaz](./luis-reference-regions.md)
