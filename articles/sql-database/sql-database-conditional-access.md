---
title: Podmíněný přístup
description: Přečtěte si, jak nakonfigurovat podmíněný přístup pro Azure SQL Database a Azure synapse.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124894"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Podmíněný přístup (MFA) s Azure SQL Database a Azure synapse Analytics

Azure [SQL Database](sql-database-technical-overview.md), [spravovaná instance](sql-database-managed-instance.md)a [Azure synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporují podmíněný přístup Microsoft. 

> [!NOTE]
> Toto téma se týká Azure SQL serveru a SQL Database a Azure synapse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá při odkazování na SQL Database a Azure synapse.

Následující kroky ukazují, jak nakonfigurovat SQL Database pro vymáhání zásad podmíněného přístupu.  

## <a name="prerequisites"></a>Požadavky  
- Pro podporu ověřování Azure Active Directory je nutné nakonfigurovat SQL Database nebo fond SQL ve službě Azure synapse. Konkrétní postup najdete v tématu [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database nebo Azure synapse](sql-database-aad-authentication-configure.md).  
- Je-li povoleno Multi-Factor Authentication, je třeba se připojit pomocí nástroje s podporovaným nástrojem, jako je například nejnovější SSMS. Další informace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurace certifikační autority pro Azure SQL DB/DW  
1. Přihlaste se k portálu, vyberte **Azure Active Directory**a potom vyberte **podmíněný přístup**. Další informace najdete v tématu [technické informace o podmíněném přístupu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Okno podmíněný přístup](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. V okně **podmíněný přístup – zásady** klikněte na **nové zásady**, zadejte název a pak klikněte na **Konfigurovat pravidla**.  
3. V části **přiřazení**vyberte **Uživatelé a skupiny**, zaškrtněte **políčko Vybrat uživatele a skupiny**a pak vyberte uživatele nebo skupinu pro podmíněný přístup. Klikněte na **Vybrat**a potom kliknutím na **Hotovo** přijměte svůj výběr.  
   ![Vybrat uživatele a skupiny](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Vyberte **cloudové aplikace**, klikněte na **vybrat aplikace**. Zobrazí se všechny aplikace, které jsou k dispozici pro podmíněný přístup. Vyberte **Azure SQL Database**, v dolní části klikněte na **Vybrat**a pak klikněte na **Hotovo**.  
   ![Vyberte SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Pokud nemůžete najít **Azure SQL Database** uvedených v následujícím třetím snímku obrazovky, proveďte následující kroky:   
   - Přihlaste se k instanci Azure SQL DB/DW pomocí SSMS s účtem správce AAD.  
   - Provést `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Přihlaste se k AAD a ověřte, že Azure SQL Database a Azure synapse jsou uvedené v aplikacích v AAD.  

5. Vyberte **řízení přístupu**, vyberte **udělit**a pak zkontrolujte zásadu, kterou chcete použít. V tomto příkladu vybereme možnost **vyžadovat službu Multi-Factor Authentication**.  
   ![vybrat udělit přístup](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Souhrn  
Vybraná aplikace (Azure SQL Database), která umožňuje připojení k Azure SQL DB/DW pomocí Azure AD Premium nyní vynutila vybrané zásady podmíněného přístupu, **požadované vícefaktorové ověřování.**  
Pokud máte dotazy týkající se Azure SQL Database a Azure synapse týkající se služby Multi- MFAforSQLDB@microsoft.comFactor Authentication, kontaktujte.  

## <a name="next-steps"></a>Další kroky  

Kurz najdete v tématu [zabezpečení Azure SQL Database](sql-database-security-tutorial.md).
