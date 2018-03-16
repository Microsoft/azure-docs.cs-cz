---
title: "Ověření Azure Active Directory - SQL Azure (přehled) | Microsoft Docs"
description: "Další informace o tom, jak používat Azure Active Directory k ověřování připojení k databázi SQL, spravované Instance a datový sklad SQL"
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: cfad53a3f86450163b2e29d5e4d4ed2726ecb0bc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Pomocí ověřování Azure Active Directory k ověřování připojení k databázi SQL, spravované Instance nebo SQL Data Warehouse
Ověřování Azure Active Directory je mechanismus připojit k Microsoft Azure SQL Database a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit v Azure Active Directory (Azure AD). Při ověřování Azure AD můžete centrálně spravovat identity uživatelů, databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID poskytuje jednotné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění. Výhody patří:

* Nabízí alternativu k ověřování serveru SQL Server.
* Pomáhá zastavit šíření identit uživatelů mezi servery databáze.
* Umožňuje otočení heslo na jednom místě
* Zákazníci můžete spravovat oprávnění databáze pomocí skupin externí (Azure AD).
* Ukládání hesel se může eliminovat tím, že umožňuje integrované ověřování systému Windows a jiných forem ověřování podporovaných službou Azure Active Directory.
* K ověření identity na úrovni databáze používá Azure AD authentication uživatele databáze s omezením.
* Azure AD podporuje ověřování na základě tokenu pro aplikace, připojení k databázi SQL.
* Ověřování služby Azure AD podporuje služby AD FS (federation domény) nebo ověřování nativní uživatele a heslo pro místní Azure Active Directory bez synchronizace domény.  
* Azure AD podporuje připojení z SQL Server Management Studio, které používají Universal ověřování služby Active Directory, která zahrnuje Multi-Factor Authentication (MFA).  Vícefaktorové ověřování zahrnuje silné ověřování s celou řadu možností snadno ověření – telefonní hovor, textová zpráva, čipové karty s PIN kód nebo oznámení mobilní aplikace. Další informace najdete v tématu [SSMS podpora pro Azure AD MFA s SQL Database a SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  Nepodporuje připojení k SQL Server běžící na virtuálním počítači Azure pomocí účtu Azure Active Directory. Místo toho používejte doménu účtu služby Active Directory.  

Postup konfigurace obsahuje následující kroky konfigurace a použití ověřování Azure Active Directory.

1. Vytvořit a naplnit Azure AD.
2. Volitelné: Přidružení nebo změňte služby active directory, který je aktuálně přidružena předplatného Azure.
3. Vytvoření správce Azure Active Directory pro server Azure SQL Database, spravované Instance, nebo [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Nakonfigurujte klientské počítače.
5. Vytvořte uživatele databáze s omezením ve vaší databázi namapované na Azure AD identity.
6. Připojení k vaší databázi pomocí identit Azure AD.

> [!NOTE]
> Zjistěte, jak vytvořit a naplnit Azure AD a pak nakonfigurujte Azure AD s Azure SQL Database, spravované Instance a SQL Data Warehouse, najdete v tématu [konfigurovat Azure AD s Azure SQL Database](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Architektura vztahu důvěryhodnosti
Následující diagram vysoké úrovně shrnuje architektuře řešení pomocí ověřování Azure AD s Azure SQL Database. Koncepty použity k SQL Data Warehouse. Pro podporu heslo nativní uživatele Azure AD, je považován za pouze část cloudu a Azure AD nebo Azure SQL Database. Pro podporu ověřování federovaném (nebo uživatele a heslo pro přihlašovací údaje systému Windows), se vyžaduje komunikaci se službou AD FS bloku. Šipky znamenat způsobů komunikace.

![diagram ověřování aad][1]

Následující diagram označuje federace, důvěryhodnosti a vztahy hostování, které umožňují klientovi připojení k databázi odesláním token. Token ověření Azure AD a je důvěryhodný pro databázi. Odběratel 1 může představovat Azure Active Directory s nativní uživateli nebo Azure AD s federovanými uživateli. Odběratel 2 představuje možné řešení včetně importované uživatele; v tomto příkladu pocházejících z federované Azure Active Directory se službou AD FS se synchronizovat se službou Azure Active Directory. Je důležité si uvědomit, že přístup k databázi pomocí ověřování Azure AD vyžaduje, aby hostování předplatné přidružené ke službě Azure AD. Ve stejném předplatném musí použít k vytvoření SQL Server hostující Azure SQL Database nebo SQL Data Warehouse.

![předplatné relace][2]

## <a name="administrator-structure"></a>Struktura správce
Pokud používáte ověřování Azure AD, jsou dva účty správce pro server SQL Database a spravované Instance; původní správce systému SQL Server a správce Azure AD. Koncepty použity k SQL Data Warehouse. Pouze správce, které jsou založené na účet služby Azure AD můžete vytvořit první uživatel databáze Azure AD, které jsou obsažené v uživatelské databázi. Přihlášení správce Azure AD může být Azure Active Directory nebo skupině služby Azure AD. Pokud správce účtu skupiny, můžete použít kteréhokoli člena skupiny povolení více správců Azure AD pro instanci systému SQL Server. Pomocí účtu skupiny jako správce rozšiřuje možnosti správy tím, že můžete centrálně přidávat a odebírat členy skupiny ve službě Azure AD, aniž byste museli měnit uživatele a oprávnění v databázi SQL. Kdykoli se dá nakonfigurovat jenom jeden správce Azure AD (uživatele či skupinu).

![Struktura správce][3]

## <a name="permissions"></a>Oprávnění
Chcete-li vytvořit nové uživatele, musíte mít `ALTER ANY USER` oprávnění v databázi. `ALTER ANY USER` Lze udělit oprávnění uživatelé databáze. `ALTER ANY USER` Oprávnění je také uchovávat účty správce serveru a uživatele databáze s `CONTROL ON DATABASE` nebo `ALTER ON DATABASE` oprávnění pro tuto databázi a členové `db_owner` role databáze.

Chcete-li vytvořit uživatele databáze s omezením v Azure SQL Database, spravované Instance nebo SQL Data Warehouse, je nutné se připojit k databázi nebo instanci pomocí Azure AD identity. Vytvoření první databáze s omezením uživatele, je nutné připojit k databázi pomocí Správce Azure AD (který je vlastníkem databáze). Tento postup je znázorněn v [konfigurovat a spravovat ověřování Azure Active Directory s SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md). Ověřování služby Azure AD je možné, pokud správce Azure AD byl vytvořen pro server Azure SQL Database nebo SQL Data Warehouse. Pokud správce Azure Active Directory byla odebrána ze serveru, stávající uživatele Azure Active Directory vytvořili dříve v systému SQL Server můžete už připojení k databázi pomocí svých přihlašovacích údajů Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkce Azure AD a omezení
Následující členy Azure AD může být zřízen v Azure SQL server nebo SQL Data Warehouse:

- Nativní členy: člen vytvořené ve službě Azure AD ve spravované doméně nebo v doméně zákazníka. Další informace najdete v tématu [přidat vlastní název domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Federované domény členy: člen vytvořené v Azure AD s federovanou doménu. Další informace najdete v tématu [Microsoft Azure teď podporuje federační službou Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Importované členy z jiných Azure AD, kteří jsou členy nativní nebo federované domény.
- Active Directory skupiny vytvořené jako skupin zabezpečení.

Související s spravované instanci Azure AD omezení:
- Správce služby Azure AD mohou vytvářet databáze, jen uživatele Azure AD jsou vymezeny do jedné databáze a nemají toto oprávnění
- Vlastnictví databáze:
  - Objekt zabezpečení Azure AD nemůže změnit vlastnictví pro databázi (vlastnosti AUTORIZACE ON databáze) a nelze ji nastavit jako vlastníka.
  - U databází vytvořených správcem služby Azure AD je nastaven žádný vlastnictví (owner_sid pole v sys.sysdatabases je 0x1)
- Agent serveru SQL nelze spravovat, když se přihlásí pomocí objektů služby Azure AD. 
- Nelze zosobnit správce Azure AD pomocí EXECUTE AS
- Připojení DAC nepodporuje objekty zabezpečení Azure AD. 

Tyto funkce systému návratové hodnoty NULL při spuštění v Azure AD objekty:
- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování Azure Active Directory podporuje tyto metody připojení k databázi pomocí Azure AD identity:

* Pomocí integrovaného ověřování systému Windows
* Pomocí služby Azure AD hlavní název a heslo
* Pomocí ověření pomocí tokenu aplikace

### <a name="additional-considerations"></a>Další aspekty

* K vylepšení možností správy, doporučujeme zřídit vyhrazené Azure AD jako správce.   
* Pouze jeden správce Azure AD (uživatele či skupinu) mohou být konfigurovány pro server Azure SQL Database, spravované Instance nebo Azure SQL Data Warehouse kdykoli.   
* Pouze správce Azure AD pro SQL Server můžete nejprve připojit k serveru Azure SQL Database, spravované Instance nebo Azure SQL Data Warehouse pomocí účtu Azure Active Directory. Správce služby Active Directory můžete nakonfigurovat další služby Azure AD databázi uživatelů.   
* Doporučujeme nastavit časový limit připojení na 30 sekund.   
* SQL Server 2016 Management Studio a SQL Server Data Tools pro Visual Studio 2015 (verze 14.0.60311.1April 2016 nebo novější) podporují ověřování Azure Active Directory. (Ověřování azure AD podporuje **zprostředkovatel dat .NET Framework pro SQL Server**; minimální verze rozhraní .NET Framework 4.6). Proto nejnovější verze těchto nástrojů a aplikací na datové vrstvě (DAC a. Souboru BACPAC) můžete použít ověřování Azure AD.   
* [ODBC verze 13.1](https://www.microsoft.com/download/details.aspx?id=53339) podporuje ověřování Azure Active Directory, ale `bcp.exe` nelze se připojit pomocí ověřování Azure Active Directory, protože používá poskytovatele starší ODBC.   
* `sqlcmd` podporuje Azure Active Directory authentication počínaje 13.1 dostupné od verze [Download Center](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools pro Visual Studio 2015 vyžaduje alespoň. dubna 2016 verzi nástroje Data (verze 14.0.60311.1). Aktuálně nejsou Azure AD uživatelům zobrazí v Průzkumníku objektů rozšíření SSDT. Jako alternativní řešení, zobrazit uživateli v [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [6.0 ovladač JDBC Microsoft pro systém SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ověřování podporuje Azure AD. Další informace naleznete v [nastavení vlastnosti připojení](https://msdn.microsoft.com/library/ms378988.aspx).   
* PolyBase se nemůže ověřit pomocí ověřování Azure AD.   
* Ověřování služby Azure AD je podporováno pro databázi SQL pomocí portálu Azure **Import databáze** a **Export databáze** okna. Import a export pomocí ověřování Azure AD je podporováno také z příkaz prostředí PowerShell.   
* Ověřování služby Azure AD je podporováno pro databázi SQL, spravované Instance a SQL Data Warehouse pomocí rozhraní příkazového řádku. Další informace najdete v tématu [konfigurovat a spravovat ověřování Azure Active Directory s SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md) a [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Další postup
- Chcete-li zjistit, jak vytvořit a naplnit Azure AD a pak nastavte Azure AD s Azure SQL Database nebo Azure SQL Data Warehouse, přečtěte si téma [konfigurovat a spravovat ověřování Azure Active Directory s databází SQL, spravované Instance nebo SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
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

