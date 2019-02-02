---
title: Pokyny pro SQL Database aplikace s více tenanty příklad – SaaS aplikace Wingtip | Dokumentace Microsoftu
description: Poskytuje kroky a pokyny pro instalaci a spuštění ukázkové aplikace více tenanty využívající Azure SQL Database, SaaS aplikace Wingtip Tickets příklad.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: af0e642a1f8ec34e654295ae7dcf2960c8664d91
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565560"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Obecné pokyny pro práci s Wingtip Tickets ukázkové SaaS aplikace

Tento článek obsahuje obecné pokyny pro spuštění ukázkové SaaS aplikace Wingtip Tickets, využívající Azure SQL Database. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Stáhněte si a odblokování SaaS aplikace Wingtip Tickets skriptů

Windows může blokovat spustitelný obsah (skripty, knihovny DLL) při stahování z externího zdroje a extrahovat soubory zip. Při extrahování skripty ze souboru zip **postupujte následovně chcete odblokovat soubor .zip před extrahováním**. Tím se zajistí, že je povolené spouštět skripty.

1. Přejděte do úložiště SaaS GitHub aplikace Wingtip Tickets pro vzor databáze tenantů, kterou chcete prozkoumat: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klikněte na tlačítko **klonovat nebo stáhnout**.
3. Klikněte na tlačítko **stáhnout zip** a soubor uložte.
4. Klikněte pravým tlačítkem na soubor zip a vyberte **vlastnosti**. Název souboru zip bude odpovídat názvu úložiště. (např.) _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na **Obecné** kartu, vyberte možnost **Odblokovat**.
6. Klikněte na **OK**.
7. Extrahujte soubory.

Skripty jsou umístěné v *... \\Learning Modules* složky.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Práce se skripty Powershellu Wingtip Tickets

Chcete-li získat co nejlépe ukázku, kterou je potřeba věnovat prozkoumání poskytnutých skriptů. Použití zarážek a procházení skriptů tak, jak spustit a prozkoumat, jak se implementují různé vzorce SaaS. Chcete-li snadno si poskytnuté skripty a moduly pro nejlepší pochopení, doporučujeme použít [prostředí PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualizovat konfigurační soubor nasazení

Upravit **UserConfig.psm1** hodnotou prostředku skupiny a uživatele, které jste nastavili při nasazování souboru:

1. Otevřít *prostředí PowerShell ISE* a zatížení... \\Learning Modules\\*UserConfig.psm1* 
2. Aktualizace *ResourceGroupName* a *název* s konkrétní hodnoty pro vaše nasazení (na řádcích 10 a 11 pouze).
3. Uložte změny.

Nastavení tyto hodnoty tady jednoduše umožňuje nebudou muset aktualizovat tyto hodnoty specifické pro nasazení ve všech skriptech.

### <a name="execute-the-scripts-by-pressing-f5"></a>Spuštění skriptů stisknutím klávesy F5

Řada skriptů používá *$PSScriptRoot* k procházení složek, a *$PSScriptRoot* je vyhodnocen pouze při spuštění skriptů stisknutím klávesy **F5**.  Zvýraznění a spuštění výběru (**F8**) může vést k chybám, takže stiskněte **F5** při spouštění skriptů.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Procházení skriptů k vyzkoušení implementace

Nejlepší způsob, jak pochopit skripty je krokování zjistíte, co dělají. Podívejte se na zahrnutou **Demo -** skripty, které se snadno sledovat pracovní postup vysoké úrovně. **Demo -** skriptech kroky potřebné k dosažení jednotlivých úkolů, takže nastavte zarážky a přejít hlouběji do jednotlivých volání zobrazíte podrobnosti o implementaci pro různé vzorce SaaS.

Tipy pro zkoumání a krokování prostřednictvím skriptů prostředí PowerShell:

- Otevřít **Demo -** skriptů v prostředí ISE Powershellu.
- Proveďte skript nebo pokračujte s **F5** (pomocí **F8** se nedoporučuje, protože *$PSScriptRoot* při spuštění výběrů skriptů).
- Pokud chcete umístit zarážky, klikněte na řádek nebo ho vyberte a stiskněte klávesu **F9**.
- Funkci nebo volání skriptu můžete vynechat stisknutím klávesy **F10**.
- Na funkci nebo volání skriptu můžete přejít stisknutím klávesy **F11**.
- Aktuální funkci nebo vlání skriptu můžete ukončit stisknutím kombinace **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Prozkoumání databázového schématu a spouštění dotazů SQL pomocí SSMS

Použití [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) k připojení a procházení aplikačních serverů a databází.

Nasazení má na začátku klienty a servery SQL Database katalogu pro připojení k. Názvy serverů, závisí na vzor databáze tenantů (viz níže pro konkrétní). 

   - **Samostatná aplikace:** servery pro každého tenanta (např.) *contosoconcerthall -&lt;uživatele&gt;*  server) a *katalogu-sa -&lt;uživatele&gt;*
   - **Databáze na tenanta:** *tenants1-dpt -&lt;uživatele&gt;*  a *katalogu-dpt -&lt;uživatele&gt;*  servery
   - **Databázi s více tenanty:** *tenants1-mt –&lt;uživatele&gt;*  a *katalogu-mt –&lt;uživatele&gt;*  servery

Aby se zajistilo úspěšné ukázkové připojení, mají všechny servery [pravidlo brány firewall](sql-database-firewall-configure.md) povolení všech IP adres prostřednictvím.


1. Otevřít *SSMS* a připojení ke klientům. Název serveru, závisí na databázi tenantů vzor vámi zvolená (viz níže pro konkrétní):
    - **Samostatná aplikace:** servery jednotlivých tenantů (např.) *contosoconcerthall-&lt;User&gt;.database.windows.net*) 
    - **Databáze na tenanta:** *tenants1-dpt -&lt;uživatele&gt;. database.windows.net*
    - **Databázi s více tenanty:** *tenants1-mt –&lt;uživatele&gt;. database.windows.net* 
2. Klikněte na **Připojit** > **Databázový stroj...**:

   ![katalogový server](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Přihlašovací údaje pro ukázku jsou: Přihlašovací jméno = *developer*, heslo = *P@ssword1*

    Následující obrázek ukazuje přihlášení pro *databáze na tenanta* vzor. 
    ![připojení](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Opakujte kroky 2 až 3 a připojte se k serveru katalogu (viz níže pro konkrétní server názvy založena na vzoru databáze tenantů vybrané)
    - **Samostatná aplikace:** *katalogu-sa -&lt;uživatele&gt;. database.windows.net*
    - **Databáze na tenanta:** *katalogu-dpt -&lt;uživatele&gt;. database.windows.net*
    - **Databázi s více tenanty:** *katalogu-mt –&lt;uživatele&gt;. database.windows.net*


Po úspěšném připojení byste měli vidět všechny servery. Seznam databází se může lišit v závislosti na klienty, kterou jste zřídili.

Následující obrázek ukazuje přihlášení pro *databáze na tenanta* vzor.

![průzkumník objektů](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Další postup
- [Nasazení samostatného SaaS aplikaci Wingtip Tickets](saas-standaloneapp-get-started-deploy.md)
- [Nasazení databáze Wingtip Tickets SaaS na Tenanta aplikaci](saas-dbpertenant-get-started-deploy.md)
- [Nasazení aplikace Wingtip Tickets SaaS víceklientskou databází](saas-multitenantdb-get-started-deploy.md)

