---
title: "Portál Azure: maskování dynamická data databáze SQL | Microsoft Docs"
description: "Jak začít pracovat s dynamickými daty maskování SQL Database na portálu Azure"
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 11/22/2016
ms.author: ronitr
ms.openlocfilehash: 2db546d8165956dc6f9fad21f66af315ba301c11
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Začínáme s dynamickými daty SQL Database maskování pomocí portálu Azure

Tento článek ukazuje, jak implementovat [dynamická data maskování](sql-database-dynamic-data-masking-get-started.md) pomocí portálu Azure. Můžete taky implementovat pomocí maskování dynamická data [rutiny Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084.aspx) nebo [REST API](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Nastavit dynamické maskování dat pro vaši databázi pomocí portálu Azure
1. Spuštění portálu Azure v [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku nastavení databáze, která obsahuje důvěrné osobní údaje, které chcete k maskování.
3. Klikněte na tlačítko **dynamického maskování dat** dlaždici, která spustí **dynamického maskování dat** stránku konfigurace.
   
   * Případně přejděte dolů na **operace** části a klikněte na tlačítko **dynamického maskování dat**.
     
     ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. V **dynamického maskování dat** stránku konfigurace, mohou se zobrazit některé databáze sloupce, které má modul doporučení označeny k maskování. Aby bylo možné přijímat doporučení, stačí kliknout na **přidat masku** pro jeden nebo více sloupců a masky je vytvořen na základě typu výchozí pro tento sloupec. Funkci maskování můžete změnit tak, že kliknete na pravidlo maskování úpravy maskování pole formátu do jiného formátu podle svého výběru. Ujistěte se, klikněte na **Uložit** uložte nastavení.
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Chcete-li přidat masku pro všechny sloupce v databázi, v horní části **dynamického maskování dat** stránku konfigurace, klikněte na tlačítko **přidat masku** otevřete **přidat pravidlo maskování** stránku konfigurace .
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Vyberte **schématu**, **tabulky** a **sloupec** k určené pole k maskování.
7. Vyberte **maskování formátu pole** ze seznamu citlivých dat maskování kategorií.
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Klikněte na tlačítko **Uložit** v datech maskování pravidlo stránku a aktualizujte sadu maskování pravidla v dynamické maskování zásad dat.
9. Zadejte uživatele SQL nebo AAD identity, které mají být vyloučeny z maskování a mají přístup k odmaskovaná citlivá data. To by měl být středníky oddělený seznam uživatelů. Uživatelé s oprávněním správce měla vždycky přístup k původní odmaskovaná data.
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Chcete-li tak aplikační vrstvu můžete zobrazit citlivá data pro aplikace privilegovaný uživatele, přidání uživatele SQL nebo identita AAD aplikace používá k dotazování databáze. Důrazně doporučujeme, aby tento seznam obsahovat minimální počet mohou uživatelé s oprávněním k minimalizaci ohrožení citlivých dat.
   > 
   > 
10. Klikněte na tlačítko **Uložit** v maskování konfigurační stránku pro uložení nových nebo aktualizovaných dat maskování zásad.


## <a name="next-steps"></a>Další postup

* Přehled maskování dynamickými daty naleznete v tématu [dynamická data maskování](sql-database-dynamic-data-masking-get-started.md).
* Můžete taky implementovat pomocí maskování dynamická data [rutiny Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084.aspx) nebo [REST API](https://msdn.microsoft.com/library/dn505719.aspx).
