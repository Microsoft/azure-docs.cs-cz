---
title: Export a odstranění dat zákazníka - LEOŠ - kognitivní služeb Azure || Microsoft Docs
description: Referenční dokumentace pro export a odstranění dat zákazníka ze služby znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: f684b8ab875e2fbb774dc4a29bce25be41b24e6d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343719"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Export a odstranit svá data zákazníků v jazyk Principy (LEOŠ) v kognitivní služby

## <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavek data zákazníků
Jazyk Principy inteligentního služby (LEOŠ) chrání obsah zákazníka pro provoz služby, ale uživatel LEOŠ má plnou kontrolu nad zobrazení nebo odstranění svá data. To lze provést prostřednictvím webového LEOŠ [portál](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) nebo [programovací rozhraní API LEOŠ](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zákazník obsahu se ukládají zašifrovaně v místní úložišti Azure Microsoft a zahrnuje:

- Obsah účet uživatele se shromažďují při registraci
- Školení data potřebná k vytváření modelů (tj. záměr & entity)
- Dotazy uživatele zaznamenána v době běhu k vylepšení modely uživatele
  - Uživatelé můžou vypnout protokolování dotazu připojením `&log=false` požadavek, podrobnosti [sem](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-resources-faq#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Odstraňování dat zákazníka
LEOŠ mají úplnou kontrolu odstranit jakýkoli uživatele obsah, a to buď prostřednictvím webového portálu LEOŠ nebo LEOŠ programovací rozhraní API. V následující tabulce jsou odkazy, které s oběma:

| | **Uživatelský účet** | **Aplikace** | **Utterance(s)** | **Dotazy koncového uživatele** |
| --- | --- | --- | --- | --- |
| **Azure Portal** | [Odkaz](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-account-settings) | [Odkaz](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Odkaz](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Odkaz](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Export dat zákazníka
LEOŠ uživatelé mají plnou kontrolu, pokud chcete zobrazit data na portálu, ale musí být exportován prostřednictvím LEOŠ programovací rozhraní API. V následující tabulce jsou odkazy, které se exportuje data prostřednictvím LEOŠ programovací rozhraní API:

| | **Uživatelský účet** | **Aplikace** | **Utterance(s)** | **Dotazy koncového uživatele** |
| --- | --- | --- | --- | --- |
| **Rozhraní API** | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Odkaz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Referenční dokumentace LEOŠ oblastí](./luis-reference-regions.md)
