---
title: Jak vytvořit projekt – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak vytvořit a spravovat projekt ve vlastním překladateli Azure Cognitive Services.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9e3aa52323f44e6c1407fe2a542e40ee06370043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895794"
---
# <a name="create-a-project"></a>Vytvoření projektu

Projekt je kontejner pro modely, dokumenty a testy. Každý projekt automaticky obsahuje všechny dokumenty, které jsou odeslány do daného pracovního prostoru, které mají správnou dvojici jazyků.

Vytvoření projektu je prvním krokem k vytvoření modelu.

## <a name="create-a-project"></a>Vytvořit projekt:

1.  Na portálu [Custom Translator](https://portal.customtranslator.azure.ai) klikněte na vytvořit projekt.

    ![Vytvoření projektu](media/how-to/how-to-create-project.png)

2.  Do dialogového okna zadejte následující podrobnosti o projektu:

    a.  Název projektu (povinné): uveďte svůj projekt jako jedinečný, smysluplný název. V rámci názvu není nutné uvádět jazyky.

    b.  Popis: stručný souhrn o projektu. Tento popis nemá žádný vliv na chování vlastního překladatele ani na výsledný vlastní systém, ale může vám povýšit rozdíl mezi různými projekty.

    c.  Dvojice jazyků (povinné): Vyberte jazyk, ze kterého chcete překládáte, a na.

    d.  Kategorie (povinné): vyberte kategorii, která je nejvhodnější pro váš projekt. Kategorie popisuje terminologii a styl dokumentů, které chcete přeložit.

    e.  Kategorie Popis: Toto pole použijte k lepšímu popisu konkrétního pole nebo odvětví, ve kterém pracujete. Pokud je vaše kategorie například lékařství, můžete přidat konkrétní dokument, takovou chirurgie nebo Pediatrics. Popis nemá žádný vliv na chování vlastního překladatele ani na výsledný vlastní systém.

    f.  Popisek projektu: [popisek projektu](workspace-and-project.md#project-labels) rozlišuje mezi projekty a stejnou jazykovou dvojicí a kategorií. Jako osvědčený postup použijte popisek *pouze* v případě, že plánujete sestavit více projektů pro stejnou jazykovou dvojici a stejnou kategorii a chcete získat přístup k těmto projektům s jiným KódKategorie. Toto pole nepoužívejte, pokud vytváříte systémy pouze pro jednu kategorii. Popisek projektu není vyžadován a není vhodný k rozlišení mezi dvojicemi jazyků. Můžete použít stejný popisek pro více projektů.

    ![Dialogové okno vytvořit projekt](media/how-to/how-to-create-project-dialog.png)

3.  Kliknutí na Vytvořit

## <a name="view-project-details"></a>Zobrazit podrobnosti projektu

Úvodní stránka pro vlastní překladatele zobrazuje prvních 10 projektů v pracovním prostoru. Zobrazuje název projektu, dvojici jazyků, kategorii, stav a BLEU skóre.

Po výběru projektu se na stránce projektu zobrazí následující:

- CategoryID: pole CategoryID je vytvořeno pomocí zřetězení ID pracovního prostoru, popisku projektu a kódu kategorie. K získání vlastních překladů použijete KódKategorie s rozhraním API pro překladatele textu. Chcete-li kopírovat, klikněte na ikonu Kopírovat.

- Tlačítko Výuka: pomocí tohoto tlačítka můžete spustit [výuku modelu](how-to-train-model.md).

- Tlačítko Přidat dokumenty: Toto tlačítko slouží k [nahrání dokumentů](how-to-upload-document.md).

- Tlačítko filtrovat dokumenty: Toto tlačítko slouží k filtrování a hledání konkrétních dokumentů.

    ![Zobrazit podrobnosti projektu](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Další kroky

- Naučte se [Hledat, upravovat a odstraňovat projekty](how-to-search-edit-delete-projects.md).
- Přečtěte si, [Jak nahrát dokument](how-to-upload-document.md) pro sestavení modelů překladu.
