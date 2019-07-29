---
title: Jak nahrát dokument – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Pomocí funkce nahrávání dokumentu můžete pro vaše školení nahrát paralelní dokument. Paralelní dokumenty jsou páry dokumentů, kde jeden je převod druhé. Jeden dokument ve dvojici obsahuje ve zdrojovém jazyce věty a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f2bd6103c27d455265ee967554fb27513f78a472
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595644"
---
# <a name="upload-a-document"></a>Nahrání dokumentu

Ve [vlastním překladateli](https://portal.customtranslator.azure.ai)můžete nahrávat paralelní dokumenty pro výuku vašich modelů překladu. [Paralelní dokumenty](what-are-parallel-documents.md) jsou páry dokumentů, kde jedna je překlad druhé. Jeden dokument ve dvojici obsahuje ve zdrojovém jazyce věty a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.

Před nahráním dokumentů si přečtěte téma [formáty dokumentů a pokyny](document-formats-naming-convention.md) k konvenci pojmenování a ujistěte se, že je ve vašem vlastním překladateli podporován formát souboru.

## <a name="how-to-upload-document"></a>Jak nahrát dokument?

V portálu pro [vlastní překladatele](https://portal.customtranslator.azure.ai) klikněte na kartu dokumenty a přejděte na stránku dokumenty.

![Odkaz na nahrání dokumentu](media/how-to/how-to-upload-1.png)


1.  Klikněte na tlačítko nahrát soubory na stránce dokumenty.

    ![Nahrát stránku dokumentu](media/how-to/how-to-upload-2.png)

2.  V dialogovém okně zadejte následující informace:

    a.  Typ dokumentu:

    -  Trénování: Tyto dokumenty se budou používat pro školicí sadu.
    -  Ladění Tyto dokumenty se budou používat pro optimalizaci sady.
    -  Zkouší Tyto dokumenty budou použity pro testování sady.
    -  Slovník frází: Tyto dokumenty budou použity pro slovník frází.
    -  Slovník vět: Tyto dokumenty se použijí pro slovník vět.

    b.  Dvojice jazyků

    c.  Přepsat dokument, pokud existuje: Zaškrtněte toto políčko, pokud chcete přepsat všechny existující dokumenty se stejným názvem.

    d.  Vyplňte příslušné části pro paralelní data nebo kombinovaná data.

    -  Paralelní data:
        -  Zdrojový soubor: Vyberte zdrojový soubor jazyka z místního počítače.
        -  Cílový soubor: Z místního počítače vyberte cílový jazykový soubor.
        -  Název dokumentu: Používá se pouze v případě, že odesíláte paralelní soubory.

    - Data kombinovaného:
        -  Soubor se seznamem: Vyberte soubor se seznamem ze svého místního počítače. Soubor se seznamem obsahuje oba věty zdrojového i cílového jazyka. [Zásady vytváření názvů](document-formats-naming-convention.md) jsou důležité pro soubory se seznamem.

    e.  Klikněte na tlačítko Odeslat

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

## <a name="next-steps"></a>Další postup

- Na [stránce s podrobnostmi o dokumentu](how-to-view-document-details.md) si můžete prohlédnout seznam extrahovaných vět.
- [Postup výuky modelu](how-to-train-model.md).
