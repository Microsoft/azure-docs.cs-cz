---
title: Připojení k uživatelům Office 365
description: Správa profilů uživatelů pomocí rozhraní REST API uživatelů Office 365 a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: addb64a9b43c51af8363caa6f0fb3261a618e893
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789511"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Začínáme s konektorem uživatelů Office 365
Připojte se k uživatelům Office 365, abyste získali profily, mohli hledat uživatele a další. Uživatelé Office 365 můžou:

* Sestavte svůj obchodní tok na základě dat, která dostanete od uživatelů Office 365. 
* Použijte akce, které získají přímé sestavy, Získejte uživatelský profil manažera a další. Tyto akce obdrží odpověď a následně zpřístupní výstup pro jiné akce. Můžete například získat přímé podřízené osoby a následně tyto informace a aktualizace databáze SQL Azure. 

Můžete začít vytvořením aplikace logiky hned teď v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Vytvoření připojení k uživatelům Office 365
Když přidáte tento konektor do aplikace logiky, musíte se přihlásit ke svému účtu uživatelů Office 365 a dovolit službě Logic Apps, aby se připojili k vašemu účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po vytvoření připojení zadáte vlastnosti uživatelů Office 365, jako je ID uživatele. Tyto vlastnosti popisuje **odkaz na REST API** v tomto článku.

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Zobrazit všechny triggery a akce definované v Swagger a také zobrazit omezení v [podrobnostech konektoru](/connectors/officeusers/).

## <a name="more-connectors"></a>Další konektory
Vraťte se do [seznamu rozhraní API](apis-list.md).