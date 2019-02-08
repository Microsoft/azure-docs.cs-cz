---
title: Vlastní výslovnost pomocí služby Custom Speech Service v Azure | Dokumentace Microsoftu
description: Naučíte se vytvářet jazykový model pomocí služby Custom Speech Service ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: nitinme
ms.openlocfilehash: 4387307a516b3ebb39b777b3a1fd32e7608c4a82
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867762"
---
# <a name="enable-custom-pronunciation"></a>Povolit vlastní výslovnost

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Vlastní výslovnost umožňuje uživatelům definovat zapsané ve fonetické formuláře a zobrazení slovo nebo termín. Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete je soubor výslovnosti (soubor simple .txt).

Zde je, jak to funguje. V souboru .txt jeden můžete zadat několik položek vlastní výslovnost. Struktura je následujícím způsobem:

```
Display form <Tab> Spoken form <Newline>
```

*Příklady*:

| Formulář pro zobrazení | Mluvené slovo formuláře |
|----------|-------|
| C3PO | zobrazit tři pea o |
| L8R | pozdní jsou |
| CNTK | Zobrazit n čaje tis.|

## <a name="requirements-for-the-spoken-form"></a>Požadavky na mluvené slovo formuláře
Mluvené slovo formuláře musí obsahovat malá písmena, která se dá vynutit během importu. Kromě toho je nutné zadat programu pro import dat se změnami. Smí obsahovat žádné karty mluvené slovo formulář nebo formulář pro zobrazení. Existuje může, ale, zakázáno více znaků ve formuláři zobrazení chcete (například ~ a ^).

Každý soubor .txt může mít několik položek. Viz například na následujícím snímku obrazovky:

![Snímek obrazovky Poznámkový blok s několika položkami pro výslovnost zkratka](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

Mluvené slovo formuláře je zapsané ve fonetické posloupnost formulář pro zobrazení. Skládá se z písmen, slova nebo slabik. V současné době neexistuje žádný další doprovodné materiály nebo sada standardů vám usnadní mluvené slovo formuláře. 

## <a name="supported-pronunciation-characters"></a>Podporované výslovnost znaků
Vlastní Výslovnost je aktuálně podporované pro angličtina (en US) a němčina (de-de). Znaková sada, která je možné vyjádřit mluvené formy termín (v souboru vlastní výslovnost) můžete vidět v následující tabulce: 

| Jazyk | Postavy |
|---------- |----------|
| Angličtina (en US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Němčina (de-de) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

>[POZNÁMKA] Formulář pro zobrazení na dobu (v souboru výslovnost) by měly být napsány stejným způsobem jako v jazyce přizpůsobení datové sady.

## <a name="requirements-for-the-display-form"></a>Požadavky pro formulář pro zobrazení
Formulář pro zobrazení může být pouze vlastní slovo, výraz, zkratka nebo složených slov, které kombinují existující slova. Můžete také zadat alternativní výslovnosti pro běžná slova. 

>[!NOTE]
Nedoporučujeme použití této funkce, aby byla znovu formulována běžná slova nebo pro úpravu mluvené slovo formuláře. Je lepší pro spuštění dekodér zobrazíte, pokud nejsou korektně dekódováno některé neobvyklé slova (jako je například zkratky, technické slova a cizí slova). Pokud ano, můžete je přidat do vlastní výslovnost souboru. V jazykový Model pouze a vždy používejte formulář pro zobrazení slova. 

## <a name="requirements-for-the-file-size"></a>Požadavky na velikost souboru
Velikost souboru TXT, který obsahuje položky Výslovnost je omezena na 1 MB. Obvykle není potřeba nahrávání velkých objemů dat prostřednictvím tohoto souboru. Většina souborů vlastní výslovnost můžou mít jenom pár znalostní báze velikosti. Kódování souboru .txt pro všechna národní prostředí by měl být BOM kódování UTF-8. Pro anglické národní prostředí je také ANSI přijatelné.

## <a name="next-steps"></a>Další postup
* Zlepšit přesnost rozpoznávání vytvořením vaše [vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md).
* [Vytvoření vlastního koncového bodu převodu řeči na text](cognitive-services-custom-speech-create-endpoint.md), který můžete použít z aplikace.
