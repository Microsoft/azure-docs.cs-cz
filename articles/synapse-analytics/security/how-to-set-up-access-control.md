---
title: Jak nastavit řízení přístupu pro pracovní prostor synapse
description: Tento článek vás seznámí s tím, jak řídit přístup k pracovnímu prostoru synapse pomocí rolí Azure, rolí synapse, oprávnění SQL a oprávnění Gitu.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7dbbe3afb2a5eb19b5c4893bd73eddbf11e788d4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218072"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Jak nastavit řízení přístupu pro pracovní prostor synapse 

Tento článek vás seznámí s tím, jak řídit přístup k pracovnímu prostoru synapse pomocí rolí Azure, rolí synapse, oprávnění SQL a oprávnění Gitu.   

V této příručce nastavíte pracovní prostor a nakonfigurujete základní systém řízení přístupu, který je vhodný pro mnoho projektů synapse.  Pak popisuje pokročilejší možnosti pro jemnější řízení, které byste měli potřebovat.  

Řízení přístupu synapse se dá zjednodušit pomocí skupin zabezpečení, které jsou zarovnané na role a osoby ve vaší organizaci.  Pro správu přístupu stačí přidat a odebrat uživatele ze skupin zabezpečení.

Než začnete tento návod, přečtěte si [Přehled řízení přístupu synapse](./synapse-workspace-access-control-overview.md) a seznamte se s mechanismy řízení přístupu, které používá synapse.   

## <a name="access-control-mechanisms"></a>Mechanismy řízení přístupu

> [!NOTE]
> Postup pořízený v tomto průvodci je vytvoření několika skupin zabezpečení a přiřazení rolí k těmto skupinám. Po nastavení skupin stačí spravovat členství v rámci skupin zabezpečení, abyste mohli řídit přístup k pracovnímu prostoru.

Pokud chcete zabezpečit pracovní prostor synapse, postupujte podle vzoru pro konfiguraci následujících položek:

- **Skupiny zabezpečení** pro seskupení uživatelů s podobnými požadavky na přístup.
- **Role Azure** slouží k řízení, kdo může vytvářet a spravovat fondy SQL, Apache Spark fondy a prostředí Integration runtime a přistupovat k adls Gen2mu úložišti.
- **Role synapse**, pro řízení přístupu k artefaktům publikovaných kódu, použití Apache Spark výpočetních prostředků a prostředí Integration runtime 
- **Oprávnění SQL** pro řízení přístupu k administrativnímu a rovině dat pro fondy SQL. 
- **Oprávnění Gitu** pro řízení, kdo může přistupovat ke artefaktům kódu ve správě zdrojového kódu, pokud konfigurujete podporu Gitu pro pracovní prostor 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Postup zabezpečení pracovního prostoru synapse

Tento dokument používá ke zjednodušení pokynů standardní názvy. Nahraďte je názvy dle vašeho výběru.

|Nastavení | Standardní název | Popis |
| :------ | :-------------- | :---------- |
| **Pracovní prostor synapse** | `workspace1` |  Název, který bude mít pracovní prostor synapse. |
| **Účet ADLSGEN2** | `storage1` | Účet ADLS, který se má používat s vaším pracovním prostorem. |
| **Kontejner** | `container1` | Kontejner v STG1, který bude ve výchozím nastavení používat pracovní prostor |
| **Tenant služby Active Directory** | `contoso` | název tenanta služby Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>Krok 1: nastavení skupin zabezpečení

>[!Note] 
>Během období Preview se doporučuje vytvořit skupiny zabezpečení namapované na synapse **synapse SQL Administrator** a **synapse Apache Spark role správců** .  Díky zavedení nových jemnějších rolí a oborů synapse RBAC se teď doporučuje používat tyto nové funkce k řízení přístupu k vašemu pracovnímu prostoru.  Tyto nové role a obory poskytují větší flexibilitu v konfiguraci a rozpoznávají, že vývojáři často používají kombinaci SQL a Sparku při vytváření analytických aplikací a můžou se jim dát udělit přístup ke konkrétním prostředkům, a ne k celému pracovnímu prostoru. [Přečtěte si další informace](./synapse-workspace-synapse-rbac.md) o synapse RBAC.

Vytvořte následující skupiny zabezpečení pro váš pracovní prostor:

- **`workspace1_SynapseAdministrators`** pro uživatele, kteří potřebují plnou kontrolu nad pracovním prostorem.  Nejdřív přidejte do této skupiny zabezpečení aspoň sebe.
- **`workspace1_SynapseContributors`** pro vývojáře, kteří potřebují vyvíjet, ladit a publikovat kód ve službě.   
- **`workspace1_SynapseComputeOperators`** pro uživatele, kteří potřebují spravovat a monitorovat fondy Apache Spark a prostředí Integration runtime.
- **`workspace1_SynapseCredentialUsers`** pro uživatele, kteří potřebují ladit a spouštět kanály orchestrace pomocí přihlašovacích údajů pracovního prostoru MSI (spravované služby) a zrušit spuštění kanálu.   

Brzy přiřadíte role synapse těmto skupinám v oboru pracovního prostoru.  

Vytvořit také tuto skupinu zabezpečení: 
- **`workspace1_SQLAdmins`**, skupina pro uživatele, kteří potřebují autoritu Správce služby SQL Active Directory v rámci fondů SQL v pracovním prostoru. 

`workspace1_SQLAdmins`Skupina se použije při konfiguraci oprávnění SQL v fondech SQL při jejich vytváření. 

Pro základní nastavení jsou tyto pět skupin dostatečné. Později můžete přidat skupiny zabezpečení, které budou obsluhovat uživatele, kteří potřebují více specializovaného přístupu, nebo poskytnout uživatelům přístup pouze k určitým prostředkům.

> [!NOTE]
>- Naučte se vytvořit skupinu zabezpečení v [tomto článku](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).
>- Naučte se přidat skupinu zabezpečení z jiné skupiny zabezpečení v [tomto článku](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).

>[!Tip]
>Jednotliví uživatelé synapse můžou pomocí Azure Active Directory v Azure Portal zobrazit jejich členství ve skupinách a určit, které role se jim mají udělit.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>Krok 2: Příprava účtu úložiště ADLS Gen2

Pracovní prostor synapse používá výchozí kontejner úložiště pro:
  - Ukládání souborů zálohovaných dat pro tabulky Spark
  - Protokoly spuštění pro úlohy Spark
  - Správa knihoven, které se rozhodnete nainstalovat

Identifikujte následující informace o úložišti:

- Účet ADLS Gen2, který se má použít pro váš pracovní prostor. Tento dokument ho volá `storage1` . `storage1` se považuje za primární účet úložiště pro váš pracovní prostor.
- Kontejner ve `workspace1` výchozím nastavení bude použit v pracovním prostoru synapse. Tento dokument ho volá `container1` . 

- Pomocí Azure Portal přiřaďte ke skupinám zabezpečení následující role Azure. `container1` 

  - Přiřaďte roli **Přispěvatel dat objektů BLOB úložiště** k `workspace1_SynapseAdmins` 
  - Přiřaďte roli **Přispěvatel dat objektů BLOB úložiště** k `workspace1_SynapseContributors`
  - Přiřaďte roli **Přispěvatel dat objektů BLOB úložiště** k `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>Krok 3: vytvoření a konfigurace pracovního prostoru synapse

V Azure Portal vytvořte pracovní prostor synapse:

- Vyberte své předplatné.
- Vyberte nebo vytvořte skupinu prostředků, pro kterou máte roli **vlastníka** Azure.
- Pojmenování pracovního prostoru `workspace1`
- Zvolit `storage1` účet úložiště
- Vyberte `container1` pro kontejner, který se používá jako systém souborů.
- Otevřít WS1 v synapse studiu
- Přejděte ke **správě**  >  **Access Control** a přiřaďte role synapse v *oboru pracovního prostoru* do skupin zabezpečení následujícím způsobem:
  - Přiřadit roli **správce synapse** k `workspace1_SynapseAdministrators` 
  - Přiřadit roli **Přispěvatel synapse**`workspace1_SynapseContributors` 
  - Přiřazení role **operátora COMPUTE synapse**`workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>Krok 4: Udělte pracovnímu prostoru MSI přístup k výchozímu kontejneru úložiště.

Aby bylo možné spouštět kanály a provádět systémové úlohy, synapse vyžaduje, aby identita spravovaného pracovního prostoru (MSI) měla přístup ke službě `container1` ve výchozím účtu adls Gen2.

- Otevřete Azure Portal.
- Vyhledejte účet úložiště, `storage1` a potom `container1`
- Pomocí **Access Control (IAM)** se ujistěte, že je role **přispěvatele dat objektu BLOB úložiště** přiřazená ke službě MSI pracovního prostoru.
  - Pokud není přiřazen, přiřaďte ho.
  - Soubor MSI má stejný název jako pracovní prostor. V tomto článku by to bylo `workspace1` .

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>Krok 5: udělení synapse správcům roli Přispěvatel Azure v pracovním prostoru 

Aby uživatelé mohli vytvářet fondy SQL, Apache Spark fondy a prostředí Integration runtime, musí mít k pracovnímu prostoru aspoň přístup k Azure přispěvateli. Role přispěvatele také umožňuje těmto uživatelům spravovat prostředky, včetně pozastavení a škálování.

- Otevřete Azure Portal.
- Vyhledejte pracovní prostor. `workspace1`
- Přiřaďte roli **přispěvatele Azure** `workspace1` ke službě `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>Krok 6: přiřazení role správce služby SQL Active Directory

Tvůrce pracovní stanice je automaticky nastavený jako správce služby SQL Active Directory pro daný pracovní prostor.  Tuto roli lze udělit pouze jednomu uživateli nebo skupině. V tomto kroku přiřadíte ke skupině zabezpečení Správce služby SQL Active Directory v pracovním prostoru `workspace1_SQLAdmins` .  Přiřazení této role poskytuje této skupině vysoce privilegovaný přístup správce ke všem fondům a databázím SQL v pracovním prostoru.   

- Otevřete Azure Portal.
- Přejděte na adresu `workspace1`.
- V části **Nastavení** vyberte **Správce služby SQL Active Directory** .
- Vyberte **nastavit správce** a zvolte **`workspace1_SQLAdmins`**

>[!Note]
>Krok 6 je nepovinný.  Můžete se rozhodnout udělit `workspace1_SQLAdmins` skupině méně privilegované role. Chcete-li přiřadit `db_owner` nebo jiné role SQL, je nutné spustit skripty v každé databázi SQL. 

## <a name="step-7-grant-access-to-sql-pools"></a>Krok 7: udělení přístupu ke fondům SQL

Ve výchozím nastavení se všem uživatelům, kteří mají přiřazenou roli správce synapse, přiřadí taky `db_owner` role SQL na neserverovém fondu SQL, integrovaném a všech jeho databázích.

Přístup k fondům SQL pro ostatní uživatele a pro pracovní prostor MSI se řídí pomocí oprávnění SQL.  Přiřazení oprávnění SQL vyžaduje, aby se skripty SQL po vytvoření spouštěly na všech databázích SQL.  Existují tři případy, které vyžadují spuštění těchto skriptů:
1. Udělení přístupu jiným uživatelům k fondu SQL bez serveru, integrovanému a databázím
2. Udělení přístupu libovolným uživatelům k vyhrazenému fondu databází
3. Udělení přístupu ke službě MSI v pracovním prostoru pro databázi fondu SQL za účelem umožnění úspěšného spuštění kanálů, které vyžadují přístup ke fondu SQL

Příklady skriptů SQL jsou uvedeny níže.

Chcete-li udělit přístup k vyhrazené databázi fondu SQL, mohou být skripty spouštěny autorem pracovního prostoru nebo libovolným členem `workspace1_SQLAdmins` skupiny.  

Chcete-li udělit přístup k fondu SQL bez serveru, "předdefinované", mohou být skripty spouštěny libovolným členem `workspace1_SQLAdmins` skupiny nebo  `workspace1_SynapseAdministrators` skupiny. 

> [!TIP]
> Níže uvedené kroky musí být spuštěny pro **každý** fond SQL, aby bylo možné udělit uživatelům přístup ke všem databázím SQL s výjimkou oddílu [oprávnění vymezeného v pracovním prostoru](#workspace-scoped-permission) , kde můžete uživateli přiřadit roli sysadmin na úrovni pracovního prostoru.

### <a name="step-71-serverless-sql-pool-built-in"></a>Krok 7,1: fond SQL bez serveru, integrovaná

V této části jsou příklady skriptů, které ukazují, jak udělit uživateli oprávnění k přístupu ke konkrétní databázi nebo ke všem databázím ve fondu SQL bez serveru, vestavěném.

> [!NOTE]
> V příkladech skriptu nahraďte *alias* aliasem uživatele nebo skupiny, kterým se uděluje přístup, a *domény* s doménou společnosti, kterou používáte.

#### <a name="database-scoped-permission"></a>Oprávnění s rozsahem databáze

Pokud chcete uživateli udělit přístup k **jediné** databázi SQL bez serveru, postupujte podle kroků v tomto příkladu:

1. Vytvořit přihlašovací údaje

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Vytvořit uživatele

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Přidat uživatele do členů zadané role

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Oprávnění s rozsahem v pracovním prostoru

Chcete-li udělit úplný přístup ke **všem** fondům SQL bez serveru v pracovním prostoru, použijte skript v tomto příkladu:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>Krok 7,2: vyhrazené fondy SQL

Pokud chcete udělit přístup k **jedné** vyhrazené databázi fondu SQL, postupujte podle těchto kroků v editoru skriptu SQL synapse:

1. Vytvořte uživatele v databázi tak, že v cílové databázi spustíte následující příkaz, který vyberete pomocí rozevíracího seznamu *připojit k* :

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Udělit uživateli roli pro přístup k databázi:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* a *db_datawriter* mohou fungovat pro oprávnění ke čtení a zápisu, pokud udělení *db_owner* oprávnění není žádoucí.
> Aby mohl uživatel Spark číst a zapisovat přímo z Sparku do nebo z fondu SQL, je nutné mít *db_owner* oprávnění.

Po vytvoření uživatelů spusťte dotazy, abyste ověřili, že fond SQL bez serveru se může dotazovat na účet úložiště.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>Krok 7,3: řízení přístupu SQL pro spuštění kanálu synapse

### <a name="workspace-managed-identity"></a>Spravovaná identita v pracovním prostoru

> [!IMPORTANT]
> Chcete-li úspěšně spustit kanály zahrnující datové sady nebo aktivity, které odkazují na fond SQL, musí být identitám pracovního prostoru udělen přístup ke fondu SQL.

Spusťte následující příkazy v každém fondu SQL, abyste povolili identitu spravovaného systému v pracovním prostoru ke spouštění kanálů v databázích fondu SQL:  

>[!note]
>V níže uvedených skriptech pro vyhrazenou databázi fondu SQL je název DatabaseName stejný jako název fondu.  Pro databázi v předdefinovaném fondu SQL bez serveru je název databáze DatabaseName názvem databáze.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

Toto oprávnění je možné odebrat spuštěním následujícího skriptu na stejném fondu SQL:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>Krok 8: Přidání uživatelů do skupin zabezpečení

Počáteční konfigurace vašeho systému řízení přístupu je hotová.

Chcete-li spravovat přístup, můžete přidat nebo odebrat uživatele do skupin zabezpečení, které jste nastavili.  I když můžete uživatele ručně přiřadit k rolím synapse, pokud tak učiníte, nebudou jejich oprávnění konzistentně konfigurovat. Místo toho přidejte nebo odeberte uživatele do skupin zabezpečení.

## <a name="step-9-network-security"></a>Krok 9: zabezpečení sítě

Jako poslední krok zabezpečení pracovního prostoru byste měli zabezpečit přístup k síti pomocí:
- [Firewall pracovního prostoru](./synapse-workspace-ip-firewall.md)
- [Spravovaná virtuální síť](./synapse-workspace-managed-vnet.md) 
- [Privátní koncové body](./synapse-workspace-managed-private-endpoints.md)
- [Privátní propojení](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>Krok 10: dokončení

Váš pracovní prostor je teď plně nakonfigurovaný a zabezpečený.

## <a name="supporting-more-advanced-scenarios"></a>Podpora pokročilejších scénářů

Tato příručka se zaměřuje na nastavení základního systému řízení přístupu. Pokročilejší scénáře můžete podporovat vytvořením dalších skupin zabezpečení a přiřazením těchto skupin podrobnějšími rolemi v několika konkrétních oborech. Vezměte v úvahu následující případy:

**Povolit podporu Gitu** pro pracovní prostor pro pokročilejší vývojové scénáře, včetně CI/CD.  V režimu Git budou oprávnění Git určovat, jestli může uživatel potvrdit změny v pracovní větvi.  Publikování do služby probíhá pouze z větve pro spolupráci.  Zvažte vytvoření skupiny zabezpečení pro vývojáře, kteří potřebují vyvíjet a ladit aktualizace v pracovní větvi, ale nepotřebují publikovat změny v živé službě.

**Omezte přístup pro vývojáře** ke konkrétním prostředkům.  Vytvořte další jemnější skupiny zabezpečení pro vývojáře, kteří potřebují přístup jenom ke konkrétním prostředkům.  Přiřaďte tyto skupiny odpovídajícím rolím synapse, které jsou vymezeny na konkrétní fondy Spark, modulu runtime integrace nebo přihlašovací údaje.

**Omezte přístup k artefaktům kódu pomocí operátorů**.  Vytvořte skupiny zabezpečení pro operátory, kteří potřebují monitorovat provozní stav synapse výpočetních prostředků a zobrazovat protokoly, ale kteří nepotřebují přístup ke kódu nebo publikovat aktualizace služby. Přiřaďte tyto skupiny role operátora COMPUTE v oboru pro konkrétní fondy Spark a prostředí Integration runtime.  

## <a name="next-steps"></a>Další kroky

Naučte [se spravovat přiřazení rolí RBAC synapse](./how-to-manage-synapse-rbac-role-assignments.md) vytvořit [pracovní prostor synapse](../quickstart-create-workspace.md) .