---
title: Jak vytvořit projekt - Vlastní Překladač
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak vytvořit a spravovat projekt v Azure Cognitive Services Vlastní překladač.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5b3a6f2e903d8178554b7a076ae1277633569353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836569"
---
# <a name="create-a-project"></a>Vytvoření projektu

Projekt je kontejner pro modely, dokumenty a testy. Každý projekt automaticky zahrnuje všechny dokumenty, které jsou odeslány do tohoto pracovního prostoru, které mají správný pár jazyků.

Vytvoření projektu je prvním krokem k vytvoření modelu.

## <a name="create-a-project"></a>Vytvoření projektu:

1.  Na portálu [Vlastní překladač](https://portal.customtranslator.azure.ai) klikněte na Vytvořit projekt.

    ![Vytvoření projektu](media/how-to/how-to-create-project.png)

2.  V dialogovém okně zadejte následující podrobnosti o projektu:

    a.  Název projektu (povinné): Pojmenujte projekt jedinečným a smysluplným názvem. Není nutné zmiňovat jazyky v názvu.

    b.  Popis: Krátké shrnutí projektu. Tento popis nemá žádný vliv na chování vlastního překladače nebo výsledného vlastního systému, ale může vám pomoci rozlišovat mezi různými projekty.

    c.  Dvojice jazyků (povinné): Vyberte jazyk, ze kterého překládáte a do kterého překládáte.

    d.  Kategorie (povinné): Vyberte kategorii, která je pro váš projekt nejvhodnější. Kategorie popisuje terminologii a styl dokumentů, které chcete přeložit.

    e.  Popis kategorie: Toto pole slouží k lepšímu popisu konkrétního oboru nebo odvětví, ve kterém pracujete. Pokud je například vaší kategorií medicína, můžete přidat určitý dokument, takovou operaci nebo pediatrii. Popis nemá žádný vliv na chování vlastního překladače nebo výsledného vlastního systému.

    f.  Popisek projektu: [Popisek projektu](workspace-and-project.md#project-labels) rozlišuje mezi projekty se stejným jazykovým párem a kategorií. Jako osvědčený postup použijte popisek *pouze* v případě, že plánujete vytvořit více projektů pro stejnou dvojici jazyků a stejnou kategorii a chcete získat přístup k těmto projektům s jiným CategoryID. Toto pole nepoužívejte, pokud vytváříte systémy pouze pro jednu kategorii. Popisek projektu není vyžadován a není užitečný pro rozlišení mezi páry jazyků. Stejný popisek můžete použít pro více projektů.

    ![Dialogové okno Vytvořit projekt](media/how-to/how-to-create-project-dialog.png)

3.  Kliknutí na Vytvořit

## <a name="view-project-details"></a>Zobrazit podrobnosti projektu

Vstupní stránka vlastního překladače zobrazuje prvních 10 projektů ve vašem pracovním prostoru. Zobrazí název projektu, dvojici jazyků, kategorii, stav a skóre BLEU.

Po výběru projektu se na stránce projektu zobrazí následující:

- CategoryID: A CategoryID je vytvořen zřetězením ID pracovního prostoru, popisek projektu a kód kategorie. Pomocí CategoryID s rozhraním API překladače textu získat vlastní překlady.

- Tlačítko Vlak: Pomocí tohoto tlačítka můžete zahájit [trénování modelu](how-to-train-model.md).

- Tlačítko Přidat dokumenty: Toto tlačítko slouží k [nahrávání dokumentů](how-to-upload-document.md).

- Tlačítko Filtrovat dokumenty: Pomocí tohoto tlačítka můžete filtrovat a vyhledávat konkrétní dokumenty.

    ![Zobrazit podrobnosti projektu](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Další kroky

- Naučte [se vyhledávat, upravovat, odstraňovat projekt](how-to-search-edit-delete-projects.md).
- Přečtěte [si, jak nahrát dokument](how-to-upload-document.md) k vytvoření modelů překladů.
