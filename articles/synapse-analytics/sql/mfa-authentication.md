---
title: Použití vícefaktorového ověřování AAD
description: Synapse SQL podporuje připojení ze služby SQL Server Management Studio (SSMS) pomocí univerzálního ověřování služby Active Directory.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 8e8f17f54f52aef6f552b7c211aa8f0e498b94df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424920"
---
# <a name="using-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Použití vícefaktorového ověřování AAD se službou Synapse SQL (podpora SSMS pro vícefaktorovou pomoc)

Synapse SQL podporuje připojení ze služby SQL Server Management Studio (SSMS) pomocí *univerzálního ověřování služby Active Directory*. 

Tento článek popisuje rozdíly mezi různými možnostmi ověřování a také omezení spojená s používáním univerzálního ověřování. 

**Stáhněte si nejnovější SSMS** - Na klientském počítači, stáhněte si nejnovější verzi SSMS, z [download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 

**Stáhněte si nejnovější SSMS** - Na klientském počítači, stáhněte si nejnovější verzi SSMS, z [download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Pro všechny funkce popsané v tomto článku použijte alespoň červenec 2017 verze 17.2.  Nejnovější dialogové okno připojení by mělo vypadat podobně jako na následujícím obrázku:

![1mfa-univerzální připojení](./media/mfa-authentication/1mfa-universal-connect.png "Dokončí pole Uživatelské jméno.")  

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

Popis vícefaktorového ověřování naleznete v tématu [Vícefaktorové ověřování](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Název domény Azure AD nebo parametr ID klienta

Počínaje [SSMS verze 17](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), uživatelé, které jsou importovány do aktuální služby Active Directory z jiných adresářů Azure Active jako uživatelé typu Host, můžete poskytnout název domény Azure AD nebo ID klienta při připojení. 

Mezi uživatele typu Host patří uživatelé pozvaní z jiných azure dispon, účtů Microsoft, jako jsou outlook.com, hotmail.com, live.com nebo jiné účty, jako jsou gmail.com. Tyto informace umožňují **službě Active Directory Universal s ověřováním více faktorech ověřování** identifikovat správné oprávnění. Tato možnost je také vyžadována pro podporu účtů Microsoft (MSA), jako jsou outlook.com, hotmail.com, live.com nebo jiné účty MSA. 

Všichni tito uživatelé, kteří chtějí být ověřeni pomocí univerzálního ověřování, musí zadat svůj název domény Azure AD nebo ID klienta. Tento parametr představuje aktuální ID domény azure ad nebo klienta, se kterým je Azure Server propojený. 

Například pokud Je přidružený k doméně `contosotest.onmicrosoft.com` Azure `joe@contosodev.onmicrosoft.com` AD, kde je uživatel hostován jako importovaný uživatel z domény `contosodev.onmicrosoft.com`Azure AD , název domény potřebné k ověření tohoto uživatele je `contosotest.onmicrosoft.com`. 

Pokud je uživatel nativním uživatelem Azure AD propojeného se serverem Azure A a není účtem MSA, není vyžadován žádný název domény ani ID klienta. 

Chcete-li zadat parametr (začínající ssms verze 17.2), vyplňte v dialogovém okně **Připojit k databázi** dialogové okno, vyberte **položku Active Directory – Universal s ověřováním více faktorem pro více faktorech,** klepněte na příkaz **Možnosti**, vyplňte pole **Uživatelské jméno** a klepněte na kartu **Vlastnosti připojení.** 

Zaškrtněte **políčko Název domény služby AD nebo ID klienta** a zadejte ověřovací autoritu, například název domény (**contosotest.onmicrosoft.com**) nebo identifikátor GUID ID klienta.  

   ![mfa-nájemce-ssms](./media/mfa-authentication/mfa-tenant-ssms.png)

Pokud používáte SSMS 18.x nebo novější, pak název domény služby AD nebo ID klienta již není potřeba pro uživatele typu Host, protože 18.x nebo novější automaticky rozpozná.

   ![mfa-nájemce-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Podpora azure a a business   
Uživatelé Azure AD podporovaní pro scénáře Azure AD B2B jako uživatelé typu Host (viz [Co je spolupráce Azure B2B](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) se můžete připojit k Synapse SQL jenom jako součást členů skupiny vytvořené v aktuální azure ad a mapované ručně pomocí příkazu Transact-SQL `CREATE USER` v dané databázi. 

Například pokud `steve@gmail.com` je pozván do `contosotest` Azure AD (s `contosotest.onmicrosoft.com`doménou Azure Ad), `usergroup` skupiny Azure AD, jako `steve@gmail.com` je třeba vytvořit ve službě Azure AD, která obsahuje člena. Potom tato skupina musí být vytvořena pro konkrétní databázi (to znamená MyDatabase) správcem Azure AD SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` nebo Azure AD DBO spuštěním příkazu Transact-SQL. 

Po vytvoření uživatele databáze se `steve@gmail.com` uživatel může `MyDatabase` přihlásit pomocí možnosti `Active Directory – Universal with MFA support`ověřování SSMS . 

Skupina uživatelů má ve výchozím nastavení pouze oprávnění k připojení a jakýkoli další přístup k datům, který bude nutné udělit normálním způsobem. 

Všimněte `steve@gmail.com` si, že uživatel jako uživatel typu Host `contosotest.onmicrosoft.com` musí zaškrtnout políčko a přidat název domény služby AD v dialogovém okně **Vlastnost připojení** SSMS. Možnost **Název domény služby AD nebo ID klienta** je podporována pouze pro možnosti připojení Univerzální s vícefaktorové připojení, jinak je zašedlá.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Omezení univerzálního ověřování pro synapse SQL

- SSMS a SqlPackage.exe jsou jedinými nástroji, které jsou aktuálně povoleny pro vícefaktorové ověřování prostřednictvím univerzálního ověřování služby Active Directory.
- SSMS verze 17.2, podporuje víceuživatelský souběžný přístup pomocí univerzálního ověřování pomocí vícefaktorové ověřování. Verze 17.0 a 17.1 omezila přihlášení pro instanci SSMS pomocí univerzálního ověřování na jeden účet služby Azure Active Directory. Chcete-li se přihlásit jako jiný účet Azure AD, musíte použít jinou instanci SSMS. (Toto omezení je omezeno na univerzální ověřování služby Active Directory; pomocí ověřování heslem služby Active Directory, integrovaného ověřování služby Active Directory nebo ověřování serveru SQL Server).
- Služba SSMS podporuje univerzální ověřování služby Active Directory pro Průzkumníka objektů, Editor dotazů a vizualizaci úložiště dotazů.
- SSMS verze 17.2 poskytuje dacfx průvodce podporu pro export / extrahovat / nasadit databázi dat. Jakmile je konkrétní uživatel ověřen pomocí dialogového okna počátečního ověřování pomocí univerzálního ověřování, Průvodce DacFx funguje stejně jako u všech ostatních metod ověřování.
- Návrhář tabulky SSMS nepodporuje univerzální ověřování.
- Pro univerzální ověřování služby Active Directory nejsou k dispozici žádné další požadavky na software, s výjimkou nutnosti použít podporovanou verzi služby SSMS.  
- Verze ADAL (ADAL) knihovny adutentizace služby Active Directory byla aktualizována na nejnovější dostupnou verzi adal.dll 3.13.9. Viz [Knihovna ověřování služby Active Directory 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Další kroky
Další informace naleznete v článku [Připojení k synapse SQL pomocí sql server management studio.](get-started-ssms.md) 

