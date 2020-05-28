---
title: Podmíněný přístup
description: Přečtěte si, jak nakonfigurovat podmíněný přístup pro Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: cc4857c32eca924051ba72fb716e29231327f543
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043524"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Podmíněný přístup (MFA) s Azure SQL Database a Azure synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [spravovaná instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)a [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporují podmíněný přístup Microsoft.

Následující kroky ukazují, jak nakonfigurovat Azure SQL Database, spravovanou instanci SQL nebo Azure synapse, aby vynutily zásady podmíněného přístupu (CA).  

## <a name="prerequisites"></a>Požadavky

- Pro podporu ověřování Azure Active Directory musíte nakonfigurovat SQL Database, spravovanou instanci SQL nebo fond SQL ve službě Azure synapse. Konkrétní postup najdete v tématu [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database nebo Azure synapse](authentication-aad-configure.md).  
- Je-li povoleno Multi-Factor Authentication, je třeba se připojit pomocí nástroje na podporovaném nástroji, jako je například nejnovější SQL Server Management Studio (SSMS). Další informace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

> [!NOTE]
> Následující příklad používá Azure SQL Database, ale měli byste vybrat příslušný produkt, pro který chcete nakonfigurovat podmíněný přístup.

1. Přihlaste se k portálu, vyberte **Azure Active Directory**a potom vyberte **podmíněný přístup**. Další informace najdete v tématu [technické informace o podmíněném přístupu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Okno podmíněný přístup](./media/conditional-access-configure/conditional-access-blade.png)

2. V okně **podmíněný přístup – zásady** klikněte na **nové zásady**, zadejte název a pak klikněte na **Konfigurovat pravidla**.  
3. V části **přiřazení**vyberte **Uživatelé a skupiny**, zaškrtněte **políčko Vybrat uživatele a skupiny**a pak vyberte uživatele nebo skupinu pro podmíněný přístup. Klikněte na **Vybrat**a potom kliknutím na **Hotovo** přijměte svůj výběr.  
   ![Vybrat uživatele a skupiny](./media/conditional-access-configure/select-users-and-groups.png)  

4. Vyberte **cloudové aplikace**, klikněte na **vybrat aplikace**. Zobrazí se všechny aplikace, které jsou k dispozici pro podmíněný přístup. Vyberte **Azure SQL Database**, v dolní části klikněte na **Vybrat**a pak klikněte na **Hotovo**.  
   ![Vyberte SQL Database](./media/conditional-access-configure/select-sql-database.png)  
   Pokud nemůžete najít **Azure SQL Database** uvedených v následujícím třetím snímku obrazovky, proveďte následující kroky:
   - Přihlaste se ke svému Azure SQL Database pomocí účtu správce Azure AD pomocí SSMS.  
   - Provést `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Přihlaste se k Azure AD a ověřte, že Azure SQL Database, Managed instance SQL nebo Azure synapse, jsou uvedené v aplikacích v AAD.  

5. Vyberte **řízení přístupu**, vyberte **udělit**a pak zkontrolujte zásadu, kterou chcete použít. V tomto příkladu vybereme možnost **vyžadovat službu Multi-Factor Authentication**.  
   ![vybrat udělit přístup](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Souhrn

Vybraná aplikace (Azure SQL Database) pomocí Azure AD Premium nyní vynutila vybrané zásady podmíněného přístupu, **požadované vícefaktorové ověřování.**

Pokud máte dotazy týkající se Azure SQL Database a Azure synapse týkající se služby Multi-Factor Authentication, kontaktujte <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Další kroky  

Kurz najdete v tématu [zabezpečení Azure SQL Database](secure-database-tutorial.md).
