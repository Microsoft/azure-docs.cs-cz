---
title: Vytvoření nové aplikace pomocí služby LUIS | Dokumentace Microsoftu
description: Vytvářet a spravovat vaše aplikace na webové stránce Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225410"
---
# <a name="create-an-app"></a>Vytvoření nové aplikace
Vytvořit novou aplikaci různými způsoby: 

* [Spustit](#create-new-app) s prázdnou aplikaci a vytvořit záměrů, projevy a entity.
* [Spustit](#create-new-app) s prázdnou aplikaci a přidejte [předem připravených domény](luis-how-to-use-prebuilt-domains.md).
* [Import aplikace LUIS](#import-new-app) ze souboru JSON, který již obsahuje záměrů, projevy a entity.

## <a name="what-is-an-app"></a>Co je aplikace
Aplikace obsahuje [verze](luis-how-to-manage-versions.md) z modelu stejně jako jakoukoli [spolupracovníci](luis-how-to-collaborate.md) pro aplikaci. Když vytvoříte aplikaci, vyberte jazykovou verzi ([jazyk](luis-supported-languages.md)) který **není možné později změnit**. 

Výchozí verze novou aplikaci je "0.1". 

Můžete vytvářet a spravovat vaše aplikace na **Moje aplikace** stránky. Na této stránce můžete vždy přístup tak, že vyberete **Moje aplikace** na horním navigačním panelu [LUIS](luis-reference-regions.md) webu. 

[![](media/luis-create-new-app/apps-list.png "Snímek obrazovky seznam aplikací")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Vytvoření nové aplikace

1. Na **Moje aplikace** stránce **vytvořit novou aplikaci**.
2. V dialogovém okně pojmenujte svoji aplikaci "TravelAgent".

    ![Vytvoření nového dialogového okna aplikace](./media/luis-create-new-app/create-app.png)

3. Zvolte jazykovou verzi vaší aplikace (TravelAgent aplikaci, zvolte možnost angličtina) a pak vyberte **provádí**. 

    >[!NOTE]
    >Jazykovou verzi nelze změnit po vytvoření aplikace. 

## <a name="import-new-app"></a>Importovat novou aplikaci
Název (maximálně 50 znaků), verze (maximálně 10 znaků) a popis aplikace můžete nastavit v souboru JSON. Příklady souborů JSON aplikace najdete na adrese [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. Na **Moje aplikace** stránce **novou aplikaci Import**.
2. V **novou aplikaci Import** dialogového okna, vyberte soubor JSON, který definuje aplikaci LUIS.

    ![Importovat nové dialogové okno aplikace](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Exportovat aplikaci
1. Na **Moje aplikace** stránky, vyberte tři tečky (***...*** ) na konci řádku aplikace.

    [![](media/luis-create-new-app/apps-list.png "Snímek obrazovky zobrazení dialogu akce pro aplikaci")](media/luis-create-new-app/three-dots.png#lightbox)

2. Vyberte **Export aplikace** z nabídky. 

## <a name="rename-app"></a>Přejmenování aplikace

1. Na **Moje aplikace** stránky, vyberte tři tečky (***...*** ) na konci řádku aplikace. 
2. Vyberte **přejmenovat** z nabídky.
3. Zadejte nový název aplikace a vyberte **provádí**.

## <a name="delete-app"></a>Odstranit aplikaci

> [!CAUTION]
> Odstraňujete aplikaci pro všechny spolupracovníky a vlastník. [Export](#export-app) aplikace před jejím odstraněním. 

1. Na **Moje aplikace** stránky, vyberte tři tečky (***...*** ) na konci řádku aplikace. 
2. Vyberte **odstranit** z nabídky.
3. Vyberte **Ok** v potvrzovacím okně.

## <a name="export-endpoint-logs"></a>Exportovat protokoly koncového bodu
Protokoly obsahovat dotazu, čas UTC a odpověď LUIS JSON.

1. Na **Moje aplikace** stránky, vyberte tři tečky (***...*** ) na konci řádku aplikace. 
2. Vyberte **exportovat protokoly koncového bodu** z nabídky.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Další postup

Vaše první úkol v aplikaci je [přidání záměrů](luis-how-to-add-intents.md).