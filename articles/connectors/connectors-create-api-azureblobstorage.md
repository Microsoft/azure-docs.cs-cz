---
title: "Přidejte konektor Azure blob storage ve vašich Logic Apps | Microsoft Docs"
description: "Začínáme a nakonfigurovat konektor úložiště objektů blob v Azure v aplikaci logiky"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: estfan; ladocs
ms.openlocfilehash: 7aaff2ac78201c4484105c6cacc5f0fef19ca7b5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>Použití konektoru úložiště objektů blob v Azure v aplikaci logiky
Pomocí konektoru úložiště objektů Blob v Azure nahrát, aktualizace, získání a odstranění objektů BLOB v účtu úložiště, vše v rámci aplikace logiky.  

S úložištěm Azure blob můžete:

* Vytvořte pracovní postup odesílání nových projektů nebo získávání souborů, které byly nedávno aktualizovány.
* Pomocí akcí můžete získat metadata souboru, odstraňte soubor, kopírovat soubory a další. Například při aktualizaci nástroj v Azure webový server (aktivační událost), aktualizujte soubor v úložišti objektů blob (akce). 

Toto téma ukazuje, jak používat konektor úložiště objektů blob v aplikaci logiky.

Další informace o Logic Apps najdete v tématu [co jsou logic apps](../logic-apps/logic-apps-overview.md) a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-blob-storage"></a>Připojení do Azure blob storage
Než se aplikace logiky k jakékoli služby, nejprve vytvořit *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například pokud chcete připojit k účtu úložiště, nejprve vytvoříte úložiště objektů blob *připojení*. Chcete-li vytvořit připojení, zadejte přihlašovací údaje, které standardně používáte k přístupu ke službě, ke kterému se připojujete. Proto s Azure storage, zadejte přihlašovací údaje k účtu úložiště k vytvoření připojení. 

#### <a name="create-the-connection"></a>Vytvoření připojení
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Použít aktivační událost
Tento konektor nemá žádné aktivační události. Spusťte aplikaci logiky, jako jsou aktivační událost opakování, aktivační události Webhooku protokolu HTTP, aktivační události, které jsou k dispozici ostatní konektory a další pomocí jiných aktivační události. [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) poskytuje příklad.

## <a name="use-an-action"></a>Použít akci.
Akce je operace prováděné definované v aplikaci logiky pracovního postupu.

1. Vyberte znaménko plus. Zobrazí několik možností: **přidat akci**, **přidat podmínku**, nebo jeden z **Další** možnosti.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Zvolte **přidat akci**.
3. Do textového pole zadejte "blob" získat seznam všechny dostupné akce.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. V našem příkladu zvolte **AzureBlob - Get metadata souboru pomocí cesty**. Pokud připojení již existuje, pak vyberte **...** Tlačítko (Zobrazit výběr) a vyberte soubor.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Pokud se zobrazí výzva pro informace o připojení, zadejte podrobnosti k vytvoření připojení. [Vytvoření připojení](connectors-create-api-azureblobstorage.md#create-the-connection) v tomto tématu popisuje tyto vlastnosti. 
   
   > [!NOTE]
   > V tomto příkladu se nám získat metadata souboru. Pokud chcete zobrazit metadata, přidejte akci, která se vytvoří nový soubor pomocí jiný konektor. Například přidejte OneDrive akci, která vytvoří nový soubor "test" založené na metadatech. 


5. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky je uloženo a automaticky povolí.

> [!TIP]
> [Storage Explorer](http://storageexplorer.com/) je skvělý nástroj pro správu více účtů úložiště.

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte dalších dostupných konektorů v Logic Apps v našem [rozhraní API seznamu](apis-list.md).

