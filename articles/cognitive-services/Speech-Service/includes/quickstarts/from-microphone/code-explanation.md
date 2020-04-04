---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638102"
---
Klíč a oblast odběru prostředku řeči jsou nutné k vytvoření objektu konfigurace řeči. Konfigurační objekt je potřeba k vytvoření instance objektu rozpoznávání řeči.

Instance rozpoznávání zveřejňuje několik způsobů, jak rozpoznat řeč. V tomto příkladu je rozpoznávání řeči jednou. Tato funkce umožňuje službě Řeč vědět, že odesíláte jednu frázi pro rozpoznávání a že jakmile je fráze identifikována, aby přestala rozpoznávat řeč. Jakmile je výsledek přinesl, kód zapíše důvod rozpoznávání do konzoly.

> [!TIP]
> Sada Speech SDK bude ve `en-us` výchozím nastavení rozpoznána použití jazyka, viz [Určení zdrojového jazyka pro řeč na text,](../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.