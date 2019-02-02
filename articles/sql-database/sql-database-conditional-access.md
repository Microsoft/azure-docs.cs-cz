---
title: Podmíněný přístup – Azure SQL Database a Data Warehouse | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat podmíněný přístup pro Azure SQL Database a Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 95103ec7438a01e73617587bf919404e2bdcc686
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567192"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Podmíněný přístup (MFA) s Azure SQL Database a Data Warehouse  

Azure [SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporují podmíněný přístup společnosti Microsoft. 

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

Následující kroky ukazují, jak nakonfigurovat databázi SQL k vynucení zásad podmíněného přístupu.  

## <a name="prerequisites"></a>Požadavky  
- Je nutné nakonfigurovat SQL Database nebo SQL Data Warehouse pro podporu ověřování Azure Active Directory. Konkrétní pokyny najdete v článku [konfigurovat a spravovat ověřování Azure Active Directory s SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Pokud je povoleno ověřování Multi-Factor Authentication, je nutné připojit s na podporované nástroje, jako je například nejnovější verze aplikace SSMS. Další informace najdete v tématu [konfigurace služby Azure SQL Database vícefaktorové ověřování pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurace certifikační Autority pro Azure SQL DB/DW  
1.  Přihlaste se k portálu vyberte **Azure Active Directory**a pak vyberte **podmíněného přístupu**. Další informace najdete v tématu [technické informace o Azure Active Directory podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![okna podmíněného přístupu](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  V **zásady podmíněného přístupu** okna, klikněte na tlačítko **nové zásady**, zadejte název a potom klikněte na tlačítko **nakonfigurovat pravidla**.  
3.  V části **přiřazení**vyberte **uživatelů a skupin**, zkontrolujte **výběr uživatelů a skupin**a pak vyberte uživatele nebo skupinu pro podmíněný přístup. Klikněte na tlačítko **vyberte**a potom klikněte na tlačítko **provádí** potvrďte svůj výběr.  
  ![Vyberte uživatele a skupiny](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Vyberte **cloudové aplikace**, klikněte na tlačítko **vyberte aplikace**. Zobrazí všechny aplikace dostupné pro podmíněný přístup. Vyberte **Azure SQL Database**, v dolní části klikněte na tlačítko **vyberte**a potom klikněte na tlačítko **provádí**.  
  ![select SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
  Pokud nemůžete najít **Azure SQL Database** uvedené v následujícím snímku obrazovky třetí, proveďte následující kroky:   
  - Přihlaste se k vaší instanci Azure SQL DB/DW pomocí aplikace SSMS pomocí účtu správce AAD.  
  - Spuštění `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Přihlaste se k AAD a ověřte, že Azure SQL Database a datovým skladem vykazovaných jsou uvedeny v aplikacích v AAD.  

5.  Vyberte **ovládací prvky přístupu**vyberte **udělení**a potom zkontrolujte zásady, které chcete použít. V tomto příkladu vybereme **vyžadovat vícefaktorové ověřování**.  
  ![Vyberte udělit přístup](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Souhrn  
Vybrané aplikace (Azure SQL Database), umožňující připojení k Azure SQL DB/DW prostřednictvím Azure AD Premium, nyní vynucuje vybrané zásady podmíněného přístupu, **vyžaduje ověřování službou Multi-Factor Authentication.**  
Obraťte se na dotazy týkající se Azure SQL Database a datovým skladem vykazovaných týkající se služby Multi-Factor authentication, MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Další postup  

Podívejte se kurz [zabezpečení služby Azure SQL Database](sql-database-security-tutorial.md).
