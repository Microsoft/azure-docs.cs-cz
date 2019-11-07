---
title: Použití vícefaktorového ověřování AAD s Azure SQL Database a Azure SQL Data Warehouse
description: Azure SQL Database a Azure SQL Data Warehouse podporují připojení z SQL Server Management Studio (SSMS) pomocí univerzálního ověřování služby Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/08/2018
ms.openlocfilehash: d9d391c1496102d38e9da767ad26b408860ca682
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687079"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Použití vícefaktorového ověřování AAD s Azure SQL Database a Azure SQL Data Warehouse (podpora SSMS pro MFA)
Azure SQL Database a Azure SQL Data Warehouse podporují připojení z SQL Server Management Studio (SSMS) pomocí *univerzálního ověřování služby Active Directory*. Tento článek popisuje rozdíly mezi různými možnostmi ověřování a také omezeními souvisejícími s používáním univerzálního ověřování. 

**Stáhněte si nejnovější SSMS** -v klientském počítači, Stáhněte si nejnovější verzi nástroje SSMS, od [Stažení SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Pro všechny funkce popsané v tomto článku použijte minimálně červenec 2017, verze 17,2.  Dialogové okno nejaktuálnější připojení by mělo vypadat podobně jako na následujícím obrázku:
 
  ![1mfa – Universal-Connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Dokončí pole uživatelské jméno.")  

## <a name="the-five-authentication-options"></a>Pět možností ověřování  

Univerzální ověřování služby Active Directory podporuje dvě metody ověřování, které nejsou interaktivní:
    - ověřování `Active Directory - Password`
    - ověřování `Active Directory - Integrated`

K dispozici jsou také dva modely neinteraktivního ověřování, které lze použít v mnoha různých aplikacích (ADO.NET, JDCB, ODC atd.). Tyto dvě metody nikdy nevedou k místním dialogovým oknům: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Interaktivní metoda je, že také podporuje službu Azure Multi-Factor Authentication (MFA): 
- `Active Directory - Universal with MFA` 


Azure MFA pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování s využitím široké škály možností jednoduchého ověřování (telefonní hovor, textová zpráva, čipové karty s PIN kódem nebo oznámení pro mobilní aplikace), které uživatelům umožňují vybrat si metodu, které dáváte přednost. Interaktivní vícefaktorové ověřování pomocí Azure AD může mít za následek automaticky otevírané okno k ověření.

Popis Multi-Factor Authentication najdete v tématu [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Postup konfigurace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parametr názvu domény Azure AD nebo ID tenanta   

Počínaje [verzí SSMS 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)mohou uživatelé, kteří jsou importováni do aktuální služby Active Directory z jiných Azure Active Directory jako uživatelé typu Host, poskytovat název domény služby Azure AD nebo ID tenanta při připojení. Uživatelé typu Host zahrnují uživatele pozvaní z jiných reklam Azure, účtů Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty, jako je gmail.com. Tyto informace umožňují, aby **Služba Active Directory Universal s ověřováním MFA** identifikovala správnou ověřovací autoritu. Tato možnost je také nutná k podpoře účtů Microsoft (MSA), jako jsou účty outlook.com, hotmail.com, live.com nebo jiné než MSA. Všichni uživatelé, kteří chtějí být ověřeni pomocí univerzálního ověřování, musí zadat svůj název domény nebo ID tenanta služby Azure AD. Tento parametr představuje aktuální název domény nebo ID tenanta Azure AD, se kterým je server Azure propojený. Pokud je například Azure Server přidružený k doméně služby Azure AD `contosotest.onmicrosoft.com`, kde je uživatelská `joe@contosodev.onmicrosoft.com` hostovaná jako importovaný uživatel z domény Azure AD `contosodev.onmicrosoft.com`, název domény, který je potřeba k ověření tohoto uživatele, je `contosotest.onmicrosoft.com`. Když je uživatel nativním uživatelem služby Azure AD připojeným k serveru Azure a nejedná se o účet MSA, není nutné mít žádný název domény ani ID tenanta. Pokud chcete zadat parametr (počínaje verzí SSMS 17,2), v dialogovém okně **připojit k databázi** dokončete dialogové okno, vyberte **Active Directory – univerzální s** ověřováním MFA, klikněte na **Možnosti**, dokončete **uživatelské jméno.** a potom klikněte na kartu **Vlastnosti připojení** . Zkontrolujte **název domény služby AD nebo ID tenanta** a poskytněte ověřovací autoritu, jako je například název domény (**contosotest.onmicrosoft.com**) nebo identifikátor GUID ID tenanta.  
   ![MFA-tenant-SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Pokud používáte SSMS 18. x nebo novější, název domény služby AD nebo ID tenanta už není potřeba pro uživatele typu Host, protože 18. x nebo novější ho automaticky rozpoznává.

   ![MFA – tenant – SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Podpora Azure AD pro firmy   
Uživatelé Azure AD podporovaní ve scénářích Azure AD B2B jako uživatelé typu Host (viz [co je spolupráce Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) se můžou připojit k SQL Database a SQL Data Warehouse jenom jako součást členů skupiny vytvořeného v aktuální službě Azure AD a ručně namapovat pomocí jazyka Transact-SQL. příkaz `CREATE USER` v dané databázi. Pokud je například `steve@gmail.com` pozván na `contosotest` Azure AD (s doménou Azure AD `contosotest.onmicrosoft.com`), musí být ve službě Azure AD, která obsahuje `steve@gmail.com` člena, vytvořená skupina Azure AD, například `usergroup`. Tato skupina se pak musí vytvořit pro konkrétní databázi (tj. MyDatabase) správcem Azure AD SQL nebo Azure AD DBO spuštěním příkazu Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER`. Po vytvoření uživatele databáze se uživatel `steve@gmail.com` může přihlásit k `MyDatabase` pomocí možnosti ověřování SSMS `Active Directory – Universal with MFA support`. Ve výchozím nastavení má uživatel ve výchozím nastavení pouze oprávnění připojit a jakýkoliv další přístup k datům, který bude potřeba udělit běžným způsobem. Všimněte si, že uživatel `steve@gmail.com` jako uživatel typu Host musí zaškrtnout políčko a přidat název domény služby AD `contosotest.onmicrosoft.com` v dialogovém okně **Vlastnosti připojení** SSMS. Možnost **název domény AD nebo ID tenanta** je podporovaná jenom pro možnosti připojení univerzální s MFA, jinak je šedá.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Omezení univerzálního ověřování pro SQL Database a SQL Data Warehouse
- SSMS a SqlPackage. exe jsou jediné nástroje aktuálně povolené pro MFA prostřednictvím univerzálního ověřování služby Active Directory.
- SSMS verze 17,2 podporuje souběžný přístup více uživatelů s využitím univerzálního ověřování s MFA. Verze 17,0 a 17,1 omezuje přihlášení pro instanci SSMS pomocí univerzálního ověřování na jeden Azure Active Directory účet. Pokud se chcete přihlásit jako jiný účet služby Azure AD, musíte použít jinou instanci SSMS. (Toto omezení je omezené na univerzální ověřování Active Directory. můžete se přihlásit k různým serverům pomocí ověřování hesla služby Active Directory, integrovaného ověřování Active Directory nebo SQL Server ověřování).
- SSMS podporuje univerzální ověřování služby Active Directory pro Průzkumník objektů, Editor dotazů a vizualizaci úložiště dotazů.
- SSMS verze 17,2 poskytuje podporu Průvodce DacFx pro export/extrakci nebo nasazení dat databáze. Po ověření konkrétního uživatele prostřednictvím dialogu počátečního ověřování pomocí univerzálního ověřování funguje Průvodce DacFx stejným způsobem jako u všech ostatních metod ověřování.
- Návrhář tabulky SSMS nepodporuje univerzální ověřování.
- Pro univerzální ověřování služby Active Directory nejsou k dispozici žádné další požadavky na software, s výjimkou, že je nutné použít podporovanou verzi SSMS.  
- Verze Active Directory Authentication Library (ADAL) pro univerzální ověřování se aktualizovala na nejnovější vydanou verzi ADAL. dll, která 3.13.9 k dispozici. Viz [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Další kroky

- Postup konfigurace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Udělit ostatním přístup k vaší databázi: [SQL Database ověřování a autorizace: udělení přístupu](sql-database-manage-logins.md)  
- Zajistěte, aby se uživatelé mohli připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí Azure Portal](sql-database-configure-firewall-settings.md)  
- [Konfigurace a správa ověřování služby Azure Active Directory s využitím služby SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Import souboru BACPAC do nové databáze Azure SQL](../sql-database/sql-database-import.md)  
- [Export databáze Azure SQL do souboru BACPAC](../sql-database/sql-database-export.md)  
- C#rozhraní [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx) Interface  
- Při používání **služby Active Directory – univerzální s** ověřováním MFA je k dispozici trasování ADAL od [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ve výchozím nastavení můžete trasování ADAL zapnout pomocí nabídky **nástroje**, **Možnosti** , v části **služby Azure**, **cloud Azure**, **ADAL okno výstup úroveň trasování**a následným povolením **výstupu** v nabídce **zobrazení** . Trasování jsou k dispozici v okně výstup při výběru **možnosti Azure Active Directory**.  
