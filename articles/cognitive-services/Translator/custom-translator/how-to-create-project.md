---
title: Jak vytvořit projekt? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Postup vytvoření projektu v Translator vlastní?
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 9886ba39846d9489a66c57bcc614f90e2bf70d4e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218100"
---
# <a name="create-a-project"></a>Vytvoření projektu

Projekt je kontejner pro modely, dokumenty a testy. Každý projekt automaticky obsahuje všechny dokumenty, které jsou odeslány do pracovního prostoru, které mají pár správný jazyk. 

Vytvoření projektu je prvním krokem k sestavení modelu. 

## <a name="create-a-project"></a>Vytvoření projektu:

1.  V [vlastní Translator](https://portal.customtranslator.azure.ai) portálu, klikněte na tlačítko Vytvořit projekt.

    ![Vytvoření projektu](media/how-to/how-to-create-project.png)

2.  V dialogovém okně zadejte následující podrobnosti o projektu:

    a.  Název projektu (povinné): Pojmenujte svůj projekt jedinečný a smysluplné. Není nutné uvést jazyků v rámci názvu.

    b.  Popis: Krátké shrnutí o projektu. Tento popis nemá vliv na chování vlastní překladač nebo výsledné vlastní systému, ale může pomoci rozlišit mezi různými projekty.

    c.  Dvojice jazyk (povinné): Vyberte jazyk, ve kterém jste překladu od a do.

    d.  Kategorie (povinné): Vyberte kategorii, která je nejvhodnější pro váš projekt. Kategorie popisuje terminologie a stylu dokumenty, které chcete přeložit.

    e.  Popis kategorie: Toto pole použijte zájmu lepšího popsání konkrétní pole nebo oboru, ve kterém pracujete. Například pokud kategorii medicíně, může být přidáte určitému dokumentu, takový operaci nebo pediatrics. Popis nemá vliv na chování vlastní překladač nebo výsledné vlastní systému.

    f.  Popisek projektu: [Projektu popisek](workspace-and-project.md#project-labels) rozlišuje mezi projekty pomocí stejného páru jazyka a kategorie. Jako nejlepší postup použijte popisek *pouze* Pokud plánujete sestavování více projektů pro stejného páru jazyka a stejnou category a chtějí mít přístup k tyto projekty s jinou ID kategorie. Nepoužívejte toto pole, pokud vytváříte systémy pro pouze jednu kategorii. Popisek projektu není povinné a neužitečný rozlišovat mezi dvojice jazyků. Stejný popisek můžete použít pro více projektů.

    ![Vytvořit dialogové okno projektu](media/how-to/how-to-create-project-dialog.png)

3.  Kliknutí na Vytvořit

## <a name="view-project-details"></a>Zobrazit podrobnosti projektu

Cílová stránka vlastní Translator ukazuje prvních 10 projektů ve vašem pracovním prostoru. Zobrazí název projektu, dvojici jazyků, kategorie, stav a BLEU skóre.

Až vyberete projekt, uvidíte na stránce projektu následující:

- ID kategorie: ID kategorie se vytváří zřetězením ID pracovního prostoru, popisek projektu a kód kategorie. Chcete-li získat vlastní překlady použijete ID kategorie s Translator Text API.

- Trénování tlačítka: Pomocí tohoto tlačítka můžete začít [trénujete model pro](how-to-train-model.md).

- Přidejte tlačítko dokumenty: Pomocí tohoto tlačítka [nahrávat dokumenty](how-to-upload-document.md).

- Tlačítko filtr dokumentů: Pomocí tohoto tlačítka na filtr a vyhledejte konkrétní dokumenty.

    ![Zobrazit podrobnosti projektu](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Další postup

- Přečtěte si [jak hledat, upravit, odstranit projekt](how-to-search-edit-delete-projects.md).
- Přečtěte si [jak nahrát dokument](how-to-upload-document.md) sestavovat modely překladu.
