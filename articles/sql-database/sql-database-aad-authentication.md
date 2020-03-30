---
title: Azure Active Directory
description: Informace o tom, jak používat Azure Active Directory pro ověřování pomocí databáze SQL, spravované instance a datového skladu SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: ef20c5b8babdf378b88997ae2fd7b98350c31319
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124925"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Použití ověřování azure služby Active Directory pro ověřování pomocí SQL

Azure Active Directory ověřování je mechanismus připojení k Azure [SQL Database](sql-database-technical-overview.md), [spravované instance](sql-database-managed-instance.md)a SQL [Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit ve službě Azure Active Directory (Azure AD). 

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

Díky ověřování Azure AD můžete na jednom místě centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění. Mezi jeho výhody patří následující:

- Poskytuje alternativu k ověřování serveru SQL Server.
- Pomáhá zastavit šíření identit uživatelů napříč databázovými servery.
- Umožňuje střídání hesel na jednom místě.
- Zákazníci mohou spravovat oprávnění databáze pomocí externích skupin (Azure AD).
- Umožňuje integrované ověřování systému Windows a další formy ověřování podporované službou Azure Active Directory.
- Ověřování Azure AD používá obsažené uživatele databáze k ověřování identit na úrovni databáze.
- Azure AD podporuje ověřování na základě tokenů pro aplikace připojení k databázi SQL.
- Azure AD ověřování podporuje ADFS (federace domény) nebo nativní ověřování uživatelů/hesel pro místní Azure Active Directory bez synchronizace domény.
- Azure AD podporuje připojení z aplikace SQL Server Management Studio využívající univerzální ověřování Active Directory, což zahrnuje službu Multi-Factor Authentication (MFA).  MFA zahrnuje silné ověřování s využitím celé řady možností ověření – telefonických hovorů, textových zpráv, čipových karet s kódem PIN nebo oznámení mobilní aplikace. Další informace najdete v tématu [Podpora vícefaktorového ověřování Azure AD pro služby SQL Database a SQL Data Warehouse v aplikaci SSMS](sql-database-ssms-mfa-authentication.md).
- Azure AD podporuje podobná připojení z nástrojů SQL Server Data Tools (SSDT) využívající interaktivní ověřování Active Directory. Další informace najdete v tématu [Podpora Azure Active Directory v nástrojích SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Připojení k SQL Serveru spuštěnéna na virtuálním počítači Azure není podporované pomocí účtu Azure Active Directory. Místo toho použijte účet služby Active Directory.  

Kroky konfigurace zahrnují následující postupy konfigurace a použití ověřování služby Azure Active Directory.

1. Vytvořte a naplňte Azure AD.
2. Volitelné: Přidružte nebo změňte aktivní adresář, který je aktuálně přidružený k vašemu předplatnému Azure.
3. Vytvořte správce Služby Azure Active Directory pro databázový server Azure SQL, spravovanou instanci nebo [datový sklad Azure SQL](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Nakonfigurujte klientské počítače.
5. Vytvořte obsažené uživatele databáze v databázi mapované na identity Azure AD.
6. Připojte se k databázi pomocí identit Azure AD.

> [!NOTE]
> Informace o tom, jak vytvořit a naplnit Azure AD a pak nakonfigurovat Azure AD pomocí Azure SQL Database, spravované instance a SQL Data Warehouse, najdete [v tématu Konfigurace Azure AD s Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architektura důvěryhodnosti

Následující diagram vysoké úrovně shrnuje architekturu řešení pomocí ověřování Azure AD s Azure SQL Database. Stejné koncepty platí pro sql datový sklad. Pro podporu nativního uživatelského hesla Azure AD se považuje za jenom cloudová část a Azure AD/Azure SQL Database. Pro podporu federovaného ověřování (nebo uživatele/hesla pro pověření systému Windows) je vyžadována komunikace s blokem Služby ADFS. Šipky ukazují komunikační cesty.

![aad auth diagram][1]

Následující diagram označuje vztahy federace, důvěryhodnosti a hostování, které umožňují klientovi připojit se k databázi odesláním tokenu. Token je ověřen službou Azure AD a databáze mu důvěřuje. Zákazník 1 může představovat službu Azure Active Directory s nativními uživateli nebo Službu Azure AD s federovanými uživateli. Zákazník 2 představuje možné řešení včetně importovaných uživatelů; V tomto příkladu pochází z federované služby Azure Active Directory s ADFS synchronizovány s Azure Active Directory. Je důležité si uvědomit, že přístup k databázi pomocí ověřování Azure AD vyžaduje, aby hostování předplatné je přidružené k Azure AD. Stejné předplatné musí být použito k vytvoření SQL Serveru hostujícího Azure SQL Database nebo SQL Data Warehouse.

![vztah předplatného][2]

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD existují dva účty správce pro server SQL Database a spravovanou instanci; původního správce serveru SQL Server a správce služby Azure AD. Stejné koncepty platí pro sql datový sklad. Pouze správce na základě účtu Azure AD můžete vytvořit první Azure AD obsahoval databázi uživatele v databázi uživatele. Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud je správce účet skupiny, může jej použít libovolný člen skupiny, což umožňuje více správců Azure AD pro instanci SQL Server. Použití účtu skupiny jako správce zvyšuje možnosti správy tím, že umožňuje centrálně přidávat a odebírat členy skupiny ve službě Azure AD bez evidenčních změn uživatelů nebo oprávnění v databázi SQL. Kdykoli lze nakonfigurovat pouze jednoho správce Azure AD (uživatele nebo skupinu).

![struktura správce][3]

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit nové uživatele, musíte mít `ALTER ANY USER` oprávnění v databázi. Oprávnění `ALTER ANY USER` lze udělit libovolnému uživateli databáze. Oprávnění `ALTER ANY USER` je také v držení účtů správce serveru `CONTROL ON DATABASE` a `ALTER ON DATABASE` uživatelů databáze s oprávněním `db_owner` nebo pro tuto databázi a členy role databáze.

Chcete-li vytvořit uživatele obsažené databáze v Azure SQL Database, Spravované instance nebo SQL Data Warehouse, musíte se připojit k databázi nebo instanci pomocí identity Azure AD. Chcete-li vytvořit první obsažené uživatele databáze, musíte se připojit k databázi pomocí správce Azure AD (kdo je vlastníkem databáze). To je znázorněno v [článku Konfigurace a správa ověřování služby Azure Active Directory pomocí databáze SQL Database nebo datového skladu SQL](sql-database-aad-authentication-configure.md). Jakékoli ověřování Azure AD je možné jenom v případě, že správce Azure AD byl vytvořen pro Azure SQL Database nebo SQL Data Warehouse server. Pokud byl správce služby Azure Active Directory odebrán ze serveru, stávající uživatelé služby Azure Active Directory vytvořené dříve uvnitř SQL Serveru se již nemohou připojit k databázi pomocí svých přihlašovacích údajů služby Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkce a omezení Azure AD

- Následující členy Azure AD lze zřídit v Azure SQL server nebo SQL Data Warehouse:

  - Nativní členové: Člen vytvořený ve službě Azure AD ve spravované doméně nebo v doméně zákazníka. Další informace najdete [v tématu Přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Členové federované domény: Člen vytvořený ve službě Azure AD s federovovovnou doménou. Další informace naleznete v tématu [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Importovaní členové z jiných členů Azure AD, kteří jsou nativní nebo federované členy domény.
  - Skupiny služby Active Directory vytvořené jako skupiny zabezpečení.

- Uživatelé Azure AD, kteří jsou `db_owner` součástí skupiny, která má roli serveru, nemohou použít syntaxi **[CREATE DATABASE SCOPED credential](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** proti Azure SQL Database a Azure SQL Data Warehouse. Zobrazí se následující chyba:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udělte `db_owner` roli přímo jednotlivým uživatelům služby Azure AD ke zmírnění problému **s pověřením OBORU VYTVOŘENÍ DATABÁZE.**

- Tyto systémové funkce vrátí hodnoty NULL při spuštění v rámci objektů zabezpečení Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Spravované instance

- Objekty serveru Azure AD (přihlášení) a uživatelé jsou podporovány jako funkce náhledu pro [spravované instance](sql-database-managed-instance.md).
- Nastavení objektů zabezpečení serveru Azure AD (přihlášení) mapované na skupinu Azure AD jako vlastník databáze není podporováno ve [spravovaných instanci](sql-database-managed-instance.md).
    - Rozšíření tohoto je, že při přidání skupiny `dbcreator` jako součást role serveru, uživatelé z této skupiny se mohou připojit ke spravované instanci a vytvořit nové databáze, ale nebude mít přístup k databázi. Důvodem je, že nový vlastník databáze je SA a nikoli uživatel služby Azure AD. Tento problém se neprojeví, pokud `dbcreator` je jednotlivý uživatel přidán do role serveru.
- Sql Agent správy a provádění úloh je podporována pro objekty zabezpečení serveru Azure AD (přihlášení).
- Operace zálohování a obnovení databáze lze provést pomocí objektů zabezpečení serveru Azure AD (přihlášení).
- Auditování všech příkazů souvisejících s objekty zabezpečení serveru Azure AD (přihlášení) a události ověřování je podporována.
- Vyhrazené připojení správce pro objekty serveru Azure AD (přihlášení), které jsou členy role serveru sysadmin je podporována.
    - Podporováno prostřednictvím nástroje SQLCMD Utility a sql server management studio.
- Přihlašovací aktivační události jsou podporovány pro události přihlášení pocházející z objektů zabezpečení serveru Azure AD (přihlášení).
- Service Broker a DB mail lze nastavit pomocí hlavního serveru Azure AD (přihlášení).


## <a name="connecting-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování Azure Active Directory podporuje následující metody připojení k databázi pomocí identit Azure AD:

- Heslo služby Azure Active Directory
- Integrovaná služba Azure Active Directory
- Univerzální služba Azure Active Directory s vícefaktorovou pomocí
- Použití ověřování tokenu aplikace

Následující metody ověřování jsou podporované pro objekty zabezpečení serveru Azure AD (přihlášení)**(public Preview**):

- Heslo služby Azure Active Directory
- Integrovaná služba Azure Active Directory
- Univerzální služba Azure Active Directory s vícefaktorovou pomocí


### <a name="additional-considerations"></a>Další aspekty

- Chcete-li zvýšit možnosti správy, doporučujeme zřídit vyhrazenou skupinu Azure AD jako správce.   
- Pro server Azure SQL Database nebo Azure SQL Data Warehouse lze kdykoli nakonfigurovat jenom jednoho správce Azure AD (uživatele nebo skupiny).
  - Přidání objektů zabezpečení serveru Azure AD (přihlášení) pro spravované instance **(public preview)** umožňuje možnost vytvoření více objektů zabezpečení serveru `sysadmin` Azure AD (přihlášení), které lze přidat do role.
- K serveru Azure SQL Database, spravované instanci nebo datovému skladu Azure SQL se může zpočátku připojit jenom správce Azure AD pro SQL Server pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat následné uživatele databáze Azure AD.   
- Doporučujeme nastavit časový limit připojení na 30 sekund.   
- SQL Server 2016 Management Studio a SQL Server Datové nástroje pro Visual Studio 2015 (verze 14.0.60311.1Duben 2016 nebo novější) podporují ověřování Azure Active Directory. (Azure AD ověřování je podporováno **zprostředkovatelem dat rozhraní .NET Framework pro SqlServer**; alespoň verze .NET Framework 4.6). Proto nejnovější verze těchto nástrojů a aplikace datové vrstvy (DAC a . BACPAC) můžete použít ověřování Azure AD.   
- Počínaje verzí 15.0.1 podporují [nástroj sqlcmd](/sql/tools/sqlcmd-utility) a [nástroj bcp](/sql/tools/bcp-utility) interaktivní ověřování služby Active Directory pomocí vícefaktorového ověřování.
- Sql Server Datové nástroje pro Visual Studio 2015 vyžaduje alespoň z dubna 2016 verzi datových nástrojů (verze 14.0.60311.1). V současné době uživatelé Azure AD nejsou zobrazeny v Průzkumníku objektů SSDT. Jako zástupné řešení zobrazíte uživatele v [souboru sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC Driver 6.0 for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) podporuje ověřování Azure AD. Viz také [nastavení vlastností připojení](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase nelze ověřit pomocí ověřování Azure AD.   
- Ověřování Azure AD je podporované pro SQL Database podle datových listů **databáze importu** portálu Azure a **exportu.** Import a export pomocí ověřování Azure AD je taky podporovaný z příkazu PowerShell.   
- Ověřování Azure AD je podporované pro SQL Database, Spravovanou instanci a SQL Datový sklad pomocí příkazového příkazového příkazu. Další informace naleznete v [tématu Konfigurace a správa ověřování služby Azure Active Directory pomocí databáze SQL Database nebo sql data warehouse](sql-database-aad-authentication-configure.md) a SQL Server - [az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Další kroky

- Pokud se chcete dozvědět, jak vytvořit a naplnit Azure AD a pak nakonfigurovat Azure AD pomocí Azure SQL Database nebo Azure SQL Data Warehouse, přečtěte si, [že najdete v tématu Konfigurace a správa ověřování Azure Active Directory pomocí databáze SQL, spravované instance nebo datového skladu SQL](sql-database-aad-authentication-configure.md).
- Kurz použití objektů zabezpečení serveru Azure AD (přihlášení) se spravovanými instancemi najdete v [tématu Objekty zabezpečení serveru Azure AD (přihlášení) se spravovanými instancemi](sql-database-managed-instance-aad-security-tutorial.md)
- Přehled přihlášení, uživatelů, databázových rolí a oprávnění v databázi SQL naleznete v tématu [Přihlášení, uživatelé, databázové role a oprávnění](sql-database-manage-logins.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](https://msdn.microsoft.com/library/ms181127.aspx).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](https://msdn.microsoft.com/library/ms189121.aspx).
- Syntaxe při vytváření objektů zabezpečení serveru Azure AD (přihlášení) pro spravované instance najdete v [tématu CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
