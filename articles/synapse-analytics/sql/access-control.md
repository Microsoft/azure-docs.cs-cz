---
title: Správa přístupu k pracovním prostorům, datům a kanálům
description: Zjistěte, jak spravovat řízení přístupu k pracovním prostorům, datům a kanálům v pracovním prostoru Azure Synapse Analytics (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424766"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Správa přístupu k pracovním prostorům, datům a kanálům

Zjistěte, jak spravovat řízení přístupu k pracovním prostorům, datům a kanálům v pracovním prostoru Azure Synapse Analytics (preview).

> [!NOTE]
> Pro GA bude RBAC rozvinutější zavedením rolí Azure RBAC specifických pro Synapse

## <a name="access-control-for-workspace"></a>Řízení přístupu pro pracovní prostor

Pro produkční nasazení do pracovního prostoru Azure Synapse doporučujeme uspořádat vaše prostředí tak, aby bylo snadné zřídit uživatele a správce.

> [!NOTE]
> Zde přijatý přístup je vytvořit několik skupin zabezpečení a potom nakonfigurovat pracovní prostor tak, aby je používal konzistentně. Po nastavení skupin potřebuje správce pouze spravovat členství v rámci skupin zabezpečení.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Krok 1: Nastavení skupin zabezpečení s názvy podle tohoto vzoru

1. Vytvořit skupinu zabezpečení s názvem`Synapse_WORKSPACENAME_Users`
2. Vytvořit skupinu zabezpečení s názvem`Synapse_WORKSPACENAME_Admins`
3. Přidání `Synapse_WORKSPACENAME_Admins` do `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Krok 2: Příprava výchozího účtu ADLS Gen2

Při zřizování pracovního prostoru jste museli vybrat účet ADLSGEN2 a kontejner pro souborový systém pro pracovní prostor, který chcete použít.

1. Otevření [portálu Azure](https://portal.azure.com)
2. Přechod na účet ADLSGEN2
3. Přechod na kontejner (souborový systém), který jste vybrali pro pracovní prostor Azure Synapse
4. Klikněte na **Řízení přístupu (IAM)**
5. Přiřaďte následující role:
   1. **Role čtenáře:**`Synapse_WORKSPACENAME_Users`
   2. Role **vlastníka dat objektu blob úložiště:**`Synapse_WORKSPACENAME_Admins`
   3. Role **přispěvatele dat objektu blob úložiště:**`Synapse_WORKSPACENAME_Users`
   4. Role **vlastníka dat objektu blob úložiště:**`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Krok 3: Konfigurace seznamu správců pracovního prostoru

1. Přejít na [ **webové uživatelské uzuliny Azure Synapse**](https://web.azuresynapse.net)
2. Přejít na **spravovat**  > **řízení přístupu** **zabezpečení** > 
3. Klikněte na **Přidat správce**a vyberte`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Krok 4: Konfigurace přístupu sql admin pro pracovní prostor

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Přechod do pracovního prostoru
3. Přejít na **správce služby** > Active**Directory nastavení.**
4. Klikněte na **Nastavit správce.**
5. Vyberte `Synapse_WORKSPACENAME_Admins`
6. klikněte na **Vybrat**
7. klikněte na **Uložit**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Krok 5: Přidání a odebrání uživatelů a správců do skupin zabezpečení

1. Přidání uživatelů, kteří potřebují přístup pro správu,`Synapse_WORKSPACENAME_Admins`
2. Přidat všechny ostatní uživatele do`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Řízení přístupu k datům

Řízení přístupu k podkladovým datům je rozděleno do tří částí:

- Přístup k účtu úložiště v rovině dat (již nakonfigurovaný výše v kroku 2)
- Přístup k databázím SQL (pro fondy SQL i SQL na vyžádání)
- Vytvoření pověření pro databáze SQL na vyžádání přes účet úložiště

## <a name="access-control-to-sql-databases"></a>Řízení přístupu k databázím SQL

> [!TIP]
> Níže uvedené kroky je třeba spustit pro **každou** databázi SQL udělit uživateli přístup ke všem databázím SQL.

### <a name="sql-on-demand"></a>SQL na vyžádání

Chcete-li uživateli udělit přístup k **jedné** databázi na vyžádání SQL, postupujte podle kroků v tomto příkladu:

1. Vytvořit přihlášení

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Vytvořit uživatele

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Přidat UŽIVATELE k členům zadané role

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>Fondy SQL

Chcete-li uživateli udělit přístup k **jedné** databázi SQL, postupujte takto:

1. Vytvořte uživatele v databázi spuštěním následujícího příkazu, který cílí na požadovanou databázi v selektoru kontextu (rozevírací nabídka pro výběr databází):

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
> *db_datareader* a *db_datawriter* mohou pracovat pro oprávnění ke čtení a zápisu, pokud je udělení *oprávnění db_owner* nežádoucí.
> Pro uživatele Spark číst a zapisovat přímo ze Spark do nebo z fondu SQL, *db_owner* oprávnění je vyžadováno.

Po vytvoření uživatelů ověřte, že SQL na vyžádání může dotaz ovat účet úložiště:

- Spusťte následující příkaz zaměřený na **hlavní** databázi SQL na vyžádání:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Řízení přístupu ke spuštění kanálu pracovního prostoru

### <a name="workspace-managed-identity"></a>Identita spravovaná pracovním prostorem

> [!IMPORTANT]
> Chcete-li úspěšně spustit kanály, které obsahují datové sady nebo aktivity, které odkazují na fond SQL, musí být identitě pracovního prostoru udělen přístup přímo do fondu SQL.

Spusťte následující příkazy v každém fondu SQL, abyste umožnili spravované identitě pracovního prostoru spouštět kanály v databázi fondu SQL:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Toto oprávnění lze odebrat spuštěním následujícího skriptu ve stejném fondu SQL:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Další kroky

Přehled přístupu a řízení v synapse SQL naleznete v tématu [Synapse SQL řízení přístupu](../sql/access-control.md). Další informace o objektových objektech databáze naleznete [v tématu Principals](https://msdn.microsoft.com/library/ms181127.aspx). Další informace o databázových rolích naleznete v článku [Role databáze.](https://msdn.microsoft.com/library/ms189121.aspx)
