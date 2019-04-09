---
title: Přizpůsobení výslovnost – hlasové služby
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak přizpůsobit výslovnost službou Speech. Pomocí vlastních výslovnost můžete definovat zapsané ve fonetické formuláře a zobrazení slovo nebo termín. Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít se výslovnost soubor – soubor .txt jednoduché.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: f825cf8f381a7a2974b150a74a091412b24b09bc
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005161"
---
# <a name="enable-custom-pronunciation"></a>Povolit vlastní výslovnost

S použitím vlastní výslovnost, můžete definovat zapsané ve fonetické formuláře a zobrazení slovo nebo termín (zkratka). Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít se výslovnost soubor – soubor .txt jednoduché.

Zde je, jak to funguje. V souboru .txt jeden můžete zadat několik položek vlastní výslovnost. Struktura je následujícím způsobem:

```
Display form <Tab> Spoken form <Newline>
```

V následující tabulce jsou uvedeny několik příkladů:

| Formulář pro zobrazení | Mluvené slovo formuláře |
|----------|-------|
| 3CPO | zobrazit tři pea o |
| L8R | pozdní jsou |
| CNTK | k c n t|

## <a name="requirements-for-the-spoken-form"></a>Požadavky na mluvené slovo formuláře

Mluvené slovo formuláře musí obsahovat malá písmena, která můžete vynutit během importu. Musíte také zajistit kontroly v programu pro import dat. Smí obsahovat žádné karty mluvené slovo formulář nebo formulář pro zobrazení. Ale existuje může být více je zakázané znaky ve formuláři zobrazení (například ~ a ^).

Každý soubor .txt může mít několik položek, jak je znázorněno na následujícím obrázku:

![Příklady výslovnost zkratka](media/stt/custom-speech-pronunciation-file.png)

Mluvené slovo formuláře je zapsané ve fonetické posloupnost formulář pro zobrazení. Skládá se z písmen, slova nebo slabik. V současné době neexistuje žádný další doprovodné materiály nebo sada standardů vám usnadní mluvené slovo formuláře.

## <a name="supported-pronunciation-characters"></a>Podporované výslovnost znaků

Vlastní Výslovnost je aktuálně podporované pro angličtina (en US) a němčina (de-de). V následující tabulce jsou uvedeny znakových sad, které vám umožní express mluvené formy termín (v souboru vlastní výslovnost):

| Jazyk | Postavy |
|---------- |----------|
| Angličtina (en US) | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Němčina (de-de) | ä, ö, ü, ?, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> Formulář pro zobrazení na dobu (v souboru výslovnost) by měly být napsány stejným způsobem jako v datové sadě přizpůsobení jazyka.

## <a name="requirements-for-the-display-form"></a>Požadavky pro formulář pro zobrazení

Formulář pro zobrazení může být pouze vlastní slova, zkratka nebo složených slov, které kombinují existující slova.

>[!NOTE]
>Nedoporučujeme používat tuto funkci, aby byla znovu formulována běžná slova nebo pro úpravu mluvené slovo formuláře. Je lepší kontrolu, jestli některé neobvyklé slova (jako je například zkratky, technické slova nebo cizí slova) jsou nesprávně transribed před použitím této funkce. Pokud ano, můžete je přidat do vlastní výslovnost souboru. V jazykový model pouze a vždy používejte formulář pro zobrazení slova.

## <a name="requirements-for-the-file-size"></a>Požadavky na velikost souboru
Velikost souboru TXT, který obsahuje položky Výslovnost je omezena na 1 MB (1KB pro úroveň free klíče). Obvykle není nutné nahrávání velkých objemů dat prostřednictvím tohoto souboru. Většina souborů vlastní výslovnost můžou mít velikost několika kilobajtů (kB). Kódování souboru .txt pro všechna národní prostředí by měl být BOM kódování UTF-8. Pro anglické národní prostředí je také ANSI přijatelné.

## <a name="next-steps"></a>Další postup
* Zlepšit přesnost rozpoznávání tak, že vytvoříte [vlastního akustického modelu](how-to-customize-acoustic-models.md).
* Zlepšit přesnost rozpoznávání tak, že vytvoříte [vlastního jazykového modelu](how-to-customize-language-model.md).
