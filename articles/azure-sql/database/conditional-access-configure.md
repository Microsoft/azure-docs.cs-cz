---
title: Podmíněný přístup
description: Přečtěte si, jak nakonfigurovat podmíněný přístup pro Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: ad80f68c1ab3b3583c5a22de49b77211571f345e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443987"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Podmíněný přístup s Azure SQL Database a Azure synapse Analytics

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [spravovaná instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)a [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporují podmíněný přístup Microsoft.

Následující kroky ukazují, jak nakonfigurovat Azure SQL Database, spravovanou instanci SQL nebo Azure synapse, aby vynutily zásady podmíněného přístupu (CA).  

## <a name="prerequisites"></a>Požadavky

- Aby bylo možné podporovat ověřování Azure Active Directory (Azure AD), musíte v Azure synapse nakonfigurovat Azure SQL Database, spravovanou instanci Azure SQL nebo fond Azure SQL. Konkrétní postup najdete v tématu [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database nebo Azure synapse](authentication-aad-configure.md).  
- Pokud je povolená Multi-Factor Authentication, musíte se připojit pomocí podporovaného nástroje, jako je například nejnovější SQL Server Management Studio (SSMS). Další informace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

> [!NOTE]
> Následující příklad používá Azure SQL Database, ale měli byste vybrat příslušný produkt, pro který chcete nakonfigurovat podmíněný přístup.

1. Přihlaste se k Azure Portal, vyberte **Azure Active Directory**a potom vyberte **podmíněný přístup**. Další informace najdete v tématu [technické informace o podmíněném přístupu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Okno podmíněný přístup](./media/conditional-access-configure/conditional-access-blade.png)

2. V okně **podmíněný přístup – zásady** klikněte na **nové zásady**, zadejte název a pak klikněte na **Konfigurovat pravidla**.  
3. V části **přiřazení**vyberte **Uživatelé a skupiny**, zaškrtněte **políčko Vybrat uživatele a skupiny**a pak vyberte uživatele nebo skupinu pro podmíněný přístup. Klikněte na **Vybrat**a potom kliknutím na **Hotovo** přijměte svůj výběr.  
   ![Vybrat uživatele a skupiny](./media/conditional-access-configure/select-users-and-groups.png)  

4. Vyberte **cloudové aplikace**, klikněte na **vybrat aplikace**. Zobrazí se všechny aplikace, které jsou k dispozici pro podmíněný přístup. Vyberte **Azure SQL Database**, v dolní části klikněte na **Vybrat**a pak klikněte na **Hotovo**.  
   ![Vyberte SQL Database](./media/conditional-access-configure/select-sql-database.png)  
   Pokud nemůžete najít **Azure SQL Database** uvedených v následujícím třetím snímku obrazovky, proveďte následující kroky:
   - Připojte se k databázi v Azure SQL Database pomocí SSMS s účtem správce Azure AD.  
   - Provést `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Přihlaste se k Azure AD a ověřte, že Azure SQL Database, spravovaná instance SQL nebo Azure synapse, jsou uvedené v aplikacích v instanci služby Azure AD.  

5. Vyberte **řízení přístupu**, vyberte **udělit**a pak zkontrolujte zásadu, kterou chcete použít. V tomto příkladu vybereme možnost **vyžadovat službu Multi-Factor Authentication**.  
   ![vybrat udělit přístup](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Shrnutí

Vybraná aplikace (Azure SQL Database) pomocí Azure AD Premium nyní vynutila vybrané zásady podmíněného přístupu, **požadované vícefaktorové ověřování.**

Pokud máte dotazy týkající se Azure SQL Database a Azure synapse týkající se služby Multi-Factor Authentication, kontaktujte <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Další kroky  

Kurz najdete v tématu [zabezpečení databáze v SQL Database](secure-database-tutorial.md).
