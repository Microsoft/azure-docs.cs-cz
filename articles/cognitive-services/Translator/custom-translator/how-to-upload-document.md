---
title: Jak nahrát dokument – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Funkce nahrání dokumentu nahrává paralelní dokumenty (dva dokumenty, kde jeden je počátek a druhý je překlad) do služby.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 8597b3e4ffab44672e88374010fa829211fbc18b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507299"
---
# <a name="upload-a-document"></a>Nahrání dokumentu

Ve [vlastním překladateli](https://portal.customtranslator.azure.ai)můžete nahrávat paralelní dokumenty pro výuku vašich modelů překladu. [Paralelní dokumenty](what-are-parallel-documents.md) jsou páry dokumentů, kde jedna je překlad druhé. Jeden dokument ve dvojici obsahuje ve zdrojovém jazyce věty a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.

Před nahráním dokumentů si přečtěte téma [formáty dokumentů a pokyny k konvenci pojmenování a](document-formats-naming-convention.md) Ujistěte se, že je ve vašem vlastním překladateli podporován formát souboru.

## <a name="how-to-upload-document"></a>Jak nahrát dokument?

V portálu pro [vlastní překladatele](https://portal.customtranslator.azure.ai) klikněte na kartu dokumenty a přejděte na stránku dokumenty.

![Odkaz na nahrání dokumentu](media/how-to/how-to-upload-1.png)


1.  Klikněte na tlačítko nahrát soubory na stránce dokumenty.

    ![Nahrát stránku dokumentu](media/how-to/how-to-upload-2.png)

2.  V dialogovém okně zadejte následující informace:

    a.  Typ dokumentu:

    -  Školení: tyto dokumenty se budou používat pro školicí sadu.
    -  Ladění: tyto dokumenty budou použity pro optimalizaci sady.
    -  Testování: tyto dokumenty budou použity pro testování sady.
    -  Slovník frází: tyto dokumenty se použijí pro slovník frází.
    -  Slovník vět: tyto dokumenty se použijí pro slovník vět.

    b.  Dvojice jazyků

    c.  Přepsat dokument, pokud existuje: zaškrtněte toto políčko, pokud chcete přepsat všechny existující dokumenty se stejným názvem.

    d.  Vyplňte příslušné části pro paralelní data nebo kombinovaná data.

    -  Paralelní data:
        -  Zdrojový soubor: ze svého místního počítače vyberte zdrojový soubor jazyka.
        -  Cílový soubor: Vyberte cílový jazykový soubor z místního počítače.
        -  Název dokumentu: používá se pouze v případě, že odesíláte paralelní soubory.

    - Data kombinovaného:
        -  Soubor se seznamem: ze svého místního počítače vyberte soubor se seznamem. Soubor se seznamem obsahuje oba věty zdrojového i cílového jazyka. [Zásady vytváření názvů](document-formats-naming-convention.md) jsou důležité pro soubory se seznamem.

    e.  Klikněte na nahrát.

    ![Nahrát dialog dokumentu](media/how-to/how-to-upload-dialog.png)

3.  V tomto okamžiku zpracováváme vaše dokumenty a zkusíme extrahovat věty. Kliknutím na Zobrazit průběh nahrávání můžete sledovat stav svých dokumentů při jejich zpracování.

    ![Odeslat dialog pro zpracování dokumentů](media/how-to/how-to-upload-processing-dialog.png)

4.  Na této stránce se zobrazí stav a všechny chyby pro každý soubor v rámci nahrávání. Stav nahrávání po odeslání můžete kdykoli zobrazit kliknutím na kartu odeslat historii.

    ![Dialogové okno nahrát historii dokumentu](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Zobrazit historii nahrávání

Na stránce Historie nahrávání můžete zobrazit historii všech odesílání dokumentů, jako je typ dokumentu, dvojice jazyků, stav nahrávání atd.

1. Na portálu [vlastního překladatele](https://portal.customtranslator.azure.ai) klikněte na nahrát kartu Historie a zobrazte historii.

    ![Nahrát kartu Historie](media/how-to/how-to-upload-history-1.png)

2. Tato stránka zobrazuje stav všech vašich minulých nahrávání. Zobrazuje nahrávání z nejaktuálnějšího a nejméně nedávného. U každého nahrávání se zobrazuje název dokumentu, stav odeslání, datum odeslání, počet odeslaných souborů, typ odeslaných souborů a dvojici jazyků souboru.

    ![Odeslat stránku historie](media/how-to/how-to-document-history-2.png)

3. Klikněte na libovolný záznam historie nahrávání. Na stránce Podrobnosti o historii odeslání můžete zobrazit soubory odeslané v rámci nahrávání, odeslat stav souboru, jazyk souboru a chybové zprávy (Pokud se v nahrávání vyskytne nějaká chyba).

## <a name="next-steps"></a>Další kroky

- Na [stránce s podrobnostmi o dokumentu](how-to-view-document-details.md) si můžete prohlédnout seznam extrahovaných vět.
- [Postup výuky modelu](how-to-train-model.md).
