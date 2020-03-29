---
title: 'Portál Azure: Dynamické maskování dat'
description: Jak začít s maskováním dynamických dat databáze SQL na webu Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722114"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Začínáme s dynamickým zamaskováním dat sql database s portálem Azure

Tento článek ukazuje, jak implementovat [dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md) s portálem Azure. Dynamické maskování dat můžete také implementovat pomocí [rutin Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) nebo rozhraní REST [API](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Nastavení dynamického maskování dat pro databázi pomocí portálu Azure

1. Spusťte portál [https://portal.azure.com](https://portal.azure.com)Azure na webu .
2. Přejděte na stránku nastavení databáze, která obsahuje citlivá data, která chcete maskovat.
3. Klikněte na dlaždici **Dynamické maskování dat,** která spouští konfigurační stránku **Dynamické maskování dat.**

   * Případně se můžete posunout dolů do části **Operace** a klepnout na **dynamické maskování dat**.

     ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Na stránce konfigurace **dynamické hořáčné maskování** se mohou zobrazit některé sloupce databáze, které modul doporučení označil pro maskování. Chcete-li doporučení přijmout, stačí kliknout na **přidat masku** pro jeden nebo více sloupců a maska se vytvoří na základě výchozího typu pro tento sloupec. Funkci maskování můžete změnit kliknutím na pravidlo maskování a úpravou formátu maskovacího pole do jiného zvoleného formátu. Chcete-li uložit nastavení, klepněte na tlačítko **Uložit.**

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Pokud chcete přidat masku pro libovolný sloupec v databázi, otevřete v horní části konfigurační stránky **Dynamické masky** možnost **Přidat masku** a otevřete stránku Konfigurace **pravidla masky.**

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Vyberte **schéma**, **tabulku** a **sloupec** a definujte určené pole pro maskování.
7. Ze seznamu kategorií maskování citlivých dat zvolte **formát maskovacího pole.**

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Kliknutím na **Uložit** na stránce pravidla maskování dat aktualizujte sadu pravidel maskování v zásadách maskování dynamických dat.
9. Zadejte uživatele SQL nebo identity AAD, které by měly být vyloučeny z maskování a mají přístup k nemaskovaným citlivým datům. Měl by se jedná o seznam uživatelů oddělený středníkem. Uživatelé s oprávněními správce mají vždy přístup k původním nemaskovaným datům.

    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Chcete-li, aby aplikační vrstva mohla zobrazovat citlivá data pro uživatele s oprávněními aplikací, přidejte identitu uživatele SQL nebo identity AAD, kterou aplikace používá k dotazování na databázi. Důrazně doporučujeme, aby tento seznam obsahoval minimální počet privilegovaných uživatelů, aby se minimalizovala expozice citlivých dat.

10. Kliknutím na **Uložit** na stránce konfigurace maskování dat uložte novou nebo aktualizovanou zásadu maskování.

## <a name="next-steps"></a>Další kroky

* Přehled maskování dynamických dat naleznete v tématu [dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md).
* Dynamické maskování dat můžete také implementovat pomocí [rutin Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) nebo rozhraní REST [API](https://docs.microsoft.com/rest/api/sql/).
