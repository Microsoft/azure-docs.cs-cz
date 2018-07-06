---
title: Použití konektoru pro SharePoint Server v Logic Apps | Dokumentace Microsoftu
description: Začínáme s využitím konektoru serveru SharePoint ve službě Logic apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 3ac3105231017cdbf8bfcf143b26451a68da5283
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868697"
---
# <a name="get-started-with-the-sharepoint-connector"></a>Začínáme s konektorem služby SharePoint
Konektor SharePoint poskytuje způsob, jak fungují se seznamy v Sharepointu.

Začněte vytvořením aplikace logiky; Zobrazit [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Vytvoření připojení k Sharepointu
K používání konektoru služby SharePoint, je třeba nejprve vytvořit **připojení** pak zadejte podrobnosti o těchto vlastností: 

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| Podpisový |Ano |Zadejte přihlašovací údaje k SharePointu |

Pro připojení k **SharePoint**, zadejte svou identitu (uživatelské jméno a heslo, pověření čipové karty a tak dále). Po ověření, můžete přejít k používání konektoru služby SharePoint v aplikaci logiky. 

V návrháři aplikace logiky pomocí následujícího postupu přihlásit a vytvořit **připojení** pro použití ve vaší aplikaci logiky:

1. Do vyhledávacího pole zadejte SharePoint a počkejte, hledání vrátí všechny záznamy se Sharepointem v názvu:   
   ![Konfigurace Sharepointu][1]  
2. Vyberte **SharePoint – když se vytvoří soubor**   
3. Vyberte **přihlášení do Sharepointu**:   
   ![Konfigurace Sharepointu][2]    
4. Zadejte svoje přihlašovací údaje služby SharePoint pro přihlášení k ověření pomocí služby SharePoint   
   ![Konfigurace Sharepointu][3]     
5. Až se ověřování dokončí, budete přesměrováni do aplikace logiky k dokončení konfigurace Sharepointu **při vytvoření souboru** dialogového okna.          
   ![Konfigurace Sharepointu][4]  
6. Potom můžete přidat další aktivační události a akce, které potřebujete k dokončení vaší aplikace logiky.   
7. Uložte svou práci tak, že vyberete **Uložit** v nabídce (směrem nahoru).

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/sharepoint/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
