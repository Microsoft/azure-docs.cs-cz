---
title: Správa verzí – LUIS
titleSuffix: Azure Cognitive Services
description: Verze umožňují vytvářet a publikovat různé modely. Dobrým postupem je klonovat aktuální aktivní model na jinou verzi aplikace před provedením změn v modelu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220894"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Použití verzí k úpravám a testování bez dopadu na pracovní nebo produkční aplikace

Verze umožňují vytvářet a publikovat různé modely. Dobrým postupem je klonovat aktuální aktivní model na jinou [verzi](luis-concept-version.md) aplikace před provedením změn v modelu. 

Pokud chcete pracovat s verzemi, otevřete aplikaci tak, že na stránce **Moje aplikace** vyberete její název, a pak v horní části řádku vyberte **Spravovat** a v levém navigačním panelu vyberte **Verze.** 

Seznam verzí ukazuje, které verze jsou publikovány, kde jsou publikovány a která verze je aktuálně aktivní. 

> [!div class="mx-imgBorder"]
> [![Spravovat oddíl, stránka verze](./media/luis-how-to-manage-versions/versions-import.png "Spravovat oddíl, stránka verze")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonovat verzi

1. Vyberte verzi, kterou chcete klonovat, a pak na panelu nástrojů vyberte **Klonovat.** 

2. V dialogovém okně **Klonovací verze** zadejte název nové verze, například "0.2".

   ![Dialogové okno Klonovat verzi](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > ID verze se může skládat pouze ze znaků, číslic nebo "" a nemůže být delší než 10 znaků.
 
   Nová verze se zadaným názvem je vytvořena a nastavena jako aktivní verze.

## <a name="set-active-version"></a>Nastavit aktivní verzi

Vyberte verzi ze seznamu a pak z panelu nástrojů vyberte **Aktivovat.** 

> [!div class="mx-imgBorder"]
> [![Správa oddílu, stránky verzí, provedení akce verze](./media/luis-how-to-manage-versions/versions-other.png "Správa oddílu, stránky verzí, provedení akce verze")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importovat verzi

Můžete importovat `.json` nebo `.lu` verzi aplikace.

1. Na panelu nástrojů vyberte **Importovat** a pak vyberte formát. 

2. V rozbalovacím okně **Importovat novou verzi** zadejte nový název verze deseti znaků. ID verze je potřeba pouze v případě, že verze v souboru již v aplikaci existuje.

    ![Správa oddílu, stránky verzí, import nové verze](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Po importu verze se nová verze stane aktivní verzí.

### <a name="import-errors"></a>Chyby importu

* Tokenizer chyby: Pokud se při importu zobrazí **chyba tokenizer,** pokoušíte se importovat verzi, která používá jiný [tokenizer](luis-language-support.md#custom-tokenizer-versions) než aplikace aktuálně používá. Chcete-li tento problém vyřešit, přečtěte si téma [Migrace mezi verzemi tokenizeru](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Další akce

* Chcete-li **odstranit** verzi, vyberte verzi ze seznamu a pak na panelu nástrojů vyberte **Odstranit.** Vyberte **OK**. 
* Chcete-li verzi **přejmenovat,** vyberte verzi ze seznamu a pak na panelu nástrojů vyberte **Přejmenovat.** Zadejte nový název a vyberte **Hotovo**. 
* Chcete-li **exportovat** verzi, vyberte verzi ze seznamu a pak na panelu nástrojů vyberte **Exportovat aplikaci.** Zvolte JSON exportovat pro zálohování, zvolte **Export pro kontejner** pro použití této aplikace v [kontejneru LUIS](luis-container-howto.md).  

