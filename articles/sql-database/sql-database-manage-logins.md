---
title: Autorizace přístupu k serveru a databázi pomocí přihlášení a uživatelských účtů
description: Přečtěte si, jak Azure SQL Database a Azure Synapse Analytics ověřují uživatele pro přístup pomocí přihlašovacích údajů a uživatelských účtů. Také se dozvíte, jak databázovat role a explicitní oprávnění k autorizaci přihlášení a uživatelům k provádění akcí a dotazování dat.
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
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124822"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Autorizace přístupu k databázím ověřeným uživatelům k databázi SQL database a Azure Synapse Analytics pomocí přihlášení a uživatelských účtů

V tomto článku se dozvíte o:

- Možnosti konfigurace Azure SQL Database a Azure Synapse Analytics (dříve Azure SQL Data Warehouse) tak, aby uživatelé mohli provádět úlohy správy a přístup k datům uloženým v těchto databázích.
- Konfigurace přístupu a autorizace po počátečním vytvoření nové databáze Azure SQL Database
- Jak přidat přihlášení a uživatelské účty do hlavní databáze a uživatelských účtů a potom těmto účtům udělit oprávnění správce
- Jak přidat uživatelské účty do uživatelských databází, buď přidružené k přihlášení, nebo jako obsažené uživatelské účty
- Konfigurace uživatelských účtů s oprávněními v uživatelských databázích pomocí databázových rolí a explicitních oprávnění

> [!IMPORTANT]
> Databáze v Azure SQL Database a Azure Synapse Analytics (dříve Azure SQL Data Warehouse) se ve zbývající části tohoto článku souhrnně označují jako databáze nebo jako Azure SQL (pro jednoduchost).

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

[**Ověřování**](sql-database-security-overview.md#authentication) je proces dokazování, že uživatel je tím, za koho se považuje. Uživatel se připojí k databázi pomocí uživatelského účtu.
Když se uživatel pokusí připojit k databázi, poskytne uživatelský účet a ověřovací informace. Uživatel je ověřen pomocí jedné z následujících dvou metod ověřování:

- [Ověřování SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Při této metodě ověřování uživatel zaskáčne název uživatelského účtu a přidružené heslo pro navázání připojení. Toto heslo je uloženo v hlavní databázi uživatelských účtů propojených s přihlášením nebo v databázi obsahující uživatelský účet pro uživatelské účty, které nejsou propojeny s přihlášením.
- [Ověřování služby Azure AD](sql-database-aad-authentication.md)

  Pomocí této metody ověřování uživatel odešle název uživatelského účtu a požádá o to, aby služba používala informace o pověření uložené ve službě Azure Active Directory.

**Přihlášení a uživatelé**: V Azure SQL může být uživatelský účet v databázi přidružený k přihlášení, které je uloženo v hlavní databázi nebo může být uživatelské jméno uložené v jednotlivé databázi.

- **Přihlášení** je individuální účet v hlavní databázi, ke kterému lze propojit uživatelský účet v jedné nebo více databázích. Při přihlášení jsou informace o přihlašovacích údajích pro uživatelský účet uloženy spolu s přihlášením.
- **Uživatelský účet** je individuální účet v libovolné databázi, která může být, ale nemusí být propojena s přihlášením. U uživatelského účtu, který není propojen s přihlášením, jsou informace o pověření uloženy spolu s uživatelským účtem.

[**Autorizace**](sql-database-security-overview.md#authorization) pro přístup k datům a provádění různých akcí jsou spravovány pomocí databázových rolí a explicitních oprávnění. Autorizace odkazuje na oprávnění přiřazená uživateli a určuje, co má uživatel povoleno. Autorizace je řízena [členstvím v](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) databázové roli vašeho uživatelského účtu a [oprávněními na úrovni objektů](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Doporučený postup je udělit uživatelům co nejmenší možná oprávnění.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Existující přihlášení a uživatelské účty po vytvoření nové databáze

Při vytváření prvního nasazení Azure SQL zadáte přihlašovací údaje správce a přidružené heslo pro toto přihlášení. Tento účet pro správu se nazývá **Správce serveru**. Následující konfigurace přihlášení a uživatelů v hlavní a uživatelské databáze dochází během nasazení:

- Přihlášení SQL s oprávněními správce je vytvořeno pomocí zadaného přihlašovacího jména. [Přihlášení](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) je jednotlivé uživatelské účty pro přihlášení k databázi SQL.
- Toto přihlášení je uděleno úplná oprávnění správce pro všechny databáze jako [zaregistrovaný objekt na úrovni serveru](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Toto přihlášení má všechna dostupná oprávnění v rámci databáze SQL a nelze je omezit. Ve spravované instanci je toto přihlášení přidáno do [role pevného serveru sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (tato role neexistuje s jedním nebo sdruženými databázemi).
- Pro toto `dbo` přihlášení v každé databázi uživatelů je vytvořen svolaný [uživatelský účet.](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) Uživatel [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) má všechna oprávnění databáze v databázi `db_owner` a je mapován na pevnou databázovou roli. Další pevné databázové role jsou popsány dále v tomto článku.

Pokud chcete identifikovat účty správce pro databázi, otevřete portál Azure a přejděte na kartu **Vlastnosti** serveru nebo spravované instance.

![Správci SQL serveru](media/sql-database-manage-logins/sql-admins.png)

![Správci SQL serveru](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> Přihlašovací jméno správce nelze po vytvoření změnit. Pokud chcete obnovit heslo pro správce logického serveru, přejděte na [portál Azure](https://portal.azure.com), klikněte na **SERVERY SQL**, vyberte server ze seznamu a potom klikněte na Obnovit **heslo**. Pokud chcete obnovit heslo pro server spravovaných instancí, přejděte na portál Azure, klikněte na instanci a klikněte na **Obnovit heslo**. Můžete taky použít PowerShell nebo Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Vytvoření dalších přihlášení a uživatelů s oprávněními správce

V tomto okamžiku je vaše instance Azure SQL nakonfigurovaná jenom pro přístup pomocí jediného přihlášení sql a uživatelského účtu. Chcete-li vytvořit další přihlášení s úplnými nebo částečnými oprávněními správce, máte následující možnosti (v závislosti na režimu nasazení):

- **Vytvoření účtu správce služby Azure Active Directory s úplnými oprávněními správce**

  Povolte ověřování Azure Active Directory a vytvořte přihlášení správce Azure AD. Jeden účet Služby Azure Active Directory lze nakonfigurovat jako správce nasazení databáze SQL s úplnými oprávněními správce. Tento účet může být individuální nebo účet skupiny zabezpečení. Správce Azure AD **musí** být nakonfigurovaný, pokud chcete použít účty Azure AD pro připojení k databázi SQL. Podrobné informace o povolení ověřování Azure AD pro všechny typy nasazení databáze SQL najdete v následujících článcích:

  - [Použití ověřování azure služby Active Directory pro ověřování pomocí SQL](sql-database-aad-authentication.md)
  - [Konfigurace a správa ověřování Azure Active Directory pomocí SQL](sql-database-aad-authentication-configure.md)

- **V nasazení spravované instance vytvořte přihlášení SQL s úplnými oprávněními správce**

  - Vytvoření dalšího přihlášení serveru SQL Server ve spravované instanci
  - Přidejte přihlášení do [role pevného serveru sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) pomocí příkazu [ALTER SERVER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) Toto přihlášení bude mít úplná oprávnění správce.
  - Případně můžete vytvořit [přihlášení Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) pomocí syntaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN.</a>

- **V jednom nebo sdruženém nasazení vytvořte přihlášení SQL s omezenými oprávněními správce.**

  - Vytvoření dalšího přihlášení SQL v hlavní databázi pro nasazení jedné nebo sdružené databáze nebo pro nasazení spravované instance
  - Vytvoření uživatelského účtu v hlavní databázi přidružené k tomuto novému přihlášení
  - Přidejte uživatelský účet `dbmanager`do `loginmanager` , role nebo `master` obojí v databázi pomocí příkazu [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (pro Azure Synapse Analytics použijte příkaz [sp_addrolemember).](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

  > [!NOTE]
  > `dbmanager`a `loginmanager` role se nepřipomínají nasazení spravovaných instancí. **not**

  Členové těchto [zvláštních rolí hlavní databáze](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) pro jednu nebo sdružené databáze umožňují uživatelům oprávnění vytvářet a spravovat databáze nebo vytvářet a spravovat přihlášení. V databázích vytvořených uživatelem, který `dbmanager` je členem role, je `db_owner` člen mapován na pevnou databázovou roli a může se k této databázi přihlašovat a spravovat pomocí uživatelského `dbo` účtu. Tyto role nemají žádná explicitní oprávnění mimo hlavní databázi.

  > [!IMPORTANT]
  > Nelze vytvořit další přihlášení SQL s úplnými oprávněními správce v jedné nebo sdružené databázi.

## <a name="create-accounts-for-non-administrator-users"></a>Vytvoření účtů pro uživatele, kteří nejsou správci

Účty pro uživatele, kteří nejsou administrativní, můžete vytvořit pomocí jedné ze dvou metod:

- **Vytvoření přihlášení**

  Vytvořte přihlášení SQL v hlavní databázi. Potom vytvořte uživatelský účet v každé databázi, ke kterému tento uživatel potřebuje přístup a přidružte uživatelský účet k tomuto přihlášení. Tento přístup je upřednostňován, pokud uživatel musí přistupovat k více databázím a chcete zachovat synchronizované hesla. Tento přístup má však složitosti při použití s geografickou replikací jako přihlášení musí být vytvořen na primární server a sekundární server (y). Další informace najdete [v tématu Konfigurace a správa zabezpečení Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání](sql-database-geo-replication-security-config.md).
- **Vytvoření uživatelského účtu**

  Vytvořte uživatelský účet v databázi, ke kterému uživatel potřebuje přístup (také nazývaný [obsažený uživatel).](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)

  - Pomocí jedné nebo sdružené databáze můžete vždy vytvořit tento typ uživatelského účtu.
  - S databází spravovaných instancí, která nepodporuje [objekty serveru Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), můžete vytvořit pouze tento typ uživatelského účtu v [uzavřené databázi](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Díky spravované instanci podporující [objekty serveru Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)můžete vytvořit uživatelské účty pro ověření spravované instance bez nutnosti vytvoření uživatelů databáze jako uživatele s obsahem databáze.

  S tímto přístupem jsou informace o ověřování uživatelů uloženy v každé databázi a automaticky replikovány do geograficky replikovaných databází. Pokud však stejný účet existuje ve více databázích a používáte ověřování SQL, je nutné zachovat ručně synchronizovaná hesla. Navíc pokud uživatel má účet v různých databázích s různými hesly, zapamatování těchto hesel může stát problém.

> [!IMPORTANT]
> Chcete-li vytvořit obsažené uživatele mapované na identity Azure AD, musíte být přihlášeni pomocí účtu Azure AD, který je správcem v databázi SQL. Ve spravované instanci `sysadmin` přihlášení SQL s oprávněními můžete také vytvořit přihlášení Azure AD nebo uživatele.

Příklady, které ukazují, jak vytvořit přihlášení a uživatele, naleznete v tématu:

- [Vytvoření přihlášení pro jednu nebo sdruženou databázi](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Vytvořit přihlášení pro databázi spravovaných instancí](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Vytvoření přihlášení do databáze Azure Synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Vytvořit uživatele](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Vytvoření azure ad obsahoval uživatele](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Kurz zabezpečení, který zahrnuje vytvoření SQL Serveru obsažených uživatelů v jedné nebo sdružené databázi, naleznete v [tématu Kurz: Zabezpečení jedné nebo sdružené databáze](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Použití pevných a vlastních databázových rolí

Po vytvoření uživatelského účtu v databázi, buď na základě přihlášení nebo jako uzavřený uživatel, můžete autorizovat tohoto uživatele k provádění různých akcí a přístupu k datům v určité databázi. K autorizaci přístupu můžete použít následující metody:

- **Pevné databázové role**

  Přidejte uživatelský účet do [pevné databázové role](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). K dispozici je 9 pevných databázových rolí, z nichž každá má definovanou sadu oprávnění. Nejběžnější pevné databázové role jsou: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**a **db_denydatareader**. Role **db_owner** se obvykle používá k udělení úplných oprávnění pouze několika uživatelům. Ostatní pevné databázové role jsou užitečné pro rychlé vytvoření jednoduché databáze ve vývojovém prostředí, ale nedoporučují se pro většinu databází v produkčním prostředí. Například **db_datareader** pevná role databáze uděluje přístup pro čtení do každé tabulky v databázi, což je více, než je nezbytně nutné.

  - Přidání uživatele do pevné databázové role:

    - V Azure SQL Database použijte příkaz [ALTER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) Příklady například [v příkladech ROLE ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics, použijte [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) prohlášení. Příklady naleznete [sp_addrolemember příklady](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Vlastní role databáze**

  Vytvořte vlastní databázovou roli pomocí příkazu [CREATE ROLE.](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) Vlastní role umožňuje vytvořit vlastní uživatelem definované databázové role a pečlivě udělit každé roli nejmenší oprávnění potřebná pro obchodní potřeby. Potom můžete přidat uživatele do vlastní role. Pokud je uživatel členem více rolí, všechna jejich oprávnění se agregují.
- **Udělit oprávnění přímo**

  Udělte [přímo oprávnění](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) uživatelského účtu. Ve službě SQL Database je dostupných více než 100 oprávnění, která můžete jednotlivě přidělit nebo zamítnout. Mnohá z těchto oprávnění jsou vnořená. Oprávnění `UPDATE` pro schéma například zahrnuje oprávnění `UPDATE` pro každou tabulku v tomto schématu. Podobně jako ve většině systémů oprávnění má zamítnutí oprávnění přednost před udělením oprávnění a přepíše ho. Kvůli velkému počtu oprávnění a používání vnořených oprávnění může návrh vhodného systému oprávnění vyžadovat pečlivou studii, aby byla vaše databáze dobře chráněna. Začněte seznamem oprávnění podle tématu [Oprávnění (databázový stroj)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) a prohlédněte si [plakát](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) s přehledem oprávnění.

## <a name="using-groups"></a>Použití skupin

Efektivní správa přístupu používá oprávnění přiřazená skupinám zabezpečení služby Active Directory a pevným nebo vlastním rolím místo jednotlivým uživatelům.

- Při použití ověřování Azure Active Directory převezte uživatele služby Azure Active Directory do skupiny zabezpečení Služby Azure Active Directory. Pro tuto skupinu vytvořte uživatele databáze s omezením. Umístěte jednoho nebo více uživatelů databáze do vlastní databázové role se specifickými oprávněními odpovídajícími této skupině uživatelů.

- Při použití ověřování SQL vytvořte v databázi obsažené uživatele databáze. Umístěte jednoho nebo více uživatelů databáze do vlastní databázové role se specifickými oprávněními odpovídajícími této skupině uživatelů.

  > [!NOTE]
  > Skupiny můžete použít také pro neobsažené uživatele databáze.

Měli byste se seznámit s následujícími funkcemi, které jde použít k omezení nebo zvýšení oprávnění:

- K dočasnému bezpečnému zvýšení oprávnění můžete použít [zosobnění](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) a [přihlašování k modulům](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server).
- K omezení řádků, ke kterým má uživatel přístup, můžete použít [zabezpečení na úrovni řádku](https://docs.microsoft.com/sql/relational-databases/security/row-level-security).
- K omezení rizika ohrožení citlivých dat můžete použít [maskování dat](sql-database-dynamic-data-masking-get-started.md).
- K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine).

## <a name="next-steps"></a>Další kroky

Přehled všech funkcí zabezpečení služby SQL Database najdete v [přehledu zabezpečení SQL](sql-database-security-overview.md).
