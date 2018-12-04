---
title: Ověřování Azure Active Directory - Azure SQL | Dokumentace Microsoftu
description: Další informace o tom, jak používat Azure Active Directory pro ověřování pomocí SQL Database Managed Instance a SQL Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: ff9011dda4a94f323b430a3860eadc8d970a23f7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838612"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Pomocí ověřování Azure Active Directory pro ověřování pomocí SQL

Ověřování pomocí Azure Active Directory je mechanismus pro připojení k Azure [SQL Database](sql-database-technical-overview.md), [Managed Instance](sql-database-managed-instance.md), a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) s využitím identit v Azure Active Directory (Azure AD). 

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

Pomocí ověřování Azure AD můžete centrálně spravovat identity uživatelů databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID zajišťuje centrální místo pro správu uživatelů databáze a zjednodušuje správu oprávnění. Mezi výhody patří následující:

- To poskytuje alternativu k ověřování serveru SQL Server.
- Pomáhá zastavit šíření identit uživatelů více databázových serverů.
- Umožňuje rotace hesla na jednom místě.
- Uživatelé mohou spravovat oprávnění k databázi pomocí skupin externí (Azure AD).
- Ukládání hesel se může eliminovat tím, že integrované ověřování Windows a další formy ověřování podporovaných službou Azure Active Directory.
- Ověřování Azure AD používá k ověření identity na úrovni databáze, uživatele databáze s omezením.
- Azure AD podporuje ověřování pomocí tokenu pro aplikace, připojení k SQL Database.
- Ověřování Azure AD podporuje služby AD FS (federation domény) nebo ověřování nativní uživatele a hesla pro místní Azure Active Directory bez synchronizace domény.  
- Azure AD podporuje připojení z SQL Server Management Studio, které pomocí univerzálního ověřování Active Directory, která zahrnuje Vícefaktorové ověřování (MFA).  Vícefaktorové ověřování zahrnuje silné ověřování s řadou jednoduchých možností – telefonní hovor, textová zpráva, čipové karty s PIN kódu nebo oznámení přes mobilní aplikaci. Další informace najdete v tématu [podpora nástroje SSMS pro ověřování Azure AD MFA s SQL Database a SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

> [!NOTE]  
> Připojení k SQL serveru běžícího na Virtuálním počítači Azure není podporováno použití účtu služby Azure Active Directory. Místo toho použijte doménu účtu služby Active Directory.  

Postup konfigurace obsahuje následující kroky konfigurace a používání ověřování Azure Active Directory.

1. Vytvoření a naplnění služby Azure AD.
2. Volitelné: Přiřadit nebo změnit služby active directory, která je teď přidružená k předplatnému Azure.
3. Vytvoření správce Azure Active Directory pro server Azure SQL Database Managed Instance, nebo [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurace klientských počítačů.
5. Vytvořte uživatele databáze s omezením v databázi namapované na identit Azure AD.
6. Připojení k databázi pomocí identit Azure AD.

> [!NOTE]
> Zjistěte, jak vytvořit a naplnit Azure AD a Azure AD nakonfigurovat pomocí Azure SQL Database Managed Instance a SQL Data Warehouse, najdete v článku [konfigurovat Azure AD se službou Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architektura vztahu důvěryhodnosti

Následující podrobný diagram obsahuje souhrn architekturu řešení s Azure SQL Database pomocí ověřování Azure AD. Stejné koncepty platí i pro SQL Data Warehouse. Pro podporu nativní uživatelská hesla Azure AD, se považuje za část cloudu a Azure AD nebo Azure SQL Database. Pro podporu ověřování federativní (nebo heslo pro přihlašovací údaje Windows), komunikace se službou AD FS bloku je povinný. Šipky označují způsobů komunikace.

![diagram ověřování aad][1]

Následující diagram označuje federace, důvěryhodnosti a vztahy hostování, které umožňují klientům připojení k databázi, odešlete token. Token, který je ověřen službou Azure AD a je důvěryhodný pro databázi. Odběratel 1 může představovat službu Azure Active Directory s nativní uživateli nebo Azure AD s federovanými uživateli. Odběratel 2 představuje možné řešení, včetně importované uživatele. v tomto příkladu, pocházejí z federovaného Azure Active Directory se službou AD FS zabránit v synchronizaci se službou Azure Active Directory. Je důležité pochopit, že přístup k databázi pomocí ověřování Azure AD vyžaduje, aby hostující předplatné přidružené ke službě Azure AD. Stejného předplatného musíte použít k vytvoření SQL serveru, který hostuje Azure SQL Database nebo SQL Data Warehouse.

![vztah předplatného][2]

## <a name="administrator-structure"></a>Struktura správce

Při používání ověřování Azure AD, jsou dva účty správce pro server služby SQL Database a Managed Instance; původní správce SQL serveru a správce Azure AD. Stejné koncepty platí i pro SQL Data Warehouse. Pouze správce účtu Azure AD podle vytvořit první uživatele databáze s Azure AD, které jsou obsaženy v uživatelské databázi. Přihlášení správce Azure AD může být uživatele služby Azure AD nebo skupiny služby Azure AD. Když správce je účet skupiny, lze použít libovolný člen skupiny povolením více správců Azure AD pro instanci systému SQL Server. Tím, že je centrálně přidávat a odebírat členy skupiny ve službě Azure AD beze změny uživatelů nebo oprávnění ve službě SQL Database pomocí účtu skupiny jako správce vylepšuje možnosti správy. Kdykoli je možné nakonfigurovat jen jednoho správce Azure AD (uživatel nebo skupina).

![Struktura správce][3]

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit nové uživatele, musíte mít `ALTER ANY USER` oprávnění v databázi. `ALTER ANY USER` Oprávnění lze udělit jakémukoli uživateli databáze. `ALTER ANY USER` Oprávnění se také nachází serveru účty správce a uživatele databáze s `CONTROL ON DATABASE` nebo `ALTER ON DATABASE` oprávnění pro tuto databázi a členy `db_owner` databázové role.

Chcete-li vytvořit uživatele databáze s omezením v Azure SQL Database, mi nebo SQL Data Warehouse, musí připojit k databázi nebo instanci pomocí identity Azure AD. Vytvoření prvního uživatele databáze s omezením, musí připojit k databázi pomocí Správce Azure AD (kdo je vlastníkem databáze). To je patrné [konfigurovat a spravovat ověřování Azure Active Directory s SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md). Ověřování Azure AD je možné, pouze pokud byl vytvořen správce Azure AD pro server Azure SQL Database nebo SQL Data Warehouse. Pokud správce Azure Active Directory byla odebrána ze serveru, stávající uživatele Azure Active Directory vytvořené dříve v SQL serveru můžete už připojení k databázi pomocí svých přihlašovacích údajů Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkce Azure AD a omezení

Následující členy služby Azure AD se dá zřídit v Azure SQL server nebo SQL Data Warehouse:

- Nativní členy: člen vytvořené ve službě Azure AD ve spravované doméně nebo v doméně zákazníka. Další informace najdete v tématu [přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Federované domény členy: člen vytvořené ve službě Azure AD s federovanou doménu. Další informace najdete v tématu [Microsoft Azure teď podporuje federace se službou Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Importované členové z jiných Azure AD, kteří jsou členy nativní nebo federované domény.
- Skupiny služby Active Directory vytvoří jako skupin zabezpečení.

Azure AD přihlášení a uživatelů jsou podporovány jako funkci ve verzi preview pro [spravované instance](sql-database-managed-instance.md)

Tyto funkce systému vracet hodnoty NULL při spuštění v rámci objektů zabezpečení Azure AD:

- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování pomocí Azure Active Directory podporuje následující způsoby připojení k databázi pomocí identit Azure AD:

- Pomocí integrovaného ověřování Windows
- Pomocí hlavního názvu služby Azure AD a heslo
- Pomocí ověřování tokenu aplikací

### <a name="additional-considerations"></a>Další aspekty

- Pokud chcete zlepšit možnosti správy, doporučujeme zřídit vyhrazené Azure AD jako správce.   
- Pro server Azure SQL Database, mi nebo Azure SQL Data Warehouse kdykoli lze nastavit jen jednoho správce Azure AD (uživatel nebo skupina).
- Pouze správce Azure AD pro SQL Server můžete nejprve připojit k serveru Azure SQL Database, mi nebo Azure SQL Data Warehouse pomocí účtu služby Azure Active Directory. Správce služby Active Directory můžete nakonfigurovat další služby Azure AD databázi uživatelů.   
- Doporučujeme nastavit časový limit připojení na 30 sekund.   
- SQL Server 2016 Management Studio a SQL Server Data Tools pro Visual Studio 2015 (verze 14.0.60311.1April 2016 nebo novější) podporují ověřování pomocí Azure Active Directory. (Podporuje ověřování azure AD **.NET Framework Data Provider pro SqlServer**; minimální verze rozhraní .NET Framework 4.6). Proto nejnovější verze těchto nástrojů a aplikací datové vrstvy (DAC a. BACPAC) můžete použít ověřování Azure AD.   
- [ODBC 13.1 verze](https://www.microsoft.com/download/details.aspx?id=53339) podporuje ověřování Azure Active Directory, ale `bcp.exe` nelze připojit pomocí ověřování Azure Active Directory, protože používá starší poskytovatele rozhraní ODBC.   
- `sqlcmd` podporuje ověřování Azure Active Directory začíná 13.1 k dispozici od verze [Download Center](https://go.microsoft.com/fwlink/?LinkID=825643).
- SQL Server Data Tools pro Visual Studio 2015 vyžaduje minimálně verzi. dubna 2016 (verze 14.0.60311.1) v nástrojích Data. Aktuálně uživatelů Azure AD se nezobrazují v Průzkumníku objektů rozšíření SSDT. Jako alternativní řešení, zobrazení uživatelů v [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [6.0 ovladač Microsoft JDBC pro SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ověřování Azure AD podporuje. Viz také [nastavení vlastnosti připojení](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase se nemůže ověřit pomocí ověřování Azure AD.   
- Ověřování Azure AD je podporována SQL Database pomocí webu Azure portal **importovat databázi** a **exportovat databázi** oken. Import a export pomocí ověřování Azure AD je také podporována z příkazu prostředí PowerShell.   
- Ověřování Azure AD podporuje pro SQL Database Managed Instance a SQL Data Warehouse pomocí rozhraní příkazového řádku. Další informace najdete v tématu [konfigurovat a spravovat ověřování Azure Active Directory s SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md) a [SQL Server – az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak vytvořit a naplnit Azure AD a Azure AD nakonfigurovat pomocí Azure SQL Database nebo Azure SQL Data Warehouse, najdete v článku [konfigurovat a spravovat ověřování Azure Active Directory s SQL Database, mi nebo SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
- Přehled řízení a přístupu pro SQL Database najdete v tématu věnovaném [řízení a přístupu k SQL Database](sql-database-control-access.md).
- Přehled přihlášení, uživatelů a databázových rolí ve službě SQL Database najdete v tématu věnovaném [přihlášením, uživatelům a databázovým rolím](sql-database-manage-logins.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](https://msdn.microsoft.com/library/ms181127.aspx).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](https://msdn.microsoft.com/library/ms189121.aspx).
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
