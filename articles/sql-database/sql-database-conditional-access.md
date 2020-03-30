---
title: Podmíněný přístup
description: Přečtěte si, jak nakonfigurovat podmíněný přístup pro Azure SQL Database a Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124894"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Podmíněný přístup (MFA) s Azure SQL Database a Azure Synapse Analytics

Azure [SQL Database](sql-database-technical-overview.md), [spravovaná instance](sql-database-managed-instance.md)a Azure [Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporují podmíněný přístup Microsoftu. 

> [!NOTE]
> Toto téma se týká Serveru Azure SQL a databáze SQL a Azure Synapse, které jsou vytvořené na serveru Azure SQL. Pro jednoduchost SQL Database se používá při odkazování na SQL Database a Azure Synapse.

Následující kroky ukazují, jak nakonfigurovat databázi SQL tak, aby vynucovala zásady podmíněného přístupu.  

## <a name="prerequisites"></a>Požadavky  
- Musíte nakonfigurovat databázi SQL nebo fond SQL v Azure Synapse pro podporu ověřování Azure Active Directory. Konkrétní kroky najdete [v tématu Konfigurace a správa ověřování Azure Active Directory pomocí databáze SQL Database nebo Azure Synapse](sql-database-aad-authentication-configure.md).  
- Pokud je povoleno vícefaktorové ověřování, musíte se připojit k podporovanému nástroji, jako je například nejnovější SSMS. Další informace naleznete [v tématu Konfigurace vícefaktorového ověřování azure sql database pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurace certifikační autority pro Azure SQL DB/DW  
1. Přihlaste se k portálu, vyberte **Azure Active Directory**a pak vyberte Podmíněný **přístup**. Další informace naleznete v technické příručce pro [podmíněný přístup služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Okno podmíněného přístupu](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. V okně **Zásady podmíněného přístupu** klikněte na **Nová zásada**, zadejte název a potom klepněte na tlačítko **Konfigurovat pravidla**.  
3. V části **Přiřazení**vyberte **Možnost Uživatelé a skupiny**, **zaškrtněte políčko Vybrat uživatele a skupiny a**vyberte uživatele nebo skupinu pro podmíněný přístup. Klikněte na **Vybrat**a potom kliknutím na **Hotovo** výběr přijměte.  
   ![výběr uživatelů a skupin](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Vyberte **Cloudové aplikace**, klikněte na **Vybrat aplikace**. Zobrazí se všechny aplikace dostupné pro podmíněný přístup. Vyberte **Azure SQL Database**, v dolní části klikněte na **Vybrat**a potom klikněte na **Hotovo**.  
   ![vybrat databázi SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Pokud nemůžete najít **Azure SQL Database** uvedené v následujícím třetím snímku obrazovky, postupujte takto:   
   - Přihlaste se k instanci Azure SQL DB/DW pomocí SSMS s účtem správce AAD.  
   - Spusťte `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Přihlaste se k AAD a ověřte, že Azure SQL Database a Azure Synapse jsou uvedené v aplikacích ve vašem AAD.  

5. Vyberte **ovládací prvky aplikace Access**, vyberte Možnost **Udělit**a zaškrtněte zásadu, kterou chcete použít. V tomto příkladu vybereme **Vyžadovat vícefaktorové ověřování**.  
   ![vybrat udělit přístup](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Souhrn  
Vybraná aplikace (Azure SQL Database), která umožňuje připojení k Azure SQL DB/DW pomocí Azure AD Premium, teď vynucuje vybrané zásady podmíněného **přístupu, požadované vícefaktorové ověřování.**  
Máte-li dotazy týkající se Azure SQL Database a MFAforSQLDB@microsoft.comAzure Synapse týkající se vícefaktorového ověřování, kontaktujte .  

## <a name="next-steps"></a>Další kroky  

Kurz najdete v tématu [Zabezpečení databáze Azure SQL](sql-database-security-tutorial.md)Database .
