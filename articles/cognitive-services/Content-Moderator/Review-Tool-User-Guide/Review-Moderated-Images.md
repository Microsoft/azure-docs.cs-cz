---
title: Použití obsahu kontroly prostřednictvím nástroje pro recenze – Content Moderator
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak nástroj pro recenze umožňuje lidské moderátory recenze obrázků na webovém portálu.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60628999"
---
# <a name="create-human-reviews"></a>Vytvoření recenze prováděné lidmi

V této příručce se dozvíte víc o nastavení [kontroly](../review-api.md#reviews) na webu pro nástroj pro revize. Recenze uložit a zobrazit obsah pro lidské moderátory k vyhodnocení. Moderátoři mohou alter značky použité a použít vlastní vlastní značky podle potřeby. Po dokončení přezkoumání uživatele výsledky se posílají do zadaného zpětného volání koncového bodu a obsah je odebrán z webu.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet v Content Moderatoru [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) lokality.

## <a name="image-reviews"></a>Recenze obrázků

1. Přejděte na [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/), vyberte **zkuste** kartu a nahrávání některých obrázků ke kontrole.
1. Po dokončení zpracování nahraných obrázků, přejděte do **revize** kartě a vyberte **Image**.

    ![Nástroj pro recenze zobrazující se zvýrazněnou možností zkontrolovat Image prohlížeče Chrome](images/review-images-1.png)

    Image se zobrazí všechny popisky, které se týkají procesu automatické moderování. Obrázky, které jste odeslali pomocí nástroje pro recenze nejsou viditelné pro ostatní recenzenty.

1. V případě potřeby přesuňte **kontroly, aby mohla zobrazit** posuvníku (1) Chcete-li upravit počtu imagí, které se zobrazí na obrazovce. Klikněte na **příznakem** nebo **neoznačených** tlačítka (2) k seřazení imagí odpovídajícím způsobem. Klikněte na panel značky (3) pro ho zapnout nebo vypnout.

    ![Prohlížeč Chrome ukazující nástroj pro recenze s označený imagemi k revizi](images/review-images-2.png)

1. Chcete-li zobrazit další informace o bitovou kopii, klikněte na miniaturu a vyberte tři tečky **podrobnosti**. Image můžete přiřadit dílčího týmu s **přesunout do** možnost (najdete v článku [týmy](./configure.md#manage-team-and-subteams) naleznete další informace o podtýmy).

    ![Obrázek se zvýrazněnou možností podrobnosti zobrazení](images/review-images-3.png)

1. Informace o moderování obrázků na stránce s podrobnostmi o procházení.

    ![Obrázek s podrobnostmi o moderování uvedené v samostatné podokno](images/review-images-4.png)

1. Jakmile zkontrolujete a podle potřeby aktualizovat přiřazení značky, klikněte na tlačítko **Další** odeslat revizích. Po odeslání, budete muset asi pěti sekundách kliknout **předchozí** tlačítko pro návrat na předchozí obrazovku a znovu zkontrolujte bitové kopie. Poté jsou obrázky už ve frontě pro odesílání a **předchozí** tlačítko už není k dispozici.

## <a name="text-reviews"></a>Recenze textu

Text obsahuje přehled funkce podobně jako na obrázku kontroly. Namísto odesílání obsahu, stačí napsat nebo vložte text (až 1 024 znaků). Potom Content Moderator analyzuje text a použije značky (kromě jiných moderování informace, například vulgárních výrazů a osobních údajů). V recenze textu můžete přepnout použité značky nebo použít vlastní značky před odesláním revizi.

![Snímek obrazovky znázorňující nástroj pro revize označený text v okně prohlížeče Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Další postup

V této příručce, jste zjistili, jak nastavit a začít používat kontroly od Content Moderator [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com). V dalším kroku najdete v článku [pokyny k rozhraní REST API](../try-review-api-review.md) nebo [sady .NET SDK průvodce](../moderation-reviews-quickstart-dotnet.md) informace o vytvoření recenze prostřednictvím kódu programu.