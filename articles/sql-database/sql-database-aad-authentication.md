---
title: Azure Active Directory
description: Přečtěte si, jak používat Azure Active Directory pro ověřování pomocí SQL Database, spravované instance a SQL Data Warehouse
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
ms.openlocfilehash: bc779df06d92d2483755ae888fda121b8e493a18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269156"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Pro ověřování pomocí SQL použít Azure Active Directory ověřování

Ověřování Azure Active Directory je mechanismus připojení ke službě Azure [SQL Database](sql-database-technical-overview.md), [spravované instanci](sql-database-managed-instance.md)a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit v Azure Active Directory (Azure AD). 

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

Díky ověřování Azure AD můžete na jednom místě centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění. Mezi jeho výhody patří následující:

- Nabízí alternativu k ověřování SQL Server.
- Pomáhá zastavit šíření identit uživatelů napříč databázovými servery.
- Umožňuje otočení hesla na jednom místě.
- Zákazníci můžou spravovat databázová oprávnění pomocí skupin externích (Azure AD).
- Může eliminovat ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které Azure Active Directory podporuje.
- Ověřování Azure AD používá k ověřování identit na úrovni databáze uživatele databáze s omezením.
- Azure AD podporuje ověřování založené na tokenech pro aplikace, které se připojují k SQL Database.
- Ověřování Azure AD podporuje službu ADFS (federaci domén) nebo nativní ověřování uživatelů a hesel pro místní Azure Active Directory bez synchronizace domény.
- Azure AD podporuje připojení z aplikace SQL Server Management Studio využívající univerzální ověřování Active Directory, což zahrnuje službu Multi-Factor Authentication (MFA).  MFA zahrnuje silné ověřování s využitím celé řady možností ověření – telefonických hovorů, textových zpráv, čipových karet s kódem PIN nebo oznámení mobilní aplikace. Další informace najdete v tématu [Podpora vícefaktorového ověřování Azure AD pro služby SQL Database a SQL Data Warehouse v aplikaci SSMS](sql-database-ssms-mfa-authentication.md).
- Azure AD podporuje podobná připojení z nástrojů SQL Server Data Tools (SSDT) využívající interaktivní ověřování Active Directory. Další informace najdete v tématu [Podpora Azure Active Directory v nástrojích SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Připojení k SQL Server běžícímu na virtuálním počítači Azure se pomocí účtu Azure Active Directory nepodporuje. Místo toho použijte účet domény služby Active Directory.  

Kroky konfigurace obsahují následující postupy pro konfiguraci a použití ověřování Azure Active Directory.

1. Vytvoření a naplnění služby Azure AD.
2. Volitelné: přidružte nebo změňte službu Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
3. Vytvořte správce Azure Active Directory pro Azure SQL Database Server, spravovanou instanci nebo [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Nakonfigurujte klientské počítače.
5. Vytvořte uživatele databáze s omezením ve vaší databázi namapované na identity Azure AD.
6. Připojte se k databázi pomocí identit Azure AD.

> [!NOTE]
> Informace o tom, jak vytvořit a naplnit Azure AD a jak nakonfigurovat Azure AD pomocí Azure SQL Database, spravované instance a SQL Data Warehouse najdete v tématu [konfigurace Azure AD pomocí Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architektura důvěryhodnosti

Následující diagram vysoké úrovně shrnuje architekturu řešení pro použití ověřování Azure AD s Azure SQL Database. Stejné koncepty platí i pro SQL Data Warehouse. Aby bylo možné podporovat heslo nativního uživatele služby Azure AD, bude zvážena pouze část cloudu a Azure AD/Azure SQL Database. Aby bylo možné podporovat federované ověřování (nebo uživatele/heslo pro přihlašovací údaje systému Windows), je vyžadována komunikace s blokem ADFS. Šipky označují cesty komunikace.

![Diagram ověřování AAD][1]

Následující diagram označuje vztahy federace, vztahu důvěryhodnosti a hostování, které umožňují klientovi připojit se k databázi odesláním tokenu. Token se ověřuje pomocí Azure AD a je důvěryhodný pro databázi. Zákazník 1 může představovat Azure Active Directory s nativními uživateli nebo Azure AD s federovaným uživateli. Zákazník 2 představuje možné řešení, včetně importovaných uživatelů; v tomto příkladu přichází z federované Azure Active Directory se službou AD FS synchronizovanou pomocí Azure Active Directory. Je důležité pochopit, že přístup k databázi pomocí ověřování Azure AD vyžaduje, aby se k Azure AD přidružil hostitelské předplatné. Stejné předplatné se musí použít k vytvoření SQL Server hostujícího Azure SQL Database nebo SQL Data Warehouse.

![vztah předplatného][2]

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD jsou k dispozici dva účty správců pro SQL Database Server a spravovanou instanci. původní správce SQL Server a správce Azure AD. Stejné koncepty platí i pro SQL Data Warehouse. Pouze správce, který je založen na účtu Azure AD, může vytvořit prvního uživatele databáze s omezením Azure AD v uživatelské databázi. Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Když je správcem účet skupiny, dá se použít kterýkoli člen skupiny, což umožňuje více správců služby Azure AD pro instanci SQL Server. Použití účtu skupiny jako správce vylepšuje spravovatelnost tím, že umožňuje centrálně přidávat a odebírat členy skupin ve službě Azure AD beze změny uživatelů nebo oprávnění v SQL Database. Kdykoli se dá nakonfigurovat jenom jeden správce Azure AD (uživatel nebo skupina).

![struktura správy][3]

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit nové uživatele, musíte mít v databázi oprávnění `ALTER ANY USER`. Oprávnění `ALTER ANY USER` lze udělit libovolnému uživateli databáze. Oprávnění `ALTER ANY USER` jsou také uložena v účtech správců serveru a uživatelé databáze s oprávněním `CONTROL ON DATABASE` nebo `ALTER ON DATABASE` pro tuto databázi a členy role databáze `db_owner`.

Chcete-li vytvořit uživatele databáze s omezením v Azure SQL Database, spravované instanci nebo SQL Data Warehouse, je nutné se připojit k databázi nebo instanci pomocí identity Azure AD. Chcete-li vytvořit prvního uživatele databáze s omezením, je nutné se připojit k databázi pomocí správce Azure AD (který je vlastníkem databáze). To je znázorněno v [konfiguraci a správě Azure Active Directory ověřování pomocí SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md). Jakékoli ověřování Azure AD je možné pouze v případě, že byl vytvořen správce Azure AD pro Azure SQL Database nebo SQL Data Warehouse Server. Pokud byl správce Azure Active Directory odebraný ze serveru, stávající uživatelé Azure Active Directory dříve vytvořeni dříve v SQL Server se již nebudou moci připojit k databázi pomocí příslušných přihlašovacích údajů Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkce a omezení služby Azure AD

- V Azure SQL serveru nebo SQL Data Warehouse můžou být zřízené následující členové Azure AD:

  - Nativní členové: člen vytvořený v Azure AD ve spravované doméně nebo v doméně zákazníka. Další informace najdete v tématu [Přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Členové federované domény: člen vytvořený v Azure AD se federované doménou. Další informace najdete v tématu [Microsoft Azure nyní podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Importované členy z jiných členů služby Azure AD, kteří jsou nativní nebo federované domény.
  - Skupiny služby Active Directory se vytvořily jako skupiny zabezpečení.

- Uživatelé Azure AD, kteří jsou součástí skupiny, která má `db_owner` role serveru, nemůže použít syntaxi **[přihlašovacích údajů CREATE DATABASE s rozsahem](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** Azure SQL Database a Azure SQL Data Warehouse. Zobrazí se následující chyba:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udělte roli `db_owner` přímo individuálnímu uživateli služby Azure AD, aby se zmírnil problém s **rozsahem vytváření databázových údajů** .

- Tyto systémové funkce vrací hodnoty NULL při spuštění v rámci objektů zabezpečení Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Spravované instance

- Objekty zabezpečení serveru Azure AD (přihlášení) a uživatelé se podporují jako funkce verze Preview pro [spravované instance](sql-database-managed-instance.md).
- Nastavení objektů zabezpečení serveru Azure AD (přihlášení) mapovaných na skupinu Azure AD, protože vlastník databáze není ve [spravovaných instancích](sql-database-managed-instance.md)podporován.
    - Toto rozšíření znamená, že když se skupina přidá jako součást role serveru `dbcreator`, uživatelé z této skupiny se můžou připojit ke spravované instanci a vytvářet nové databáze, ale nebudou mít přístup k databázi. Je to proto, že nový vlastník databáze je SA, nikoli uživatel Azure AD. Tento problém nemanifestuje, pokud je jednotlivý uživatel přidán do role serveru `dbcreator`.
- Provádění úloh správy a úloh SQL agentů je podporováno pro objekty zabezpečení serveru Azure AD (přihlášení).
- Operace zálohování a obnovení databáze můžou provádět objekty zabezpečení serveru Azure AD (přihlášení).
- Je podporováno auditování všech příkazů souvisejících s objekty zabezpečení serveru Azure AD (přihlášeními) a událostmi ověřování.
- Vyhrazené připojení správce pro objekty zabezpečení serveru Azure AD (přihlášení), které jsou členy role serveru sysadmin, jsou podporovány.
    - Podporováno prostřednictvím nástroje SQLCMD a SQL Server Management Studio.
- Aktivační události přihlášení jsou podporovány pro události přihlášení přicházející z objektů zabezpečení serveru Azure AD (přihlášení).
- E-maily Service Broker a DB je možné nastavit pomocí objektu zabezpečení serveru Azure AD (přihlášení).


## <a name="connecting-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování Azure Active Directory podporuje následující metody připojení k databázi pomocí identit Azure AD:

- Azure Active Directory heslo
- Azure Active Directory integrovaný
- Azure Active Directory univerzální s MFA
- Použití ověřování pomocí tokenu aplikace

Následující metody ověřování jsou podporovány pro objekty zabezpečení serveru Azure AD (přihlášení) (**Public Preview**):

- Azure Active Directory heslo
- Azure Active Directory integrovaný
- Azure Active Directory univerzální s MFA


### <a name="additional-considerations"></a>Další rozhodnutí

- Pro zlepšení spravovatelnosti doporučujeme zřídit vyhrazenou skupinu Azure AD jako správce.   
- Pro Azure SQL Database Server nebo Azure SQL Data Warehouse můžete kdykoli nakonfigurovat pouze jednoho správce Azure AD (uživatele nebo skupinu).
  - Přidání objektů zabezpečení serveru Azure AD (přihlášení) pro spravované instance (**Public Preview**) umožňuje vytvořit více objektů zabezpečení serveru Azure AD (přihlášení), které je možné přidat do role `sysadmin`.
- Pouze správce Azure AD pro SQL Server se může zpočátku připojit k serveru Azure SQL Database, spravované instanci nebo Azure SQL Data Warehouse pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat další uživatele databáze služby Azure AD.   
- Doporučujeme nastavit časový limit připojení na 30 sekund.   
- SQL Server 2016 Management Studio a SQL Server Data Tools for Visual Studio 2015 (verze 14.0.60311.1 Duben 2016 nebo novější) podporují Azure Active Directory ověřování. (Ověřování Azure AD podporuje **.NET Framework Zprostředkovatel dat pro SQLServer**; minimálně verze .NET Framework 4,6). Proto nejnovější verze těchto nástrojů a aplikací na datové vrstvě (DAC a. BACPAC) může používat ověřování Azure AD.   
- Od verze 15.0.1 [Nástroj Sqlcmd Utility](/sql/tools/sqlcmd-utility) a [BCP](/sql/tools/bcp-utility) podporuje interaktivní ověřování služby Active Directory s MFA.
- Nástroj SQL Server Data Tools for Visual Studio 2015 vyžaduje alespoň 2016 verze nástrojů Data Tools (verze 14.0.60311.1) z dubna. V SSDT Průzkumník objektů se aktuálně nezobrazuje uživatelé Azure AD. Alternativním řešením je zobrazit uživatele v zobrazení [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC Driver 6,0 pro SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) podporuje ověřování Azure AD. Viz také [Nastavení vlastností připojení](https://msdn.microsoft.com/library/ms378988.aspx).   
- Základ se nedá ověřit pomocí ověřování Azure AD.   
- Ověřování Azure AD je podporované pro SQL Database Azure Portal **Import databáze** a **Export databázových** oken. Import a export pomocí ověřování Azure AD se taky podporuje z příkazu PowerShellu.   
- Ověřování Azure AD se podporuje pro SQL Database, spravovanou instanci a SQL Data Warehouse pomocí rozhraní příkazového řádku (CLI). Další informace najdete v tématu [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md) a [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak vytvořit a naplnit Azure AD a jak nakonfigurovat Azure AD pomocí Azure SQL Database nebo Azure SQL Data Warehouse, najdete v tématu [Konfigurace a správa Azure Active Directory ověřování pomocí SQL Database, spravované instance nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md).
- Kurz použití objektů zabezpečení serveru Azure AD (přihlášení) se spravovanými instancemi najdete v tématu [objekty zabezpečení serveru Azure AD (přihlášení) se spravovanými instancemi](sql-database-managed-instance-aad-security-tutorial.md) .
- Přehled přihlašovacích údajů, uživatelů, databázových rolí a oprávnění v SQL Database najdete v tématu [přihlášení, uživatelé, databázové role a oprávnění](sql-database-manage-logins.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](https://msdn.microsoft.com/library/ms181127.aspx).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](https://msdn.microsoft.com/library/ms189121.aspx).
- Syntaxi při vytváření objektů zabezpečení serveru Azure AD (přihlášení) pro spravované instance najdete v tématu [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)údajů.
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
