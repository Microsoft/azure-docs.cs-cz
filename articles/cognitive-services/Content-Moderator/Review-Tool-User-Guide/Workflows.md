---
title: Definice a používání pracovních postupů v obsahu moderátora Azure | Microsoft Docs
description: Informace o vytváření vlastních pracovních postupů na základě vašeho obsahu zásad.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/07/2018
ms.author: sajagtap
ms.openlocfilehash: dfe3ba8a2ef1bcbc69ef585b504a9367d9420bf0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342436"
---
# <a name="define-test-and-use-workflows"></a>Definovat, testování a použití pracovních postupů

Návrhář postupu provádění moderátora obsahu Azure a rozhraní API můžete použít k definování vlastní pracovní postupy a prahové hodnoty na základě vašeho obsahu zásad.

Pracovní postupy "připojit" k obsahu moderátora API pomocí konektorů. Jiná rozhraní API můžete použít, pokud je k dispozici konektor pro toto rozhraní API. Tady příklad používá obsahu moderátora konektor, který je zahrnut ve výchozím nastavení.

## <a name="browse-to-the-workflows-section"></a>Přejděte do části pracovní postupy

Na **nastavení** vyberte **pracovních**.

  ![Nastavení pracovních postupů](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Spuštění nového pracovního postupu

Vyberte **přidat pracovní postup**.

  ![Přidat pracovní postup](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Zadejte název a popis

Název pracovního postupu, zadejte popis a vyberte, zda pracovní postup zpracovává bitové kopie nebo text.

  ![Název pracovního postupu a popis](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Zadejte kritéria hodnocení ("podmínky")

Na následujícím snímku obrazovky uvidíte pole a v případě-pak-jinak možnosti, které je třeba definovat vaše pracovní postupy. Vyberte konektor. Tento příklad používá **obsahu moderátora**. V závislosti na tento konektor, který zvolíte změnit možnosti k dispozici pro výstup.

  ![Definovat podmínku pracovního postupu](images/ocr-workflow-step-2-condition.PNG)

Když zvolíte konektor a její výstup, který chcete, vyberte operátor a hodnotu pro podmínku.

## <a name="define-the-action-to-take"></a>Definovat akci provést

Vyberte akci provést a jako podmínku splňují. Následující příklad vytvoří kontrolní bitové kopie, přiřadí značku `a`a zvýrazňuje pro podmínku vidět. Také můžete kombinovat více podmínek získat výsledky, které chcete. Volitelně lze přidáte alternativní cestu (Else).

  ![Definovat akci pracovního postupu](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Uložení pracovního postupu

Nakonec uložit pracovního postupu a poznamenejte si název pracovního postupu. Je třeba název, který má spustit úlohu přerušování pomocí rozhraní API revize.

## <a name="test-the-workflow"></a>Test pracovního postupu

Teď, když jste definovali vlastní pracovní postup, testujte s ukázkovým obsahem.

Vyberte odpovídající **spuštění pracovního postupu** tlačítko.

  ![Pracovní postup test](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Nahrání souboru

Uložit [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na místní jednotku. Chcete-li testování pracovního postupu, vyberte **zvolte soubory** a odeslat bitovou kopii.

  ![Nahrát soubor bitové kopie](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Sledování pracovní postup

Pracovní postup sledovat, jak se provede.

  ![Sledování pracovního postupu provádění](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Zkontrolujte všechny bitové kopie s příznakem pro lidské přerušování

Zobrazí se obrázek, zkontrolujte, přejděte na **Image** v části **zkontrolujte**.

  ![Recenze obrázků](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Další postup 

K vyvolání pracovního postupu z kódu, použijte vlastní pracovní postupy s [ `Job` rozhraní API konzoly quickstart](../try-review-api-job.md) a [rychlý start .NET SDK](../moderation-jobs-quickstart-dotnet.md).
