---
title: Předem připravených entit pro Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Služba LUIS zahrnuje sadu předem připravených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měny. Podpora předem připravených entit se liší podle jazykové verze vaší aplikace LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647591"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Předem připravených entit rozpoznat běžné typy dat

Služba LUIS zahrnuje sadu předem připravených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měny. 

## <a name="add-a-prebuilt-entity"></a>Přidat předem připravených entit

1. Otevřete aplikaci klepnutím na jeho název na **Moje aplikace** stránce a potom klikněte na tlačítko **entity** na levé straně. 

1. Na **entity** klikněte na **spravovat předem připravených entit**.

1. V **přidání předem připravených entit** dialogového okna, vyberte datetimeV2 předem připravených entit. 

    ![Přidat – dialogové okno předem připravených entit](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Vyberte **Done** (Hotovo).

## <a name="publish-the-app"></a>Publikování aplikace

Nejjednodušší způsob, jak zobrazit hodnotu z předem připravených entit je dotaz z publikovaných koncového bodu. 

1. V horním panelu nástrojů vyberte **publikovat**. Publikovat do **produkční**. 

1. Jakmile se zobrazí oznámení zelené úspěchu, vyberte **najdete v seznamu koncových bodů** odkaz zobrazíte koncových bodů.

1. Vyberte koncový bod. Do tohoto koncového bodu se otevře na nové kartě prohlížeče. Ponechat otevřenou kartu prohlížeče a i nadále **Test** oddílu.

## <a name="test"></a>Test
Po přidání entity není potřeba trénování aplikace. 

Test nové záměr na koncovém bodu podle přidán hodnotu **q** parametru. Následující tabulku můžete použít pro navrhované projevy pro **q**:

|Utterance testu|Hodnota entity|
|--|:--|
|Kniha let zítra|2018-10-19|
|Zrušit 3. března|Služba LUIS vrátil posledního dne 3 v minulosti (2018-03-03) a dne 3 v budoucnu (2019-03-03) vzhledem k tomu, utterance neurčili, nevložily ročně.|
|Naplánovat schůzku v 10: 00|10:00:00|


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Přehled předem připravených entit](./luis-reference-prebuilt-entities.md)
