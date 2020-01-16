---
title: Správa oprávnění databáze v Azure Průzkumník dat
description: Tento článek popisuje řízení přístupu na základě rolí pro databáze a tabulky v Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030091"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Správa oprávnění pro databázi Azure Průzkumník dat

Azure Průzkumník dat umožňuje řídit přístup k databázím a tabulkám pomocí modelu *řízení přístupu založeného na rolích* . V tomto modelu jsou *objekty zabezpečení* (uživatelé, skupiny a aplikace) namapovány na *role*. Objekty zabezpečení mají přístup k prostředkům v závislosti na rolích, které jsou přiřazeny.

Tento článek popisuje dostupné role a postup přiřazení objektů zabezpečení k těmto rolím pomocí příkazů Azure Portal a Azure Průzkumník dat Management.

## <a name="roles-and-permissions"></a>Role a oprávnění

Azure Průzkumník dat má následující role:

|Role                       |Oprávnění                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Správce databáze             |Může provádět cokoli v oboru konkrétní databáze.|
|Uživatel databáze              |Může číst všechna data a metadata v databázi. Kromě toho mohou vytvořit tabulky (stanou se správcem tabulky pro tuto tabulku) a funkce v databázi.|
|Prohlížeč databáze            |Může číst všechna data a metadata v databázi.|
|Ingestování databáze          |Může ingestovat data do všech existujících tabulek v databázi, ale nedotazovat data.|
|Monitorování databáze           |Dá se spustit. show... příkazy v kontextu databáze a jejích podřízených entit.|
|Správce tabulky                |Může provádět cokoli v oboru konkrétní tabulky. |
|Ingestování tabulek             |Může ingestovat data v oboru konkrétní tabulky, ale nedotazovat data.|

## <a name="manage-permissions-in-the-azure-portal"></a>Správa oprávnění v Azure Portal

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. Přejděte do clusteru Azure Průzkumník dat.

1. V části **Přehled** vyberte databázi, ve které chcete spravovat oprávnění.

    ![Výběr databáze](media/manage-database-permissions/select-database.png)

1. Vyberte **oprávnění** a pak **Přidat**.

    ![Databázová oprávnění](media/manage-database-permissions/database-permissions.png)

1. V části **Přidat oprávnění databáze**vyberte roli, pro kterou chcete objekt zabezpečení přiřadit, a pak **Vyberte objekty zabezpečení**.

    ![Přidat databázová oprávnění](media/manage-database-permissions/add-permission.png)

1. Vyhledejte objekt zabezpečení, vyberte ho a pak **Vyberte**.

    ![Správa oprávnění v Azure Portal](media/manage-database-permissions/new-principals.png)

1. Vyberte **Uložit**.

    ![Správa oprávnění v Azure Portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Správa oprávnění s příkazy pro správu

1. Přihlaste se k [https://dataexplorer.azure.com](https://dataexplorer.azure.com)a přidejte svůj cluster, pokud ještě není dostupný.

1. V levém podokně vyberte příslušnou databázi.

1. Pomocí příkazu `.add` přiřaďte k rolím objekty zabezpečení: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Chcete-li přidat uživatele do role uživatele databáze, spusťte následující příkaz, který nahradí název databáze a uživatele.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Výstup příkazu zobrazuje seznam existujících uživatelů a rolí, k nimž se přiřadí v databázi.
    
    Příklady týkající se Azure Active Directory a autorizačního modelu Kusto najdete v tématu [Principy a zprostředkovatelé identity](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers) .

## <a name="next-steps"></a>Další kroky

[Zápis dotazů](write-queries.md)
