---
title: Použití revizí obsahu prostřednictvím nástroje pro kontrolu – Content Moderator
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak Nástroj pro kontrolu umožňuje lidským moderátorům kontrolovat obrázky na webovém portálu.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: cfda4d7970c734d92c9f2355d553721ef6165e43
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911847"
---
# <a name="create-human-reviews"></a>Vytvořit recenze lidí

V této příručce se dozvíte, jak nastavit [recenze](../review-api.md#reviews) na webu nástrojů pro revize. Kontroluje ukládání a zobrazování obsahu pro Moderátoři pro lidské moderování k vyhodnocení. Moderátoři mohou změnit použité značky a podle potřeby použít vlastní značky. Když uživatel dokončí revizi, výsledky se odešlou do zadaného koncového bodu zpětného volání a obsah se odebere z webu.

## <a name="prerequisites"></a>Předpoklady

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.

## <a name="image-reviews"></a>Recenze obrázků

1. Spusťte nástroj pro [revizi](https://contentmoderator.cognitive.microsoft.com/), vyberte kartu **vyzkoušet** a nahrajte některé obrázky, které chcete zkontrolovat.
1. Po dokončení zpracování odeslaných imagí přejdete na kartu **Revize** a vyberte **Obrázek** .

    ![Prohlížeč Chrome, který zobrazuje Nástroj pro revizi s zvýrazněnou možností revize image](images/review-images-1.png)

    Obrázky se zobrazí s případnými popisky, které byly přiřazeny procesem automatického moderování. Obrázky, které jste odeslali prostřednictvím nástroje pro kontrolu, nejsou viditelné pro ostatní kontrolory.

1. Volitelně můžete přesunutím posuvníků **na zobrazení** (1) upravit počet obrázků zobrazených na obrazovce. Kliknutím na **označená** nebo **neoznačená** tlačítka (2) seřadíte obrázky odpovídajícím způsobem. Kliknutím na panel značek (3) ho můžete zapnout nebo vypnout.

    ![Prohlížeč Chrome zobrazující Nástroj pro revizi s tagovanými obrázky ke kontrole](images/review-images-2.png)

1. Chcete-li zobrazit další informace o obrázku, klikněte na tlačítko se třemi tečkami na miniaturu a vyberte **Zobrazit podrobnosti** . K dílčímu týmu můžete přiřadit obrázek pomocí možnosti **přesunout do** (Další informace o podtýmích najdete v části [týmy](./configure.md#manage-team-and-subteams) ).

    ![Obrázek s zvýrazněnou možností zobrazit podrobnosti](images/review-images-3.png)

1. Projděte si informace o moderování imagí na stránce s podrobnostmi.

    ![Obrázek s podrobnostmi moderování, které jsou uvedeny v samostatném podokně](images/review-images-4.png)

1. Po kontrole a aktualizaci přiřazení značek podle potřeby klikněte na tlačítko **Další** a odešlete své recenze. Po odeslání budete asi pět sekund, než se **kliknutím na předchozí** obrazovku vraťte na předchozí obrazovku a znovu zkontrolujete image. V takovém případě obrázky již nejsou ve frontě pro odesílání a **Předchozí** tlačítko již není k dispozici.

## <a name="text-reviews"></a>Recenze textu

Kontroly textu fungují podobně jako recenze obrázků. Místo nahrávání obsahu stačí napsat text (až 1 024 znaků) a vkládat ho do něj. Pak Content Moderator analyzuje text a aplikuje značky (kromě dalších informací o moderování, jako jsou například vulgární výrazy a osobní údaje). V recenzích textu můžete před odesláním revize přepnout použité značky nebo použít vlastní značky.

![Snímek obrazovky s nástrojem pro kontrolu zobrazující text označený příznakem v okně prohlížeče Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak nastavit a používat recenze z [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com)Content moderator. Další informace o tom, jak vytvořit recenze prostřednictvím kódu programu, najdete v [průvodci REST API](../try-review-api-review.md) nebo v článku věnovaném [rychlému startu sady .NET SDK](../client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) .