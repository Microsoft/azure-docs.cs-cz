---
title: Export nebo odstranění nastavení portálu Azure
description: Zjistěte, jak můžete exportovat nebo odstranit uživatelská nastavení, soukromé řídicí panely a vlastní nastavení na webu Azure Portal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900740"
---
# <a name="export-or-delete-user-settings"></a>Export nebo odstranění uživatelských nastavení

Pomocí nastavení a funkcí na webu Azure Portal můžete vytvořit vlastní prostředí. Informace o vašich vlastních nastaveních se ukládají v Azure. Můžete exportovat nebo odstranit následující uživatelská data:

* Privátní řídicí panely na webu Azure Portal
* Uživatelská nastavení, jako jsou oblíbená předplatná nebo adresáře a poslední přihlášený adresář
* Motivy a další vlastní nastavení portálu

Před odstraněním nastavení je vhodné nastavení exportovat a zkontrolovat. Opětovné sestavení řídicích panelů nebo opakování vlastních nastavení může být časově náročné.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Export nebo odstranění nastavení portálu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V záhlaví portálu vyberte ![](media/azure-portal-export-delete-settings/settings-icon.png) ikonu nastavení **Nastavení**.

1. Vyberte **Exportovat všechna nastavení** nebo **Odstranit všechna nastavení a soukromé řídicí panely**.

    ![Nastavení a nastavení portálu Azure](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Následující tabulka popisuje tyto akce.

      | Akce | Popis |
      | --- | --- |
      | **Exportovat všechna nastavení** | Vytvoří soubor *JSON,* který obsahuje uživatelská nastavení, jako je barevný motiv, oblíbené položky a soukromé řídicí panely.|
      | **Odstranění všech nastavení a soukromých řídicích panelů** | Odstraní všechny odkazy na soukromé řídicí panely a další vlastní nastavení, která jste na portálu vytvořili. |

> [!NOTE]
> Vzhledem k dynamické povaze uživatelských nastavení a riziku poškození dat nelze importovat nastavení ze souboru *JSON.*
>
>

## <a name="next-steps"></a>Další kroky

* [Sdílení řídicích panelů Azure prostřednictvím Řízení přístupu na základě role](azure-portal-dashboard-share-access.md)
* [Přidání, odebrání a změna uspořádání oblíbených položek](azure-portal-add-remove-sort-favorites.md)
