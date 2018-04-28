---
title: Multi-Factor authentication – Azure SQL | Microsoft Docs
description: Azure SQL Database a Azure SQL Data Warehouse podporují připojení ze serveru SQL Server Management Studio (SSMS) pomocí univerzální ověřování služby Active Directory.
services: sql-database
documentationcenter: ''
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: mireks
ms.openlocfilehash: 8c6472dcde55a7b642ced7bbf0d93893df9208e0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Univerzální ověřování se službou SQL Database a SQL Data Warehouse (SSMS podporu vícefaktorového ověřování)
Azure SQL Database a Azure SQL Data Warehouse podporují připojení pomocí SQL Server Management Studio (SSMS) *Universal ověřování služby Active Directory*. 
**Stáhněte si nejnovější aplikace SSMS** – v klientském počítači stáhněte nejnovější verzi aplikace SSMS, z [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pro všechny funkce v tomto tématu použijte alespoň července 2017 verze 17.2.  Poslední připojení dialogových oken, vypadá takto: ![1mfa universal připojit](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "dokončení pole uživatelské jméno.")  

## <a name="the-five-authentication-options"></a>Možnosti pět ověřování  
- Univerzální ověřování služby Active Directory podporuje dvě metody neinteraktivního ověřování (`Active Directory - Password` ověřování a `Active Directory - Integrated` ověřování). Neinteraktivně `Active Directory - Password` a `Active Directory - Integrated` metody ověřování lze použít v mnoha různých aplikacích (ADO.NET, JDBC, ODBC, atd.). Tyto dvě metody nikdy výsledkem automaticky otevíraná okna.

- `Active Directory - Universal with MFA` ověřování je interaktivní metoda, která podporuje také *Azure Multi-Factor Authentication* (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování s celou řadu možností snadno ověřování (telefonní hovor, textová zpráva, čipové karty s PIN kód nebo oznámení mobilní aplikace), což umožňuje uživatelům zvolit metodu dávají přednost. Interaktivní vícefaktorového ověřování s Azure AD může způsobit v místním dialogovém okně pro ověření.

Popis služby Multi-Factor Authentication najdete v tématu [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Postup konfigurace najdete v tématu [nakonfigurovat databázi SQL Azure Multi-Factor authentication pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD domain Název nebo klienta parametr ID   

Počínaje [SSMS verze 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), uživatelé, které jsou importovány do aktuální službě Active Directory z jiných Azure Active Directory jako uživatele typu Host, můžete zadat název domény služby Azure AD nebo ID klienta, když se připojují. Uživatele typu Host zahrnete uživatele pozvat z jiné služby Active Directory Azure, účty Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty jako gmail.com. Tyto informace umožňují **Active Universal Directory s ověřováním MFA** k identifikaci správný ověřovací autorita. Tato možnost je také nutný pro podporu účty Microsoft (MSA) jako je outlook.com, hotmail.com, live.com nebo – účet spravované služby účty. Všechny tyto uživatele, kteří mají být ověřeny pomocí ověřování Universal musí zadejte jejich název domény služby Azure AD nebo klienta ID. Tento parametr představuje aktuální Azure AD domain Název nebo ID klienta serveru Azure souvisí s. Například, pokud Azure Server souvisí s Azure AD domain `contosotest.onmicrosoft.com` kde uživatele `joe@contosodev.onmicrosoft.com` je umístěn jako importované uživatele z Azure AD domain `contosodev.onmicrosoft.com`, je název domény vyžadovaný k ověření tohoto uživatele `contosotest.onmicrosoft.com`. Pokud uživatel je nativní uživatele Azure AD propojená se systémem Azure Server a není účet účet spravované služby, je požadováno ID názvu nebo klienta žádné domény. Zadat parametr (počínaje SSMS verze 17.2), v **připojit k databázi** dialogové okno pole, do dialogového okna zadejte výběr **Universal pomocí vícefaktorového ověřování služby Active Directory -** ověřování, klikněte na tlačítko  **Možnosti**, dokončení **uživatelské jméno** a klikněte **vlastnosti připojení** kartě. Zkontrolujte **ID názvu nebo klienta domény AD** pole a zadejte ověřovací autorita, jako je název domény (**contosotest.onmicrosoft.com**) nebo identifikátor GUID ID klienta.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Obchodní na firemní podporu Azure AD   
Uživatele Azure AD, podporuje pro scénáře Azure AD B2B jako uživatele typu Host (viz [co je spolupráce Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) může připojit k SQL Database a SQL Data Warehouse pouze jako součást členové skupiny vytvořené v aktuální Azure AD a namapovat ručně pomocí jazyka Transact-SQL `CREATE USER` příkaz v na danou databázi. Například pokud `steve@gmail.com` vyzván k Azure AD `contosotest` (s Azure Ad domain `contosotest.onmicrosoft.com`), Azure AD jako skupinu `usergroup` musí být vytvořeny ve službě Azure AD, který obsahuje `steve@gmail.com` člen. Potom tuto skupinu musí vytvořit pro konkrétní databázi (tj. databáze) Správce Azure AD SQL nebo Azure AD DBO spuštěním Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` příkaz. Když uživatel databáze vytvořené, pak uživatele `steve@gmail.com` se může přihlásit k `MyDatabase` pomocí možnosti ověřování aplikace SSMS `Active Directory – Universal with MFA support`. Ve výchozím nastavení usergroup, má pouze oprávnění connect a všechny další přístup k datům, která bude muset být udělena běžným způsobem. Upozorňujeme, že uživatel `steve@gmail.com` jako uživatel guest musí zaškrtněte políčko a přidat název domény AD `contosotest.onmicrosoft.com` v aplikaci SSMS **vlastnost připojení** dialogové okno. **ID názvu nebo klienta domény AD** možnost je podporována pouze pro univerzální s možností připojení vícefaktorového ověřování, jinak je šedá.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Univerzální omezení ověřování pro SQL Database a SQL Data Warehouse
- Aplikace SSMS a SqlPackage.exe jsou nástroje pouze pro aktuálně povoleno pro MFA prostřednictvím Universal ověřování služby Active Directory.
- Aplikace SSMS verze 17.2, podporuje více uživatelů souběžný přístup pomocí Universal ověřování pomocí vícefaktorového ověřování. Verze 17,0 a 17.1, omezený přihlášení pro instanci aplikace SSMS Universal ověřováním jeden účet Azure Active Directory. Přihlásit se jako jiný účet služby Azure AD, je nutné použít jiná instance aplikace SSMS. (Toto omezení je omezený na univerzální ověřování služby Active Directory, můžete se přihlásit na jiné servery pomocí ověřování hesla Active Directory, integrované ověřování Active Directory nebo ověřování systému SQL Server).
- Aplikace SSMS podporuje univerzální ověřování služby Active Directory pro vizualizaci Průzkumník objektů, Editor dotazů a úložiště dotazů.
- Aplikace SSMS verze 17.2 poskytuje podporu DacFx Průvodce pro Export/Extract nebo nasadit Data databáze. Konkrétní uživatel ověřen pomocí dialogu počáteční ověřování pomocí Universal ověřování, Průvodce DacFx funkce stejným způsobem, jakým funguje pro všechny ostatní metody ověřování.
- Návrhář tabulky SSMS nepodporuje univerzální ověřování.
- Kromě toho, že musíte použít podporovanou verzi aplikace SSMS nejsou žádné další požadavky na software pro univerzální ověřování služby Active Directory.  
- Verze Active Directory Authentication Library (ADAL) pro univerzální ověřování aktualizoval na nejnovější dostupné vydané verzi ADAL.dll 3.13.9. V tématu [knihovna ověřování Active Directory 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Další postup

- Postup konfigurace najdete v tématu [nakonfigurovat databázi SQL Azure Multi-Factor authentication pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Ostatní udělit přístup k vaší databázi: [SQL databáze ověřování a autorizace: udělení přístupu](sql-database-manage-logins.md)  
- Zajistěte, aby ostatní můžete připojit přes bránu firewall: [konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí portálu Azure](sql-database-configure-firewall-settings.md)  
- [Konfigurovat a spravovat ověřování Azure Active Directory s SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Architektura aplikace datové vrstvy Microsoft SQL Server (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Importovat soubor souboru BACPAC pro novou databázi SQL Azure](../sql-database/sql-database-import.md)  
- [Exportovat do souboru BACPAC souboru Azure SQL database](../sql-database/sql-database-export.md)  
- C# rozhraní [IUniversalAuthProvider rozhraní](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Při použití **Active Directory - Universal s MFA** ověřování ADAL trasování je k dispozici počínaje [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Vypnout ve výchozím nastavení, můžete zapnout ADAL trasování pomocí **nástroje**, **možnosti** nabídky v části **služeb Azure**, **cloudu Azure**,  **ADAL úroveň trasování okno výstup**, za nímž povolením **výstup** v **zobrazení** nabídky. Trasování jsou k dispozici v okně výstupu. při výběru **možnost Azure Active Directory**.  
