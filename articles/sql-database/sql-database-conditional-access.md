---
title: Podmíněný přístup
description: Přečtěte si, jak nakonfigurovat podmíněný přístup pro Azure SQL Database a datový sklad.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 9b8c0dbe03e47d32d8194408663973f07a07b1b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827167"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Podmíněný přístup (MFA) s Azure SQL Database a datovým skladem  

Azure [SQL Database](sql-database-technical-overview.md), [spravovaná instance](sql-database-managed-instance.md)a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporují podmíněný přístup Microsoftu. 

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

Následující kroky ukazují, jak nakonfigurovat SQL Database pro vymáhání zásad podmíněného přístupu.  

## <a name="prerequisites"></a>Požadavky  
- Je nutné nakonfigurovat SQL Database nebo SQL Data Warehouse, aby podporovaly Azure Active Directory ověřování. Konkrétní postup najdete v tématu [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Je-li povoleno Multi-Factor Authentication, je třeba se připojit pomocí nástroje s podporovaným nástrojem, jako je například nejnovější SSMS. Další informace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurace certifikační autority pro Azure SQL DB/DW  
1. Přihlaste se k portálu, vyberte **Azure Active Directory**a potom vyberte **podmíněný přístup**. Další informace najdete v tématu [technické informace o podmíněném přístupu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![okno podmíněného přístupu](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. V okně **podmíněný přístup – zásady** klikněte na **nové zásady**, zadejte název a pak klikněte na **Konfigurovat pravidla**.  
3. V části **přiřazení**vyberte **Uživatelé a skupiny**, zaškrtněte **políčko Vybrat uživatele a skupiny**a pak vyberte uživatele nebo skupinu pro podmíněný přístup. Klikněte na **Vybrat**a potom kliknutím na **Hotovo** přijměte svůj výběr.  
   ![vybrat uživatele a skupiny](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Vyberte **cloudové aplikace**, klikněte na **vybrat aplikace**. Zobrazí se všechny aplikace, které jsou k dispozici pro podmíněný přístup. Vyberte **Azure SQL Database**, v dolní části klikněte na **Vybrat**a pak klikněte na **Hotovo**.  
   ![vybrat SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Pokud nemůžete najít **Azure SQL Database** uvedených v následujícím třetím snímku obrazovky, proveďte následující kroky:   
   - Přihlaste se k instanci Azure SQL DB/DW pomocí SSMS s účtem správce AAD.  
   - Spusťte `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Přihlaste se k AAD a ověřte, že Azure SQL Database a datový sklad jsou uvedené v aplikacích v AAD.  

5. Vyberte **řízení přístupu**, vyberte **udělit**a pak zkontrolujte zásadu, kterou chcete použít. V tomto příkladu vybereme možnost **vyžadovat službu Multi-Factor Authentication**.  
   ![vyberte udělit přístup](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Souhrn  
Vybraná aplikace (Azure SQL Database), která umožňuje připojení k Azure SQL DB/DW pomocí Azure AD Premium nyní vynutila vybrané zásady podmíněného přístupu, **požadované vícefaktorové ověřování.**  
Pokud máte dotazy týkající se služby Multi-Factor Authentication Azure SQL Database a datového skladu, obraťte se na MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Další kroky  

Kurz najdete v tématu [zabezpečení Azure SQL Database](sql-database-security-tutorial.md).
