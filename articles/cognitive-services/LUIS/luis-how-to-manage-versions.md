---
title: Správa verzí – LUIS
titleSuffix: Azure Cognitive Services
description: Verze umožňují sestavovat a publikovat různé modely. Dobrým postupem je naklonování aktuálního aktivního modelu do jiné verze aplikace před provedením změn v modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: aa1cbd08bdf8d92653a8f30ae67ecd813e563999
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467510"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Použití verzí k úpravám a testování bez dopadu na pracovní nebo produkční aplikace

Verze umožňují sestavovat a publikovat různé modely. Dobrým postupem je naklonování aktuálního aktivního modelu do jiné [verze](luis-concept-version.md) aplikace před provedením změn v modelu. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Pokud chcete pracovat s verzemi, otevřete svoji aplikaci tak, že na stránce **Moje aplikace** vyberete její název a potom v horním panelu vyberete spravovat a pak v levém navigačním panelu **vyberete možnost** **Správa** . 

Seznam verzí ukazuje, které verze jsou publikovány, kde jsou publikovány a která verze je aktuálně aktivní. 

[![Správa oddílu, verze – stránka](./media/luis-how-to-manage-versions/versions-import.png "Správa oddílu, verze – stránka")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonovat verzi

1. Vyberte verzi, kterou chcete klonovat, a pak na panelu nástrojů vyberte **klonovat** . 

2. V dialogovém okně **naklonovat verzi** zadejte název nové verze, například "0,2".

   ![Dialogové okno naklonování verze](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > ID verze se může skládat jenom z znaků, číslic nebo. a nemůže být delší než 10 znaků.
 
   Vytvoří se nová verze se zadaným názvem, která se nastaví jako aktivní verze.

## <a name="set-active-version"></a>Nastavit aktivní verzi

Vyberte ze seznamu verzi a pak na panelu nástrojů vyberte možnost **nastavit jako aktivní** . 

[![Stránka spravovat oddíl, verze, vytvořit akci verze](./media/luis-how-to-manage-versions/versions-other.png "Stránka spravovat oddíl, verze, vytvořit akci verze")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importovat verzi

1. Na panelu nástrojů vyberte **importovat verzi** . 

2. V automaticky otevíraném okně **importovat novou verzi** zadejte nový název verze o desítku znaků. ID verze je potřeba nastavit jenom v případě, že verze v souboru JSON už v aplikaci existuje.

    ![Správa oddílu, stránky verze, importování nové verze](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Po importu verze se nová verze zobrazí jako aktivní verze.

### <a name="import-errors"></a>Chyby importu

* Provádějících tokenizaci chyby: Pokud při importu dojde k **chybě provádějících tokenizaci** , snažíte se naimportovat verzi, která používá jiný [provádějících tokenizaci](luis-language-support.md#custom-tokenizer-versions) , než právě používá aplikace. Chcete-li tento problém vyřešit, přečtěte si téma [migrace mezi provádějících tokenizaci verzemi](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Další akce

* Pokud chcete **Odstranit** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **Odstranit** . Vyberte **OK**. 
* Pokud chcete **Přejmenovat** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **Přejmenovat** . Zadejte nový název a vyberte **Hotovo**. 
* Pokud chcete **exportovat** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **exportovat aplikaci** . Vyberte JSON pro export pro zálohování, vyberte **Exportovat pro kontejner** pro [použití této aplikace v kontejneru Luis](luis-container-howto.md).  

