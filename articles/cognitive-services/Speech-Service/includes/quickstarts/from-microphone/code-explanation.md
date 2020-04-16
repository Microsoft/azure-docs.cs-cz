---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400803"
---
Klíč a oblast odběru prostředku řeči jsou nutné k vytvoření objektu konfigurace řeči. Konfigurační objekt je potřeba k vytvoření instance objektu rozpoznávání řeči.

Instance rozpoznávání zveřejňuje několik způsobů, jak rozpoznat řeč. V tomto příkladu je rozpoznávání řeči jednou. Tato funkce umožňuje službě Řeč vědět, že odesíláte jednu frázi pro rozpoznávání a že jakmile je fráze identifikována, aby přestala rozpoznávat řeč. Jakmile je výsledek přinesl, kód zapíše důvod rozpoznávání do konzoly.

> [!TIP]
> Sada Speech SDK bude ve `en-us` výchozím nastavení rozpoznána použití jazyka, viz [Určení zdrojového jazyka pro řeč na text,](../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.