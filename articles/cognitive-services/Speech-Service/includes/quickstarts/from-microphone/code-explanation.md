---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400803"
---
Pro vytvoření objektu konfigurace řeči se vyžaduje klíč a oblast předplatného prostředku pro rozpoznávání řeči. Objekt konfigurace je nutný k vytvoření instance objektu pro rozpoznávání řeči.

Instance nástroje pro rozpoznávání nabízí více způsobů rozpoznávání řeči. V tomto příkladu je rozpoznávání řeči rozpoznáno jednou. Tato funkce umožňuje, aby služba Speech Service věděli, že posíláte jednu frázi pro rozpoznávání, a že po identifikaci fráze pro zastavení rozpoznávání řeči. Po získání výsledku kód zapíše důvod pro rozpoznávání do konzoly.

> [!TIP]
> Sada Speech SDK bude ve výchozím nastavení rozpoznávat použití `en-us` pro jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro rozpoznávání řeči textu](../../../how-to-specify-source-language.md) .