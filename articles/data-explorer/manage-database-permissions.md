---
title: Spravovat oprávnění k databázi Průzkumníka dat služby Azure
description: Tento článek popisuje řízení přístupu na základě rolí pro databáze a tabulky v Průzkumníku dat Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b0fb2cd79c0ee685ad2000c5444a84d53e462f24
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868271"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Spravovat oprávnění k databázi Průzkumníka dat služby Azure

Průzkumník služby Azure Data vám umožňuje řídit přístup k databází a tabulek, použití *řízení přístupu na základě rolí* modelu. V rámci tohoto modelu *objekty zabezpečení* (uživatelé, skupiny a aplikace) se mapují na *role*. Objekty zabezpečení přístup k prostředkům podle rolí, které jste přiřadili.

Tento článek popisuje dostupné role a přiřazení k rolím objekty zabezpečení.

## <a name="roles-and-permissions"></a>Role a oprávnění

Průzkumník služby Azure Data má následující role:

|Role                       |Oprávnění                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Správce databáze             |Můžete provádět v rámci konkrétní databáze.|
|Uživatel databáze              |Může číst všechna data a metadata v databázi. Kromě toho může vytvořit tabulky (stát správce tabulky pro tabulku) a funkce v databázi.|
|Databáze prohlížeče            |Může číst všechna data a metadata v databázi.|
|Přijímač databáze          |Můžete ingestovat data do všech existujících tabulek v databázi, ale ne zadávat dotazy na data.|
|Monitorování databáze           |Můžete spustit příkazy .show... v kontextu databáze a její podřízené entity.|
|Správce tabulku                |Můžete provádět v rámci konkrétní tabulku. |
|Přijímač tabulky             |Můžete ingestovat data v rámci určité tabulce, ale ne zadávat dotazy na data.|

## <a name="manage-permissions-in-the-azure-portal"></a>Spravovat oprávnění na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Přejděte ke svému clusteru Průzkumník dat Azure.

1. V **přehled** části, vyberte databázi, ve které chcete spravovat oprávnění.

    ![Vyberte databázi](media/manage-database-permissions/select-database.png)

1. Vyberte **oprávnění** pak **přidat**.

    ![Oprávnění k databázi](media/manage-database-permissions/database-permissions.png)

1. V části **přidat databázi oprávnění**, vyberte roli, kterou chcete přiřadit objektu zabezpečení, pak **vyberte objekty zabezpečení**.

    ![Přidejte oprávnění k databázi](media/manage-database-permissions/add-permission.png)

1. Vyhledání objektu zabezpečení, vyberte ho, pak **vyberte**.

    ![Spravovat oprávnění na webu Azure Portal](media/manage-database-permissions/new-principals.png)

1. Vyberte **Uložit**.

    ![Spravovat oprávnění na webu Azure Portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Spravovat oprávnění s příkazy pro správu

1. Přihlaste se do [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com)a přidat do clusteru, pokud již není k dispozici.

1. V levém podokně vyberte příslušné databáze.

1. Použití `.add` příkazu přiřaďte objekty zabezpečení k rolím: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Chcete-li přidat uživatele k roli uživatele databáze, spusťte následující příkaz, kde nahradíte název databáze a uživatele.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Výstup příkazu se zobrazí seznam stávajících uživatelů a rolí, které jsou přiřazení v databázi.

## <a name="next-steps"></a>Další postup

[Zápis dotazů](write-queries.md)
