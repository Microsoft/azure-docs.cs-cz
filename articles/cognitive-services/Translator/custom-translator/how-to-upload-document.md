---
title: Jak nahrát dokument - Vlastní Překladač
titleSuffix: Azure Cognitive Services
description: Funkce nahrávání dokumentů nahrává paralelní dokumenty (dva dokumenty, kde jeden je původ a druhý je překlad) do služby.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: b2a249a40d8c782d54a12df43d33655f3409753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647385"
---
# <a name="upload-a-document"></a>Nahrání dokumentu

V [aplikaci Custom Translator](https://portal.customtranslator.azure.ai)můžete nahrát paralelní dokumenty a trénovat modely překladů. [Paralelní dokumenty](what-are-parallel-documents.md) jsou dvojice dokumentů, kde jeden je překlad druhého. Jeden dokument v páru obsahuje věty ve zdrojovém jazyce a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.

Před nahráním dokumentů zkontrolujte [formáty dokumentů a pokyny k vytváření konvencí,](document-formats-naming-convention.md) abyste se ujistili, že je formát souboru podporován v nástroji Vlastní překladač.

## <a name="how-to-upload-document"></a>Jak nahrát dokument?

Na portálu [Vlastní překladač](https://portal.customtranslator.azure.ai) klikněte na záložku Dokumenty a přejděte na stránku Dokumenty.

![Odkaz na nahrání dokumentu](media/how-to/how-to-upload-1.png)


1.  Klikněte na tlačítko Nahrát soubory na stránce dokumenty.

    ![Nahrát stránku dokumentu](media/how-to/how-to-upload-2.png)

2.  V dialogu vyplňte následující informace:

    a.  Typ dokumentu:

    -  Školení: Tyto dokumenty budou použity pro tréninkovou sadu.
    -  Ladění: Tyto dokumenty budou použity pro ladění sady.
    -  Testování: Tyto dokumenty budou použity pro testovací sadu.
    -  Frázový slovník: Tyto dokumenty budou použity pro slovník frází.
    -  Větný slovník: Tyto dokumenty budou použity pro slovník vět

    b.  Jazykový pár

    c.  Přepsat dokument, pokud existuje: Toto políčko zaškrtněte, pokud chcete přepsat existující dokumenty se stejným názvem.

    d.  Vyplňte příslušnou část pro paralelní data nebo kombinovaná data.

    -  Paralelní data:
        -  Zdrojový soubor: Vyberte zdrojový soubor jazyka z místního počítače.
        -  Cílový soubor: Vyberte cílový soubor jazyka z místního počítače.
        -  Název dokumentu: Používá se pouze v případě, že nahráváte paralelní soubory.

    - Combo data:
        -  Combo Soubor: Vyberte soubor combo z místního počítače. Kombo soubor obsahuje jak zdrojové, tak cílové jazykové věty. [Konvence pojmenování](document-formats-naming-convention.md) je důležité pro kombinované soubory.

    e.  Klikněte na Nahrát

    ![Dialogové okno Odeslat dokument](media/how-to/how-to-upload-dialog.png)

3.  V tuto chvíli zpracováváme vaše dokumenty a pokoušíme se extrahovat věty. Kliknutím na tlačítko "Zobrazit průběh nahrávání" můžete zkontrolovat stav dokumentů při jejich zpracování.

    ![Dialogové okno Nahrát zpracování dokumentu](media/how-to/how-to-upload-processing-dialog.png)

4.  Na této stránce se zobrazí stav a všechny chyby pro každý soubor v rámci nahrávání. Stav nahrávání v minulosti můžete kdykoli zobrazit kliknutím na kartu "Nahrát historii".

    ![Dialogové okno Historie dokumentů](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Zobrazit historii nahrávání

Na stránce historie nahrávání můžete zobrazit historii všech detailů dokumentu, jako je typ dokumentu, pár jazyků, stav nahrávání atd.

1. Na portálu [Vlastní překladač](https://portal.customtranslator.azure.ai) klikněte na kartu Nahrát historii a zobrazte historii.

    ![Karta Historie nahrávání](media/how-to/how-to-upload-history-1.png)

2. Na této stránce je uveden stav všech vašich minulých nahrávek. Zobrazuje nahrávání od nejnovějších po nejméně poslední. U každého nahrání se zobrazí název dokumentu, stav nahrání, datum nahrání, počet nahraných souborů, typ nahraného souboru a jazykový pár souboru.

    ![Nahrát stránku historie](media/how-to/how-to-document-history-2.png)

3. Klikněte na libovolný záznam historie nahrávání. Na stránce podrobností o historii nahrávání můžete zobrazit soubory nahrané jako součást nahrávání, nahraný stav souboru, jazyk souboru a chybovou zprávu (pokud dojde k chybě při nahrávání).

## <a name="next-steps"></a>Další kroky

- Na [stránce podrobností dokumentu](how-to-view-document-details.md) můžete zkontrolovat seznam extrahovaných vět.
- [Jak trénovat model](how-to-train-model.md).
