---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582430"
---
Služba a typ vašeho předplatného určuje počet dotazů, které můžete provést za sekundu (QPS). Ujistěte se, že vaše aplikace obsahuje logiku potřebnou k nepřekročení vaší kvóty. Při dosažení nebo překročení limitu QPS požadavek selže a vrátí se stavový kód HTTP 429. Odpověď zahrnuje hlavičku `Retry-After`, která označuje, jak dlouho je nutné počkat před odesláním dalšího požadavku.

## <a name="denial-of-service-versus-throttling"></a>Odepření služby a omezování

Služba rozlišuje mezi útokem s cílem odepření služby (DoS) a porušením QPS. Pokud služba má podezření na útok DoS, požadavek bude úspěšný (stavový kód HTTP je 200 OK). Text odpovědi však bude prázdný.