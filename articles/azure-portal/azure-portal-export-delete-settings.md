---
title: Export nebo odstranění nastavení Azure Portal
description: Přečtěte si, jak můžete exportovat nebo odstranit uživatelská nastavení, soukromé řídicí panely a vlastní nastavení v Azure Portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900740"
---
# <a name="export-or-delete-user-settings"></a>Export nebo odstranění uživatelských nastavení

Pomocí nastavení a funkcí v Azure Portal můžete vytvořit vlastní prostředí. Informace o vlastním nastavení jsou uložené v Azure. Můžete exportovat nebo odstranit následující uživatelská data:

* Soukromé řídicí panely v Azure Portal
* Uživatelská nastavení, jako jsou oblíbená předplatná nebo adresáře a poslední přihlášený adresář
* Motivy a další vlastní nastavení portálu

Před odstraněním je vhodné nastavení exportovat a zkontrolovat. Opětovné sestavení řídicích panelů nebo opakované provádění vlastních nastavení může být časově náročné.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Export nebo odstranění nastavení portálu

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V záhlaví portálu vyberte ikonu nastavení ![](media/azure-portal-export-delete-settings/settings-icon.png) **Nastavení**.

1. Vyberte **exportovat všechna nastavení** nebo **Odstranit všechna nastavení a privátní řídicí panely**.

    ![Nastavení Azure Portal a možnosti nastavení](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Následující tabulka popisuje tyto akce.

      | Akce | Popis |
      | --- | --- |
      | **Exportovat všechna nastavení** | Vytvoří soubor *. JSON* , který obsahuje vaše uživatelská nastavení, jako je váš barevný motiv, oblíbené a soukromé řídicí panely.|
      | **Odstranit všechna nastavení a privátní řídicí panely** | Odstraní všechny odkazy na soukromé řídicí panely a další vlastní nastavení, která jste na portálu udělali. |

> [!NOTE]
> Z důvodu dynamické povahy uživatelských nastavení a rizika poškození dat nemůžete importovat nastavení ze souboru *. JSON* .
>
>

## <a name="next-steps"></a>Další kroky

* [Sdílení řídicích panelů Azure pomocí Access Control na základě rolí](azure-portal-dashboard-share-access.md)
* [Přidání, odebrání a změna uspořádání oblíbených položek](azure-portal-add-remove-sort-favorites.md)
