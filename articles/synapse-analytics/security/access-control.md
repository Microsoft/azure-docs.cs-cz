---
title: Správa přístupu k pracovním prostorům, datům a kanálům
description: Naučte se spravovat řízení přístupu k pracovním prostorům, datům a kanálům v Azure synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: c4304aeadf2950c1a91ee50ba9ecd895b2561b41
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461343"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Správa přístupu k pracovním prostorům, datům a kanálům

Naučte se spravovat řízení přístupu k pracovním prostorům, datům a kanálům v Azure synapse Analytics.

> [!NOTE]
> V případě GA se Azure RBAC bude vyvíjet prostřednictvím zavedení rolí Azure, které jsou specifické pro synapse.

## <a name="access-control-for-workspace"></a>Access Control pro pracovní prostor

V případě produkčního nasazení do pracovního prostoru Azure synapse doporučujeme organizovat vaše prostředí a usnadnit tak zřizování uživatelů a správců.

> [!NOTE]
> Tady je postup, jak vytvořit několik skupin zabezpečení a pak nakonfigurovat pracovní prostor tak, aby se konzistentně používal. Po nastavení skupin stačí správce spravovat členství v rámci skupin zabezpečení.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Krok 1: nastavení skupin zabezpečení s názvy za tímto vzorem

1. Vytvořit skupinu zabezpečení nazvanou `Synapse_WORKSPACENAME_Users`
2. Vytvořit skupinu zabezpečení nazvanou `Synapse_WORKSPACENAME_Admins`
3. Přidání `Synapse_WORKSPACENAME_Admins` do `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Naučte se vytvořit skupinu zabezpečení v [tomto článku](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>
> Naučte se přidat skupinu zabezpečení z jiné skupiny zabezpečení v [tomto článku](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> PRACOVNÍ prostor – tuto část byste měli nahradit skutečným názvem vašeho pracovního prostoru.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Krok 2: Příprava výchozího účtu ADLS Gen2

Když jste zřídili pracovní prostor, museli jste vybrat účet [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) a kontejner pro systém souborů, který má pracovní prostor použít.

1. Otevřete [Azure Portal](https://portal.azure.com).
2. Přejít na účet Azure Data Lake Storage Gen2
3. Přejít do kontejneru (FileSystem), který jste vybrali pro pracovní prostor Azure synapse
4. Vybrat **Access Control (IAM)**
5. Přiřaďte následující role:
   1. Role **čtecího modulu** :`Synapse_WORKSPACENAME_Users`
   2. Role **vlastníka dat objektu BLOB úložiště** :`Synapse_WORKSPACENAME_Admins`
   3. Role **přispěvatele dat objektu BLOB služby Storage** :`Synapse_WORKSPACENAME_Users`
   4. Role **vlastníka dat objektu BLOB úložiště** :`WORKSPACENAME`

> [!NOTE]
> PRACOVNÍ prostor – tuto část byste měli nahradit skutečným názvem vašeho pracovního prostoru.

### <a name="step-3-configure-the-workspace-admin-list"></a>Krok 3: Konfigurace seznamu správců pracovního prostoru

1. Přejít do [ **webového uživatelského rozhraní Azure synapse**](https://web.azuresynapse.net)
2. Přejít na **Správa**   >  **Security**  >  **řízení přístupu** zabezpečení
3. Vyberte **přidat správce** a vyberte `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Krok 4: Konfigurace přístupu správce SQL pro pracovní prostor

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Přejděte do pracovního prostoru.
3. Přejít na **Nastavení**  >  **Správce služby Active Directory**
4. Vyberte **nastavit správce** .
5. Vyberte `Synapse_WORKSPACENAME_Admins`
6. Zvolte **Vybrat**
7. Vyberte **Uložit**.

> [!NOTE]
> PRACOVNÍ prostor – tuto část byste měli nahradit skutečným názvem vašeho pracovního prostoru.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Krok 5: Přidání a odebrání uživatelů a správců do skupin zabezpečení

1. Přidat uživatele, kteří potřebují přístup správce k `Synapse_WORKSPACENAME_Admins`
2. Přidat všechny ostatní uživatele do `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Naučte se, jak přidat uživatele jako člena do skupiny zabezpečení v [tomto článku](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal) .
> 
> PRACOVNÍ prostor – tuto část byste měli nahradit skutečným názvem vašeho pracovního prostoru.

## <a name="access-control-to-data"></a>Access Control k datům

Řízení přístupu k podkladovým datům je rozděleno na tři části:

- Přístup k účtu úložiště (už je nakonfigurovaný výše v kroku 2) – rovina dat
- Přístup k databázím SQL (pro vyhrazené fondy SQL i pro SQL fond bez serveru) prostřednictvím datové roviny
- Vytváření přihlašovacích údajů pro databáze fondu SQL bez serveru přes účet úložiště

## <a name="access-control-to-sql-databases"></a>Řízení přístupu k databázím SQL

> [!TIP]
> Níže uvedené kroky musí být spuštěny pro **každou** databázi SQL, aby bylo možné udělit uživatelům přístup ke všem databázím SQL s výjimkou [oprávnění na úrovni serveru](#server-level-permission) , kde můžete přiřadit uživatele k roli sysadmin.

### <a name="serverless-sql-pool"></a>Bezserverový fond SQL

V této části najdete příklady, jak udělit uživateli oprávnění ke konkrétní databázi nebo úplnému oprávnění serveru.

#### <a name="database-level-permission"></a>Oprávnění na úrovni databáze

Chcete-li udělit uživateli přístup k **jediné** databázi fondu SQL bez serveru, postupujte podle kroků v tomto příkladu:

1. Vytvořit přihlašovací údaje

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Vytvořit uživatele

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Přidat uživatele do členů zadané role

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Nahraďte alias aliasem uživatele, kterému chcete udělit přístup a doménu k doméně společnosti, kterou používáte.

#### <a name="server-level-permission"></a>Oprávnění na úrovni serveru

Chcete-li uživateli udělit úplný přístup ke **všem** databázím fondu SQL bez serveru, postupujte podle kroků v tomto příkladu:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="dedicated-sql-pool"></a>Vyhrazený fond SQL

Pokud chcete uživateli udělit přístup k **jedné** databázi SQL, postupujte takto:

1. Vytvořte uživatele v databázi tak, že spustíte následující příkaz, který cílí na požadovanou databázi v selektor kontextu (rozevírací seznam pro výběr databází):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Udělit uživateli roli pro přístup k databázi:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* a *db_datawriter* mohou fungovat pro oprávnění ke čtení a zápisu, pokud udělení oprávnění *db_owner* není žádoucí.
> Aby mohl uživatel Spark číst a zapisovat přímo z Spark do nebo z vyhrazeného fondu SQL, je nutné mít *db_owner* oprávnění.

Po vytvoření uživatelů ověřte, že se můžete dotazovat na účet úložiště pomocí serveru SQL bez serveru.

## <a name="access-control-to-workspace-pipeline-runs"></a>Řízení přístupu ke spuštěním kanálu v pracovním prostoru

### <a name="workspace-managed-identity"></a>Identita spravovaná pracovním prostorem

> [!IMPORTANT]
> Aby bylo možné úspěšně spustit kanály zahrnující datové sady nebo aktivity, které odkazují na vyhrazený fond SQL, musí být identitám pracovního prostoru udělen přímý přístup ke fondu SQL.

Spuštěním následujících příkazů v každém vyhrazeném fondu SQL umožněte, aby identita spravovaná pracovním prostorem spouštěla kanály v databázi fondu SQL:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Toto oprávnění je možné odebrat spuštěním následujícího skriptu na stejném fondu SQL:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Další kroky

Přehled identity spravované v pracovním prostoru synapse najdete v tématu [spravovaná identita Azure synapse Workspace](../security/synapse-workspace-managed-identity.md). Další informace o objektech zabezpečení databáze naleznete v tématu [objekty zabezpečení](https://msdn.microsoft.com/library/ms181127.aspx). Další informace o databázových rolích najdete v článku [databázové role](https://msdn.microsoft.com/library/ms189121.aspx) .
