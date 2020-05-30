---
title: Připojení k uživatelům Office 365
description: Automatizace úloh a pracovních postupů, které získávají a spravují profily v profilech uživatelů Office 365 pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 0361d42ea614b394142e32a9193ab1d48d1a4a6c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194241"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Získání a Správa profilů v uživatelích Office 365 pomocí Azure Logic Apps

Připojte se k uživatelům Office 365, abyste získali profily, mohli hledat uživatele a další. Uživatelé Office 365 můžou:

* Sestavte svůj obchodní tok na základě dat, která dostanete od uživatelů Office 365. 
* Použijte akce, které získají přímé sestavy, Získejte uživatelský profil manažera a další. Tyto akce obdrží odpověď a následně zpřístupní výstup pro jiné akce. Můžete například získat přímé podřízené osoby a následně tyto informace a aktualizaci databáze v Azure SQL Database. 

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