---
title: Připojení k Office 365 uživatelé – Azure Logic Apps | Microsoft Docs
description: Spravovat profily uživatelů pomocí Office 365 uživatelé REST API a Azure Logic Apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 92eaa2f323da9a96b0be4379d2dbcea8d90733e4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609424"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Začínáme s konektorem uživatelé Office 365.
Připojte k Office 365 uživatelům profily, uživatelé vyhledávající a další. Uživatelé služeb Office 365 můžete:

* Sestavení vaší firmy toku na základě dat, které můžete získat z uživatelé služeb Office 365. 
* Použití akce, které získají přímé podřízené načíst profil uživatele správce a další. Tyto akce se odpověď a pak proveďte výstup k dispozici pro další akce. Například získat přímé podřízené osoby a pak proveďte tyto informace a aktualizace databáze SQL Azure. 

Můžete začít s vytvářením aplikace logiky teď najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Umožňuje vytvořit připojení k uživatelé Office 365.
Když přidáte tento konektor aplikace logiky, musíte přihlásit ke svému účtu Office 365 uživatelů a povolit logic apps, abyste připojení k vašemu účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po vytvoření připojení, zadáte vlastnosti uživatelé služeb Office 365, jako je ID uživatele. **Odkazu k REST API** v tomto článku popisuje tyto vlastnosti.

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/officeusers/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).