---
title: Definice a používání obsahu pracovní postupy nástroje pro recenze – Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator návrháře postupu provádění můžete definovat vlastní pracovní postupy a prahových hodnot na základě obsahu zásad.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795793"
---
# <a name="define-and-use-moderation-workflows"></a>Definice a používání pracovních postupů pro moderování

V této příručce se naučíte nastavit a používat [pracovních postupů](../review-api.md#workflows) na [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com) webu. Pracovní postupy jsou založené na cloudu vlastní filtry, které vám umožní efektivněji zpracovat obsah. Pracovní postupy můžete připojit k různým službám v různých způsobů filtrování obsahu a poté přijmout vhodná opatření. Tato příručka obsahuje pokyny k používání konektoru Content Moderator (která je obsažena ve výchozím nastavení) a filtrovat obsah nastavit recenze prováděné lidmi ve scénáři typické moderování.

## <a name="create-a-new-workflow"></a>Vytvoření nového pracovního postupu

Přejděte [nástroj pro kontrolu Content Moderator](https://contentmoderator.cognitive.microsoft.com/) a přihlaste se. Na **nastavení** kartu, vyberte možnost **pracovních postupů**.

![Nastavení pracovních postupů](images/2-workflows-0.png)

Na další obrazovce vyberte **přidat pracovní postup**.

![Přidat pracovní postup](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Zadejte název a popis

Název pracovního postupu, zadejte popis a zvolte, zda pracovní postup bude zpracovávat Image nebo text.

![Název pracovního postupu a popis](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definujte kritéria hodnocení

Přejděte na další obrazovce **Pokud** oddílu. V horní rozevírací nabídky vyberte **podmínku**. To vám umožní nakonfigurovat podmínku, na kterém bude pracovní postup provést akci. Pokud chcete použít více podmínek, vyberte **kombinaci** místo. 

V dalším kroku vyberte konektor. Tento příklad používá **Content Moderator**. V závislosti na konektoru, kterou zvolíte se zobrazí různé možnosti pro výstupní data. Zobrazit [konektory](./configure.md#connectors) části Průvodce nastavení nástroj pro kontrolu se naučíte nastavit jiné konektory.

![Vyberte konektor pracovního postupu](images/image-workflow-connect-to.PNG)

Zvolte požadovaný výstup používat a nastavit podmínky tak zkontrolovat, že před.

![Definujte podmínku pracovního postupu](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definovat akci

Přejděte **pak** části, kde můžete vybrat akci. Následující příklad vytvoří revizi image a přiřadí značku. Volitelně můžete přidat alternativní cesta (jinak) a také nastavit akci pro tento.

![Definovat akce pracovního postupu](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Uložit pracovní postup

Poznamenejte si název pracovního postupu; budete potřebovat název, který má spustit úlohu moderování s rozhraním API pro pracovní postup (viz níže). Nakonec uložte pomocí pracovního postupu **Uložit** tlačítko v horní části stránky.

## <a name="test-the-workflow"></a>Test pracovního postupu

Teď, když jste definovali vlastní pracovní postup, otestujte ho s ukázkovým obsahem. Přejděte na **pracovních postupů** a vyberte odpovídající **spuštění pracovního postupu** tlačítko.

![Test pracovního postupu](images/image-workflow-execute.PNG)

Uložte zkopírované řádky [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) na váš místní disk. Potom vyberte **zvolte soubory** a odešlete image do pracovního postupu.

![Runner s nabídkou bude zobrazen na obrázku](images/sample-text.jpg)

### <a name="track-progress"></a>Sledování průběhu

V automaticky otevíraném okně Další můžete zobrazit průběh pracovního postupu.

![Sledujte provádění pracovního postupu](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Ověření akce pracovního postupu

Přejděte **Image** kartu **zkontrolujte** a ověřte, zda je přezkoumání nově vytvořenou image.

![Recenze obrázků](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Další postup

V této příručce, jste zjistili, jak nastavit a používat moderování pracovních postupů z Content Moderator [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com). V dalším kroku najdete v článku [pokyny k rozhraní REST API](../try-review-api-workflow.md) se naučíte vytvářet pracovní postupy prostřednictvím kódu programu.
