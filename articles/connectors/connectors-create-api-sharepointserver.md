---
title: "Pomocí konektoru serveru SharePoint Server ve vašich Logic Apps | Microsoft Docs"
description: "Začněte používat konektor Server služby SharePoint ve vašich Logic apps"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: d342b3c4f84c5dab212b9327d6a72759934d0ae5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sharepoint-connector"></a>Začínáme s konektorem služby SharePoint
Konektor služby SharePoint poskytuje způsob, jak pracovat s seznamy služby SharePoint.

Začněte vytvořením aplikace logiky; v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Umožňuje vytvořit připojení do služby SharePoint
K používání konektoru služby SharePoint, je třeba nejprve vytvořit **připojení** pak zadejte podrobnosti pro tyto vlastnosti: 

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| Podpisový |Ano |Zadejte pověření serveru SharePoint |

Pro připojení k **SharePoint**, zadejte svoji identitu (uživatelské jméno a heslo, pověření čipové karty a tak dále). Jakmile došlo k ověření, můžete přejít k používání konektoru služby SharePoint v aplikaci logiky. 

V návrháři aplikace logiky, použijte následující postup k přihlášení a vytvořte **připojení** pro použití v aplikaci logiky:

1. Zadejte do pole vyhledávání služby SharePoint a počkat na výsledky vyhledávání na vrátí všechny položky se službou SharePoint v názvu:   
   ![Konfigurace služby SharePoint][1]  
2. Vyberte **SharePoint – když dojde k vytvoření souboru**   
3. Vyberte **přihlásit do služby SharePoint**:   
   ![Konfigurace služby SharePoint][2]    
4. Zadejte svoje přihlašovací údaje služby SharePoint pro přihlášení k ověření pomocí služby SharePoint   
   ![Konfigurace služby SharePoint][3]     
5. Po dokončení ověření budete přesměrováni na svou aplikaci logiky dokončit konfigurací služby SharePoint na **vytvoření souboru** dialogové okno.          
   ![Konfigurace služby SharePoint][4]  
6. Poté můžete přidat další aktivační události a akce, které potřebujete k dokončení svou aplikaci logiky.   
7. Uložte si práci, výběrem **Uložit** v nabídce (směrem nahoru).

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/sharepoint/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
