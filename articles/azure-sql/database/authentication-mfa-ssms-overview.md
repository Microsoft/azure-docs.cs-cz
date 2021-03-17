---
title: Použití ověřování Multi-Factor Azure Active Directory
description: Azure SQL Database, spravovaná instance Azure SQL a Azure synapse Analytics podporují připojení z SQL Server Management Studio (SSMS) pomocí univerzálního ověřování služby Active Directory.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841124"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Použití ověřování Multi-Factor Azure Active Directory
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics podporují připojení od [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) pomocí ověřování *MFA (Azure Active Directory – Universal* ). Tento článek popisuje rozdíly mezi různými možnostmi ověřování a také omezení související s používáním univerzálního ověřování v Azure Active Directory (Azure AD) pro Azure SQL.

**Stáhněte si nejnovější SSMS** -v klientském počítači, Stáhněte si nejnovější verzi nástroje SSMS, od [Stažení SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


Pro všechny funkce popsané v tomto článku použijte minimálně červenec 2017, verze 17,2. Dialogové okno nejaktuálnější připojení by mělo vypadat podobně jako na následujícím obrázku:

  ![Snímek obrazovky dialogového okna připojit k serveru v SQL Server Management Studio, v němž se zobrazí nastavení pro typ serveru, název serveru a ověřování.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Možnosti ověřování

Existují dva modely neinteraktivního ověřování pro Azure AD, které je možné použít v mnoha různých aplikacích (ADO.NET, JDCB, ODC atd.). Tyto dvě metody nikdy nevedou k místním dialogovým oknům:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Interaktivní metoda, která podporuje taky Multi-Factor Authentication Azure AD (MFA), je: 

- `Azure Active Directory - Universal with MFA`

Azure AD MFA pomáhá chránit přístup k datům a aplikacím a současně splňuje požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování s využitím široké škály možností jednoduchého ověřování (telefonní hovor, textová zpráva, čipové karty s PIN kódem nebo oznámení pro mobilní aplikace), které uživatelům umožňují vybrat si metodu, které dáváte přednost. Interaktivní vícefaktorové ověřování pomocí Azure AD může mít za následek automaticky otevírané okno k ověření.

Popis Multi-Factor Authentication služby Azure AD najdete v článku [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).
Postup konfigurace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parametr názvu domény Azure AD nebo ID tenanta

Počínaje [verzí SSMS 17](/sql/ssms/download-sql-server-management-studio-ssms)mohou uživatelé, kteří jsou importováni do aktuální služby Azure AD z jiných adresářů Azure Active Directory jako uživatelé typu Host, poskytovat název domény služby Azure AD nebo ID tenanta při připojení. Uživatelé typu Host zahrnují uživatele pozvaní z jiných reklam Azure, účtů Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty, jako je gmail.com. Tyto informace umožňují `Azure Active Directory - Universal with MFA` ověřování totožnosti správné ověřovací autority. Tato možnost je také nutná k podpoře účtů Microsoft (MSA), jako jsou účty outlook.com, hotmail.com, live.com nebo jiné než MSA. 

Všichni uživatelé typu Host, kteří chtějí být ověřeni pomocí univerzálního ověřování, musí zadat název domény nebo ID tenanta služby Azure AD. Tento parametr představuje aktuální název domény nebo ID tenanta služby Azure AD, ke kterému je přidružen logický Server Azure SQL. Pokud je třeba logický server SQL přidružený k doméně služby Azure AD `contosotest.onmicrosoft.com` , kde je uživatel `joe@contosodev.onmicrosoft.com` hostovaný jako importovaný uživatel z domény služby Azure AD `contosodev.onmicrosoft.com` , název domény, který je potřeba k ověření tohoto uživatele, je `contosotest.onmicrosoft.com` . Když je uživatel nativním uživatelem služby Azure AD, který je přidružený k logickému serveru SQL, a není to účet MSA, není potřeba žádný název domény ani ID tenanta. Zadání parametru (počínaje SSMS verze 17,2):


1. Otevřete připojení v SSMS. Zadejte název serveru a vyberte **Azure Active Directory – univerzální s** ověřováním MFA. Přidejte **uživatelské jméno** , se kterým se chcete přihlásit.
1. Vyberte pole **Možnosti** a přejděte na kartu **Vlastnosti připojení** . V dialogovém okně **připojit k databázi** vyplňte dialogové okno pro vaši databázi. Zaškrtněte pole **název domény služby AD nebo ID tenanta** a poskytněte ověřovací autoritu, jako je například název domény (**contosotest.onmicrosoft.com**) nebo identifikátor GUID ID tenanta. 

   ![Snímek obrazovky s kartou vlastností připojení zvýrazněním nastavení pro připojení k databázi a názvu domény služby AD nebo ID tenanta.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Pokud používáte SSMS 18. x nebo novější, název domény služby AD nebo ID tenanta už není potřeba pro uživatele typu Host, protože 18. x nebo novější ho automaticky rozpoznává.

   ![Snímek obrazovky karty Vlastnosti připojení v dialogovém okně připojit k serveru v S s M s. "MyDatabase" je vybrán v poli připojit k databázi.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Podpora Azure AD pro firmy

> [!IMPORTANT]
> Podpora pro uživatele typu Host pro připojení k Azure SQL Database, spravované instance SQL a Azure synapse bez nutnosti být součástí skupiny je aktuálně ve **verzi Public Preview**. Další informace najdete v tématech [Vytvoření uživatelů typu Host v Azure AD a nastavení jako správce Azure AD](authentication-aad-guest-users.md).

Uživatelé Azure AD, kteří jsou podporováni ve scénářích Azure AD B2B jako uživatelé typu Host (viz [co je spolupráce Azure B2B](../../active-directory/external-identities/what-is-b2b.md)) se mohou připojit k SQL Database a Azure synapse jenom jako součást členů skupiny vytvořené v přidružené službě Azure AD a ručně je namapovat pomocí příkazu [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) v dané databázi. Pokud `steve@gmail.com` je například Pozvánka do Azure AD `contosotest` (s doménou Azure AD `contosotest.onmicrosoft.com` ), `usergroup` musí být ve službě Azure AD, která obsahuje člena, vytvořená skupina Azure AD `steve@gmail.com` . Pak je potřeba tuto skupinu vytvořit pro konkrétní databázi (například `MyDatabase` ) pomocí správce Azure AD SQL nebo Azure AD dbo, a to spuštěním příkazu Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Po vytvoření uživatele databáze se uživatel `steve@gmail.com` může přihlásit `MyDatabase` pomocí možnosti ověřování SSMS `Azure Active Directory – Universal with MFA` . Ve výchozím nastavení `usergroup` má pouze oprávnění připojit. Každý další přístup k datům bude nutné [udělit](/sql/t-sql/statements/grant-transact-sql) v databázi uživatelem s dostatečným oprávněním. 

> [!NOTE]
> Pro SSMS 17. x, `steve@gmail.com` který používá jako uživatel typu Host, musíte zaškrtnout pole **název domény služby AD nebo ID tenanta** a přidat název domény služby AD `contosotest.onmicrosoft.com` do dialogového okna **Vlastnosti připojení** . Možnost **název domény AD nebo ID tenanta** je podporovaná jenom pro **Azure Active Directory-Universal s** ověřováním MFA. Jinak je zaškrtávací políčko šedé.

## <a name="universal-authentication-limitations"></a>Omezení univerzálního ověřování

- SSMS a SqlPackage.exe jsou jediné nástroje aktuálně povolené pro MFA prostřednictvím univerzálního ověřování služby Active Directory.
- SSMS verze 17,2 podporuje souběžný přístup více uživatelů s využitím univerzálního ověřování s MFA. Pro SSMS verze 17,0 a 17,1 nástroj omezuje přihlášení pro instanci SSMS s použitím univerzálního ověřování na jeden Azure Active Directory účet. Pokud se chcete přihlásit jako jiný účet služby Azure AD, musíte použít jinou instanci SSMS. Toto omezení je omezené na univerzální ověřování Active Directory; k jinému serveru se můžete přihlásit pomocí `Azure Active Directory - Password` ověřování, `Azure Active Directory - Integrated` ověřování nebo `SQL Server Authentication` .
- SSMS podporuje univerzální ověřování služby Active Directory pro Průzkumník objektů, Editor dotazů a vizualizaci úložiště dotazů.
- SSMS verze 17,2 poskytuje podporu Průvodce DacFx pro export/extrakci nebo nasazení dat databáze. Po ověření konkrétního uživatele prostřednictvím dialogu počátečního ověřování pomocí univerzálního ověřování funguje Průvodce DacFx stejným způsobem jako u všech ostatních metod ověřování.
- Návrhář tabulky SSMS nepodporuje univerzální ověřování.
- Pro univerzální ověřování služby Active Directory nejsou k dispozici žádné další požadavky na software, s výjimkou, že je nutné použít podporovanou verzi SSMS.  
- V následujícím odkazu najdete nejnovější verzi Active Directory Authentication Library (ADAL) pro univerzální ověřování: [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Další kroky

- Postup konfigurace najdete v tématu [konfigurace Azure SQL Database Multi-Factor Authentication pro SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Udělit ostatním přístup k vaší databázi: [SQL Database ověřování a autorizace: udělení přístupu](logins-create-manage.md)  
- Zajistěte, aby se uživatelé mohli připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru pomocí Azure Portal](firewall-configure.md)  
- [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database nebo Azure synapse](authentication-aad-configure.md)
- [Vytvoření uživatelů Azure AD typu host a nastavení jako správce Azure AD](authentication-aad-guest-users.md) 
- [Microsoft SQL Server aplikační Framework Data-Tier (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [Import souboru BACPAC do nové databáze](database-import.md)  
- [Export databáze do souboru BACPAC](database-export.md)  
- [Rozhraní IUniversalAuthProvider](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider) pro rozhraní C#  
- Při použití **Azure Active Directory – Universal s** ověřováním MFA je k dispozici trasování ADAL od [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Ve výchozím nastavení můžete trasování ADAL zapnout pomocí nabídky **nástroje**, **Možnosti** , v části **služby Azure**, **cloud Azure**, **ADAL okno výstup úroveň trasování** a následným povolením **výstupu**  v nabídce **zobrazení** . Trasování jsou k dispozici v okně výstup při výběru **možnosti Azure Active Directory**.