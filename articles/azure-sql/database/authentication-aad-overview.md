---
title: Ověřování služby Azure Active Directory
description: Přečtěte si, jak používat Azure Active Directory k ověřování pomocí Azure SQL Database, spravované instance Azure SQL a Azure synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: d90cc76da20861ae9eca7aaf59a49e5f3e866c92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444428"
---
# <a name="use-azure-active-directory-authentication"></a>Použití ověřování Azure Active Directory

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ověřování pomocí Azure Active Directory (Azure AD) je mechanismus pro připojení k [Azure SQL Database](sql-database-paas-overview.md), [spravované instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)a [azure synapse Analytics (dříve SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit ve službě Azure AD.

> [!NOTE]
> Tento článek se týká Azure SQL Database, spravované instance SQL a Azure synapse Analytics.

Prostřednictvím ověřování služby Azure AD můžete centrálně spravovat identity uživatelů databází a další služby Microsoftu z jednoho umístění. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění. Mezi jeho výhody patří následující:

- Nabízí alternativu k ověřování SQL Server.
- Pomáhá zastavit šíření identit uživatelů napříč servery.
- Umožňuje otočení hesla na jednom místě.
- Zákazníci můžou spravovat databázová oprávnění pomocí skupin externích (Azure AD).
- Může eliminovat ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které Azure Active Directory podporuje.
- Ověřování Azure AD používá k ověřování identit na úrovni databáze uživatele databáze s omezením.
- Azure AD podporuje ověřování založené na tokenech pro aplikace, které se připojují k SQL Database a spravované instanci SQL.
- Ověřování Azure AD podporuje:
  - Jenom cloudové identity Azure AD.
  - Hybridní identity Azure AD, které podporují:
    - Cloudové ověřování se dvěma možnostmi, které jsou spojeny s bezproblémové předávací ověřování **pomocí** jednotného přihlašování (SSO) a ověřování **hodnoty hash hesla** .
    - Federované ověřování.
  - Další informace o metodách ověřování Azure AD a o tom, která z nich si můžete vybrat, najdete v následujícím článku:
    - [Vyberte správnou metodu ověřování pro Azure Active Directory řešení hybridní identity.](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD podporuje připojení z SQL Server Management Studio, která používají univerzální ověřování služby Active Directory, což zahrnuje Multi-Factor Authentication. Multi-Factor Authentication zahrnují silné ověřování s využitím široké škály možností jednoduchého ověřování – telefonní hovor, textová zpráva, čipové karty s kódem PIN nebo oznámení mobilní aplikace. Další informace najdete v tématu [Podpora SSMS pro Azure AD Multi-Factor Authentication s využitím Azure SQL Database, spravované instance SQL a Azure synapse](authentication-mfa-ssms-overview.md) .

- Azure AD podporuje podobná připojení z nástrojů SQL Server Data Tools (SSDT) využívající interaktivní ověřování Active Directory. Další informace najdete v tématu [podpora Azure Active Directory v nástrojích pro SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory) .

> [!NOTE]  
> Připojení k instanci SQL Server, která běží na virtuálním počítači Azure (VM), se pomocí účtu Azure Active Directory nepodporuje. Použijte místo toho doménový účet Active Directory.  

Kroky konfigurace obsahují následující postupy pro konfiguraci a použití ověřování Azure Active Directory.

1. Vytvoření a naplnění služby Azure AD.
2. Volitelné: přidružte nebo změňte službu Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
3. Vytvořte správce Azure Active Directory.
4. Nakonfigurujte klientské počítače.
5. Vytvořte uživatele databáze s omezením ve vaší databázi namapované na identity Azure AD.
6. Připojte se k databázi pomocí identit Azure AD.

> [!NOTE]
> Další informace o tom, jak vytvořit a naplnit Azure AD a jak nakonfigurovat Azure AD pomocí Azure SQL Database, spravované instance SQL a Azure synapse, najdete v tématu [konfigurace Azure AD s Azure SQL Database](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Architektura důvěryhodnosti

- Pro podporu nativních hesel uživatelů Azure AD se považuje jenom cloudová část Azure AD, SQL Database, spravovaná instance SQL a Azure synapse.
- Aby bylo možné podporovat přihlašovací údaje jednotného přihlašování Windows (nebo uživatel/heslo pro přihlašovací údaje systému Windows), použijte Azure Active Directory přihlašovací údaje z federované nebo spravované domény, která je nakonfigurovaná pro bezproblémové jednotné přihlašování pro předávací a zatřiďovací ověřování pomocí hesla. Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](../../active-directory/hybrid/how-to-connect-sso.md).
- Aby bylo možné podporovat federované ověřování (nebo uživatele/heslo pro přihlašovací údaje systému Windows), je vyžadována komunikace s blokem ADFS.

Další informace o hybridních identitách Azure AD, nastavení a synchronizaci najdete v následujících článcích:

- Ověřování hodnotou hash hesla – [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Předávací ověřování – [Azure Active Directory předávací ověřování](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federované ověřování – [nasazení Active Directory Federation Services (AD FS) v Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) a [Azure AD Connect a federace](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Ukázkové federované ověřování s infrastrukturou ADFS (nebo uživatel/heslo pro přihlašovací údaje systému Windows) najdete v diagramu níže. Šipky označují cesty komunikace.

![Diagram ověřování AAD][1]

Následující diagram označuje vztahy federace, vztahu důvěryhodnosti a hostování, které umožňují klientovi připojit se k databázi odesláním tokenu. Token se ověřuje pomocí Azure AD a je důvěryhodný pro databázi. Zákazník 1 může představovat Azure Active Directory s nativními uživateli nebo Azure AD s federovaným uživateli. Zákazník 2 představuje možné řešení, včetně importovaných uživatelů, v tomto příkladu přichází z federovaného Azure Active Directory se službou AD FS synchronizovanou pomocí Azure Active Directory. Je důležité pochopit, že přístup k databázi pomocí ověřování Azure AD vyžaduje, aby se k Azure AD přidružil hostitelské předplatné. K vytvoření Azure SQL Database, spravované instance SQL nebo prostředků Azure synapse se musí použít stejné předplatné.

![vztah předplatného][2]

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD jsou k dispozici dva účty správce: původní správce Azure SQL Database a správce Azure AD. Stejné koncepty platí i pro Azure synapse. Pouze správce, který je založen na účtu Azure AD, může vytvořit prvního uživatele databáze s omezením Azure AD v uživatelské databázi. Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Když je správcem účet skupiny, může ho použít libovolný člen skupiny a povolit pro tento server více správců Azure AD. Použití účtu skupiny jako správce vylepšuje spravovatelnost tím, že umožňuje centrálně přidávat a odebírat členy skupin ve službě Azure AD beze změny uživatelů nebo oprávnění v SQL Database nebo Azure synapse. Kdykoli se dá nakonfigurovat jenom jeden správce Azure AD (uživatel nebo skupina).

![struktura správy][3]

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit nové uživatele, musíte mít `ALTER ANY USER` v databázi oprávnění. `ALTER ANY USER`Oprávnění lze udělit libovolnému uživateli databáze. `ALTER ANY USER`Oprávnění je také uchováváno pomocí účtů správců serveru a uživatelů databáze s `CONTROL ON DATABASE` `ALTER ON DATABASE` oprávněním nebo pro tuto databázi a členy `db_owner` databázové role.

Pokud chcete vytvořit uživatele databáze s omezením v Azure SQL Database, spravované instanci SQL nebo Azure synapse, musíte se připojit k databázi nebo instanci pomocí identity Azure AD. Chcete-li vytvořit prvního uživatele databáze s omezením, je nutné se připojit k databázi pomocí správce Azure AD (který je vlastníkem databáze). To je znázorněno v [konfiguraci a správě Azure Active Directory ověřování pomocí SQL Database nebo Azure synapse](authentication-aad-configure.md). Ověřování Azure AD je možné pouze v případě, že byl vytvořen správce Azure AD pro Azure SQL Database, spravovanou instanci SQL nebo Azure synapse. Pokud byl správce Azure Active Directory odebraný ze serveru, stávající uživatelé Azure Active Directory dříve vytvořeni dříve v SQL Server se již nebudou moci připojit k databázi pomocí příslušných přihlašovacích údajů Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkce a omezení služby Azure AD

- Pro Azure SQL Database se dají zřídit následující členové Azure AD:

  - Nativní členové: člen vytvořený v Azure AD ve spravované doméně nebo v doméně zákazníka. Další informace najdete v tématu [Přidání vlastního názvu domény do Azure AD](../../active-directory/fundamentals/add-custom-domain.md).
  - Členové domény služby Active Directory federované s Azure Active Directory ve spravované doméně, která je nakonfigurovaná pro bezproblémové jednotné přihlašování pomocí předávacího nebo hesla hash ověřování. Další informace najdete v tématu [Microsoft Azure nyní podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) a [Azure Active Directory bezproblémové jednotné přihlašování](../../active-directory/hybrid/how-to-connect-sso.md).
  - Importované členy z jiných členů služby Azure AD, kteří jsou nativní nebo federované domény.
  - Skupiny služby Active Directory se vytvořily jako skupiny zabezpečení.

- Uživatelé Azure AD, kteří jsou součástí skupiny, která má `db_owner` roli serveru, nemůžou používat syntaxi **[CREATE DATABASE s oborem pověření](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** proti Azure SQL Database a Azure synapse. Zobrazí se následující chyba:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udělte `db_owner` roli přímo individuálnímu uživateli služby Azure AD, aby se zmírnil problém s **rozsahem pro vytvoření databáze s OBORem přihlašovacích údajů** .

- Tyto systémové funkce vrací hodnoty NULL při spuštění v rámci objektů zabezpečení Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>Spravovaná instance SQL

- Objekty zabezpečení serveru Azure AD (přihlášení) a uživatelé se podporují pro [spravovanou instanci SQL](../managed-instance/sql-managed-instance-paas-overview.md).
- Nastavení objektů zabezpečení serveru Azure AD (přihlášení) mapovaných na skupinu Azure AD, protože vlastník databáze není podporovaný ve [spravované instanci SQL](../managed-instance/sql-managed-instance-paas-overview.md).
  - Toto rozšíření znamená, že když se skupina přidá jako součást `dbcreator` role serveru, uživatelé z této skupiny se můžou připojit k spravované instanci SQL a vytvářet nové databáze, ale nebudou mít přístup k databázi. Je to proto, že nový vlastník databáze je SA, nikoli uživatel Azure AD. Tento problém se neprojeví, pokud se jednotlivé uživatele přidají do `dbcreator` role serveru.
- Spouštění úloh a správa agenta SQL se podporují pro objekty zabezpečení serveru Azure AD (přihlášení).
- Operace zálohování a obnovení databáze můžou provádět objekty zabezpečení serveru Azure AD (přihlášení).
- Je podporováno auditování všech příkazů souvisejících s objekty zabezpečení serveru Azure AD (přihlášeními) a událostmi ověřování.
- Vyhrazené připojení správce pro objekty zabezpečení serveru Azure AD (přihlášení), které jsou členy role serveru sysadmin, jsou podporovány.
  - Podporováno prostřednictvím nástroje SQLCMD a SQL Server Management Studio.
- Aktivační události přihlášení jsou podporovány pro události přihlášení přicházející z objektů zabezpečení serveru Azure AD (přihlášení).
- E-maily Service Broker a DB je možné nastavit pomocí objektu zabezpečení serveru Azure AD (přihlášení).

## <a name="connect-by-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování Azure Active Directory podporuje následující metody připojení k databázi pomocí identit Azure AD:

- Azure Active Directory heslo
- Azure Active Directory integrovaný
- Azure Active Directory univerzální s Multi-Factor Authentication
- Použití ověřování pomocí tokenu aplikace

Pro objekty zabezpečení serveru Azure AD (přihlášení) se podporují následující metody ověřování:

- Azure Active Directory heslo
- Azure Active Directory integrovaný
- Azure Active Directory univerzální s Multi-Factor Authentication

### <a name="additional-considerations"></a>Další aspekty

- Pro zlepšení spravovatelnosti doporučujeme zřídit vyhrazenou skupinu Azure AD jako správce.
- Pro server v SQL Database nebo Azure synapse se dá nakonfigurovat jenom jeden správce Azure AD (uživatel nebo skupina).
  - Přidání objektů zabezpečení serveru Azure AD (přihlášení) pro spravovanou instanci SQL umožňuje vytvořit více objektů zabezpečení serveru Azure AD (přihlášení), které je možné do `sysadmin` role přidat.
- Pouze správce Azure AD pro server se může zpočátku připojit k serveru nebo spravované instanci pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat další uživatele databáze služby Azure AD.
- Doporučujeme nastavit časový limit připojení na 30 sekund.
- SQL Server 2016 Management Studio a SQL Server Data Tools for Visual Studio 2015 (verze 14.0.60311.1 Duben 2016 nebo novější) podporují Azure Active Directory ověřování. (Ověřování Azure AD podporuje **.NET Framework Zprostředkovatel dat pro SQLServer**; minimálně verze .NET Framework 4,6). Proto můžou nejnovější verze těchto nástrojů a aplikací na datové vrstvě (DAC a BACPAC) používat ověřování Azure AD.
- Od verze 15.0.1 [Nástroj Sqlcmd Utility](/sql/tools/sqlcmd-utility) a [BCP](/sql/tools/bcp-utility) podporuje interaktivní ověřování služby Active Directory s Multi-Factor Authentication.
- Nástroj SQL Server Data Tools for Visual Studio 2015 vyžaduje alespoň 2016 verze nástrojů Data Tools (verze 14.0.60311.1) z dubna. V současné době se uživatelé Azure AD nezobrazí v SSDT Průzkumník objektů. Alternativním řešením je zobrazit uživatele v [Sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Microsoft JDBC Driver 6,0 pro SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) podporuje ověřování Azure AD. Viz také [Nastavení vlastností připojení](/sql/connect/jdbc/setting-the-connection-properties).
- Základ se nedá ověřit pomocí ověřování Azure AD.
- Ověřování Azure AD se podporuje pro Azure SQL Database a Azure synapse pomocí Azure Portal **importovat databáze** a **exportovat databáze** . Import a export pomocí ověřování Azure AD se taky podporuje z příkazu PowerShellu.
- Ověřování Azure AD se podporuje pro SQL Database, spravovanou instanci SQL a Azure synapse s použitím rozhraní příkazového řádku. Další informace najdete v tématu [Konfigurace a Správa ověřování Azure AD pomocí SQL Database nebo Azure synapse](authentication-aad-configure.md) a [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvořit a naplnit instanci Azure AD a jak ji nakonfigurovat pomocí Azure SQL Database, spravované instance SQL nebo Azure synapse, najdete v tématu [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database, spravované instance SQL nebo Azure synapse](authentication-aad-configure.md).
- Kurz použití objektů zabezpečení serveru Azure AD (přihlášení) se službou SQL Managed instance najdete v tématu [objekty zabezpečení serveru Azure AD (přihlášení) pomocí spravované instance SQL](../managed-instance/aad-security-configure-tutorial.md) .
- Přehled přihlašovacích údajů, uživatelů, databázových rolí a oprávnění v SQL Database najdete v tématu [přihlášení, uživatelé, databázové role a oprávnění](logins-create-manage.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](https://msdn.microsoft.com/library/ms181127.aspx).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](https://msdn.microsoft.com/library/ms189121.aspx).
- Syntaxi při vytváření objektů zabezpečení serveru Azure AD (přihlášení) pro spravovanou instanci SQL najdete v tématu  [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)údajů.
- Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
