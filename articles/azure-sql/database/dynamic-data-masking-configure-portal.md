---
title: 'Azure Portal: dynamické maskování dat'
description: Jak začít s Azure SQL Database dynamické maskování dat v Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: e551925bbd1426256a81ac2ca3f245af7697245b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028008"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Začínáme s SQL Database dynamické maskování dat s využitím Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto článku se dozvíte, jak implementovat [dynamické maskování dat](dynamic-data-masking-overview.md) pomocí Azure Portal. Dynamické maskování dat můžete také implementovat pomocí [rutin Azure SQL Database](/powershell/module/az.sql/) nebo [REST API](/rest/api/sql/).

> [!NOTE]
> Tuto funkci nelze nastavit pomocí portálu pro spravovanou instanci SQL (použijte PowerShell nebo REST API). Další informace najdete v tématu [dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí Azure Portal

1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com) .
2. Přejděte na stránku nastavení databáze, která obsahuje citlivá data, která chcete maskovat.
3. V části **zabezpečení** databáze klikněte na okno **Maskování dynamických dat** .

   ![Snímek obrazovky, který zobrazuje oddíl zabezpečení s zvýrazněnou možností dynamického maskování dat](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. Na stránce konfigurace **dynamického maskování dat** se může zobrazit několik databázových sloupců, které modul doporučení pro maskování označil. Pokud chcete přijmout doporučení, stačí kliknout na **Přidat masku** pro jeden nebo více sloupců a na základě výchozího typu pro tento sloupec se vytvoří maska. Funkci maskování můžete změnit kliknutím na pravidlo maskování a úpravou formátu pole maskování na jiný formát podle vašeho výběru. Nezapomeňte uložit nastavení kliknutím na **Uložit** .

    ![Snímek obrazovky zobrazující stránku konfigurace dynamického maskování dat](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Chcete-li přidat masku pro libovolný sloupec v databázi, klikněte v horní části stránky konfigurace **dynamického maskování dat** na tlačítko **Přidat masku** a otevřete konfigurační stránku **Přidat pravidlo maskování** .

    ![Snímek obrazovky zobrazující stránku konfigurace pravidla maskování](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Vyberte **schéma**, **tabulku** a **sloupec** pro definování určeného pole pro maskování.
7. **Vyberte, jak se má maskovat** ze seznamu kategorií pro maskování citlivých dat.

    ![Snímek obrazovky zobrazující kategorie citlivých dat v části Výběr způsobu maskování](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Kliknutím na **Přidat** na stránce pravidlo maskování dat aktualizujete sadu pravidel maskování v zásadách dynamického maskování dat.
9. Zadejte identity uživatelů a Azure Active Directory (Azure AD) SQL, které se mají vyloučit z maskování a mají přístup k nemaskováným citlivým datům. Mělo by se jednat o středníkem oddělený seznam uživatelů. Uživatelé s oprávněními správce mají vždy přístup k původním nemaskováným datům.

    ![Navigační podokno](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Aby mohla vrstva aplikace zobrazit citlivá data pro privilegované uživatele aplikace, přidejte uživatele SQL nebo identitu Azure AD, kterou aplikace používá k dotazování databáze. Důrazně doporučujeme, aby tento seznam obsahoval minimální počet privilegovaných uživatelů, aby bylo možné minimalizovat expozici citlivých dat.

10. Kliknutím na **Uložit** na stránce konfigurace maskování dat uložíte nové nebo aktualizované zásady maskování.

## <a name="next-steps"></a>Další kroky

- Přehled dynamického maskování dat najdete v tématu [dynamické maskování dat](dynamic-data-masking-overview.md).
- Dynamické maskování dat můžete také implementovat pomocí [rutin Azure SQL Database](/powershell/module/az.sql/) nebo [REST API](/rest/api/sql/).
