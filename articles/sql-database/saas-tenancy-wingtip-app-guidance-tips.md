---
title: Pokyny pro příklad víceklientské aplikace SQL Database - Wingtip SaaS | Microsoft Docs
description: Poskytuje kroky a pokyny pro instalaci a spuštění aplikace víceklientské ukázka, která používá Azure SQL Database, příklad Wingtip lístky SaaS.
keywords: kurz k sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 1e3ecd27ce7ec2e2763a7249428875e51872bf63
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Obecné pokyny pro práci s Wingtip lístky ukázkové aplikace SaaS

Tento článek obsahuje obecné pokyny pro spouštění aplikací SaaS ukázkové Wingtip lístků, které používají Azure SQL Database. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Stáhněte si a odblokování skripty Wingtip lístky SaaS

Spustitelný soubor obsah (skripty, knihovny DLL) mohou být blokovány Windows, když jsou soubory zip stažené z externího zdroje a rozbalené. Při extrahování skripty ze souboru zip, **použijte následující postup, chcete-li odblokovat soubor .zip před extrahování**. Tím se zajistí, že je povoleno spustit skripty.

1. Přejděte do úložiště SaaS GitHub Wingtip lístky pro databáze klientů vzor, který chcete prozkoumat: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klikněte na tlačítko **klonovat nebo stáhnout**.
3. Klikněte na tlačítko **stáhnout zip** a soubor uložte.
4. Klikněte pravým tlačítkem na soubor zip a vyberte **vlastnosti**. Název úložiště bude odpovídat názvu souboru zip. (např. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na **Obecné** vyberte **Odblokovat**.
6. Klikněte na **OK**.
7. Extrahujte soubory.

Skripty, které jsou umístěné v *... \\Learning moduly* složky.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Práce s skripty prostředí PowerShell Wingtip lístky

Chcete-li maximální využití ukázku ponořte do zadané skripty. Použití zarážek a projděte skripty, protože spuštění a zkontrolujte, jak jsou implementované různé vzorce SaaS. Snadno procházet postupně zadaný skriptech a modulech pro nejlepší pochopení, doporučujeme používat [prostředí PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualizovat konfigurační soubor pro vaše nasazení

Upravit **UserConfig.psm1** soubor s hodnotou prostředků skupiny a uživatele, který nastavíte během nasazení:

1. Otevřete *prostředí PowerShell ISE* a zatížení... \\Učení moduly\\*UserConfig.psm1* 
2. Aktualizace *ResourceGroupName* a *název* s konkrétními hodnotami pro vaše nasazení (na řádky 10 a 11 pouze).
3. Uložte změny!

Tyto hodnoty nastavení tady jednoduše nebude nutné aktualizovat tyto hodnoty specifické pro nasazení v každé skriptu.

### <a name="execute-the-scripts-by-pressing-f5"></a>Spusťte skripty tak, že stisknete F5

Používat několik skriptů *$PSScriptRoot* k procházení složek, a *$PSScriptRoot* je Vyhodnocená jenom když jsou spouštět skripty, stisknutím klávesy **F5**.  Zvýraznění a spuštění výběr (**F8**) může vést k chybám, takže stiskněte **F5** při spouštění skriptů.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Procházení skriptů k vyzkoušení implementace

Nejlepší způsob, jak porozumět skripty je procházení je chcete zobrazit, co dělají. Podívejte se na zahrnutou **Demo -** skripty, které jsou k dispozici snadno postupujte podle pracovní postup vysoké úrovně. **Demo -** skripty zobrazit kroky potřebné k provedení každý úkol, takže nastavit zarážky a přejít k podrobnostem hlubší do jednotlivých volání zobrazíte podrobnosti implementace pro různé vzorce SaaS.

Tipy pro zkoumání a procházení skriptů prostředí PowerShell:

- Otevřete **Demo -** skripty v integrovaném Skriptovacím prostředí PowerShell.
- Spustit nebo pokračovat s **F5** (pomocí **F8** se nedoporučuje, protože *$PSScriptRoot* při spuštění výběr skriptu, nebude hodnocen).
- Pokud chcete umístit zarážky, klikněte na řádek nebo ho vyberte a stiskněte klávesu **F9**.
- Funkci nebo volání skriptu můžete vynechat stisknutím klávesy **F10**.
- Na funkci nebo volání skriptu můžete přejít stisknutím klávesy **F11**.
- Aktuální funkci nebo vlání skriptu můžete ukončit stisknutím kombinace **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Prozkoumání databázového schématu a spouštění dotazů SQL pomocí SSMS

Použití [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) k připojení a procházet aplikačních serverů a databází.

Nasazení má původně klienty a servery SQL Database katalogu pro připojení k. Názvy serverů závisí na vzoru klientů databáze (dole najdete podrobnosti.). 

   - **Samostatné aplikace:** servery pro každého klienta (např. *contosoconcerthall -&lt;uživatele&gt;*  serveru) a *katalogu-sa -&lt;uživatele&gt;*
   - **Databáze za klienta:** *tenants1-dpt -&lt;uživatele&gt;*  a *katalogu-dpt -&lt;uživatele&gt;*  servery
   - **Databáze víceklientské:** *tenants1-mt –&lt;uživatele&gt;*  a *katalogu-mt -&lt;uživatele&gt;*  servery

Aby se zajistilo úspěšné ukázku připojení, všechny servery mají [pravidlo brány firewall](sql-database-firewall-configure.md) povolení všechny IP adresy prostřednictvím.


1. Otevřete *SSMS* a připojte se ke klientům. Název serveru, závisí na vzoru databáze klientů, jste vybrali (dole najdete podrobnosti):
    - **Samostatné aplikace:** servery jednotlivých klientů (např. *contosoconcerthall-&lt;User&gt;.database.windows.net*) 
    - **Databáze za klienta:** *tenants1-dpt -&lt;uživatele&gt;. database.windows.net*
    - **Multi-tenant database:** *tenants1-mt-&lt;User&gt;.database.windows.net* 
2. Klikněte na **Připojit** > **Databázový stroj...**:

   ![katalogový server](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Přihlašovací údaje pro ukázku jsou: Přihlašovací jméno = *developer*, Heslo = *P@ssword1*

    Následující obrázek ukazuje přihlášení pro *databáze za klienta* vzor. 
    ![Připojení](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Opakujte kroky 2 až 3 a připojte se k serveru katalogu (dole najdete názvy konkrétních serverů na základě vzoru pro databáze klientů vybrané)
    - **Samostatné aplikace:** *katalogu-sa -&lt;uživatele&gt;. database.windows.net*
    - **Databáze za klienta:** *katalogu-dpt -&lt;uživatele&gt;. database.windows.net*
    - **Multi-tenant database:** *catalog-mt-&lt;User&gt;.database.windows.net*


Po úspěšném připojení by se zobrazit všechny servery. Seznam databází se může lišit v závislosti na klienty, kterou jste zřídili.

Následující obrázek ukazuje přihlášení pro *databáze za klienta* vzor.

![průzkumník objektů](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Další postup
- [Nasazení aplikace SaaS samostatné Wingtip lístky](saas-standaloneapp-get-started-deploy.md)
- [Nasazení databáze SaaS lístky Wingtip na aplikaci klienta](saas-dbpertenant-get-started-deploy.md)
- [Nasazení aplikace Wingtip lístky SaaS víceklientské databáze](saas-multitenantdb-get-started-deploy.md)

