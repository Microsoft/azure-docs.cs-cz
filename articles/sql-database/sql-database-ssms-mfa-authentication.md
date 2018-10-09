---
title: Multi-Factor authentication – Azure SQL | Dokumentace Microsoftu
description: Azure SQL Database a Azure SQL Data Warehouse podporují připojení ze serveru SQL Server Management Studio (SSMS) pomocí univerzálního ověřování Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 9837316cab503e6ade623e91a41176e6f4bfc84a
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867645"
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Univerzální ověřování pomocí SQL Database a SQL Data Warehouse (podpora SSMS pro vícefaktorové ověřování)
Azure SQL Database a Azure SQL Data Warehouse připojení pomocí SQL Server Management Studio (SSMS) podporují *univerzálního ověřování Active Directory*. 
**Stáhněte si nejnovější verze aplikace SSMS** – v klientském počítači, stáhněte si nejnovější verzi SSMS, z [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pro všechny funkce v tomto článku použijte alespoň verzi 17.2. července 2017.  Nejnovější dialogovém okně připojení, vypadá takto: ![připojit univerzální 1mfa](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "dokončení pole uživatelské jméno.")  

## <a name="the-five-authentication-options"></a>Možnosti pět ověřování  
- Univerzální ověřování služby Active Directory podporuje dvě metody jako neinteraktivní ověřování (`Active Directory - Password` ověřování a `Active Directory - Integrated` ověřování). Neinteraktivní `Active Directory - Password` a `Active Directory - Integrated` metod ověřování můžete použít v mnoha různých aplikacích (ADO.NET, JDBC, ODBC atd.). Tyto dvě metody nikdy způsobit automaticky otevíraná okna.

- `Active Directory - Universal with MFA` ověřování je interaktivní metoda, která podporuje také *ověřování Azure Multi-Factor Authentication* (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování. Nabízí silné ověřování s řadou jednoduchých možností (telefonní hovor, textová zpráva, čipové karty s PIN kódu nebo oznámení přes mobilní aplikaci), uživatele zvolil způsob, co jim vyhovuje. Interaktivní MFA s Azure AD může vést k místní dialogové okno pro ověření.

Popis ověřování službou Multi-Factor Authentication, naleznete v tématu [ověřování službou Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Postup konfigurace najdete v tématu [konfigurace služby Azure SQL Database vícefaktorové ověřování pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD tenanta nebo název parametr ID domény   

Počínaje [SSMS verze 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), uživatelé, kteří jsou importovány do aktuální službě Active Directory z jiných adresářů Active Azure jako uživatelé typu Host, můžete zadat název domény Azure AD, nebo ID tenanta, když se připojují. Uživatelé typu Host zahrnete uživatele pozvat další Azure reklamy, účty Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty jako gmail.com. Tyto informace umožňují **aktivní univerzální Directory s ověřováním MFA** identifikovat správný ověřovací autorita. Tato možnost je také nezbytný pro podporu účty Microsoft (MSA), jako jsou outlook.com, hotmail.com, live.com nebo účtů bez MSA. Tito uživatelé, kteří chtějí ověřit pomocí univerzální ověřování musíte zadat své jméno domény Azure AD nebo ID tenanta Tento parametr představuje aktuální služby Azure AD domain Název nebo ID tenanta serveru Azure je propojena s. Například, pokud Azure Server je přidružený k doméně služby Azure AD `contosotest.onmicrosoft.com` kde uživatele `joe@contosodev.onmicrosoft.com` je hostovaný jako importované uživatele z Azure AD domain `contosodev.onmicrosoft.com`, název domény a nemusí opakovat ověření tohoto uživatele je `contosotest.onmicrosoft.com`. Když uživatel je nativní uživatele služby Azure AD propojená se systémem Server Azure a není účet MSA, je potřeba žádné ID tenanta nebo název domény. K zadání parametrů (od verze 17.2 SSMS), v **připojit k databázi** dialogové okno pole, do dialogového okna zadejte výběr **služby Active Directory – univerzální vícefaktorovým Ověřováním** ověřování, klikněte na tlačítko  **Možnosti**vyplňte **uživatelské jméno** pole a potom klikněte na tlačítko **vlastnosti připojení** kartu. Zkontrolujte, **ID tenanta nebo název domény AD** pole a zadejte ověřovací autorita, jako je název domény (**contosotest.onmicrosoft.com**) nebo identifikátor GUID ID tenanta.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Podpora Azure AD B2B   
Uživatelé Azure AD podporuje pro scénáře B2B ve službě Azure AD jako uživatele typu Host (viz [co je spolupráce B2B ve službě Azure](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) můžete připojit k SQL Database a SQL Data Warehouse pouze jako součást členové skupiny vytvořené v aktuální službě Azure AD a mapovat ručně pomocí příkazů jazyka Transact-SQL `CREATE USER` příkaz v dané databáze. Například pokud `steve@gmail.com` pozvat do služby Azure AD `contosotest` (s Azure Ad domain `contosotest.onmicrosoft.com`), skupinu Azure AD, například `usergroup` musí být vytvořeny ve službě Azure AD, který obsahuje `steve@gmail.com` člena. Potom tuto skupinu musejí být vytvořeny pro určitou databázi (například databáze) Správce Azure AD SQL nebo Azure AD DBO spuštěním příkazů jazyka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` příkazu. Po uživatele databáze, pak uživatel `steve@gmail.com` přihlásit `MyDatabase` pomocí možnosti ověřování aplikace SSMS `Active Directory – Universal with MFA support`. Této skupině uživatelů, ve výchozím nastavení, má pouze oprávnění k připojení a další data přístup, který bude muset být udělen běžným způsobem. Všimněte si, že uživatel `steve@gmail.com` jako uživatele typu Host musíte zaškrtnout políčko a přidání názvu domény AD `contosotest.onmicrosoft.com` v aplikaci SSMS **vlastnost připojení** dialogové okno. **ID tenanta nebo název domény AD** možnost je podporována pouze u univerzálních s možnostmi připojení vícefaktorové ověřování, jinak se šedě.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Univerzální omezení ověřování pro SQL Database a SQL Data Warehouse
- SSMS a SqlPackage.exe jsou pouze nástroje aktuálně povoleným vícefaktorovým Ověřováním pomocí univerzálního ověřování Active Directory.
- SSMS verze 17.2, podporuje souběžný přístup více uživatelů pomocí univerzální ověřování pomocí vícefaktorového ověřování. Verze 17,0 a 17.1, s omezením pomocí specifikátoru přihlašovací údaje pro instanci SSMS s využitím univerzální ověřování na jeden účet služby Azure Active Directory. Přihlásit se jako jiný účet služby Azure AD, je nutné použít jinou instanci aplikace ssms. (Toto omezení je omezená na univerzálního ověřování Active Directory, se můžete přihlásit na jiné servery pomocí ověřování hesla Active Directory, integrované ověřování Active Directory nebo ověřování systému SQL Server).
- SSMS podporuje univerzálního ověřování Active Directory za účelem vizualizace Průzkumník objektů, Editor dotazů a Query Store.
- SSMS verze 17.2 poskytuje podporu průvodce DacFx pro Export/Extract/nasazení dat. databázi. Konkrétní uživatel ověřen pomocí dialogového okna počáteční ověřování pomocí univerzální ověřování, funkce Průvodce DacFx stejným způsobem funguje pro všechny ostatní metody ověřování.
- Návrhář tabulky aplikace SSMS nepodporuje univerzální ověřování.
- Nejsou žádné další požadavky na software pro univerzálního ověřování Active Directory s tím rozdílem, že musí použít podporovanou verzi aplikace ssms.  
- Verze Active Directory Authentication Library (ADAL) pro univerzální ověřování byl aktualizován na nejnovější dostupné vydané verzi ADAL.dll 3.13.9. Zobrazit [Active Directory Authentication Library 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Další postup

- Postup konfigurace najdete v tématu [konfigurace služby Azure SQL Database vícefaktorové ověřování pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Ostatní udělit přístup k databázi: [SQL Database ověřování a autorizace: udělení přístupu](sql-database-manage-logins.md)  
- Ujistěte se, že ostatní se můžete připojit přes bránu firewall: [konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí webu Azure portal](sql-database-configure-firewall-settings.md)  
- [Konfigurace a správa ověřování služby Azure Active Directory s využitím služby SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Architektura aplikace na datové vrstvě Microsoft SQL Server (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Import souboru BACPAC do nové databáze SQL Azure](../sql-database/sql-database-import.md)  
- [Export databáze SQL Azure do souboru BACPAC](../sql-database/sql-database-export.md)  
- Rozhraní jazyka C# [IUniversalAuthProvider rozhraní](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Při použití **Active Directory – univerzální vícefaktorovým Ověřováním** ověřování ADAL trasování je k dispozici od [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Vypnuto ve výchozím nastavení, můžete zapnout trasování ADAL pomocí **nástroje**, **možnosti** nabídky v části **Azure Services**, **Azure Cloud**,  **ADAL úroveň trasování okna výstup**následovaný povolení **výstup** v **zobrazení** nabídky. Trasování jsou k dispozici v okně výstupu při výběru **Azure Active Directory možnost**.  
