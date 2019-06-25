---
title: Vyzkoušejte přesnost pro Custom Speech – hlasové služby
titlesuffix: Azure Cognitive Services
description: V tomto dokumentu zjistíte jak kvantitativně jsou měřítkem kvality modelu řeči na text od Microsoftu nebo vašeho vlastního modelu. K testování přesnost je potřeba dat přepisu zvuku + lidských s názvem bez přípony a musí být zadána 30 minut až 5 hodin reprezentativní zvuku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025906"
---
# <a name="evaluate-custom-speech-accuracy"></a>Vyzkoušejte přesnost vlastní řeči

V tomto dokumentu se dozvíte, jak kvantitativně jsou měřítkem kvality modelu řeči na text od Microsoftu nebo vašeho vlastního modelu. K testování přesnost je potřeba dat přepisu zvuku + lidských s názvem bez přípony a musí být zadána 30 minut až 5 hodin reprezentativní zvuku.

## <a name="what-is-word-error-rate-wer"></a>Co je míra chyb aplikace Word (zasílání)?

Standardní měření přesnost modelu je *míra chyb slovo* (zasílání). Zasílání spočítá nesprávná slova identifikovaný při rozpoznávání a potom provede podíl celkového počtu slov v přepisu lidských s názvem bez přípony. A konečně toto číslo vynásobené 100 % k výpočtu zasílání.

![Vzorec zasílání](./media/custom-speech/custom-speech-wer-formula.png)

Nesprávně identifikován slova spadají do tří kategorií:

* Vložení (I): Slova, která se nesprávně přidají hypotézu přepisu
* Odstranění (D): Slova, která nezjištěné hypotézu přepisu
* Nahrazení (S): Slova, které byly nahrazeny mezi referenční a hypotézu

Tady je příklad:

![Příklad nesprávně identifikované slov](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Řešení chyb a vylepšení zasílání

Zasílání ve výsledcích rozpoznávání počítače můžete použít k vyhodnocení kvality modelu, který používáte s vaší aplikace, nástroj nebo produktu. Zasílání o 5-10 % se považuje za kvalitní a je připravený k použití. Zasílání 20 % je přijatelná, ale můžete chtít zvážit další školicí. Zasílání 30 % nebo víc signály kvalita a vyžaduje přizpůsobení a školení.

Jak se distribuují chyby je důležité. Pokud nedojde k mnoha chyb při odstraňování, je obvykle z důvodu slabé zvukový signál. Chcete-li vyřešit tento problém, musíte shromažďovat zvukových dat blíže ke zdroji. Vkládání chyb znamenat, že zvuk se zaznamenávají do prostředí hlučného a nežádoucím může být k dispozici, což způsobuje problémy, rozpoznávání. Substituce jsou často došlo k chybám při nedostatečné ukázka podmínek specifického pro doménu se zadat buď jako přepisů lidských s názvem bez přípony nebo související s text.

Díky analýze jednotlivé soubory, můžete určit, jaký typ chyby existují, a které chyby jsou jedinečné pro konkrétní soubor. Principy problémů na úrovni souborů můžete cílit na vylepšení.

## <a name="create-a-test"></a>Vytvoření testu

Pokud chcete pro otestování kvality modelu směrného plánu převodu řeči na text od Microsoftu nebo vlastní model, který jsme natrénovali, můžete porovnání dvou modelů vedle sebe, k vyhodnocení přesnost. Porovnání obsahuje výsledky zasílání a uznání. Obvykle vlastního modelu se porovná se základní model od Microsoftu.

O vyhodnocení modelů vedle sebe:

1. Přejděte do **Speech to text > Custom Speech > testování**.
2. Klikněte na tlačítko **přidat Test**.
3. Vyberte **vyhodnotit přesnost**. Zadejte název, popis, testu a vyberte vaše datová sada přepisu zvuku + lidských s názvem bez přípony.
4. Vyberte až dva modely, které chcete testovat.
5. Klikněte na možnost **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat výsledky vedle sebe.

## <a name="side-by-side-comparison"></a>Porovnání vedle sebe

Po dokončení testu indikován stav změny *Succeeded*, zjistíte číslo zasílání pro oba modely součástí vašeho testu. Klikněte na název testu, chcete-li zobrazit testovací stránce s podrobnostmi. Tato stránka podrobností uvádí všechny projevy ve vaší datové sadě, určující rozpoznávání výsledky ze dvou modelů vedle přepis z odeslané datové sady. Chcete-li zkontrolovat porovnání vedle sebe, můžete přepínat různých typů chyb včetně vkládání, odstraňování a nahrazení. Poslech Audio nahrávky patřící a porovnáním rozpoznávání výsledky v každém sloupci, který ukazuje přepis lidských s názvem bez přípony a výsledky pro dva modely řeči na text, můžete rozhodnout, které model bude vyhovovat vašim potřebám a kde jsou další školicí a vylepšení povinné.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte svůj model](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další materiály

* [Připravit a otestovat vašich dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
