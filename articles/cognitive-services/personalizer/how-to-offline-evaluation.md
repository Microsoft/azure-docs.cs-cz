---
title: Testování offline – Personalizer
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak analyzovat learning smyčku s použitím offline zkušební verzi
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b719e6e693471415350007a4f4fabed917b8e12d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722327"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Jak analyzovat learning smyčku s použitím offline zkušební verzi


Zjistěte, jak dokončit offline zkušební verzi a pochopení výsledky.

Offline hodnocení umožňují měřit jak efektivní Personalizer je ve srovnání s výchozí chování vaší aplikace, přečtěte si, jaké funkce jsou nejvíce přispívá k přizpůsobení a Objevte nové nastavení machine learning automaticky.

Přečtěte si informace o [Offline hodnocení](concepts-offline-evaluation.md) Další informace.


## <a name="prerequisites"></a>Požadavky

1. Musíte mít smyčku Personalizer nakonfigurované
1. Smyčka Personalizer musí mít nejméně 50 000 událostí v jeho protokoly pro vyhodnocení smysluplné výsledky.

Volitelně může také jste dříve exportovali _učení zásad_ soubory můžete porovnat a otestovat stejný hodnocení.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Kroky ke spuštění nové Offline vyhodnocení

1. Vyhledejte prostředek smyčky přizpůsobení na webu Azure Portal.
1. Přejděte do části "Vyhodnocení".
1. Klikněte na nové hodnocení
1. Vyberte počáteční a koncové datum pro offline hodnocení. Jedná se o data v minulosti, které určují rozsah data se mají použít v pořadí vyhodnocování. Tato data musí být k dispozici v protokolech, jak je uvedeno v [uchovávání dat](how-to-settings.md) nastavení.
1. Volitelně můžete nahrát vlastní zásady učení. 
1. Určuje, zda by měl Personalizer vytvořit zásadu optimalizované učení na základě chování uživatelů v tomto časovém období.
1. Spustit vyhodnocení

## <a name="results"></a>Výsledky

Hodnocení může trvat dlouhou dobu pro spuštění, v závislosti na množství dat ke zpracování, počet učení zásady pro porovnání, a určuje, zda byl vyžádán optimalizace.

Po dokončení se zobrazí následující výsledky:

1. Porovnání zásad učení, včetně:
    * **Zásady pro online**: Aktuální zásady Learning používané Personalizer
    * **Směrný plán**: Výchozí aplikace (počítáno od první akci odesílají v pořadí volání)
    * **Náhodné zásad**: Imaginární řadit chování, která vždy vrátí náhodné volby Akce z nabízených.
    * **Vlastní zásady**: Další zásady Learning odeslat při spouštění hodnocení.
    * **Zásada, jehož**: Pokud vyhodnocení byl spuštěn s možností zjišťování optimalizované zásad, budou také porovnání a bude možné si ho stáhnout nebo ji zásady online výukové kurzy nahrazení stávající.

1. Efektivitu [funkce](concepts-features.md) pro akce a kontextu.


## <a name="more-information"></a>Další informace

* Přečtěte si [jak offline hodnocení fungovat](concepts-offline-evaluation.md).