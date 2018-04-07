---
title: Podmíněný přístup – Azure SQL Database a Data Warehouse | Microsoft dokumentů
description: Postup konfigurace podmíněného přístupu pro Azure SQL Database a datového skladu.
services: sql-database
author: GithubMirek
manager: johammer
ms.custom: security
ms.service: sql-database
ms.topic: article
ms.date: 04/01/2018
ms.author: mireks
ms.openlocfilehash: f7f34bc7a28cdf3a23af54785002b12fba4baf1d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Podmíněný přístup (MFA) s Azure SQL Database a datového skladu  

SQL Database a SQL Data Warehouse podporují podmíněný přístup společnosti Microsoft. Následující kroky ukazují, jak nakonfigurovat databázi SQL k vynucení zásad podmíněného přístupu.  

## <a name="prerequisites"></a>Požadavky  
- Je nutné nakonfigurovat SQL Database nebo SQL Data Warehouse pro podporu ověřování Azure Active Directory. Konkrétní pokyny najdete v tématu [konfigurovat a spravovat ověřování Azure Active Directory s SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Pokud je povoleno ověřování Multi-Factor authentication, je nutné připojit s v podporovaných nástroji, jako nejnovější aplikace SSMS. Další informace najdete v tématu [nakonfigurovat databázi SQL Azure Multi-Factor authentication pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurace certifikační Autority pro Azure SQL DB nebo datového skladu  
1.  Přihlaste se k portálu, vyberte **Azure Active Directory**a potom vyberte **podmíněného přístupu**. Další informace najdete v tématu [technické informace o Azure Active Directory podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![okno podmíněného přístupu](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  V **zásady podmíněného přístupu** okně klikněte na tlačítko **nové zásady**, zadejte název a potom klikněte na **konfigurovat pravidla**.  
3.  V části **přiřazení**, vyberte **uživatelů a skupin**, zkontrolujte **vyberte uživatele a skupiny**a poté vyberte uživatele nebo skupinu pro podmíněný přístup. Klikněte na tlačítko **vyberte**a potom klikněte na **provádí** tak, aby přijímal váš výběr.  
  ![Vyberte uživatele a skupiny](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Vyberte **cloudových aplikací**, klikněte na tlačítko **vybrat aplikace**. Zobrazí všechny aplikace dostupné pro podmíněný přístup. Vyberte **Azure SQL Database**, v dolní části klikněte na tlačítko **vyberte**a potom klikněte na **provádí**.  
  ![Vyberte databázi SQL](./media/sql-database-conditional-access/select-sql-database.png)  
  Pokud nemůžete najít **Azure SQL Database** uvedené v následujícím snímku obrazovky třetí, proveďte následující kroky:   
  - Přihlaste se k vaší instanci Azure SQL DB nebo DW pomocí aplikace SSMS pomocí účtu správce AAD.  
  - Spustit `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Přihlaste se k AAD a ověřte, že Azure SQL Database a datový sklad jsou uvedeny v aplikacích v vaší AAD.  

5.  Vyberte **přístup k ovládacím prvkům**, vyberte **Grant**a potom zkontrolujte zásady, kterou chcete použít. V tomto příkladu jsme vyberte **vyžadovat vícefaktorové ověřování**.  
  ![Vyberte udělení přístupu](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Souhrn  
Vybrané aplikace (Azure SQL Database) povolení pro připojení k Azure SQL DB nebo datového skladu pomocí Azure AD Premium, teď vynucuje vybrané zásady podmíněného přístupu, **požadované služby Multi-Factor authentication.**  
Otázky týkající se Azure SQL Database a datového skladu týkající se služby Multi-Factor authentication, vám poskytne MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Další postup  

Podívejte se kurz [zabezpečení vaší databázi SQL Azure](sql-database-security-tutorial.md).
