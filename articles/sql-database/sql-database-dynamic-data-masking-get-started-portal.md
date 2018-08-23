---
title: 'Azure portal: maskování dynamických dat SQL Database | Dokumentace Microsoftu'
description: Jak začít s SQL Database dynamické maskování dat na webu Azure Portal
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: 8f7720555a3cfc39ce9e9b5ad2c392bab74b64ca
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060544"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Začínáme s dynamickými daty SQL Database pomocí webu Azure portal maskování

V tomto článku se dozvíte, jak implementovat [dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md) pomocí webu Azure portal. Můžete taky implementovat pomocí maskování dynamických dat [rutiny služby Azure SQL Database](https://docs.microsoft.com/powershell/module/azurerm.sql/) nebo [rozhraní REST API](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Nastavení dynamického maskování pro vaši databázi pomocí webu Azure portal dat
1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku nastavení databáze, která obsahuje citlivá data, které chcete k maskování.
3. Klikněte na tlačítko **maskování dynamických dat** dlaždici, která se spustí **maskování dynamických dat** stránka konfigurace.
   
   * Případně přejděte dolů na **operace** části a klikněte na tlačítko **maskování dynamických dat**.
     
     ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. V **maskování dynamických dat** stránku konfigurace, může se zobrazit některé sloupce databáze, které modul recommendations označil k maskování. Pokud chcete přijímat doporučení, stačí kliknout na **přidat masku** pro jeden nebo více sloupců a maska je založeno na výchozí typ pro tento sloupec. Funkci maskování můžete změnit kliknutím na pravidlo maskování a úpravy maskování formátu pole do jiného formátu podle svého výběru. Nezapomeňte kliknout na **Uložit** uložte nastavení.
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Přidat masku pro všechny sloupce v databázi, v horní části **maskování dynamických dat** stránku konfigurace, klikněte na tlačítko **přidat masku** otevřít **přidat pravidlo maskování** stránku konfigurace .
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Vyberte **schématu**, **tabulky** a **sloupec** k definování pole určené pro maskování.
7. Zvolte **formát pole masky** ze seznamu citlivá data maskování kategorií.
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Klikněte na tlačítko **Uložit** v datech maskování pravidlo stránku aktualizovat sadu pravidel v dynamické maskování zásad dat maskování.
9. Zadejte uživatele SQL nebo identity AAD, které mají být vyloučeny z maskování a mají přístup k odmaskovaná citlivá data. To by měl být středníkem oddělený seznam uživatelů. Uživatelé s oprávněními správce stálý přístup k původní odmaskovaná data.
   
    ![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Chcete-li tak aplikační vrstvu můžete zobrazit citlivá data pro aplikace privilegovaného uživatele, přidejte uživatele SQL nebo identity AAD, které aplikace používá k dotazování databáze. Důrazně doporučujeme, že tento seznam obsahovat minimální počet privilegovaných uživatelů, chcete-li minimalizovat rizika ohrožení citlivých dat.
   > 
   > 
10. Klikněte na tlačítko **Uložit** v maskování stránku konfigurace k uložení nových nebo aktualizovaných dat maskování zásad.


## <a name="next-steps"></a>Další postup

* Přehled dynamické maskování dat, naleznete v tématu [dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md).
* Můžete taky implementovat pomocí maskování dynamických dat [rutiny služby Azure SQL Database](https://docs.microsoft.com/powershell/module/azurerm.sql/) nebo [rozhraní REST API](https://msdn.microsoft.com/library/dn505719.aspx).
