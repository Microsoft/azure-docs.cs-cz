---
title: Autorizovat přístup k serveru a databázi pomocí přihlašovacích údajů a uživatelských účtů
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Přečtěte si, jak Azure SQL Database, Managed instance SQL a Azure synapse ověřují uživatele pro přístup pomocí přihlašovacích údajů a uživatelských účtů. Také se dozvíte, jak udělit databázové role a explicitní oprávnění k autorizaci přihlašovacích údajů a uživatelů k provádění akcí a dotazování na data.
keywords: zabezpečení databáze SQL,správa zabezpečení databáze,zabezpečení přihlášení,zabezpečení databáze,přístup k databázi
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: 7be78a0c05942aebb9e3154a79035d71f87becd3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334901"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Autorizace přístupu k databázi SQL Database, spravované instanci SQL a Azure synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

V tomto článku se dozvíte o:

- Možnosti konfigurace Azure SQL Database, spravované instance Azure SQL a služby Azure synapse Analytics (dříve SQL Data Warehouse), aby uživatelé mohli provádět úlohy správy a přistupovat k datům uloženým v těchto databázích.
- Konfigurace přístupu a autorizace po počátečním vytvoření nového serveru.
- Přidání přihlašovacích údajů a uživatelských účtů do hlavní databáze a uživatelských účtů a udělení těchto účtů oprávnění správce.
- Postup přidání uživatelských účtů v uživatelských databázích, a to buď přidružených k přihlašovacím jménům, nebo jako obsažené uživatelské účty.
- Nakonfigurujte uživatelské účty s oprávněním v uživatelských databázích pomocí databázových rolí a explicitních oprávnění.

> [!IMPORTANT]
> Databáze v Azure SQL Database, Azure SQL Managed instance a Azure synapse se ve zbývající části tohoto článku společně nazývají databáze a server odkazuje na [Server](logical-servers.md) , který spravuje databáze pro Azure SQL Database a Azure synapse.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

[**Ověřování**](security-overview.md#authentication) je proces, který označuje, že uživatel vyžádá. Uživatel se připojí k databázi pomocí uživatelského účtu.
Když se uživatel pokusí připojit k databázi, poskytne uživatelskému účtu a ověřovacím informacím. Uživatel je ověřený pomocí jedné z následujících dvou metod ověřování:

- [Ověřování SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Pomocí této metody ověřování uživatel pro navázání připojení odešle název uživatelského účtu a přidružené heslo. Toto heslo je uloženo v hlavní databázi pro uživatelské účty propojené s přihlašovacími údaji nebo uložené v databázi obsahující uživatelské účty, které *nejsou* propojeny s přihlašovacími údaji.
- [Ověřování Azure Active Directory](authentication-aad-overview.md)

  Pomocí této metody ověřování uživatel odesílá název uživatelského účtu a požaduje, aby služba používala informace přihlašovacích údajů uložené v Azure Active Directory (Azure AD).

**Přihlášení a uživatelé**: uživatelský účet v databázi může být přidružen k přihlašovacímu jménu, které je uloženo v hlavní databázi, nebo může být uživatelské jméno, které je uloženo v individuální databázi.

- **Přihlášení** je individuální účet v hlavní databázi, na který je možné propojit uživatelský účet v jedné nebo více databázích. V případě přihlášení se informace o přihlašovacích údajích pro uživatelský účet ukládají společně s přihlášením.
- **Uživatelský účet** je individuální účet v jakékoli databázi, která může být, ale nemusí být propojená s přihlašovacími údaji. V případě uživatelského účtu, který není propojený s přihlášením, se informace o přihlašovacích údajích ukládají společně s uživatelským účtem.

[**Autorizace**](security-overview.md#authorization) pro přístup k datům a provádění různých akcí se spravuje pomocí databázových rolí a explicitních oprávnění. Autorizace odkazuje na oprávnění přiřazená uživateli a určuje, co může uživatel dělat. Autorizaci řídí členství v databázové [roli](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) vašeho uživatelského účtu a oprávnění na [úrovni objektů](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Doporučený postup je udělit uživatelům co nejmenší možná oprávnění.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Existující přihlašovací údaje a uživatelské účty po vytvoření nové databáze

Při prvním nasazení Azure SQL zadejte přihlašovací jméno správce a přidružené heslo pro toto přihlášení. Tento účet správce se nazývá **Správce serveru**. Během nasazení dojde k následující konfiguraci přihlašovacích údajů a uživatelů v hlavní databázi a uživatelských databázích:

- Přihlašovací jméno SQL s oprávněními správce se vytvoří pomocí zadaného přihlašovacího jména. [Přihlášení](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) je individuální uživatelský účet pro přihlášení k SQL Database, spravované instanci SQL a Azure synapse.
- Tomuto přihlášení je uděleno úplné oprávnění správce pro všechny databáze jako [objekt zabezpečení na úrovni serveru](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Přihlášení má všechna dostupná oprávnění a nedá se omezit. V rámci spravované instance SQL se toto přihlášení přidá do [pevné role serveru sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (Tato role v Azure SQL Database neexistuje).
- [user account](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` Pro toto přihlášení se vytvoří uživatelský účet s názvem v každé uživatelské databázi. Uživatel [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) má všechna oprávnění databáze v databázi a je namapován na `db_owner` pevně danou databázovou roli. Další pevné databázové role jsou popsány dále v tomto článku.

Chcete-li identifikovat účty správců pro databázi, otevřete Azure Portal a přejděte na kartu **vlastnosti** serveru nebo spravované instance.

![Správci SQL serveru](./media/logins-create-manage/sql-admins.png)

![Správci SQL serveru](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> Přihlašovací jméno správce nelze po vytvoření změnit. Pokud chcete resetovat heslo pro správce serveru, přejděte na [Azure Portal](https://portal.azure.com), klikněte na **SQL servery**, vyberte server ze seznamu a potom klikněte na **resetovat heslo**. Heslo pro spravovanou instanci SQL resetujete tak, že přejdete na Azure Portal, kliknete na instanci a kliknete na **resetovat heslo**. Můžete také použít PowerShell nebo rozhraní příkazového řádku Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Vytvoření dalších přihlášení a uživatelů s oprávněním správce

V tuto chvíli je váš server nebo spravovaná instance nakonfigurovaný jenom pro přístup pomocí jednoho přihlašovacího a uživatelského účtu SQL. Chcete-li vytvořit další přihlášení s úplnými nebo částečnými oprávněními pro správu, máte následující možnosti (v závislosti na vašem režimu nasazení):

- **Vytvoření účtu správce Azure Active Directory s úplnými oprávněními správce**

  Povolte Azure Active Directory ověřování a vytvořte přihlašovací jméno správce Azure AD. Jeden Azure Active Directory účet může být nakonfigurovaný jako správce nasazení Azure SQL s úplnými oprávněními pro správu. Tento účet může být buď jednotlivý účet, nebo účet skupiny zabezpečení. Pokud chcete použít účty Azure AD pro připojení k SQL Database, spravované instanci SQL nebo Azure synapse, **musí** být nakonfigurovaný správce Azure AD. Podrobné informace o povolení ověřování Azure AD pro všechny typy nasazení Azure SQL najdete v následujících článcích:

  - [Pro ověřování pomocí SQL použít Azure Active Directory ověřování](authentication-aad-overview.md)
  - [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL](authentication-aad-configure.md)

- **Ve spravované instanci SQL vytvořte přihlašovací údaje SQL s úplnými oprávněními pro správu.**

  - V hlavní databázi vytvořte další přihlášení SQL.
  - Pomocí příkazu [ALTER Server role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) přidejte přihlašovací údaje k [pevné roli serveru sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) . Toto přihlášení bude mít úplná oprávnění správce.
  - Případně můžete vytvořit [přihlašovací údaje služby Azure AD](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) pomocí syntaxe [Vytvoření přihlašovacího jména](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) .

- **V SQL Database Vytvořte přihlašovací údaje SQL s omezenými oprávněními pro správu.**

  - V hlavní databázi vytvořte další přihlášení SQL.
  - Vytvořte uživatelský účet v hlavní databázi přidružené k tomuto novému přihlášení.
  - Přidejte uživatelský účet do role, do `dbmanager` `loginmanager` role nebo do obou v `master` databázi pomocí příkazu [ALTER role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) (pro Azure synapse použijte příkaz [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > `dbmanager``loginmanager`role **a se** nevztahují k nasazením spravovaných instancí SQL.

  Členové těchto [speciálních rolí hlavní databáze](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) pro Azure SQL Database mají oprávnění k vytváření a správě databází nebo k vytváření a správě přihlášení. V databázích vytvořených uživatelem, který je členem `dbmanager` role, je člen namapován na `db_owner` pevnou databázovou roli a může se přihlásit k databázi a spravovat ji pomocí `dbo` uživatelského účtu. Tyto role nemají žádná explicitní oprávnění mimo hlavní databázi.

  > [!IMPORTANT]
  > V SQL Database nemůžete vytvořit další přihlašovací jméno SQL s úplnými oprávněními pro správu.

## <a name="create-accounts-for-non-administrator-users"></a>Vytváření účtů pro uživatele bez oprávnění správce

Účty uživatelů bez oprávnění správce můžete vytvořit pomocí jedné ze dvou metod:

- **Vytvoření přihlašovacích údajů**

  V hlavní databázi vytvořte přihlašovací údaje SQL. Pak vytvořte uživatelský účet v každé databázi, ke které uživatel potřebuje přístup, a přiřaďte k tomuto přihlašovacímu účtu uživatelský účet. Tento přístup je preferovaný, když uživatel musí mít přístup k několika databázím a chcete uchovat hesla synchronizované. Tento přístup ale má při použití s geografickou replikací složité, protože přihlašovací jméno musí být vytvořené na primárním i sekundárním serveru. Další informace najdete v tématu [Konfigurace a Správa zabezpečení Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání](active-geo-replication-security-configure.md).
- **Vytvoření uživatelského účtu**

  Vytvořte uživatelský účet v databázi, ke kterému uživatel potřebuje přístup (označovaný také jako [zahrnutý uživatel](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Pomocí SQL Database můžete tento typ uživatelského účtu vždycky vytvořit.
  - Pomocí spravované instance SQL podporující [objekty zabezpečení serveru Azure AD](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)můžete vytvářet uživatelské účty pro ověřování ve spravované instanci SQL, aniž by bylo nutné vytvořit uživatele databáze jako uživatel databáze s omezením.

  V případě tohoto přístupu jsou informace o ověřování uživatele uloženy v každé databázi a automaticky replikovány do geograficky replikovaných databází. Pokud však stejný účet existuje ve více databázích a používáte ověřování Azure SQL, je nutné uchovat hesla ručně. Navíc platí, že pokud má uživatel účet v různých databázích s různými hesly, může se stát, že tato hesla budou mít potíže.

> [!IMPORTANT]
> Pokud chcete vytvořit obsažené uživatele namapované na identity Azure AD, musíte se přihlásit pomocí účtu Azure AD, který je správcem v databázi v Azure SQL Database. V rámci spravované instance SQL může přihlášení k SQL pomocí `sysadmin` oprávnění vytvořit také přihlášení nebo uživatele služby Azure AD.

Příklady, jak vytvořit přihlašovací jména a uživatele, najdete v tématech:

- [Vytvořit přihlašovací údaje pro Azure SQL Database](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Vytvoření přihlašovacích údajů pro spravovanou instanci Azure SQL](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Vytvoření přihlašovacích údajů pro Azure synapse](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Vytvořit uživatele](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Vytváření uživatelů s omezením pro Azure AD](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Kurz zabezpečení, který zahrnuje vytváření uživatelů v Azure SQL Database, najdete v tématu [kurz: zabezpečená Azure SQL Database](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Používání pevné a vlastní databázové role

Po vytvoření uživatelského účtu v databázi, a to buď na základě přihlašovacích údajů, nebo jako obsaženého uživatele, můžete tomuto uživateli povolit provádění různých akcí a přístup k datům v konkrétní databázi. K autorizaci přístupu můžete použít následující metody:

- **Pevné databázové role**

  Přidejte uživatelský účet do [pevné databázové role](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). K dispozici jsou 9 pevné databázové role, z nichž každá má definovanou sadu oprávnění. Nejběžnějšími pevnými databázovými role jsou: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**a **db_denydatareader**. Role **db_owner** se obvykle používá k udělení úplných oprávnění pouze několika uživatelům. Ostatní pevné databázové role jsou užitečné pro rychlé vytvoření jednoduché databáze ve vývojovém prostředí, ale nedoporučují se pro většinu databází v produkčním prostředí. Například **db_datareader** pevná databázová role uděluje přístup pro čtení ke všem tabulkám v databázi, což je více, než je nezbytně nutné.

  - Postup přidání uživatele do pevné databázové role:

    - V Azure SQL Database použijte příkaz [ALTER role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) . Příklady najdete v tématu [ALTER role – příklady](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples) .
    - Azure synapse, použijte příkaz [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Příklady najdete v tématu [sp_addrolemember příklady](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Vlastní databázová role**

  Pomocí příkazu [Create role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) vytvořte vlastní databázovou roli. Vlastní role vám umožní vytvořit vlastní databázové role definované uživatelem a pečlivě udělit každé roli minimální oprávnění potřebná pro potřeby podniku. Pak můžete přidat uživatele do vlastní role. Pokud je uživatel členem více rolí, všechna jejich oprávnění se agregují.
- **Udělit přímo oprávnění**

  Udělte uživatelskému účtu [oprávnění](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) přímo. Ve službě SQL Database je dostupných více než 100 oprávnění, která můžete jednotlivě přidělit nebo zamítnout. Mnohá z těchto oprávnění jsou vnořená. Oprávnění `UPDATE` pro schéma například zahrnuje oprávnění `UPDATE` pro každou tabulku v tomto schématu. Podobně jako ve většině systémů oprávnění má zamítnutí oprávnění přednost před udělením oprávnění a přepíše ho. Kvůli velkému počtu oprávnění a používání vnořených oprávnění může návrh vhodného systému oprávnění vyžadovat pečlivou studii, aby byla vaše databáze dobře chráněna. Začněte seznamem oprávnění podle tématu [Oprávnění (databázový stroj)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) a prohlédněte si [plakát](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) s přehledem oprávnění.

## <a name="using-groups"></a>Používání skupin

Efektivní správa přístupu používá oprávnění přiřazená skupinám zabezpečení služby Active Directory a pevné nebo vlastní role místo pro jednotlivé uživatele.

- Při použití ověřování Azure Active Directory vložte Azure Active Directory uživatele do skupiny zabezpečení Azure Active Directory. Pro tuto skupinu vytvořte uživatele databáze s omezením. Přidejte jednoho nebo více uživatelů databáze jako člena do vlastních nebo předdefinovaných databázových rolí s konkrétními oprávněními, která jsou pro danou skupinu uživatelů vhodná.

- Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Jeden nebo více uživatelů databáze umístěte do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

  > [!NOTE]
  > Skupiny můžete také použít pro uživatele databáze bez omezení.

Měli byste se seznámit s následujícími funkcemi, které jde použít k omezení nebo zvýšení oprávnění:

- K dočasnému bezpečnému zvýšení oprávnění můžete použít [zosobnění](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) a [přihlašování k modulům](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server).
- K omezení řádků, ke kterým má uživatel přístup, můžete použít [zabezpečení na úrovni řádku](https://docs.microsoft.com/sql/relational-databases/security/row-level-security).
- K omezení rizika ohrožení citlivých dat můžete použít [maskování dat](dynamic-data-masking-overview.md).
- K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine).

## <a name="next-steps"></a>Další kroky

Přehled všech funkcí zabezpečení Azure SQL Database a SQL Managed instance najdete v tématu [Přehled zabezpečení](security-overview.md).
