---
title: Definování a používání pracovních postupů obsahu pomocí nástroje Revize – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Návrhář pracovních postupů Moderátor obsahu Azure můžete použít k definování vlastních pracovních postupů a prahových hodnot na základě zásad obsahu.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754257"
---
# <a name="define-and-use-moderation-workflows"></a>Definování a používání pracovních postupů moderování

V této příručce se dozvíte, jak nastavit a používat [pracovní postupy](../review-api.md#workflows) na webu [nástroje revize.](https://contentmoderator.cognitive.microsoft.com) Pracovní postupy jsou cloudové přizpůsobené filtry, které můžete použít k efektivnějšímu zpracování obsahu. Pracovní postupy se mohou připojit k různým službám, aby filtrovaly obsah různými způsoby a pak podnikají příslušnou akci. Tato příručka ukazuje, jak používat konektor Content Moderator (který je součástí standardu) k filtrování obsahu a nastavení lidských recenzí v typickém scénáři moderování.

## <a name="create-a-new-workflow"></a>Vytvoření nového pracovního postupu

Přejděte do [nástroje Kontrola moderátora obsahu](https://contentmoderator.cognitive.microsoft.com/) a přihlaste se. Na kartě **Nastavení** vyberte **Pracovní postupy**.

![Nastavení pracovních postupů](images/2-workflows-0.png)

Na další obrazovce vyberte **Přidat pracovní postup**.

![Přidání pracovního postupu](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Přiřazení názvu a popisu

Pojmenujte pracovní postup, zadejte popis a zvolte, zda bude pracovní postup zpracovávat obrázky nebo text.

![Název a popis pracovního postupu](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definování kritérií hodnocení

Na další obrazovce přejděte do části **Pokud.** V horní rozevírací nabídce zvolte **Podmínka**. To vám umožní nakonfigurovat podmínku, na které bude pracovní postup provést akci. Pokud chcete použít více podmínek, zvolte **kombinaci** místo. 

Dále vyberte konektor. Tento příklad používá **Content Moderator**. V závislosti na konektoru, který zvolíte, získáte různé možnosti pro výstup dat. Informace o nastavení [dalších konektorů](./configure.md#connectors) najdete v části Konektory v příručce Pro kontrolu nastavení nástroje.

![Vybrat spojnici pracovního postupu](images/image-workflow-connect-to.PNG)

Zvolte požadovaný výstup, který chcete použít, a nastavte podmínky pro jeho kontrolu.

![Definovat podmínku pracovního postupu](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definování akce

Přejděte do části **Pak,** kde vyberete akci. Následující příklad vytvoří recenzi obrázku a přiřadí značku. Volitelně můžete přidat alternativní (Else) cestu a nastavit akci pro to také.

![Definovat akci pracovního postupu](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Uložení pracovního postupu

Poznamenejte si název pracovního postupu; potřebujete název pro spuštění úlohy moderování pomocí rozhraní WORKFLOW API (viz níže). Nakonec uložte pracovní postup pomocí tlačítka **Uložit** v horní části stránky.

## <a name="test-the-workflow"></a>Test pracovního postupu

Nyní, když jste definovali vlastní pracovní postup, otestujte jej s ukázkovým obsahem. Přejděte na **Pracovní postupy** a vyberte odpovídající tlačítko Spustit **pracovní postup.**

![Test pracovního postupu](images/image-workflow-execute.PNG)

Uložte tento [ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) na místní jednotku. Pak vyberte **Zvolit soubory a** nahrajte obrázek do pracovního postupu.

![Běžec s citátem překrývajícím se na obrázku](images/sample-text.jpg)

### <a name="track-progress"></a>Sledování průběhu

Průběh pracovního postupu můžete zobrazit v dalším vyskakovacím okně.

![Sledování provádění pracovního postupu](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Ověřit akci pracovního postupu

Přejděte na kartu **Obrázek** v části **Kontrola** a ověřte, zda se jedná o nově vytvořenou recenzi obrázku.

![Recenze obrázků](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli, jak nastavit a používat pracovní postupy moderování z [nástroje Kontrola](https://contentmoderator.cognitive.microsoft.com)moderátora obsahu . Dále najdete v [tématu REST API průvodce](../try-review-api-workflow.md) se dozvíte, jak vytvářet pracovní postupy programově.
