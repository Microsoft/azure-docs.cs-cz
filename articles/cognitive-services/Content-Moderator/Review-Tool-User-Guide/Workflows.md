---
title: Definování a použití pracovních postupů obsahu prostřednictvím nástroje pro kontrolu – Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí návrháře pracovních postupů Azure Content Moderator můžete definovat vlastní pracovní postupy a prahové hodnoty na základě zásad obsahu.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904138"
---
# <a name="define-and-use-moderation-workflows"></a>Definování a použití pracovních postupů moderování

V této příručce se dozvíte, jak nastavit a používat [pracovní postupy](../review-api.md#workflows) na webu [nástrojů pro revize](https://contentmoderator.cognitive.microsoft.com) . Pracovní postupy jsou cloudově přizpůsobené filtry, které můžete použít ke efektivnějšímu zpracování obsahu. Pracovní postupy se můžou připojit k nejrůznějším službám a filtrovat obsah různými způsoby a pak provést příslušné akce. V této příručce se dozvíte, jak pomocí konektoru Content Moderator (který je ve výchozím nastavení zahrnutý) filtrovat obsah a jak v typickém scénáři moderování nastavit lidské recenze.

## <a name="create-a-new-workflow"></a>Vytvořit nový pracovní postup

Přejdete na [Nástroj pro kontrolu Content moderator](https://contentmoderator.cognitive.microsoft.com/) a přihlaste se. Na kartě **Nastavení** vyberte **pracovní postupy**.

![Nastavení pracovních postupů](images/2-workflows-0.png)

Na další obrazovce vyberte **Přidat pracovní postup**.

![Přidat pracovní postup](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Přiřadit název a popis

Pojmenujte pracovní postup, zadejte popis a určete, zda pracovní postup zpracuje obrázky nebo text.

![Název a popis pracovního postupu](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definování kritérií vyhodnocení

Na další obrazovce přejdete do části **if** . V rozevírací nabídce nahoře vyberte **Podmínka**. To vám umožní nakonfigurovat podmínku, na které bude pracovní postup provádět akce. Pokud chcete použít více podmínek, vyberte místo toho **kombinaci** . 

V dalším kroku vyberte konektor. Tento příklad používá **Content moderator**. V závislosti na zvoleném konektoru se zobrazí různé možnosti pro výstup dat. Informace o nastavení dalších konektorů najdete v části [konektory](./configure.md#connectors) v Průvodci nastavením nástroje pro kontrolu.

![Vybrat konektor pracovního postupu](images/image-workflow-connect-to.PNG)

Vyberte požadovaný výstup, který chcete použít, a nastavte podmínky pro jeho kontrolu.

![Definovat podmínku pracovního postupu](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definovat akci

Přejít **na část a** , kde vyberete akci. Následující příklad vytvoří revizi obrázku a přiřadí značku. Volitelně můžete přidat alternativní cestu (Else) a také pro ni nastavit akci.

![Akce definování pracovního postupu](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Uložit pracovní postup

Poznamenejte si název pracovního postupu. Chcete-li spustit úlohu moderování s rozhraním API pracovního postupu (viz níže), potřebujete název. Nakonec pracovní postup uložte pomocí tlačítka **Uložit** v horní části stránky.

## <a name="test-the-workflow"></a>Test pracovního postupu

Teď, když jste definovali vlastní pracovní postup, otestujte ho s ukázkovým obsahem. Přejít na **pracovní postupy** a vyberte odpovídající tlačítko **Spustit pracovní postup** .

![Test pracovního postupu](images/image-workflow-execute.PNG)

Uložte tuto [ukázkovou image](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) na místní disk. Pak vyberte **zvolit soubory** a nahrajte image do pracovního postupu.

![Spouštěč s uvozovkou uloženou na obrázku](images/sample-text.jpg)

### <a name="track-progress"></a>Sledování průběhu

Průběh pracovního postupu můžete zobrazit v dalším překryvném okně.

![Sledovat spuštění pracovního postupu](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Akce ověření pracovního postupu

V části **Revize** klikněte na kartu **Image** a ověřte, že se právě vytvořila nově vytvořená recenze obrázku.

![Recenze obrázků](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak pomocí [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com)Content moderator nastavit a používat pracovní postupy moderování. Další informace o tom, jak programově vytvářet pracovní postupy, najdete v [Průvodci konzolou rozhraní API](../try-review-api-workflow.md) .
