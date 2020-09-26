---
title: Použití vícefaktorového ověřování AAD
description: Synapse připojení podpory SQL z SQL Server Management Studio (SSMS) pomocí univerzálního ověřování služby Active Directory.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: has-adal-ref
ms.openlocfilehash: 52964c906df7a96685c8eb80c1d23ef9a9efc6e1
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361836"
---
# <a name="use-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Použití vícefaktorového ověřování AAD s synapse SQL (podpora SSMS pro MFA)

Synapse připojení podpory SQL z SQL Server Management Studio (SSMS) pomocí *univerzálního ověřování služby Active Directory*. 

Tento článek popisuje rozdíly mezi různými možnostmi ověřování a také omezeními souvisejícími s používáním univerzálního ověřování. 

**Stáhněte si nejnovější SSMS** -v klientském počítači, Stáhněte si nejnovější verzi nástroje SSMS, od [Stažení SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Pro všechny funkce popsané v tomto článku použijte minimálně červenec 2017, verze 17,2.  Dialogové okno nejaktuálnější připojení by mělo vypadat podobně jako na následujícím obrázku:

![Snímek obrazovky se zobrazí dialogové okno připojit k serveru, kde můžete vybrat název serveru a možnost ověřování.](./media/mfa-authentication/1mfa-universal-connect.png "Dokončí pole uživatelské jméno.")  

## <a name="the-five-authentication-options"></a>Pět možností ověřování  

Univerzální ověřování služby Active Directory podporuje dvě metody ověřování, které nejsou interaktivní:
    - `Active Directory - Password` přihlašovací
    - `Active Directory - Integrated` přihlašovací

K dispozici jsou také dva modely neinteraktivního ověřování, které lze použít v mnoha různých aplikacích (ADO.NET, JDCB, ODC atd.). Tyto dvě metody nikdy nevedou k místním dialogovým oknům:

- `Active Directory - Password`
- `Active Directory - Integrated`

Interaktivní metoda je, že také podporuje službu Azure Multi-Factor Authentication (MFA):

- `Active Directory - Universal with MFA`

Azure MFA pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování s využitím široké škály možností jednoduchého ověřování (telefonní hovor, textová zpráva, čipové karty s PIN kódem nebo oznámení pro mobilní aplikace), které uživatelům umožňují vybrat si metodu, které dáváte přednost. Interaktivní vícefaktorové ověřování pomocí Azure AD může mít za následek automaticky otevírané okno k ověření.

Popis Multi-Factor Authentication najdete v tématu [Multi-Factor Authentication](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parametr názvu domény Azure AD nebo ID tenanta

Počínaje [verzí SSMS 17](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)mohou uživatelé, kteří jsou importováni do aktuální služby Active Directory z jiných Azure Active Directory jako uživatelé typu Host, poskytovat název domény služby Azure AD nebo ID tenanta při připojení. 

Uživatelé typu Host zahrnují uživatele pozvaní z jiných reklam Azure, účtů Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty, jako je gmail.com. Tyto informace umožňují, aby **Služba Active Directory Universal s ověřováním MFA** identifikovala správnou ověřovací autoritu. Tato možnost je také nutná k podpoře účtů Microsoft (MSA), jako jsou účty outlook.com, hotmail.com, live.com nebo jiné než MSA. 

Všichni uživatelé, kteří chtějí být ověřeni pomocí univerzálního ověřování, musí zadat svůj název domény nebo ID tenanta služby Azure AD. Tento parametr představuje aktuální název domény nebo ID tenanta Azure AD, se kterým je server Azure propojený. 

Pokud je například Azure Server přidružený k doméně Azure AD `contosotest.onmicrosoft.com` , kde je uživatel `joe@contosodev.onmicrosoft.com` hostovaný jako importovaný uživatel z domény služby Azure AD `contosodev.onmicrosoft.com` , název domény, který je potřeba k ověření tohoto uživatele, je `contosotest.onmicrosoft.com` . 

Když je uživatel nativním uživatelem služby Azure AD připojeným k serveru Azure a nejedná se o účet MSA, není nutné mít žádný název domény ani ID tenanta. 

Chcete-li zadat parametr (počínaje SSMS verze 17,2), v dialogovém okně **připojit k databázi** dokončete dialogové okno, vyberte možnost **Active Directory – univerzální s** ověřováním MFA, vyberte **Možnosti**, dokončete pole **uživatelské jméno** a pak vyberte kartu **Vlastnosti připojení** . 

Zaškrtněte pole **název domény služby AD nebo ID tenanta** a poskytněte ověřovací autoritu, jako je například název domény (**contosotest.onmicrosoft.com**) nebo identifikátor GUID ID tenanta.  

   ![Snímek obrazovky ukazuje připojit k serveru na kartě Vlastnosti připojení s zadanými hodnotami.](./media/mfa-authentication/mfa-tenant-ssms.png)

Pokud používáte SSMS 18. x nebo novější, pak název domény služby AD nebo ID tenanta už není potřeba pro uživatele typu Host, protože 18. x nebo novější ho automaticky rozpoznává.

   ![MFA – tenant – SSMS](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Podpora Azure AD pro firmy   
Uživatelé Azure AD podporovaní ve scénářích Azure AD B2B jako uživatelé typu Host (viz téma [co je spolupráce Azure B2B se](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) může připojit k synapse SQL pouze v rámci členů skupiny vytvořené v aktuální službě Azure AD a ručně namapovat pomocí příkazu Transact-SQL `CREATE USER` v dané databázi). 

Pokud `steve@gmail.com` je například Pozvánka do Azure AD `contosotest` (s doménou Azure AD `contosotest.onmicrosoft.com` ), `usergroup` musí být ve službě Azure AD, která obsahuje člena, vytvořená skupina Azure AD `steve@gmail.com` . Pak je potřeba tuto skupinu vytvořit pro konkrétní databázi (tj. MyDatabase) pomocí služby Azure AD SQL admin nebo Azure AD DBO spuštěním příkazu jazyka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Po vytvoření uživatele databáze se uživatel `steve@gmail.com` může přihlásit k `MyDatabase` používání možnosti ověřování SSMS `Active Directory – Universal with MFA support` . 

Ve výchozím nastavení má uživatel ve výchozím nastavení pouze oprávnění připojit a jakýkoliv další přístup k datům, který bude potřeba udělit běžným způsobem. 

Jako uživatel typu host `steve@gmail.com` musí zaškrtnout políčko a přidat název domény služby Active Directory `contosotest.onmicrosoft.com` do dialogového okna **Vlastnosti připojení** SSMS. Možnost **název domény AD nebo ID tenanta** je podporovaná jenom pro možnosti připojení univerzální s MFA, jinak je šedá.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Omezení univerzálního ověřování pro synapse SQL

- SSMS a SqlPackage.exe jsou jediné nástroje aktuálně povolené pro MFA prostřednictvím univerzálního ověřování služby Active Directory.
- SSMS verze 17,2 podporuje souběžný přístup více uživatelů s využitím univerzálního ověřování s MFA. Verze 17,0 a 17,1 omezuje přihlášení pro instanci SSMS pomocí univerzálního ověřování na jeden Azure Active Directory účet. Pokud se chcete přihlásit jako jiný účet služby Azure AD, musíte použít jinou instanci SSMS. (Toto omezení je omezené na univerzální ověřování Active Directory. můžete se přihlásit k různým serverům pomocí ověřování hesla služby Active Directory, integrovaného ověřování Active Directory nebo SQL Server ověřování).
- SSMS podporuje univerzální ověřování služby Active Directory pro Průzkumník objektů, Editor dotazů a vizualizaci úložiště dotazů.
- SSMS verze 17,2 poskytuje podporu Průvodce DacFx pro export/extrakci nebo nasazení dat databáze. Po ověření konkrétního uživatele prostřednictvím dialogu počátečního ověřování pomocí univerzálního ověřování funguje Průvodce DacFx stejným způsobem jako u všech ostatních metod ověřování.
- Návrhář tabulky SSMS nepodporuje univerzální ověřování.
- Pro univerzální ověřování služby Active Directory nejsou k dispozici žádné další požadavky na software, s výjimkou, že je nutné použít podporovanou verzi SSMS.  
- Verze Active Directory Authentication Library (ADAL) pro univerzální ověřování byla aktualizována na nejnovější vydanou verzi ADAL.dll 3.13.9. Viz [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Další kroky
Další informace najdete v článku věnovaném [připojení k synapse SQL s SQL Server Management Studio](get-started-ssms.md) . 

