---
title: Správa verzí – LUIS
titleSuffix: Azure Cognitive Services
description: Verze umožňují sestavovat a publikovat různé modely. Dobrým postupem je naklonování aktuálního aktivního modelu do jiné verze aplikace před provedením změn v modelu.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b93e9fd4047ee3731ef6fe4652bb92ce5145987d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018816"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Použití verzí k úpravám a testování bez dopadu na pracovní nebo produkční aplikace

Verze umožňují sestavovat a publikovat různé modely. Dobrým postupem je naklonování aktuálního aktivního modelu do jiné [verze](./luis-concept-app-iteration.md) aplikace před provedením změn v modelu.

Aktivní verze je verze, kterou upravujete v části **sestavení** portálu Luis, s záměry, entitami, funkcemi a vzory. Při použití rozhraní API pro vytváření obsahu není nutné nastavovat aktivní verzi, protože volání REST API specifických pro verzi zahrnují verzi v trase.

Pokud chcete pracovat s verzemi, otevřete svoji aplikaci tak, že na stránce **Moje aplikace** vyberete její název a potom v horním panelu vyberete spravovat a pak v levém navigačním panelu **vyberete možnost** **Správa** .

V seznamu verzí se zobrazí publikované verze, kde jsou publikované a jaká verze je aktuálně aktivní.

## <a name="clone-a-version"></a>Klonovat verzi

1. Vyberte verzi, kterou chcete klonovat, a pak na panelu nástrojů vyberte **klonovat** .

2. V dialogovém okně **naklonovat verzi** zadejte název nové verze, například "0,2".

   ![Dialogové okno naklonování verze](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > ID verze se může skládat jenom z znaků, číslic nebo. a nemůže být delší než 10 znaků.

   Vytvoří se nová verze se zadaným názvem, která se nastaví jako aktivní verze.

## <a name="set-active-version"></a>Nastavit aktivní verzi

V seznamu vyberte verzi a pak na panelu nástrojů vyberte **aktivovat** .

## <a name="import-version"></a>Importovat verzi

Můžete importovat `.json` nebo `.lu` verzi aplikace.

1. Na panelu nástrojů vyberte **importovat** a pak vyberte formát.

2. V automaticky otevíraném okně **importovat novou verzi** zadejte nový název verze o desítku znaků. ID verze je potřeba nastavit jenom v případě, že verze v souboru už v aplikaci existuje.

    ![Správa oddílu, stránky verze, importování nové verze](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Po importu verze se nová verze zobrazí jako aktivní verze.

### <a name="import-errors"></a>Chyby importu

* Provádějících tokenizaci chyby: Pokud při importu dojde k **chybě provádějících tokenizaci** , snažíte se naimportovat verzi, která používá jiný [provádějících tokenizaci](luis-language-support.md#custom-tokenizer-versions) , než právě používá aplikace. Chcete-li tento problém vyřešit, přečtěte si téma [migrace mezi provádějících tokenizaci verzemi](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Další akce

* Pokud chcete **Odstranit** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **Odstranit** . Vyberte **OK**.
* Pokud chcete **Přejmenovat** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **Přejmenovat** . Zadejte nový název a vyberte **Hotovo**.
* Pokud chcete **exportovat** verzi, vyberte ze seznamu verzi a pak na panelu nástrojů vyberte **exportovat aplikaci** . Vyberte JSON nebo LU, které chcete exportovat pro zálohování nebo uložit ve správě zdrojového kódu. Pokud chcete [tuto aplikaci použít v kontejneru Luis](luis-container-howto.md), vyberte **Exportovat pro kontejner** .