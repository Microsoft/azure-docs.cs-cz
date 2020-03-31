---
title: Použití vícefaktorového ověřování AAD
description: Azure SQL Database a Azure Synapse podporují připojení ze služby SQL Server Management Studio (SSMS) pomocí univerzálního ověřování služby Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255870"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>Použití vícefaktorového ověřování AAD s Azure SQL Database a Azure Synapse Analytics (podpora SSMS pro vícefaktorovou pomoc)
Azure SQL Database a Azure Synapse podporují připojení ze služby SQL Server Management Studio (SSMS) pomocí *univerzálního ověřování služby Active Directory*. Tento článek popisuje rozdíly mezi různými možnostmi ověřování a také omezení spojená s používáním univerzálního ověřování. 

**Stáhněte si nejnovější SSMS** - Na klientském počítači, stáhněte si nejnovější verzi SSMS, z [download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Pro všechny funkce popsané v tomto článku použijte alespoň červenec 2017 verze 17.2.  Nejnovější dialogové okno připojení by mělo vypadat podobně jako na následujícím obrázku:
 
  ![1mfa-univerzální připojení](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Dokončí pole Uživatelské jméno.")  

## <a name="the-five-authentication-options"></a>Pět možností ověřování  

Univerzální ověřování služby Active Directory podporuje dvě neinteraktivní metody ověřování:
    - `Active Directory - Password`Ověřování
    - `Active Directory - Integrated`Ověřování

Existují také dva neinteraktivní modely ověřování, které lze použít v mnoha různých aplikacích (ADO.NET, JDCB, ODC atd.). Výsledkem těchto dvou metod nikdy nebudou automaticky otevíraná dialogová okna: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Interaktivní metoda je, že podporuje také Azure vícefaktorové ověřování (MFA) je: 
- `Active Directory - Universal with MFA` 


Azure MFA pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování. Poskytuje silné ověřování s řadou snadných možností ověření (telefonní hovor, textová zpráva, čipové karty s pinem nebo oznámení mobilní aplikace), což uživatelům umožňuje zvolit metodu, kterou preferují. Interaktivní vícefaktorové ověřování s Azure AD může mít za následek rozbalovací dialogové okno pro ověření.

Popis vícefaktorového ověřování naleznete v tématu [Vícefaktorové ověřování](../active-directory/authentication/multi-factor-authentication.md).
Kroky konfigurace najdete [v tématu Konfigurace vícefaktorového ověřování Azure SQL Database pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Název domény Azure AD nebo parametr ID klienta   

Počínaje [SSMS verze 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), uživatelé, které jsou importovány do aktuální služby Active Directory z jiných adresářů Azure Active jako uživatelé typu Host, můžete poskytnout název domény Azure AD nebo ID klienta při připojení. Mezi uživatele typu Host patří uživatelé pozvaní z jiných azure dispon, účtů Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty, jako jsou gmail.com. Tyto informace umožňují **službě Active Directory Universal s ověřováním více faktorech ověřování** identifikovat správné oprávnění. Tato možnost je také vyžadována pro podporu účtů Microsoft (MSA), jako jsou outlook.com, hotmail.com, live.com nebo jiné účty MSA. Všichni tito uživatelé, kteří chtějí být ověřeni pomocí univerzálního ověřování, musí zadat svůj název domény Azure AD nebo ID klienta. Tento parametr představuje aktuální ID domény azure ad nebo klienta, se kterým je Azure Server propojený. Například pokud Je přidružený k doméně `contosotest.onmicrosoft.com` Azure `joe@contosodev.onmicrosoft.com` AD, kde je uživatel hostován jako importovaný uživatel z domény `contosodev.onmicrosoft.com`Azure AD , název domény potřebné k ověření tohoto uživatele je `contosotest.onmicrosoft.com`. Pokud je uživatel nativním uživatelem Azure AD propojeného se serverem Azure A a není účtem MSA, není vyžadován žádný název domény ani ID klienta. Chcete-li zadat parametr (začínající sms verze 17.2), vyplňte v dialogovém okně Připojit k **databázi** dialogové okno, vyberte **položku Active Directory – Univerzální s ověřováním vícefaktorových informací,** klikněte na **možnosti**, vyplňte pole **Uživatelské jméno** a potom klikněte na kartu **Vlastnosti připojení** **contosotest.onmicrosoft.com.** **AD domain name or tenant ID**  
   ![mfa-nájemce-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Pokud používáte SSMS 18.x nebo novější, pak název domény služby AD nebo ID klienta již není potřeba pro uživatele typu Host, protože 18.x nebo novější automaticky rozpozná.

   ![mfa-nájemce-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Podpora azure a a business   
Uživatelé Azure AD podporovaní pro scénáře Azure AD B2B jako uživatelé typu Host (viz [Co je spolupráce Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) se můžou připojit k SQL `CREATE USER` Database a Azure Synapse jenom jako součást členů skupiny vytvořené v aktuálním Azure AD a mapované ručně pomocí příkazu Transact-SQL v dané databázi. Například pokud `steve@gmail.com` je pozván do `contosotest` Azure AD (s `contosotest.onmicrosoft.com`doménou Azure Ad), `usergroup` skupiny Azure AD, jako `steve@gmail.com` je třeba vytvořit ve službě Azure AD, která obsahuje člena. Potom tato skupina musí být vytvořena pro konkrétní databázi (to znamená MyDatabase) správcem Azure AD SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` nebo Azure AD DBO spuštěním příkazu Transact-SQL. Po vytvoření uživatele databáze se `steve@gmail.com` uživatel může `MyDatabase` přihlásit pomocí možnosti `Active Directory – Universal with MFA support`ověřování SSMS . Skupina uživatelů má ve výchozím nastavení pouze oprávnění k připojení a jakýkoli další přístup k datům, který bude nutné udělit normálním způsobem. Všimněte `steve@gmail.com` si, že uživatel jako uživatel typu Host `contosotest.onmicrosoft.com` musí zaškrtnout políčko a přidat název domény služby AD v dialogovém okně **Vlastnost připojení** SSMS. Možnost **Název domény služby AD nebo ID klienta** je podporována pouze pro možnosti připojení Univerzální s vícefaktorové připojení, jinak je zašedlá.

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>Omezení univerzálního ověřování pro databázi SQL a Azure Synapse
- SSMS a SqlPackage.exe jsou jedinými nástroji, které jsou aktuálně povoleny pro vícefaktorové ověřování prostřednictvím univerzálního ověřování služby Active Directory.
- SSMS verze 17.2, podporuje víceuživatelský souběžný přístup pomocí univerzálního ověřování pomocí vícefaktorové ověřování. Verze 17.0 a 17.1 omezila přihlášení pro instanci SSMS pomocí univerzálního ověřování na jeden účet služby Azure Active Directory. Chcete-li se přihlásit jako jiný účet Azure AD, musíte použít jinou instanci SSMS. (Toto omezení je omezeno na univerzální ověřování služby Active Directory; pomocí ověřování heslem služby Active Directory, integrovaného ověřování služby Active Directory nebo ověřování serveru SQL Server).
- Služba SSMS podporuje univerzální ověřování služby Active Directory pro Průzkumníka objektů, Editor dotazů a vizualizaci úložiště dotazů.
- SSMS verze 17.2 poskytuje dacfx průvodce podporu pro export / extrahovat / nasadit databázi dat. Jakmile je konkrétní uživatel ověřen pomocí dialogového okna počátečního ověřování pomocí univerzálního ověřování, Průvodce DacFx funguje stejně jako u všech ostatních metod ověřování.
- Návrhář tabulky SSMS nepodporuje univerzální ověřování.
- Pro univerzální ověřování služby Active Directory nejsou k dispozici žádné další požadavky na software, s výjimkou nutnosti použít podporovanou verzi služby SSMS.  
- Verze ADAL (ADAL) knihovny adutentizace služby Active Directory byla aktualizována na nejnovější dostupnou verzi adal.dll 3.13.9. Viz [Knihovna ověřování služby Active Directory 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Další kroky

- Kroky konfigurace najdete [v tématu Konfigurace vícefaktorového ověřování Azure SQL Database pro SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Udělení přístupu k databázi ostatním uživatelům: [Ověřování a autorizace databáze SQL: Udělení přístupu](sql-database-manage-logins.md)  
- Ujistěte se, že se ostatní můžou připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí portálu Azure](sql-database-configure-firewall-settings.md)  
- [Konfigurace a správa ověřování Azure Active Directory pomocí databáze SQL nebo Azure Synapse](sql-database-aad-authentication-configure.md)  
- [Aplikační rozhraní datové vrstvy serveru Microsoft SQL Server (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SOUBOR SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Import souboru BACPAC do nové databáze Azure SQL](../sql-database/sql-database-import.md)  
- [Export databáze Azure SQL do souboru BACPAC](../sql-database/sql-database-export.md)  
- [Rozhraní C# IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Při použití **služby Active Directory- Universal s ověřováním Vícefaktorové** ověřování je k dispozici trasování ADAL začínající s [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ve výchozím nastavení můžete zapnout trasování ADAL pomocí nabídky **Nástroje**, **Možnosti** v části **Azure Services**, **Azure Cloud**, **ADAL Output Window Trace Level**, následované povolením **výstupu** v nabídce **Zobrazení.** Trasování jsou k dispozici ve výstupním okně při výběru **možnosti služby Azure Active Directory**.  
