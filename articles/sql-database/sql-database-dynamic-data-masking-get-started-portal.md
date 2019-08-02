---
title: 'Azure Portal: SQL Database Maskování dynamických dat | Microsoft Docs'
description: Jak začít s SQL Database dynamické maskování dat v Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: 1400a21c3fee51bb26a3271546a7553a3429b42d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568788"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Začínáme s SQL Database dynamické maskování dat s využitím Azure Portal

V tomto článku se dozvíte, jak implementovat [dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md) pomocí Azure Portal. Dynamické maskování dat můžete také implementovat pomocí [rutin Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) nebo [REST API](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí Azure Portal

1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com).
2. Přejděte na stránku nastavení databáze, která obsahuje citlivá data, která chcete maskovat.
3. Klikněte na dlaždici **Maskování dynamických dat** , která spustí stránku konfigurace **dynamického maskování dat** .

   * Případně můžete přejít dolů na část **operace** a kliknout na **dynamické maskování dat**.

     ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Na stránce konfigurace **dynamického maskování dat** se může zobrazit několik databázových sloupců, které modul doporučení pro maskování označil. Pokud chcete přijmout doporučení, stačí kliknout na **Přidat masku** pro jeden nebo více sloupců a na základě výchozího typu pro tento sloupec se vytvoří maska. Funkci maskování můžete změnit kliknutím na pravidlo maskování a úpravou formátu pole maskování na jiný formát podle vašeho výběru. Nezapomeňte uložit nastavení kliknutím na **Uložit** .

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Chcete-li přidat masku pro libovolný sloupec v databázi, klikněte v horní části stránky konfigurace **dynamického maskování dat** na tlačítko **Přidat masku** a otevřete konfigurační stránku **Přidat pravidlo maskování** .

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Vyberte **schéma**, **tabulku** a **sloupec** pro definování určeného pole pro maskování.
7. V seznamu kategorií pro maskování citlivých dat vyberte **formát pole maskování** .

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Pokud chcete aktualizovat sadu pravidel maskování v zásadách dynamického maskování dat, klikněte na **Uložit** na stránce pravidlo maskování dat.
9. Zadejte uživatele SQL nebo identity AAD, které se mají vyloučit z maskování, a přístup k nemaskováným citlivým datům. Mělo by se jednat o středníkem oddělený seznam uživatelů. Uživatelé s oprávněními správce mají vždy přístup k původním nemaskováným datům.

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Aby mohla vrstva aplikace zobrazit citlivá data pro privilegované uživatele aplikace, přidejte uživatele SQL nebo identitu AAD, které aplikace používá pro dotazování databáze. Důrazně doporučujeme, aby tento seznam obsahoval minimální počet privilegovaných uživatelů, aby bylo možné minimalizovat expozici citlivých dat.

10. Kliknutím na **Uložit** na stránce konfigurace maskování dat uložíte nové nebo aktualizované zásady maskování.

## <a name="next-steps"></a>Další kroky

* Přehled dynamického maskování dat najdete v tématu [dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md).
* Dynamické maskování dat můžete také implementovat pomocí [rutin Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) nebo [REST API](https://docs.microsoft.com/rest/api/sql/).
