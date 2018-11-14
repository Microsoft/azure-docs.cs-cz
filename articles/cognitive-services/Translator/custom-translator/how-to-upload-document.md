---
title: Postup nahrání dokumentu – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Pomocí funkce nahrání dokumentu můžete nahrát paralelní dokument pro vaši školení. Paralelní dokumenty jsou páry dokumentů, pokud je překlad druhé. Jeden dokument v páru obsahuje vět Zdrojový jazyk a druhý dokument obsahuje tyto věty přeloženy do cílového jazyka.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: bb84a3ddfba54c47cdf91aa72889a960722cdbd3
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627299"
---
# <a name="upload-a-document"></a>Nahrání dokumentu

V [vlastní Translator](https://portal.customtranslator.azure.ai), nahrajete dokumenty paralelní trénování modelů překladu. [Paralelní dokumenty](what-are-parallel-documents.md) jsou páry dokumentů, pokud je překlad druhé. Jeden dokument v páru obsahuje vět Zdrojový jazyk a druhý dokument obsahuje tyto věty přeloženy do cílového jazyka.

## <a name="how-to-upload-document"></a>Jak nahrát dokument?

Z [vlastní Translator](https://portal.customtranslator.azure.ai) portálu, klikněte na kartu "Dokumenty" přejděte na stránku dokumentů.

![Odkaz na dokument nahrávání](media/how-to/how-to-upload-1.png)


1.  Klikněte na tlačítko Nahrát soubory na stránce Dokumenty.

    ![Nahrát dokument stránky](media/how-to/how-to-upload-2.png)

2.  V dialogovém okně zadejte následující informace:

    a.  Typ dokumentu:

    -  Školení: Tyto dokumenty se použije pro trénovací sady.
    -  Ladění: Tyto dokumenty se použije pro ladění sady.
    -  Testování: Tyto dokumenty se použije pro testovací sadu.
    -  Fráze slovníku: Tyto dokumenty se použije pro fráze slovníku.
    -  Slovník větu: Tyto dokumenty se použije pro slovník větu

    b.  Pár jazyka

    c.  Přepsat dokument, pokud existuje: Toto políčko zaškrtněte, pokud ho chcete přepsat všechny existující dokumenty se stejným názvem.

    d.  Zadejte příslušné části paralelních dat nebo dat – pole se seznamem.

    -  Paralelních dat:
        -  Zdrojový soubor: Zdrojový jazyk vyberte soubor ze svého místního počítače.
        -  Cílový soubor: Vyberte cílový jazyk souboru ze svého místního počítače.
        -  Název dokumentu: použít pouze v případě, že se nahrávání paralelní souborů.

    - Pole se seznamem data:
        -  Soubor se seznamem: Vyberte soubor se seznamem ze svého místního počítače. Váš soubor se seznamem obsahuje zdrojový i cílový jazyk věty. [Zásady vytváření názvů](document-formats-naming-convention.md) je důležité pro soubory se seznamem.

    e.  Klikněte na tlačítko Odeslat

    ![Dialogové okno dokumentu nahrát](media/how-to/how-to-upload-dialog.png)

3.  V tuto chvíli jsme zpracování dokumentů a pokusu o extrahování vět. Klikněte na tlačítko "Zobrazit průběh nahrávání" Kontrola stavu dokumentů, jak se budou zpracovávat.

    ![Dialogové okno dokumentu zpracování nahrát](media/how-to/how-to-upload-processing-dialog.png)

4.  Na této stránce se zobrazí stav a všechny chyby pro každý soubor v rámci nahrávání. Můžete zobrazit poslední stav nahrávání kdykoli po kliknutí na kartu "Odeslat historii".

    ![Dialogové okno dokumentu historie nahrát](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Zobrazit historii nahrávání

V odesílání stránky s historií můžete zobrazit historii všech dokumentů nahrává podrobnosti, jako jsou typu dokumentu, dvojici jazyků, nahrát stav atd.

1. Z [vlastní Translator](https://portal.customtranslator.azure.ai) portálu, klikněte na kartu Historie nahrát zobrazení historie.

    ![Nahrát kartu Historie](media/how-to/how-to-upload-history-1.png)

2. Tato stránka zobrazuje stav všech minulých nahrávání. Zobrazí se nahrávání z nejnovějších nejstarší. Pro každý nahraný zobrazuje název dokumentu, stav nahrávání, nahrát data, počet souborů, typu nahraný soubor a pár jazyk souboru.

    ![Uložit stránky s historií](media/how-to/how-to-document-history-2.png)

3. Klikněte na libovolný záznam historie nahrávání. Na stránce Podrobnosti historie odesílání můžete zobrazit soubory nahrané jako součást nahrávání, nahrané stav souboru, jazyk souboru a chybové zprávy (Pokud je nahrání všechny chyby).

## <a name="next-steps"></a>Další postup

- Použití [stránce s podrobnostmi o dokument](how-to-view-document-details.md) zkontrolovat seznam extrahovaných věty.
- [Jak pro trénování modelu](how-to-train-model.md).
