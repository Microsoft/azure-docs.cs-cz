---
title: Připojení k uživatelům Office 365
description: Automatizace úloh a pracovních postupů, které získávají a spravují profily v profilech uživatelů Office 365 pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666852"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Získání a Správa profilů v uživatelích Office 365 pomocí Azure Logic Apps

Připojte se k uživatelům Office 365, abyste získali profily, mohli hledat uživatele a další. Uživatelé Office 365 můžou:

* Sestavte svůj obchodní tok na základě dat, která dostanete od uživatelů Office 365. 
* Použijte akce, které získají přímé sestavy, Získejte uživatelský profil manažera a další. Tyto akce obdrží odpověď a následně zpřístupní výstup pro jiné akce. Můžete například získat přímé podřízené osoby a následně tyto informace a aktualizace databáze SQL Azure. 

Můžete začít vytvořením aplikace logiky hned teď v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Vytvoření připojení k uživatelům Office 365

Když přidáte tento konektor do aplikace logiky, musíte se přihlásit ke svému účtu uživatelů Office 365, aby se Azure Logic Apps mohl připojit k vašemu účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po vytvoření připojení zadáte vlastnosti uživatelů Office 365, jako je ID uživatele. Tyto vlastnosti popisuje **odkaz na REST API** v tomto článku.

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/officeusers/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](apis-list.md)