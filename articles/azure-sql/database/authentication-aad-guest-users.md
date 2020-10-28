---
title: Vytvoření uživatelů typu Host služby Azure AD
description: Jak vytvořit uživatele hosta Azure AD a nastavit je jako správce Azure AD bez použití skupin Azure AD v Azure SQL Database, spravované instance Azure SQL a Azure synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675051"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Vytvoření uživatelů Azure AD typu host a nastavení jako správce Azure AD

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Tento článek je ve **verzi Public Preview** .

Uživatelé typu Host v Azure Active Directory (Azure AD) jsou uživatelé, kteří byli importováni do aktuální služby Azure AD z jiných adresářů Azure Active Directory nebo mimo ni. Uživatelé typu Host můžou například zahrnovat uživatele z jiných adresářů Azure Active Directory nebo z účtů, jako jsou *\@ Outlook.com* , *\@ hotmail.com* , *\@ Live.com* nebo *\@ gmail.com* . V tomto článku se dozvíte, jak vytvořit uživatele typu hosta Azure AD, a nastavit tohoto uživatele jako správce Azure AD pro logický Server Azure SQL, aniž by bylo potřeba mít tohoto uživatele hosta v rámci Azure AD jako součást skupiny.

## <a name="feature-description"></a>Popis funkce

Tato funkce výtahuje aktuální omezení, které umožňuje uživatelům typu Host připojit se k Azure SQL Database, spravované instanci SQL nebo analýze Azure synapse, když jsou členy skupiny vytvořené v Azure AD. Skupina potřebná k ručnímu namapování na uživatele pomocí příkazu [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) v dané databázi. Po vytvoření databáze uživatele pro skupinu Azure AD obsahující uživatele typu Host se uživatel typu Host může přihlásit k databázi pomocí Azure Active Directory s ověřováním MFA. V rámci této **veřejné verze Preview** je možné vytvořit uživatele typu Host a připojit se přímo k SQL Database, spravované instanci SQL nebo Azure synapse bez nutnosti jejich přidání do skupiny Azure AD a následným vytvořením uživatele databáze pro tuto skupinu Azure AD.

V rámci této funkce máte také možnost nastavit uživatele hosta Azure AD přímo jako správce služby AD pro logický Server Azure SQL. Stávající funkce, kde uživatel typu Host může být součástí skupiny Azure AD, a tuto skupinu je pak možné nastavit jako správce Azure AD pro logický Server Azure SQL, který nemá vliv. Tato změna také nemá vliv na uživatele typu Host v databázi, kteří jsou součástí skupiny Azure AD.

Další informace o stávající podpoře pro uživatele typu Host pomocí skupin Azure AD najdete v tématu [použití služby Multi-factor Azure Active Directory Authentication](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Požadavek

- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) Module nebo vyšší je potřeba při použití prostředí PowerShell k nastavení uživatele typu Host jako správce Azure AD pro logický Server Azure SQL.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Vytvořit uživatele databáze pro uživatele hosta Azure AD 

Postupujte podle těchto kroků a vytvořte uživatele databáze pomocí hostovaného uživatele Azure AD.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Vytvoření uživatele typu Host v SQL Database a Azure synapse

1. Ujistěte se, že uživatel typu Host (například `user1@gmail.com` ) je už přidaný do služby Azure AD, a pro databázový server se nastavil správce Azure AD. K ověřování Azure Active Directory se vyžaduje správce Azure AD.

1. Připojte se k databázi SQL jako správce Azure AD nebo uživatele Azure AD s dostatečnými oprávněními SQL pro vytváření uživatelů a spusťte níže uvedený příkaz v databázi, do které se musí přidat uživatel typu Host:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Nyní by měl být vytvořen uživatel databáze pro uživatele typu host `user1@gmail.com` .

1. Spuštěním následujícího příkazu ověřte, že uživatel databáze byl úspěšně vytvořen:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Odpojte se a přihlaste se k databázi jako uživatel typu host `user1@gmail.com` pomocí [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) pomocí metody ověřování **Azure Active Directory-Universal s MFA** . Další informace najdete v tématu [použití služby Multi-factor Azure Active Directory Authentication](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Vytvořit uživatele typu Host ve spravované instanci SQL

> [!NOTE]
> Spravovaná instance SQL podporuje přihlášení pro uživatele Azure AD a uživatele databáze s omezením Azure AD. Následující kroky ukazují, jak vytvořit přihlášení a uživatele pro uživatele typu hosta Azure AD ve spravované instanci SQL. Můžete také vytvořit [uživatele databáze s omezením](/sql/relational-databases/security/contained-database-users-making-your-database-portable) v SQL Managed instance pomocí metody v části [Vytvoření uživatele typu Host v SQL Database a Azure synapse](#create-guest-user-in-sql-database-and-azure-synapse) .

1. Ujistěte se, že uživatel typu Host (například `user1@gmail.com` ) je již přidán do služby Azure AD a správce Azure AD byl nastaven pro server SQL Managed instance. K ověřování Azure Active Directory se vyžaduje správce Azure AD.

1. Připojte se k serveru SQL Managed instance jako správce Azure AD nebo uživatele Azure AD s dostatečnými oprávněními SQL pro vytváření uživatelů a spuštěním následujícího příkazu v `master` databázi vytvořte přihlašovací jméno pro uživatele typu Host:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. V databázi by teď mělo být vytvořené přihlašovací jméno pro uživatele typu host `user1@gmail.com` `master` .

1. Spuštěním následujícího příkazu ověřte, že se přihlášení úspěšně vytvořilo:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. V databázi, do které se musí přidat uživatel typu Host, spusťte následující příkaz: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Nyní by měl být vytvořen uživatel databáze pro uživatele typu host `user1@gmail.com` .

1. Odpojte se a přihlaste se k databázi jako uživatel typu host `user1@gmail.com` pomocí [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) pomocí metody ověřování **Azure Active Directory-Universal s MFA** . Další informace najdete v tématu [použití služby Multi-factor Azure Active Directory Authentication](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Nastavení uživatele typu Host jako správce Azure AD

Postupujte podle těchto kroků a nastavte uživatele hosta Azure AD jako správce Azure AD pro logický server SQL.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Nastavení správce Azure AD pro SQL Database a Azure synapse

1. Zajistěte, aby byl uživatel typu Host (například `user1@gmail.com` ) již přidán do služby Azure AD.

1. Spusťte následující příkaz PowerShellu pro přidání uživatele typu Host jako správce Azure AD pro logický Server Azure SQL:

    - Nahraďte `<ResourceGroupName>` názvem skupiny prostředků Azure, která obsahuje logický Server Azure SQL.
    - Nahraďte `<ServerName>` názvem logického serveru SQL Azure. Pokud je název vašeho serveru `myserver.database.windows.net` , nahraďte parametr `<Server Name>` `myserver` .
    - Nahraďte `<DisplayNameOfGuestUser>` uživatelským jménem typu Host.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    K nastavení uživatele typu Host jako správce Azure AD pro logický Server Azure SQL můžete použít také příkaz Azure CLI [AZ SQL Server AD-admin](/cli/azure/sql/server/ad-admin) .

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Nastavit správce Azure AD pro spravovanou instanci SQL

1. Zajistěte, aby byl uživatel typu Host (například `user1@gmail.com` ) již přidán do služby Azure AD.

1. Přejít na [Azure Portal](https://portal.azure.com)a přejít na prostředek **Azure Active Directory** . V části **Spravovat** otevřete podokno **Uživatelé** . Vyberte uživatele typu Host a zaznamenejte `Object ID` . 

1. Spuštěním následujícího příkazu PowerShellu přidejte uživatele typu Host jako správce Azure AD pro vaši spravovanou instanci SQL:

    - Nahraďte `<ResourceGroupName>` názvem skupiny prostředků Azure, která obsahuje spravovanou instanci SQL.
    - Nahraďte `<ManagedInstanceName>` názvem spravované instance SQL.
    - Nahraďte `<DisplayNameOfGuestUser>` uživatelským jménem typu Host.
    - Nahraďte `<AADObjectIDOfGuestUser>` `Object ID` dříve shromážděnými.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    K nastavení uživatele typu Host jako správce Azure AD pro spravovanou instanci SQL můžete použít také příkaz Azure CLI [AZ SQL mi AD-admin](/cli/azure/sql/mi/ad-admin) .

## <a name="limitations"></a>Omezení

Existuje omezení Azure Portal, které brání výběru uživatele typu Host služby Azure AD jako spravované instance Azure AD admin pro SQL. Pro účty hostů mimo vaši službu Azure AD, jako je *\@ Outlook.com* , *\@ hotmail.com* , *\@ Live.com* nebo *\@ gmail.com* , se v selektoru pro správu služby AD zobrazí tyto účty, ale jejich zobrazení je šedé a nedá se vybrat. Pomocí výše uvedených [příkazů PowerShellu nebo rozhraní příkazového řádku](#setting-a-guest-user-as-an-azure-ad-admin) nastavte správce Azure AD. Případně skupinu Azure AD obsahujícího uživatele typu Host můžete nastavit jako správce Azure AD pro spravovanou instanci SQL.

Tato funkce bude pro spravovanou instanci SQL povolena před všeobecnou dostupností této funkce.

## <a name="next-steps"></a>Další kroky

- [Konfigurace a Správa ověřování Azure AD pomocí Azure SQL](authentication-aad-configure.md)
- [Použití ověřování Multi-Factor Azure Active Directory](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)