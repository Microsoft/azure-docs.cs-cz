---
title: Použití ověřování Multi-Factor Azure Active Directory
description: Azure SQL Database, spravovaná instance Azure SQL a Azure synapse Analytics podporují připojení z SQL Server Management Studio (SSMS) pomocí univerzálního ověřování služby Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 04/23/2020
tags: azure-synapse
ms.openlocfilehash: 607644a67fad966be25915e55a1bec83d70a1fe0
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344811"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Použití ověřování Multi-Factor Azure Active Directory
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, spravovaná instance Azure a Azure synapse Analytics podporují připojení z SQL Server Management Studio (SSMS) s využitím *Azure Active Directory-Universal s* ověřováním MFA. Tento článek popisuje rozdíly mezi různými možnostmi ověřování a také omezeními souvisejícími s používáním univerzálního ověřování.

**Stáhněte si nejnovější SSMS** -v klientském počítači, Stáhněte si nejnovější verzi nástroje SSMS, od [Stažení SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

Pro všechny funkce popsané v tomto článku použijte minimálně červenec 2017, verze 17,2. Dialogové okno nejaktuálnější připojení by mělo vypadat podobně jako na následujícím obrázku:

  ![1mfa – Universal-Connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Dokončí pole uživatelské jméno.")  

## <a name="the-five-authentication-options"></a>Pět možností ověřování  

Univerzální ověřování služby Active Directory podporuje dvě metody ověřování, které nejsou interaktivní:
    - `Azure Active Directory - Password`přihlašovací
    - `Azure Active Directory - Integrated`přihlašovací

K dispozici jsou také dva modely neinteraktivního ověřování, které lze použít v mnoha různých aplikacích (ADO.NET, JDCB, ODC atd.). Tyto dvě metody nikdy nevedou k místním dialogovým oknům:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Interaktivní metoda, která podporuje taky Azure Multi-Factor Authentication (MFA), je:`Active Directory - Universal with MFA`

Azure MFA pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování s využitím široké škály možností jednoduchého ověřování (telefonní hovor, textová zpráva, čipové karty s PIN kódem nebo oznámení pro mobilní aplikace), které uživatelům umožňují vybrat si metodu, které dáváte přednost. Interaktivní vícefaktorové ověřování pomocí Azure AD může mít za následek automaticky otevírané okno k ověření.

Popis Azure Multi-Factor Authentication najdete v článku [Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).
Postup konfigurace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parametr názvu domény Azure AD nebo ID tenanta

Počínaje [verzí SSMS 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)mohou uživatelé, kteří jsou importováni do aktuální služby Active Directory z jiných Azure Active Directory jako uživatelé typu Host, poskytovat název domény služby Azure AD nebo ID tenanta při připojení. Uživatelé typu Host zahrnují uživatele pozvaní z jiných reklam Azure, účtů Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty, jako je gmail.com. Tyto informace umožňují, aby **Služba Active Directory Universal s ověřováním MFA** identifikovala správnou ověřovací autoritu. Tato možnost je také nutná k podpoře účtů Microsoft (MSA), jako jsou účty outlook.com, hotmail.com, live.com nebo jiné než MSA. Všichni uživatelé, kteří chtějí být ověřeni pomocí univerzálního ověřování, musí zadat svůj název domény nebo ID tenanta služby Azure AD. Tento parametr představuje aktuální název domény nebo ID tenanta Azure AD, se kterým je server Azure propojený. Pokud je například Azure Server přidružený k doméně Azure AD `contosotest.onmicrosoft.com` , kde je uživatel `joe@contosodev.onmicrosoft.com` hostovaný jako importovaný uživatel z domény služby Azure AD `contosodev.onmicrosoft.com` , název domény, který je potřeba k ověření tohoto uživatele, je `contosotest.onmicrosoft.com` . Když je uživatel nativním uživatelem služby Azure AD připojeným k serveru Azure a nejedná se o účet MSA, není nutné mít žádný název domény ani ID tenanta. Chcete-li zadat parametr (počínaje verzí SSMS 17,2), v dialogovém **okně připojit k databázi** dokončete dialogové okno, vyberte možnost **Active Directory – univerzální s** ověřováním MFA, klikněte na **Možnosti**, vyplňte **pole uživatelské jméno** a potom klikněte na kartu **Vlastnosti připojení** . Zkontrolujte **název domény služby AD nebo ID tenanta** a poskytněte ověřovací autoritu, jako je například název domény (**contosotest.onmicrosoft.com**) nebo identifikátor GUID ID tenanta.  
   ![MFA – tenant – SSMS](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Pokud používáte SSMS 18. x nebo novější, název domény služby AD nebo ID tenanta už není potřeba pro uživatele typu Host, protože 18. x nebo novější ho automaticky rozpoznává.

   ![MFA – tenant – SSMS](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Podpora Azure AD pro firmy

Uživatelé Azure AD, kteří jsou podporováni ve scénářích Azure AD B2B jako uživatelé typu Host (viz [co je spolupráce Azure B2B](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) se mohou připojit k SQL Database a Azure synapse jenom jako součást členů skupiny vytvořené v aktuální službě Azure AD a ručně namapovat pomocí příkazu Transact-SQL `CREATE USER` v dané databázi. Pokud `steve@gmail.com` je například Pozvánka do Azure AD `contosotest` (s doménou Azure AD `contosotest.onmicrosoft.com` ), `usergroup` musí být ve službě Azure AD, která obsahuje člena, vytvořená skupina Azure AD `steve@gmail.com` . Pak je potřeba tuto skupinu vytvořit pro konkrétní databázi (tj. MyDatabase) pomocí služby Azure AD SQL admin nebo Azure AD DBO spuštěním příkazu jazyka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . Po vytvoření uživatele databáze se uživatel `steve@gmail.com` může přihlásit k `MyDatabase` používání možnosti ověřování SSMS `Active Directory – Universal with MFA support` . Ve výchozím nastavení má uživatel ve výchozím nastavení pouze oprávnění připojit a jakýkoliv další přístup k datům, který bude potřeba udělit běžným způsobem. Všimněte si, že uživatel `steve@gmail.com` jako uživatel typu Host musí zaškrtnout políčko a přidat název domény služby AD `contosotest.onmicrosoft.com` do dialogového okna **Vlastnosti připojení** SSMS. Možnost **název domény AD nebo ID tenanta** je podporovaná jenom pro možnosti připojení univerzální s MFA, jinak je šedá.

## <a name="universal-authentication-limitations"></a>Omezení univerzálního ověřování

- SSMS a SqlPackage. exe jsou jediné nástroje aktuálně povolené pro MFA prostřednictvím univerzálního ověřování služby Active Directory.
- SSMS verze 17,2 podporuje souběžný přístup více uživatelů s využitím univerzálního ověřování s MFA. Verze 17,0 a 17,1 omezuje přihlášení pro instanci SSMS pomocí univerzálního ověřování na jeden Azure Active Directory účet. Pokud se chcete přihlásit jako jiný účet služby Azure AD, musíte použít jinou instanci SSMS. (Toto omezení je omezené na univerzální ověřování Active Directory. můžete se přihlásit k jinému serveru pomocí ověřování hesla služby Active Directory, integrovaného ověřování Active Directory nebo SQL Server ověřování).
- SSMS podporuje univerzální ověřování služby Active Directory pro Průzkumník objektů, Editor dotazů a vizualizaci úložiště dotazů.
- SSMS verze 17,2 poskytuje podporu Průvodce DacFx pro export/extrakci nebo nasazení dat databáze. Po ověření konkrétního uživatele prostřednictvím dialogu počátečního ověřování pomocí univerzálního ověřování funguje Průvodce DacFx stejným způsobem jako u všech ostatních metod ověřování.
- Návrhář tabulky SSMS nepodporuje univerzální ověřování.
- Pro univerzální ověřování služby Active Directory nejsou k dispozici žádné další požadavky na software, s výjimkou, že je nutné použít podporovanou verzi SSMS.  
- Verze Active Directory Authentication Library (ADAL) pro univerzální ověřování se aktualizovala na nejnovější vydanou verzi ADAL. dll, která 3.13.9 k dispozici. Viz [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Další kroky

- Postup konfigurace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Udělit ostatním přístup k vaší databázi: [SQL Database ověřování a autorizace: udělení přístupu](logins-create-manage.md)  
- Zajistěte, aby se uživatelé mohli připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru pomocí Azure Portal](firewall-configure.md)  
- [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database nebo Azure synapse](authentication-aad-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Import souboru BACPAC do nové databáze](database-import.md)  
- [Export databáze do souboru BACPAC](database-export.md)  
- [Rozhraní IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx) pro rozhraní C#  
- Při používání **služby Active Directory – univerzální s** ověřováním MFA je k dispozici trasování ADAL od [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ve výchozím nastavení můžete trasování ADAL zapnout pomocí nabídky **nástroje**, **Možnosti** , v části **služby Azure**, **cloud Azure**, **ADAL okno výstup úroveň trasování**a následným povolením **výstupu** v nabídce **zobrazení** . Trasování jsou k dispozici v okně výstup při výběru **možnosti Azure Active Directory**.  
