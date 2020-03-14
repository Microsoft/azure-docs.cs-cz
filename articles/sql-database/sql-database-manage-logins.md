---
title: Přihlášení a uživatelé
description: Přečtěte si, jak Azure SQL Database a Azure synapse Analytics ověřují uživatele pro přístup pomocí přihlašovacích jmen a uživatelských účtů a používají role a explicitní oprávnění k autorizaci přihlašovacích údajů a uživatelů k provádění akcí v rámci databází i na úrovni serveru.
keywords: zabezpečení databáze SQL,správa zabezpečení databáze,zabezpečení přihlášení,zabezpečení databáze,přístup k databázi
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 7c70d5dd19ec0495fe09152b5653363ad369347c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268909"
---
# <a name="granting-database-access-and-authorization-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Udělení přístupu k databázi a její autorizace pro SQL Database a Azure synapse Analytics pomocí přihlašovacích údajů a uživatelských účtů

Ověřený přístup k databázím v Azure SQL Database a Azure synapse Analytics (dříve Azure SQL Data Warehouse) se spravují pomocí přihlašovacích údajů a uživatelských účtů. [**Ověřování**](sql-database-security-overview.md#authentication) je proces, který označuje, že uživatel vyžádá.

- Přihlášení je individuální účet v hlavní databázi.
- Uživatelský účet je individuální účet v jakékoli databázi a nemusí být přidružený k přihlašovacímu účtu.

> [!IMPORTANT]
> Databáze v Azure SQL Database a Azure synapse Analytics (dříve Azure SQL Data Warehouse) se společně ve zbývající části tohoto článku jako Azure SQL Database (pro zjednodušení).

Uživatel databáze se připojuje k databázi SQL Azure pomocí uživatelského účtu a je ověřený pomocí jedné z následujících dvou metod:

- [Ověřování SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication), které se skládá z přihlašovacího jména nebo názvu uživatelského účtu a přidruženého hesla uloženého v Azure SQL Database.
- [Ověřování Azure Active Directory](sql-database-aad-authentication.md), které používá přihlašovací údaje uložené v Azure Active Directory

Oprávnění pro přístup k datům a provádění různých akcí v rámci služby Azure SQL Database se spravují pomocí databázových rolí a explicitních oprávnění. [**Autorizace**](sql-database-security-overview.md#authorization) odkazuje na oprávnění přiřazená uživateli v rámci Azure SQL Database a určuje, co může uživatel dělat. Autorizaci řídí členství v databázové [roli](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) vašeho uživatelského účtu a oprávnění na [úrovni objektů](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Doporučený postup je udělit uživatelům co nejmenší možná oprávnění.

V tomto článku se dozvíte:

- Konfigurace přístupu a autorizace po počátečním vytvoření nového Azure SQL Database
- Přidání přihlašovacích údajů a uživatelských účtů do hlavní databáze a uživatelských účtů a udělení těchto účtů oprávnění správce
- Postup přidání uživatelských účtů v uživatelských databázích, a to buď přidružených k přihlašovacím jménům, nebo jako obsažené uživatelské účty
- Konfigurace uživatelských účtů s oprávněními v uživatelských databázích pomocí databázových rolí a explicitních oprávnění

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Existující přihlašovací údaje a uživatelské účty po vytvoření nové databáze

Když vytvoříte první nasazení Azure SQL Database, zadáváte přihlašovací jméno správce a přidružené heslo pro toto přihlášení. Tento účet správce se nazývá **Správce serveru**. Během nasazení dojde k následující konfiguraci přihlašovacích údajů a uživatelů v hlavní databázi a uživatelských databázích:

- Přihlašovací jméno SQL s oprávněními správce se vytvoří pomocí zadaného přihlašovacího jména. [Přihlášení](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) je individuální uživatelské účty pro přihlášení k SQL Database.
- Tomuto přihlášení je uděleno úplné oprávnění správce pro všechny databáze jako [objekt zabezpečení na úrovni serveru](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Toto přihlášení má všechna dostupná oprávnění v rámci SQL Database a nelze je omezit. Ve spravované instanci se toto přihlášení přidá do [pevné role serveru sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (Tato role pro databáze s jednou nebo ve fondu neexistuje).
- Pro toto přihlášení se vytvoří [uživatelský účet](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) s názvem `dbo` v každé uživatelské databázi. Uživatel [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) má všechna oprávnění databáze v databázi a je namapován na `db_owner` fixní databázovou roli. Další pevné databázové role jsou popsány dále v tomto článku.

Chcete-li identifikovat účty správců pro váš SQL Server, otevřete Azure Portal a přejděte na kartu **vlastnosti** vašeho SQL serveru nebo SQL Database.

![Správci SQL serveru](media/sql-database-manage-logins/sql-admins.png)

> [!IMPORTANT]
> Přihlašovací jméno správce nelze po vytvoření změnit. Pokud chcete resetovat heslo pro správce logického serveru, přejděte na [Azure Portal](https://portal.azure.com), klikněte na **SQL servery**, vyberte server ze seznamu a potom klikněte na **resetovat heslo**. Pokud chcete resetovat heslo pro server spravované instance, přejděte na Azure Portal, klikněte na instanci a pak klikněte na **resetovat heslo**. Můžete také použít PowerShell nebo rozhraní příkazového řádku Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Vytvoření dalších přihlášení a uživatelů s oprávněním správce

V tuto chvíli je váš SQL Database nakonfigurovaný jenom pro přístup pomocí jednoho přihlašovacího a uživatelského účtu SQL. Chcete-li vytvořit další přihlášení s úplnými nebo částečnými oprávněními pro správu, máte následující možnosti (v závislosti na vašem režimu nasazení):

- **Vytvoření účtu správce Azure Active Directory s úplnými oprávněními správce**

  Povolte Azure Active Directory ověřování a vytvořte přihlašovací jméno správce Azure AD. Jeden Azure Active Directory účet lze nakonfigurovat jako správce nasazení SQL Database s úplnými oprávněními správce. Tento účet může být buď jednotlivý účet, nebo účet skupiny zabezpečení. Pokud chcete pro připojení k SQL Database používat účty Azure AD, **musí** být nakonfigurovaný správce Azure AD. Podrobné informace o povolení ověřování Azure AD pro všechny SQL Database typy nasazení najdete v následujících článcích:

  - [Pro ověřování pomocí SQL použít Azure Active Directory ověřování](sql-database-aad-authentication.md)
  - [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL](sql-database-aad-authentication-configure.md)

- **V nasazení spravované instance Vytvořte přihlašovací údaje SQL s úplnými oprávněními pro správu.**

  - Vytvoření dalšího SQL Serverho přihlášení ve spravované instanci
  - Pomocí příkazu [ALTER Server role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) přidejte přihlašovací údaje k [pevné roli serveru sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) . Toto přihlášení bude mít úplná oprávnění správce.
  - Případně můžete vytvořit [přihlašovací údaje služby Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) pomocí syntaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Vytvoření přihlašovacího jména</a> .

- **V jednom nebo sdruženém nasazení Vytvořte přihlašovací údaje SQL s omezenými oprávněními správce.**

  - Vytvoření dalšího přihlášení SQL v hlavní databázi pro jedno nebo sdružené nasazení databáze nebo nasazení spravované instance
  - Vytvoření uživatelského účtu v hlavní databázi přidružené k tomuto novému přihlášení
  - Přidejte uživatelský účet do `dbmanager`, do `loginmanager` role nebo do obou v databázi `master` pomocí příkazu [ALTER Server role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (pro Azure synapse Analytics použijte příkaz [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > role `dbmanager` a `loginmanager` **se** nevztahují k nasazením spravovaných instancí.

  Členové těchto [speciálních rolí hlavní databáze](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) pro databáze s jednou nebo ve fondu můžou uživatelům mít oprávnění k vytváření a správě databází nebo k vytváření a správě přihlašovacích údajů. V databázích vytvořených uživatelem, který je členem role `dbmanager`, je člen namapován na roli `db_owner` pevná databáze a může se přihlásit k databázi a spravovat ji pomocí uživatelského účtu `dbo`. Tyto role nemají žádná explicitní oprávnění mimo hlavní databázi.

  > [!IMPORTANT]
  > V jedné nebo sdružené databázi nelze vytvořit další přihlašovací údaje SQL s úplnými oprávněními pro správu.

## <a name="create-accounts-for-non-administrator-users"></a>Vytváření účtů pro uživatele bez oprávnění správce

Účty uživatelů bez oprávnění správce můžete vytvořit pomocí jedné ze dvou metod:

- **Vytvoření přihlašovacích údajů**

  V hlavní databázi vytvořte přihlašovací údaje SQL. Pak vytvořte uživatelský účet v každé databázi, ke které uživatel potřebuje přístup, a přiřaďte k tomuto přihlašovacímu účtu uživatelský účet. Tento přístup je preferovaný, když uživatel musí mít přístup k několika databázím a chcete uchovat hesla synchronizované. Tento přístup ale má při použití s geografickou replikací složité, protože přihlašovací jméno musí být vytvořené na primárním i sekundárním serveru. Další informace najdete v tématu [Konfigurace a Správa zabezpečení Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání](sql-database-geo-replication-security-config.md).
- **Vytvoření uživatelského účtu**

  Vytvořte uživatelský účet v databázi, ke kterému uživatel potřebuje přístup (označovaný také jako [zahrnutý uživatel](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - U jedné nebo sdružené databáze můžete tento typ uživatelského účtu vždy vytvořit.
  - S databází spravované instance, která nepodporuje [objekty zabezpečení serveru Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), můžete tento typ uživatelského účtu vytvořit pouze v [databázi s omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Pomocí spravované instance, která podporuje [objekty zabezpečení serveru Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), můžete vytvořit uživatelské účty, které se budou ověřovat do spravované instance, aniž by bylo nutné vytvořit uživatele databáze jako uživatel databáze s omezením.

  V případě tohoto přístupu jsou informace o ověřování uživatele uloženy v každé databázi a automaticky replikovány do geograficky replikovaných databází. Pokud však stejný účet existuje ve více databázích a používáte ověřování SQL, je nutné uchovat hesla ručně. Navíc platí, že pokud má uživatel účet v různých databázích s různými hesly, může se stát, že tato hesla budou mít potíže.

> [!IMPORTANT]
> Pokud chcete vytvořit obsažené uživatele namapované na identity Azure AD, musíte se přihlásit pomocí účtu Azure AD, který je správcem v SQL Database. V rámci spravované instance můžou přihlášení k SQL pomocí `sysadmin` také vytvořit přihlašovací jméno nebo uživatele služby Azure AD.

Příklady, jak vytvořit přihlašovací jména a uživatele, najdete v tématech:

- [Vytvoření přihlašovacích údajů pro databáze s jednou nebo ve fondu](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Vytvořit přihlašovací údaje pro databázi spravované instance](https://docs.microsoft.com/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Vytvoření přihlašovacích údajů pro databázi Azure synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Vytvořit uživatele](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Vytváření uživatelů s omezením pro Azure AD](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Kurz zabezpečení, který zahrnuje vytváření SQL Server obsažených uživatelů v jedné nebo ve fondu databází, najdete v tématu [kurz: zabezpečení jedné nebo sdružené databáze](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Používání pevné a vlastní databázové role

Po vytvoření uživatelského účtu v databázi, a to buď na základě přihlašovacích údajů, nebo jako obsaženého uživatele, můžete tomuto uživateli povolit provádění různých akcí a přístup k datům v konkrétní databázi. K autorizaci přístupu můžete použít následující metody:

- **Pevné databázové role**

  Přidejte uživatelský účet do [pevné databázové role](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). K dispozici jsou 9 pevné databázové role, z nichž každá má definovanou sadu oprávnění. Nejběžnějšími pevnými databázovými role jsou: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**a **db_denydatareader**. Role **db_owner** se obvykle používá k udělení úplných oprávnění pouze několika uživatelům. Ostatní pevné databázové role jsou užitečné pro rychlé vytvoření jednoduché databáze ve vývojovém prostředí, ale nedoporučují se pro většinu databází v produkčním prostředí. Například **db_datareader** pevná databázová role uděluje přístup pro čtení ke všem tabulkám v databázi, což je více, než je nezbytně nutné.

  - Postup přidání uživatele do pevné databázové role:

    - V Azure SQL Database použijte příkaz [ALTER role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) . Příklady najdete v tématu [ALTER role – příklady](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples) .
    - Azure synapse Analytics použijte příkaz [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Příklady najdete v tématu [sp_addrolemember příklady](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Vlastní databázová role**

  Pomocí příkazu [Create role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) vytvořte vlastní databázovou roli. Vlastní role vám umožní vytvořit vlastní databázové role definované uživatelem a pečlivě udělit každé roli minimální oprávnění potřebná pro potřeby podniku. Pak můžete přidat uživatele do vlastní role. Pokud je uživatel členem více rolí, všechna jejich oprávnění se agregují.
- **Udělit přímo oprávnění**

  Udělte uživatelskému účtu [oprávnění](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) přímo. Ve službě SQL Database je dostupných více než 100 oprávnění, která můžete jednotlivě přidělit nebo zamítnout. Mnohá z těchto oprávnění jsou vnořená. Oprávnění `UPDATE` pro schéma například zahrnuje oprávnění `UPDATE` pro každou tabulku v tomto schématu. Podobně jako ve většině systémů oprávnění má zamítnutí oprávnění přednost před udělením oprávnění a přepíše ho. Kvůli velkému počtu oprávnění a používání vnořených oprávnění může návrh vhodného systému oprávnění vyžadovat pečlivou studii, aby byla vaše databáze dobře chráněna. Začněte seznamem oprávnění podle tématu [Oprávnění (databázový stroj)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) a prohlédněte si [plakát](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) s přehledem oprávnění.

## <a name="using-groups"></a>Používání skupin

Efektivní správa přístupu používá oprávnění přiřazená skupinám zabezpečení služby Active Directory a pevné nebo vlastní role místo pro jednotlivé uživatele.

- Při použití ověřování Azure Active Directory vložte Azure Active Directory uživatele do skupiny zabezpečení Azure Active Directory. Pro tuto skupinu vytvořte uživatele databáze s omezením. Jeden nebo více uživatelů databáze umístěte do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

- Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Jeden nebo více uživatelů databáze umístěte do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

  > [!NOTE]
  > Skupiny můžete také použít pro uživatele databáze bez omezení.

Měli byste se seznámit s následujícími funkcemi, které jde použít k omezení nebo zvýšení oprávnění:

- K dočasnému bezpečnému zvýšení oprávnění můžete použít [zosobnění](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) a [přihlašování k modulům](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server).
- K omezení řádků, ke kterým má uživatel přístup, můžete použít [zabezpečení na úrovni řádku](https://docs.microsoft.com/sql/relational-databases/security/row-level-security).
- K omezení rizika ohrožení citlivých dat můžete použít [maskování dat](sql-database-dynamic-data-masking-get-started.md).
- K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine).

## <a name="next-steps"></a>Další kroky

Přehled všech funkcí zabezpečení služby SQL Database najdete v [přehledu zabezpečení SQL](sql-database-security-overview.md).
