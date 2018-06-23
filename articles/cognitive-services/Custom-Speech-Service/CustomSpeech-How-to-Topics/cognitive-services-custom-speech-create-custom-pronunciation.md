---
title: Použít vlastní výslovnosti službou vlastní řeči v Azure | Microsoft Docs
description: Naučte se vytvářet jazykový model se službou vlastní řeči v kognitivní služby.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: a74b69b84cc80809a25f18b580a18abb5721b8b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342465"
---
# <a name="enable-custom-pronunciation"></a>Povolit vlastní výslovnosti
Vlastní výslovnosti umožňuje uživatelům definovat výslovnosti formulář a zobrazení slovo nebo termín. Je vhodné pro zpracování vlastní podmínky, například názvy produktů nebo zkratky. Je třeba je výslovnosti soubor (soubor simple .txt).

Zde je, jak to funguje. V souboru .txt jeden můžete zadat několik vlastní výslovnosti položky. Struktura je následující:

```
Display form <Tab> Spoken form <Newline>
```

*Příklady*:

| Formulář pro zobrazení | Mluvené formuláře |
|----------|-------|
| C3PO | v tématu o tři pea |
| L8R | rozpoznání s pozdní jsou |
| CNTK | v tématu n čaj tisíc|

## <a name="requirements-for-the-spoken-form"></a>Požadavky pro mluvené formulář
Mluvené formuláře musí být malými písmeny, který se dá vynutit během importu. Kromě toho je nutné zadat kontroly – Importér data. Žádné karty mluvené formuláře nebo zobrazení formuláře je povolená. Existuje může, ale, být zakázáno více znaků ve formě zobrazení (například ~ a ^).

Každý soubor .txt může mít několik položek. Například najdete na následujícím snímku obrazovky:

![Snímek obrazovky Poznámkový blok s několik záznamů pro výslovnost zkratce](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

Mluvené formuláře je výslovnosti pořadí zobrazení formuláře. Skládá se z písmen, slova nebo slabiky. V současné době neexistuje žádné další pokyny nebo sadu standardů, která vám usnadní mluvené formuláře. 

## <a name="supported-pronunciation-characters"></a>Podporované výslovnosti znaků
Vlastní výslovnosti se aktuálně podporuje pro angličtinu (en US) a němčina (de-de). V následující tabulce je zobrazena znaková sada, která slouží k express mluvené formy termín (v souboru vlastní výslovnosti): 

| Jazyk | Postavy |
|---------- |----------|
| Angličtina (en US) | a, b, c, d, e, f, g, h, i, j, tisíc, l, e, p, otázek, r, s, t, u, v, w, x, y, z |
| Němčina (de-de) | ä, cos, ř, ẞ, a a b, c, d, e, f, g, h, i, j, tisíc, l, e, p, otázek, r, s, t, u, v, w, x, y, z |

>[POZNÁMKA] Formulář pro zobrazení na dobu (v souboru výslovnosti) by měly být zapsány stejným způsobem jako v jazyce přizpůsobení datové sady.

## <a name="requirements-for-the-display-form"></a>Požadavky pro zobrazení formuláře
Zobrazení formuláře lze pouze vlastní aplikace word, termín, zkratku nebo složených slov, které spojují existující slova. Můžete také zadat alternativní výslovnosti pro běžná slova. 

>[!NOTE]
Nedoporučujeme použití této funkce, aby byla znovu formulována běžná slova nebo k úpravě mluvené formuláře. Je lepší spustit dekodér chcete zobrazit, pokud nejsou správně dekódovat některé neobvyklou slova (například zkratky, technické slova a cizí slova). Pokud ano, přidat je do souboru vlastní výslovnosti. V modelu jazyk pouze a vždy používejte zobrazení formuláře slova. 

## <a name="requirements-for-the-file-size"></a>Požadavky na velikost souboru
Velikost souboru .txt, který obsahuje položky výslovnosti je omezena na 1 MB. Obvykle není potřeba nahrát velké objemy dat prostřednictvím tohoto souboru. Většina souborů vlastní výslovnosti by mohly být několika články znalostní báze velikost. Kódování souboru .txt pro všechna národní prostředí by měl být BOM UTF-8. Anglickou národním prostředí je také ANSI přijatelné.

## <a name="next-steps"></a>Další postup
* Zlepšit přesnost rozpoznávání vytvořením vaší [vlastní akustickými modelu](cognitive-services-custom-speech-create-acoustic-model.md).
* [Vytvořte vlastní koncový bod řeči na text](cognitive-services-custom-speech-create-endpoint.md), který můžete použít z aplikace.
