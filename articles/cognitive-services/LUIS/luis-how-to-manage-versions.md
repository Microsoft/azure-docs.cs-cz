---
title: Správa verzí – LUIS
titleSuffix: Azure Cognitive Services
description: Verze umožňují sestavovat a publikovat různých modelů. Dobrým postupem je klonovat aktuální active vzor na jinou verzi aplikace před prováděním změn do modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393762"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Použití verzí k úpravám a testování bez dopadu na pracovní nebo produkční aplikace

Verze umožňují sestavovat a publikovat různých modelů. Dobrým postupem je naklonování aktuálního aktivního modelu do jiné [verze](luis-concept-version.md) aplikace před provedením změn v modelu. 

Pokud chcete pracovat s verzemi, otevřete svoji aplikaci tak, že na stránce **Moje aplikace** vyberete její název a potom v horním panelu vyberete spravovat a pak v levém navigačním panelu **vyberete možnost** **Správa** . 

Seznam verzí ukazuje, které verze jsou publikovány, kde jsou publikovány a která verze je aktuálně aktivní. 

> [!div class="mx-imgBorder"]
> [![Správa oddílu, verze – stránka](./media/luis-how-to-manage-versions/versions-import.png "Správa části, stránka verze")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonovat verze

1. Vyberte verzi, kterou chcete klonovat, a pak na panelu nástrojů vyberte **klonovat** . 

2. V dialogovém okně **naklonovat verzi** zadejte název nové verze, například "0,2".

   ![Dialogové okno verze klonování](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Verze ID může obsahovat pouze znaky, číslice nebo '.' a nesmí být delší než 10 znaků.
 
   Nová verze se zadaným názvem je vytvořen a nastavit jako aktivní verze.

## <a name="set-active-version"></a>Nastavit aktivní verzi

V seznamu vyberte verzi a pak na panelu nástrojů vyberte **aktivovat** . 

> [!div class="mx-imgBorder"]
> [![Stránka spravovat oddíl, verze, vytvořit akci verze](./media/luis-how-to-manage-versions/versions-other.png "Stránka spravovat oddíl, verze, vytvořit akci verze")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Import verze

Můžete importovat `.json` nebo `.lu` verzi aplikace.

1. Na panelu nástrojů vyberte **importovat** a pak vyberte formát. 

2. V automaticky otevíraném okně **importovat novou verzi** zadejte nový název verze o desítku znaků. ID verze je potřeba nastavit jenom v případě, že verze v souboru už v aplikaci existuje.

    ![Správa oddílu, stránky verze, importování nové verze](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Jakmile dokončíte import verze, nová verze stane aktivní verze.

### <a name="import-errors"></a>Chyby importu

* Provádějících tokenizaci chyby: Pokud při importu dojde k **chybě provádějících tokenizaci** , snažíte se naimportovat verzi, která používá jiný [provádějících tokenizaci](luis-language-support.md#custom-tokenizer-versions) , než právě používá aplikace. Chcete-li tento problém vyřešit, přečtěte si téma [migrace mezi provádějících tokenizaci verzemi](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Další akce

* Pokud chcete **Odstranit** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **Odstranit** . Vyberte **OK**. 
* Pokud chcete **Přejmenovat** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **Přejmenovat** . Zadejte nový název a vyberte **Hotovo**. 
* Pokud chcete **exportovat** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **exportovat aplikaci** . Vyberte JSON pro export pro zálohování, vyberte **Exportovat pro kontejner** pro [použití této aplikace v kontejneru Luis](luis-container-howto.md).  

