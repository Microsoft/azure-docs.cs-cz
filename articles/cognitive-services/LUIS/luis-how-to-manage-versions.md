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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 982dbf3555648c6a7c82da90a62740a584473c1e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932837"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Použití verzí k úpravám a testování bez dopadu na pracovní nebo produkční aplikace

Verze umožňují sestavovat a publikovat různých modelů. Dobrým postupem je klonovat aktuální active vzor na jiný [verze](luis-concept-version.md) aplikace před prováděním změn do modelu. 

Pro práci s verzí, otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **spravovat** v horním pruhu vyberte **verze** v levém navigačním panelu. 

Seznam verzí ukazuje, které verze jsou publikovány, kde jsou publikovány a která verze je aktuálně aktivní. 

[![Části Správa, verze stránky](./media/luis-how-to-manage-versions/versions-import.png "části Správa, verze stránky")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonovat verze

1. Vyberte verzi, které chcete naklonovat vyberte **klonování** z panelu nástrojů. 

2. V **klonování verze** dialogové okno, zadejte název pro novou verzi, jako je například "0.2".

   ![Dialogové okno verze klonování](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Verze ID může obsahovat pouze znaky, číslice nebo '.' a nesmí být delší než 10 znaků.
 
   Nová verze se zadaným názvem je vytvořen a nastavit jako aktivní verze.

## <a name="set-active-version"></a>Nastavit aktivní verzi

Ze seznamu vyberte verzi a potom vyberte **zkontrolujte aktivní** z panelu nástrojů. 

[![Stránka spravovat oddíl, verze, vytvořit akci verze](./media/luis-how-to-manage-versions/versions-other.png "Stránka spravovat oddíl, verze, vytvořit akci verze")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Import verze

1. Vyberte **Import verze** z panelu nástrojů. 

2. V **Import nové verze** automaticky otevírané okno, zadejte název nové verze deset znaků. Stačí nastavit ID verze, pokud už existuje verze v souboru JSON v aplikaci.

    ![Správa oddílu, stránky verze, importování nové verze](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Jakmile dokončíte import verze, nová verze stane aktivní verze.

### <a name="import-errors"></a>Chyby importu

* Provádějících tokenizaci chyby: Pokud při importu dojde k **chybě provádějících tokenizaci** , snažíte se naimportovat verzi, která používá jiný [provádějících tokenizaci](luis-language-support.md#custom-tokenizer-versions) , než který aktuálně používá aplikace. Chcete-li tento problém vyřešit, přečtěte si téma [migrace mezi provádějících tokenizaci verzemi](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Další akce

* K **odstranit** verze, ze seznamu vyberte verzi a potom vyberte **odstranit** z panelu nástrojů. Vyberte **OK**. 
* K **přejmenovat** verze, ze seznamu vyberte verzi a potom vyberte **přejmenovat** z panelu nástrojů. Zadejte nový název a vyberte **provádí**. 
* K **exportovat** verze, ze seznamu vyberte verzi a potom vyberte **Export aplikace** z panelu nástrojů. Vyberte JSON pro export pro zálohování, vyberte **Exportovat pro kontejner** pro [použití této aplikace v kontejneru Luis](luis-container-howto.md).  

