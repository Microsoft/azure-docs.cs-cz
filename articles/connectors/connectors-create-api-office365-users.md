---
title: Připojení k uživatelům Office 365
description: Automatizace úloh a pracovních postupů, které získají a spravují profily v profilech uživatelů Office 365 pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666852"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Získání a správa profilů v uživatelích Office 365 pomocí Azure Logic Apps

Připojte se k uživatelům Office 365 a získejte profily, uživatele vyhledávání a další. S uživateli Office 365 můžete:

* Sestavte si svůj obchodní tok na základě dat, která získáte od uživatelů Office 365. 
* Používejte akce, které získají přímé sestavy, získají uživatelský profil manažera a další. Tyto akce získat odpověď a potom zpřístupnit výstup pro jiné akce. Můžete například získat přímé sestavy osoby a pak tyto informace a aktualizovat databázi SQL Azure. 

Můžete začít vytvořením aplikace logiky teď, najdete v [tématu Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Vytvoření připojení k uživatelům Office 365

Když přidáte tento konektor do svých aplikací logiky, musíte se přihlásit ke svému účtu Uživatelů Office 365, aby se Azure Logic Apps mohly připojit k vašemu účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po vytvoření připojení zadáte vlastnosti Office 365 Users, jako je ID uživatele. **Odkaz rozhraní REST API** v tomto článku popisuje tyto vlastnosti.

## <a name="connector-specific-details"></a>Podrobnosti specifické pro konektor

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu swagger konektoru, zkontrolujte [referenční stránku konektoru](/connectors/officeusers/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](apis-list.md)