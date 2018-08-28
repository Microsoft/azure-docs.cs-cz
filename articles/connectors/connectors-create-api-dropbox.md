---
title: Připojte se k Dropboxu – Azure Logic Apps | Dokumentace Microsoftu
description: Odesílat a spravovat soubory pomocí rozhraní REST API Dropboxu a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/15/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 256a0b34d5050e17abe5bb98ca0c13ab0b61787e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094434"
---
# <a name="get-started-with-the-dropbox-connector"></a>Začínáme s konektor Dropbox
Připojte se k Dropboxu, abyste mohli spravovat soubory. Můžete provádět různé akce, jako je nahrávání, aktualizace, získání a odstranění souborů v Dropboxu.

Chcete-li použít [konektory](apis-list.md), musíte nejprve vytvořit aplikaci logiky. Můžete začít tak [vytvoření aplikace logiky teď](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Připojte se k Dropboxu
Předtím, než aplikace logiky můžete přistupovat k libovolné službě, je nejprve potřeba vytvořit *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například, aby bylo možné připojit se k Dropboxu, musíte nejdřív Dropbox *připojení*. Chcete-li vytvořit připojení, je třeba poskytnout přihlašovací údaje, které běžně používáte pro přístup ke službě, kterou chcete připojit k. Ano v příkladu Dropboxu, je třeba přihlašovací údaje účtu Dropbox Chcete-li vytvořit připojení k Dropboxu. 

### <a name="create-a-connection-to-dropbox"></a>Vytvoření připojení k Dropboxu
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Pomocí aktivační události Dropboxu
Trigger je událost, která umožňuje spustit pracovní postup definovaný v aplikaci logiky. [Další informace o aktivačních událostech](../logic-apps/logic-apps-overview.md#logic-app-concepts).

V tomto příkladu budeme používat **při vytvoření souboru** aktivační události. Pokud dojde k této aktivační události, budeme nazývat **získat obsah souboru pomocí cesty** akci Dropboxu. 

1. Zadejte *dropboxu* do vyhledávacího pole v návrháři pro Logic Apps, vyberte **Dropbox – při vytvoření souboru** aktivační události.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Vyberte složku, ve které chcete sledovat vytváření souborů. Vyberte... (označená červeným rámečkem) a přejděte do složky, kterou chcete vybrat pro aktivační událost se uživatelovo zadání.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Použít akci Dropboxu
Akce je operace prováděné pracovním postupu definovaném v aplikaci logiky. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Teď, když se přidala aktivační událost, použijte následující postup přidání akce, která se zobrazí nový soubor obsahu.

1. Vyberte **+ nový krok** přidáte akci chcete provést, když je vytvořen nový soubor.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Vyberte **přidat akci**. Tato otevře vyhledávací pole, kde můžete vyhledat jakoukoli akci jste chtěli provést.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Zadejte *dropboxu* hledání pro akce související s Dropboxu.  
4. Vyberte **Dropbox - získat obsah souboru pomocí cesty** jako akce má být provedena, když je vytvořen nový soubor do vybrané složky na Dropboxu. Otevře se řídicí blok akce. Zobrazí výzva k autorizaci aplikace logiky pro přístup k účtu Dropbox, pokud jste tak dosud neučinili.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Vyberte... (na pravé straně **cesta k souboru** ovládací prvek) a přejděte na cestu k souboru by chtěl používat. Nebo můžete použít **cesta k souboru** tokenu, jak zrychlit vaši vytváření aplikací logiky.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Uložte si práci a vytvořte nový soubor v Dropboxu k aktivaci pracovního postupu.  

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/dropbox/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).
