---
title: Azure Active Directory
description: Přečtěte si, jak používat Azure Active Directory k ověřování pomocí SQL Database, spravované instance a synapse SQL.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 7518d6ac8bc0cde515ab8da2f3d9c1496cb93f08
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311710"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Použití ověřování Azure Active Directory pro ověřování pomocí synapse SQL

Ověřování Azure Active Directory je mechanismus, který se připojuje ke [službě Azure synapse Analytics](../overview-faq.md) pomocí identit v Azure Active Directory (Azure AD).

Pomocí ověřování Azure AD můžete centrálně spravovat identity uživatelů, kteří mají přístup k Azure synapse, a zjednodušit tak správu oprávnění. Mezi jeho výhody patří následující:

- Nabízí alternativu k pravidelnému ověřování uživatelského jména a hesla.
- Pomáhá zastavit šíření identit uživatelů napříč servery.
- Umožňuje otočení hesla na jednom místě.
- Zákazníci můžou spravovat oprávnění pomocí externích skupin (Azure AD).
- Může eliminovat ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které Azure Active Directory podporuje.
- Azure AD podporuje ověřování založené na tokenech pro aplikace, které se připojují ke službě Azure synapse.
- Ověřování Azure AD podporuje službu ADFS (federaci domén) nebo nativní ověřování uživatelů a hesel pro místní Azure Active Directory bez synchronizace domény.
- Azure AD podporuje připojení z aplikace SQL Server Management Studio využívající univerzální ověřování Active Directory, což zahrnuje službu Multi-Factor Authentication (MFA).  MFA zahrnuje silné ověřování s využitím široké škály možností jednoduchého ověřování, včetně telefonního hovoru, textové zprávy, čipové karty s kódem PIN nebo oznámením o mobilní aplikaci. Další informace najdete v tématu [Podpora SSMS pro Azure AD MFA s synapse SQL](mfa-authentication.md).
- Azure AD podporuje podobná připojení z nástrojů SQL Server Data Tools (SSDT) využívající interaktivní ověřování Active Directory. Další informace najdete v tématu [Podpora Azure Active Directory v nástrojích SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Kroky konfigurace obsahují následující postupy pro konfiguraci a použití ověřování Azure Active Directory.

1. Vytvoření a naplnění služby Azure AD.
2. Vytvoření Azure Active Directory identity
3. Přiřazení role k vytvořeným Azure Active Directory identitě v pracovním prostoru synapse (Preview)
4. Připojte se k synapse studiu pomocí identit Azure AD.

## <a name="azure-ad-pass-through-in-azure-synapse-analytics"></a>Předávací služba Azure AD ve službě Azure synapse Analytics

Azure synapse Analytics umožňuje přístup k datům v Data Lake pomocí vaší Azure Active Directory identity.

Definování přístupových práv k souborům a datům, která jsou v různých datových modulech, vám umožní zjednodušit vaše řešení Data Lake tím, že bude mít jedno místo, kde jsou definovaná oprávnění, místo aby je bylo nutné definovat na více místech.

## <a name="trust-architecture"></a>Architektura důvěryhodnosti

Následující diagram vysoké úrovně shrnuje architekturu řešení pro použití ověřování Azure AD s synapse SQL. Aby bylo možné podporovat heslo nativního uživatele služby Azure AD, je považována pouze část cloudu a Azure AD/synapse synapse SQL. Aby bylo možné podporovat federované ověřování (nebo uživatele/heslo pro přihlašovací údaje systému Windows), je vyžadována komunikace s blokem ADFS. Šipky označují cesty komunikace.

![Diagram ověřování Azure AD](./media/aad-authentication/1-active-directory-authentication-diagram.png)

Následující diagram označuje vztahy federace, vztahu důvěryhodnosti a hostování, které umožňují klientovi připojit se k databázi odesláním tokenu. Token se ověřuje pomocí Azure AD a je důvěryhodný pro databázi. 

Zákazník 1 může představovat Azure Active Directory s nativními uživateli nebo Azure AD s federovaným uživateli. Zákazník 2 představuje možné řešení, včetně importovaných uživatelů; v tomto příkladu přichází z federované Azure Active Directory se službou AD FS synchronizovanou pomocí Azure Active Directory. 

Je důležité pochopit, že přístup k databázi pomocí ověřování Azure AD vyžaduje, aby se k Azure AD přidružil hostitelské předplatné. K vytvoření SQL Server hostování Azure SQL Database nebo vyhrazeného fondu SQL se musí použít stejné předplatné.

![vztah předplatného](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD jsou k dispozici dva účty správců pro synapse SQL. původní správce SQL Server a správce Azure AD. Pouze správce, který je založen na účtu Azure AD, může vytvořit prvního uživatele databáze s omezením Azure AD v uživatelské databázi. 

Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Když je správcem účet skupiny, může ho použít libovolný člen skupiny a povolit pro instanci SQL synapse více správců služby Azure AD. 

Použití účtu skupiny jako správce vylepšuje spravovatelnost tím, že umožňuje centrálně přidávat a odebírat členy skupin ve službě Azure AD beze změny uživatelů nebo oprávnění v pracovním prostoru synapse Analytics. Kdykoli se dá nakonfigurovat jenom jeden správce Azure AD (uživatel nebo skupina).

![struktura správy](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit nové uživatele, musíte mít `ALTER ANY USER` v databázi oprávnění. `ALTER ANY USER`Oprávnění lze udělit libovolnému uživateli databáze. `ALTER ANY USER`Oprávnění je také uchováváno pomocí účtů správců serveru a uživatelů databáze s `CONTROL ON DATABASE` `ALTER ON DATABASE` oprávněním nebo pro tuto databázi a členy `db_owner` databázové role.

Pokud chcete vytvořit uživatele databáze s omezením v synapse SQL, musíte se k databázi nebo instanci připojit pomocí identity Azure AD. Chcete-li vytvořit prvního uživatele databáze s omezením, je nutné se připojit k databázi pomocí správce Azure AD (který je vlastníkem databáze). 

Jakékoli ověřování Azure AD je možné pouze v případě, že byl vytvořen správce Azure AD pro synapse SQL. Pokud byl správce Azure Active Directory odebraný ze serveru, stávající uživatelé Azure Active Directory dříve vytvořeni dříve v synapse SQL se už nebudou moct připojit k databázi pomocí svých přihlašovacích údajů Azure Active Directory.
 
## <a name="azure-ad-features-and-limitations"></a>Funkce a omezení služby Azure AD

- V synapse SQL se dají zřídit následující členové Azure AD:

  - Nativní členové: člen vytvořený v Azure AD ve spravované doméně nebo v doméně zákazníka. Další informace najdete v tématu [Přidání vlastního názvu domény do Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Členové federované domény: člen vytvořený v Azure AD se federované doménou. Další informace najdete v tématu [Microsoft Azure nyní podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Importované členy z jiných reklam Azure, kteří jsou nativní nebo federované členy domény.
  - Skupiny služby Active Directory se vytvořily jako skupiny zabezpečení.

- Uživatelé Azure AD, kteří jsou součástí skupiny, která má `db_owner` roli serveru, nemůžou použít syntaxi **[Create Database scoped Credential](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** v synapse SQL. Zobrazí se následující chyba:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udělte `db_owner` roli přímo individuálnímu uživateli služby Azure AD, aby se zmírnil problém s **rozsahem pro vytvoření databáze s OBORem přihlašovacích údajů** .

- Tyto systémové funkce vrací hodnoty NULL při spuštění v rámci objektů zabezpečení Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connect-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování Azure Active Directory podporuje následující metody připojení k databázi pomocí identit Azure AD:

- Azure Active Directory heslo
- Azure Active Directory integrovaný
- Azure Active Directory univerzální s MFA
- Použití ověřování pomocí tokenu aplikace

Následující metody ověřování jsou podporovány pro objekty zabezpečení serveru Azure AD (přihlášení) ( **Public Preview** ):

- Azure Active Directory heslo
- Azure Active Directory integrovaný
- Azure Active Directory univerzální s MFA

### <a name="additional-considerations"></a>Další aspekty

- Pro zlepšení spravovatelnosti doporučujeme zřídit vyhrazenou skupinu Azure AD jako správce.
- Pro synapse fond SQL se dá kdykoli nakonfigurovat jenom jeden správce Azure AD (uživatel nebo skupina).
  - Přidání objektů zabezpečení serveru Azure AD (přihlášení) pro synapse SQL (Preview) umožňuje vytvořit více objektů zabezpečení serveru Azure AD (přihlášení), které je možné do `sysadmin` role přidat.
- Pouze správce Azure AD pro synapse SQL se může zpočátku připojit k synapse SQL pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat další uživatele databáze služby Azure AD.
- Doporučujeme nastavit časový limit připojení na 30 sekund.
- SQL Server 2016 Management Studio a SQL Server Data Tools for Visual Studio 2015 (verze 14.0.60311.1 Duben 2016 nebo novější) podporují Azure Active Directory ověřování. (Ověřování Azure AD podporuje **.NET Framework Zprostředkovatel dat pro SQLServer** ; minimálně verze .NET Framework 4,6). Nejnovější verze těchto nástrojů a aplikací na datové vrstvě (DAC a. BACPAC) může používat ověřování Azure AD.
- Od verze 15.0.1 [Nástroj Sqlcmd Utility](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) podporuje interaktivní ověřování služby Active Directory s MFA.
- Nástroj SQL Server Data Tools for Visual Studio 2015 vyžaduje alespoň 2016 verze nástrojů Data Tools (verze 14.0.60311.1) z dubna. V současné době se uživatelé Azure AD nezobrazí v SSDT Průzkumník objektů. Alternativním řešením je zobrazit uživatele v [Sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- [Microsoft JDBC Driver 6,0 pro SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) podporuje ověřování Azure AD. Viz také [Nastavení vlastností připojení](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Další kroky

- Přehled přístupu a řízení v synapse SQL najdete v tématu [synapse SQL Access Control](../sql/access-control.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

 