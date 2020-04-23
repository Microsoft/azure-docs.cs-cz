---
title: Azure Active Directory
description: Informace o tom, jak používat Azure Active Directory pro ověřování pomocí databáze SQL, spravované instance a synapse SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: db80c11c3b6eab3b7e682878e479729f4787a40b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086092"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Použití ověřování azure active directory pro ověřování pomocí Synapse SQL

Azure Active Directory ověřování je mechanismus připojení k [Azure Synapse Analytics](../overview-faq.md) pomocí identit ve službě Azure Active Directory (Azure AD).

Díky ověřování Azure AD můžete centrálně spravovat uživatelské identity, které mají přístup k Azure Synapse, a zjednodušit tak správu oprávnění. Mezi jeho výhody patří následující:

- Poskytuje alternativu k běžnému ověřování uživatelského jména a hesla.
- Pomáhá zastavit šíření identit uživatelů napříč databázovými servery.
- Umožňuje střídání hesel na jednom místě.
- Zákazníci mohou spravovat oprávnění pomocí externích skupin (Azure AD).
- Umožňuje integrované ověřování systému Windows a další formy ověřování podporované službou Azure Active Directory.
- Azure AD podporuje ověřování na základě tokenů pro aplikace, které se připojují k Azure Synapse.
- Azure AD ověřování podporuje ADFS (federace domény) nebo nativní ověřování uživatelů/hesel pro místní Azure Active Directory bez synchronizace domény.
- Azure AD podporuje připojení z aplikace SQL Server Management Studio využívající univerzální ověřování Active Directory, což zahrnuje službu Multi-Factor Authentication (MFA).  MFA zahrnuje silné ověřování s využitím celé řady možností ověření – telefonických hovorů, textových zpráv, čipových karet s kódem PIN nebo oznámení mobilní aplikace. Další informace naleznete v [tématu Podpora SSMS pro Azure AD MFA s Synapse SQL](mfa-authentication.md).
- Azure AD podporuje podobná připojení z nástrojů SQL Server Data Tools (SSDT) využívající interaktivní ověřování Active Directory. Další informace najdete v tématu [Podpora Azure Active Directory v nástrojích SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Kroky konfigurace zahrnují následující postupy konfigurace a použití ověřování služby Azure Active Directory.

1. Vytvořte a naplňte Azure AD.
2. Vytvoření identity služby Azure Active Directory
3. Přiřazení role vytvořené identitě služby Azure Active Directory v pracovním prostoru Synapse (preview)
4. Připojte se k Synapse Studio pomocí identit Azure AD.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>Předávací řešení AAD v Azure Synapse Analytics

Azure Synapse Analytics umožňuje přístup k datům v datovém jezeře pomocí identity Azure Active Directory.

Definování přístupových práv k souborům a datům, která jsou respektována v různých datových modulech, umožňuje zjednodušit řešení datových jezer tím, že máte jedno místo, kde jsou definována oprávnění, místo toho, abyste je museli definovat na více místech.

## <a name="trust-architecture"></a>Architektura důvěryhodnosti

Následující diagram vysoké úrovně shrnuje architekturu řešení pomocí ověřování Azure AD s Synapse SQL. Pro podporu nativní ho uživatele Azure AD, jenom cloudové části a Azure AD/Synapse Synapse SQL se považuje za. Pro podporu federovaného ověřování (nebo uživatele/hesla pro pověření systému Windows) je vyžadována komunikace s blokem Služby ADFS. Šipky ukazují komunikační cesty.

![aad auth diagram](./media/aad-authentication/1-active-directory-authentication-diagram.png)

Následující diagram označuje vztahy federace, důvěryhodnosti a hostování, které umožňují klientovi připojit se k databázi odesláním tokenu. Token je ověřen službou Azure AD a databáze mu důvěřuje. 

Zákazník 1 může představovat službu Azure Active Directory s nativními uživateli nebo Službu Azure AD s federovanými uživateli. Zákazník 2 představuje možné řešení včetně importovaných uživatelů; V tomto příkladu pochází z federované služby Azure Active Directory s ADFS synchronizovány s Azure Active Directory. 

Je důležité si uvědomit, že přístup k databázi pomocí ověřování Azure AD vyžaduje, aby hostování předplatné je přidružené k Azure AD. Stejné předplatné musí být použito k vytvoření SQL Serveru hostujícího azure SQL database nebo fond SQL.

![vztah předplatného](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD existují dva účty správce pro Synapse SQL; původního správce serveru SQL Server a správce služby Azure AD. Pouze správce na základě účtu Azure AD můžete vytvořit první Azure AD obsahoval databázi uživatele v databázi uživatele. 

Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud je správce účet skupiny, může jej použít libovolný člen skupiny, což umožňuje více správců ms administrátorů Azure AD pro instanci Synapse SQL. 

Použití účtu skupiny jako správce zvyšuje možnosti správy tím, že umožňuje centrálně přidávat a odebírat členy skupiny ve službě Azure AD beze změny uživatelů nebo oprávnění v pracovním prostoru Synapse Analytics. Kdykoli lze nakonfigurovat pouze jednoho správce Azure AD (uživatele nebo skupinu).

![struktura správce](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit nové uživatele, musíte mít `ALTER ANY USER` oprávnění v databázi. Oprávnění `ALTER ANY USER` lze udělit libovolnému uživateli databáze. Oprávnění `ALTER ANY USER` je také v držení účtů správce serveru `CONTROL ON DATABASE` a `ALTER ON DATABASE` uživatelů databáze s oprávněním `db_owner` nebo pro tuto databázi a členy role databáze.

Chcete-li vytvořit obsaženého uživatele databáze v synapse SQL, musíte se připojit k databázi nebo instanci pomocí identity Azure AD. Chcete-li vytvořit první obsažené uživatele databáze, musíte se připojit k databázi pomocí správce Azure AD (kdo je vlastníkem databáze). 

Jakékoli ověřování Azure AD je možné jenom v případě, že správce Azure AD byl vytvořen pro Synapse SQL. Pokud byl správce služby Azure Active Directory odebrán ze serveru, stávající uživatelé služby Azure Active Directory, kteří byli vytvořeni dříve v synapse SQL, se již nemohou připojit k databázi pomocí svých přihlašovacích údajů služby Azure Active Directory.
 
## <a name="azure-ad-features-and-limitations"></a>Funkce a omezení Azure AD

- Následující členy Azure AD lze zřídit v Synapse SQL:

  - Nativní členové: Člen vytvořený ve službě Azure AD ve spravované doméně nebo v doméně zákazníka. Další informace najdete [v tématu Přidání vlastního názvu domény do Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Členové federované domény: Člen vytvořený ve službě Azure AD s federovovovnou doménou. Další informace naleznete v tématu [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Importovaní členové z jiných členů Azure AD, kteří jsou nativní nebo federované členy domény.
  - Skupiny služby Active Directory vytvořené jako skupiny zabezpečení.

- Uživatelé Azure AD, kteří jsou `db_owner` součástí skupiny, která má roli serveru nelze použít **[create database scoped credential](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** syntaxe proti Synapse SQL. Zobrazí se následující chyba:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udělte `db_owner` roli přímo jednotlivým uživatelům služby Azure AD ke zmírnění problému **s pověřením OBORU VYTVOŘENÍ DATABÁZE.**

- Tyto systémové funkce vrátí hodnoty NULL při spuštění v rámci objektů zabezpečení Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

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
- Pro fond Synapse SQL lze kdykoli nakonfigurovat jenom jednoho správce služby Azure AD (uživatele nebo skupiny).
  - Přidání objektů zabezpečení serveru Azure AD (přihlášení) pro SQL na vyžádání (preview) umožňuje možnost vytvoření více objektů serveru Azure `sysadmin` AD (přihlášení), které lze přidat do role.
- Pouze správce Azure AD pro Synapse SQL můžete zpočátku připojit k Synapse SQL pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat následné uživatele databáze Azure AD.
- Doporučujeme nastavit časový limit připojení na 30 sekund.
- SQL Server 2016 Management Studio a SQL Server Datové nástroje pro Visual Studio 2015 (verze 14.0.60311.1Duben 2016 nebo novější) podporují ověřování Azure Active Directory. (Azure AD ověřování je podporováno **zprostředkovatelem dat rozhraní .NET Framework pro SqlServer**; alespoň verze .NET Framework 4.6). Proto nejnovější verze těchto nástrojů a aplikace datové vrstvy (DAC a . BACPAC) můžete použít ověřování Azure AD.
- Počínaje verzí 15.0.1 podporují [nástroj sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [nástroj bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) interaktivní ověřování služby Active Directory pomocí vícefaktorového ověřování.
- Sql Server Datové nástroje pro Visual Studio 2015 vyžaduje alespoň z dubna 2016 verzi datových nástrojů (verze 14.0.60311.1). V současné době uživatelé Azure AD nejsou zobrazeny v Průzkumníku objektů SSDT. Jako zástupné řešení zobrazíte uživatele v [souboru sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- [Microsoft JDBC Driver 6.0 for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) podporuje ověřování Azure AD. Viz také [nastavení vlastností připojení](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Další kroky

- Přehled přístupu a řízení v synapse SQL naleznete v tématu [Synapse SQL řízení přístupu](../sql/access-control.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

 