---
title: Správa oprávnění k databázím v Azure Data Exploreru
description: Tento článek popisuje ovládací prvky přístupu založené na rolích pro databáze a tabulky v Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030091"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Správa oprávnění k databázi Aplikace Azure Data Explorer

Azure Data Explorer umožňuje řídit přístup k databázím a tabulkám pomocí modelu *řízení přístupu založeného na rolích.* V rámci tohoto modelu *objekty* (uživatelé, skupiny a aplikace) jsou mapovány na *role*. Objekty zabezpečení mohou přistupovat k prostředkům podle rolí, které jsou přiřazeny.

Tento článek popisuje dostupné role a jak přiřadit objekty zabezpečení k těmto rolím pomocí portálu Azure a příkazů pro správu Průzkumníka dat Azure.

## <a name="roles-and-permissions"></a>Role a oprávnění

Azure Data Explorer má následující role:

|Role                       |Oprávnění                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Správce databáze             |Může dělat cokoliv v oboru konkrétní databáze.|
|Uživatel databáze              |Může číst všechna data a metadata v databázi. Kromě toho mohou vytvářet tabulky (stát se správcem tabulky pro tuto tabulku) a funkce v databázi.|
|Prohlížeč databáze            |Může číst všechna data a metadata v databázi.|
|Databázový ingestátor          |Může ingestovat data do všech existujících tabulek v databázi, ale není dotaz na data.|
|Monitor databáze           |Může spustit '.show ...' příkazy v kontextu databáze a jejích podřízených entit.|
|Správce tabulky                |Může dělat cokoliv v rozsahu konkrétní tabulky. |
|Tabulka ingestor             |Může ingestovat data v oboru konkrétní tabulky, ale není dotaz na data.|

## <a name="manage-permissions-in-the-azure-portal"></a>Správa oprávnění na webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Přejděte do clusteru Průzkumníka dat Azure.

1. V části **Přehled** vyberte databázi, ve které chcete spravovat oprávnění.

    ![Výběr databáze](media/manage-database-permissions/select-database.png)

1. Vyberte **oprávnění** a pak **přidejte**.

    ![Oprávnění databáze](media/manage-database-permissions/database-permissions.png)

1. V části **Přidat oprávnění k databázi**vyberte roli, ke které chcete přiřadit objekt zabezpečení, a **vyberte objekty zabezpečení**.

    ![Přidání oprávnění k databázi](media/manage-database-permissions/add-permission.png)

1. Vyhledejte objekt zabezpečení, vyberte ho a pak **vyberte**.

    ![Správa oprávnění na webu Azure Portal](media/manage-database-permissions/new-principals.png)

1. Vyberte **Uložit**.

    ![Správa oprávnění na webu Azure Portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Správa oprávnění pomocí příkazů pro správu

1. Přihlaste [https://dataexplorer.azure.com](https://dataexplorer.azure.com)se k aplikaci a přidejte cluster, pokud ještě není k dispozici.

1. V levém podokně vyberte příslušnou databázi.

1. Pomocí `.add` příkazu přiřaďte `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`objekty k rolím: . Chcete-li přidat uživatele do role uživatele databáze, spusťte následující příkaz a napočtete název databáze a uživatele.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Výstup příkazu zobrazuje seznam existujících uživatelů a role, ke kterým jsou přiřazeni v databázi.
    
    Příklady týkající se Služby Azure Active Directory a modelu autorizace Kusto naleznete v [tématu Zásady a poskytovatelé identit](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers)

## <a name="next-steps"></a>Další kroky

[Zápis dotazů](write-queries.md)
