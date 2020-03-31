---
title: Příklad aplikace pro více klientů – Wingtip SaaS
description: Obsahuje kroky a pokyny pro instalaci a spuštění ukázkové víceklientské aplikace, která používá Azure SQL Database, příklad Wingtip Tickets SaaS.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132300"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Obecné pokyny pro práci s Wingtip Tickets ukázkové Aplikace SaaS

Tento článek obsahuje obecné pokyny pro spuštění wingtip lístky ukázkové aplikace SaaS, které používají Azure SQL Database.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Stáhněte a odblokujte skripty Wingtip Tickets SaaS

Spustitelný obsah (skripty, dll) může být blokován systémem Windows při stahování souborů zip z externího zdroje a extrahování. Při extrahování skriptů ze souboru zip **odblokujte soubor ZIP před extrahováním podle následujících kroků.** Tím je zajištěno, že skripty mohou být spuštěny.

1. Přejděte na úložiště Wingtip Tickets SaaS GitHub pro vzor pronájmu databáze, který chcete prozkoumat:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klepněte na **možnost Klonovat nebo stáhnout**.
3. Klikněte na **Stáhnout zip** a uložte soubor.
4. Klepněte pravým tlačítkem myši na soubor zip a vyberte **příkaz Vlastnosti**. Název souboru zip bude odpovídat názvu repo. (např. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na kartě **Obecné** vyberte **Odblokovat**.
6. Klikněte na tlačítko **OK**.
7. Extrahujte soubory.

Skripty jsou umístěny v *.. Složka \\Výukové moduly.*


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Práce se skripty Wingtip Tickets PowerShell

Chcete-li získat co nejvíce z ukázky, musíte se ponořit do poskytovaných skriptů. Použijte zarážky a krokovat skripty při jejich spuštění a zkontrolujte, jak jsou implementovány různé vzory SaaS. Chcete-li snadno procházet poskytované skripty a moduly pro nejlepší pochopení, doporučujeme použít [prostředí PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualizace konfiguračního souboru pro nasazení

Upravte soubor **UserConfig.psm1** se skupinou prostředků a hodnotou uživatele, kterou jste nastavili během nasazení:

1. Otevřete *Prostředí PowerShell ISE* a načtěte ... \\Výukové moduly\\*UserConfig.psm1*
2. Aktualizujte *název_skupiny ResourceGroupName* a *Name* pomocí konkrétních hodnot pro nasazení (pouze na řádcích 10 a 11).
3. Uložit změny!

Nastavení těchto hodnot zde jednoduše zabrání nutnosti aktualizovat tyto hodnoty specifické pro nasazení v každém skriptu.

### <a name="execute-the-scripts-by-pressing-f5"></a>Spusťte skripty stisknutím klávesy F5

Několik skriptů používá *$PSScriptRoot* k navigaci ve složkách a *$PSScriptRoot* je vyhodnocováno pouze při spuštění skriptů stisknutím klávesy **F5**.Zvýraznění a spuštění výběru **(F8)** může mít za následek chyby, takže stiskněte **klávesu F5** při spouštění skriptů.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Procházení skriptů k vyzkoušení implementace

Nejlepší způsob, jak pochopit skripty, je krokování přes ně vidět, co dělají. Podívejte se na **přiložené ukázkové** skripty, které představují snadno sledovatelný pracovní postup na vysoké úrovni. **Ukázkové** skripty zobrazit kroky potřebné k provedení každého úkolu, takže nastavit zarážky a procházet hlouběji do jednotlivých volání zobrazit podrobnosti implementace pro různé vzory SaaS.

Tipy pro zkoumání a procházení skriptů Prostředí PowerShell:

- Otevřete **ukázkové** skripty v prostředí PowerShell ISE.
- Spustit nebo pokračovat s **F5** (pomocí **F8** se *nedoporučuje,* protože $PSScriptRoot není vyhodnocenpři spuštění výběru skriptu).
- Pokud chcete umístit zarážky, klikněte na řádek nebo ho vyberte a stiskněte klávesu **F9**.
- Funkci nebo volání skriptu můžete vynechat stisknutím klávesy **F10**.
- Na funkci nebo volání skriptu můžete přejít stisknutím klávesy **F11**.
- Aktuální funkci nebo vlání skriptu můžete ukončit stisknutím kombinace **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Prozkoumání databázového schématu a spouštění dotazů SQL pomocí SSMS

Pomocí [aplikace SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) můžete připojit a procházet aplikační servery a databáze.

Nasazení má zpočátku klienty a katalog sql database servery pro připojení. Pojmenování serverů závisí na vzor ování databáze (viz níže podrobnosti).

   - **Samostatná aplikace:** servery pro každého klienta (např. *contosoconcerthall-&lt;&gt; Uživatelský* server) a *katalog-sa-&lt;Uživatel&gt; *
   - **Databáze na klienta:** *tenants1-dpt-&lt;Uživatelské&gt; * a *katalogové-dpt-&lt;Uživatelské&gt; * servery
   - **Databáze s více klienty:** *tenants1-mt-&lt;User&gt; * a *catalog-mt-&lt;User&gt; * servers

Pro zajištění úspěšného demo připojení mají všechny servery [pravidlo brány firewall,](sql-database-firewall-configure.md) které umožňuje všechny IP adresy.


1. Otevřete *SSMS* a připojte se k klientům. Název serveru závisí na vybraném vzoru pronájmu databáze (podrobnosti viz níže):
    - **Samostatná aplikace:** servery jednotlivých klientů (např. *contosoconcerthall-&lt;&gt;Uživatel (database.windows.net)*
    - **Databáze na klienta:** *tenants1-dpt-&lt;Uživatel&gt;.database.windows.net*
    - **Víceklientská databáze:** *tenants1-mt-&lt;User&gt;.database.windows.net*
2. Klepněte na tlačítko **Připojit** > **databázový stroj...**:

   ![katalogový server](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Demo pověření jsou: Přihlášení = *vývojář*, Heslo = *P\@ssword1*

    Obrázek níže ukazuje přihlášení pro *databázi na vzorek klienta.*
    ![Připojení](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Opakujte kroky 2-3 a připojte se k serveru katalogu (viz níže konkrétní názvy serverů na základě vybraného vzoru pronájmu databáze)
    - **Samostatná aplikace:** *catalog-sa-&lt;User&gt;.database.windows.net*
    - **Databáze na klienta:** *catalog-dpt-&lt;User&gt;.database.windows.net*
    - **Víceklientská databáze:** *catalog-mt-&lt;User&gt;.database.windows.net*


Po úspěšném připojení byste měli vidět všechny servery. Seznam databází se může lišit v závislosti na klientech, které jste zříditi.

Obrázek níže ukazuje přihlášení pro *databázi na vzorek klienta.*

![průzkumník objektů](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Další kroky
- [Nasazení samostatné aplikace Wingtip Tickets SaaS](saas-standaloneapp-get-started-deploy.md)
- [Nasazení wingtip tickets SaaS database na aplikaci klienta](saas-dbpertenant-get-started-deploy.md)
- [Nasazení aplikace Wingtip Tickets SaaS Multi-tenant Database](saas-multitenantdb-get-started-deploy.md)

