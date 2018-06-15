---
title: Vytvořit novou aplikaci s LEOŠ | Microsoft Docs
description: Vytvořit a spravovat aplikace na webové stránce znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 75edd39346995cdef72bb1e1fcb9eaff53d29702
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343822"
---
# <a name="create-an-app"></a>Vytvoření aplikace
Vytvořit novou aplikaci různými způsoby: 

* [Spustit](#create-new-app) s prázdnou aplikaci a vytvořit záměry, utterances a entity.
* [Spustit](#create-new-app) s prázdnou aplikaci a přidejte [předem domény](luis-how-to-use-prebuilt-domains.md).
* [Importovat aplikace LEOŠ](#import-new-app) ze souboru JSON, který již obsahuje záměry, utterances a entity.

## <a name="what-is-an-app"></a>Co je aplikace
Aplikace obsahuje [verze](luis-how-to-manage-versions.md) z modelu stejně jako jakoukoli [spolupracovníci](luis-how-to-collaborate.md) pro aplikaci. Pokud vytvoříte aplikaci, můžete vybrat jazykovou verzi ([jazyk](luis-supported-languages.md)) který **není možné později změnit**. 

Výchozí verze novou aplikaci je "0,1." 

Můžete vytvořit a spravovat aplikace na **Moje aplikace** stránky. Vždy můžete pomocí této stránky tak, že vyberete **Moje aplikace** v horním navigačním panelu [LEOŠ](luis-reference-regions.md) webu. 

[![](media/luis-create-new-app/apps-list.png "Snímek obrazovky seznamu aplikací")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Vytvoření nové aplikace

1. Na **Moje aplikace** vyberte **vytvořit novou aplikaci**.
2. V dialogovém okně název vaší aplikace "TravelAgent".

    ![Vytvoření nové aplikace dialogové okno](./media/luis-create-new-app/create-app.png)

3. Zvolte jazykovou verzi vaší aplikace (pro TravelAgent aplikaci, vyberte angličtina) a potom vyberte **provádí**. 

    >[!NOTE]
    >Jazyková verze nelze změnit po vytvoření aplikace. 

## <a name="import-new-app"></a>Importovat novou aplikaci
Název (50 znakový max), verze (10 znakový max) a popis aplikace můžete nastavit v souboru JSON. Příklady soubory JSON aplikace jsou dostupné v [LEOŠ-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. Na **Moje aplikace** vyberte **importovat nové aplikace**.
2. V **novou aplikaci Import** dialogovém okně, vyberte soubor JSON definování LEOŠ aplikace.

    ![Import dialogové okno nové aplikace](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Export aplikace
1. Na **Moje aplikace** vyberte se třemi tečkami (...) na konci řádku aplikace.

    [![](media/luis-create-new-app/apps-list.png "Snímek obrazovky automaticky otevíraný dialog akce pro aplikaci")](media/luis-create-new-app/three-dots.png#lightbox)

2. Vyberte **Export aplikace** z nabídky. 

## <a name="rename-app"></a>Přejmenujte aplikace

1. Na **Moje aplikace** vyberte se třemi tečkami (...) na konci řádku aplikace. 
2. Vyberte **přejmenovat** z nabídky.
3. Zadejte nový název aplikace a vyberte **provádí**.

## <a name="delete-app"></a>Odstranit aplikaci

> [!CAUTION]
> Chystáte se odstranit aplikaci pro všechny spolupracovníky a vlastník. [Export](#export-app) aplikace před odstraněním. 

1. Na **Moje aplikace** vyberte se třemi tečkami (...) na konci řádku aplikace. 
2. Vyberte **odstranit** z nabídky.
3. Vyberte **Ok** v potvrzovacím okně.

## <a name="export-endpoint-logs"></a>Koncový bod protokoly exportu
Protokoly obsahovat dotazu, čas UTC a odpověď LEOŠ JSON.

1. Na **Moje aplikace** vyberte se třemi tečkami (...) na konci řádku aplikace. 
2. Vyberte **koncový bod protokoly exportu** z nabídky.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Další postup

První úlohu v aplikaci je [přidat tříd Intent](luis-how-to-add-intents.md).