---
title: Definice a používání pracovních postupů pro moderování obsahu – Content Moderator
titlesuffix: Azure Cognitive Services
description: Rozhraní API a Azure Content Moderator návrháře postupu provádění můžete definovat vlastní pracovní postupy a prahových hodnot na základě obsahu zásad.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 265d5a2bd6a256d287e6ae164e883ade783f960f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262400"
---
# <a name="define-test-and-use-workflows"></a>Definujte, testování a použití pracovních postupů

Rozhraní API a Azure Content Moderator návrháře postupu provádění můžete definovat vlastní pracovní postupy a prahových hodnot na základě obsahu zásad.

Pracovní postupy "připojení" ke Content Moderator API pomocí konektorů. Jiná rozhraní API můžete použít, pokud je k dispozici pro toto rozhraní API konektoru. Tento příklad používá konektor Content Moderator, který je zahrnut ve výchozím nastavení.

## <a name="browse-to-the-workflows-section"></a>Přejděte do části pracovní postupy

Na **nastavení** kartu, vyberte možnost **pracovních postupů**.

  ![Nastavení pracovních postupů](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Spuštění nového pracovního postupu

Vyberte **přidat pracovní postup**.

  ![Přidat pracovní postup](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Zadejte název a popis

Název pracovního postupu, zadejte popis a zvolte, zda pracovní postup zpracovává Image nebo text.

  ![Název pracovního postupu a popis](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Definujte kritéria hodnocení ("podmínky")

Na následujícím snímku obrazovky vidíte pole a If-Then-Else výběry, které je třeba definovat pro své pracovní postupy. Zvolte konektor. Tento příklad používá **Content Moderator**. V závislosti na konektoru, kterou zvolíte změňte dostupné možnosti pro výstup.

  ![Definujte podmínku pracovního postupu](images/ocr-workflow-step-2-condition.PNG)

Po zvolení konektoru a jeho výstup, který chcete, vyberte operátor a hodnotu pro podmínku.

## <a name="define-the-action-to-take"></a>Definovat akce má být provedena

Vyberte akci, která umožní a podmínky pro splnění. Následující příklad vytvoří revizi bitové kopie, přiřadí značku `a`a to pro podmínky uvedené. Také můžete zkombinovat více podmínek k získání požadovaných výsledků. Volitelně můžete přidáte alternativní cesta (jinak).

  ![Definovat akce pracovního postupu](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Uložení pracovního postupu

Nakonec uložte pracovní postup a poznamenejte si název pracovního postupu. Budete potřebovat název, který chcete spustit úlohu moderování s použitím rozhraní API pro kontrolu.

## <a name="test-the-workflow"></a>Test pracovního postupu

Teď, když jste definovali vlastní pracovní postup, otestujte ho s ukázkovým obsahem.

Vyberte odpovídající **spuštění pracovního postupu** tlačítko.

  ![Test pracovního postupu](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Nahrání souboru

Uložit [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na váš místní disk. Chcete-li otestovat pracovního postupu, vyberte **zvolte soubory** a nahráním image.

  ![Nahrát soubor bitové kopie](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Sledování pracovního postupu

Pracovní postup sledujte, jak se provede.

  ![Sledujte provádění pracovního postupu](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Zkontrolujte všechny Image s příznakem pro lidské moderování

Zobrazte image zkontrolovat, přejděte **Image** kartu **zkontrolujte**.

  ![Recenze obrázků](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Další postup 

Abyste mohli vyvolat pracovní postup z kódu, použijte vlastní pracovní postupy s [ `Job` rozhraní API konzoly rychlého startu](../try-review-api-job.md) a [rychlý start sady .NET SDK](../moderation-jobs-quickstart-dotnet.md).
