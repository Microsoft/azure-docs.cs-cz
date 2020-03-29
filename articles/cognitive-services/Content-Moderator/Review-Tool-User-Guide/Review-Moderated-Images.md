---
title: Používání recenzí obsahu prostřednictvím nástroje Revize – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nástroj Revize umožňuje lidským moderátorům kontrolovat obrázky na webovém portálu.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044117"
---
# <a name="create-human-reviews"></a>Vytvořit lidské recenze

V této příručce se dozvíte, jak nastavit [recenze](../review-api.md#reviews) na webu nástroje revize. Recenze ukládat a zobrazovat obsah pro lidské moderátory posoudit. Moderátoři mohou použité značky změnit a podle potřeby použít vlastní značky. Když uživatel dokončí kontrolu, výsledky jsou odeslány do zadaného koncového bodu zpětného volání a obsah je odebrán z webu.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet na webu [nástroje Kontrola](https://contentmoderator.cognitive.microsoft.com/) moderátora obsahu.

## <a name="image-reviews"></a>Recenze obrázků

1. Přejděte do [nástroje Revize](https://contentmoderator.cognitive.microsoft.com/), vyberte kartu **Vyzkoušet** a nahrajte některé obrázky ke kontrole.
1. Po dokončení zpracování nahraných obrázků přejděte na kartu **Revize** a vyberte **Obrázek**.

    ![Prohlížeč Chrome zobrazující nástroj pro kontrolu se zvýrazněnou možností Zkontrolovat obrázek](images/review-images-1.png)

    Obrázky se zobrazují se všemi popisky, které byly přiřazeny procesem automatického moderování. Obrázky, které jste odeslali pomocí nástroje Revize, nejsou ostatním recenzentům viditelné.

1. Volitelně můžete **posunutím** posuvníku Recenzí (1) upravit počet obrázků zobrazených na obrazovce. Klikněte na **označená** nebo **neoznačená** tlačítka (2) a podle toho seřaďte obrázky. Kliknutím na panel tagů (3) jej zapněte nebo vypněte.

    ![Prohlížeč Chrome zobrazující nástroj recenze s označenými obrázky ke kontrole](images/review-images-2.png)

1. Chcete-li zobrazit další informace o obrázku, klikněte na tři tečky v miniatuře a vyberte **Zobrazit podrobnosti**. Obrázek můžete přiřadit podtýmu s možností **Přesunout do** (další informace o podtýmech najdete v části [Týmy).](./configure.md#manage-team-and-subteams)

    ![Obrázek se zvýrazněnou volbou Zobrazit podrobnosti](images/review-images-3.png)

1. Prohlédněte si informace o moderování obrázků na stránce podrobností.

    ![Obrázek s podrobnostmi moderování uvedenými v samostatném podokně](images/review-images-4.png)

1. Jakmile zkontrolujete a aktualizujete přiřazení značek podle potřeby, klikněte na **další** a odešlete recenze. Po odeslání máte asi pět sekund na to, abyste se kliknutím na tlačítko **Předchozí** vrátili na předchozí obrazovku a znovu zkontrolovali obrázky. Poté již nejsou obrázky ve frontě Odeslat a tlačítko **Prev** již není k dispozici.

## <a name="text-reviews"></a>Recenze textu

Textové recenze fungují podobně jako recenze obrázků. Místo nahrávání obsahu jednoduše napíšete nebo vložíte text (až 1 024 znaků). Moderátor obsahu pak text analyzuje a použije značky (kromě dalších informací o moderování, jako jsou vulgární výrazy a osobní údaje). V textových recenzích můžete před odesláním recenze přepnout použité značky nebo použít vlastní značky.

![Snímek obrazovky nástroje pro kontrolu zobrazující označený text v okně prohlížeče Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli, jak nastavit a používat recenze z [nástroje Content](https://contentmoderator.cognitive.microsoft.com)Moderator Review . Dále naleznete [v průvodci rozhraní MJ API](../try-review-api-review.md) nebo [na úvodním panelu sady .NET SDK,](../dotnet-sdk-quickstart.md) kde se dozvíte, jak programově vytvářet recenze.