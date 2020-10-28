---
title: Příklad aplikace pro více tenantů – SaaS Wingtip
description: Obsahuje kroky a pokyny k instalaci a spuštění ukázkové aplikace s více klienty, která používá Azure SQL Database, jako příklad SaaS lístky Wingtip.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: aa9215fa001fb117000eb6a68867ddd46fac9b92
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780321"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Obecné pokyny pro práci s ukázkami lístků Wingtip SaaS Apps
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek obsahuje obecné pokyny pro spouštění ukázkových lístků Wingtip SaaS, které používají Azure SQL Database.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Stažení a odblokování skriptů SaaS lístků Wingtip

Obsah spustitelného souboru (skripty, knihovny DLL) může systém Windows zablokovat, pokud jsou soubory zip staženy z externího zdroje a extrahovány. Při extrakci skriptů ze souboru ZIP **postupujte podle následujících kroků, abyste před extrahováním odblokoval soubor. zip** . Tím se zajistí, že se skripty můžou spouštět.

1. Pro model architektury databáze, který chcete prozkoumat, přejděte na úložiště GitHub lístky Wingtip SaaSs.
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klikněte na **klonovat nebo stáhnout** .
3. Klikněte na **Stáhnout ZIP** a uložte soubor.
4. Klikněte pravým tlačítkem na soubor zip a vyberte **vlastnosti** . Název souboru zip bude odpovídat názvu úložiště. dodatečné. _WingtipTicketsSaaS-DbPerTenant-master.zip_ )
5. Na kartě **Obecné** vyberte **odblokovat** .
6. Klikněte na **OK** .
7. Extrahujte soubory.

Skripty jsou umístěny v *.. \\ Složka výukových modulů* .


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Práce se skripty PowerShellu pro lístky Wingtip

Pokud chcete získat z ukázky co nejvíc, musíte se podrobně do zadaných skriptů. Použití zarážek a krokování skriptů při jejich spouštění a kontrola, jak jsou implementovány různé vzory SaaS. K snadnému porozumění zadaným skriptům a modulům doporučujeme použít [prostředí POWERSHELL ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualizace konfiguračního souboru pro nasazení

Upravte soubor **userconfig. psm1** o skupinu prostředků a hodnotu uživatele, kterou jste nastavili během nasazování:

1. Otevřete *POWERSHELL ISE* a Load... \\ Výukové moduly \\ *userconfig. psm1*
2. Aktualizujte *ResourceGroupName* a *název* o konkrétní hodnoty pro vaše nasazení (jenom na řádcích 10 a 11).
3. Uložte změny.

Nastavení těchto hodnot jednoduše zabráníte v aktualizaci těchto hodnot specifických pro konkrétní nasazení v každém skriptu.

### <a name="execute-the-scripts-by-pressing-f5"></a>Spouštění skriptů stisknutím klávesy F5

Několik skriptů používá *$PSScriptRoot* k procházení složek a *$PSScriptRoot* se vyhodnocuje jenom v případě, že se skripty spouštějí stisknutím klávesy **F5** .  Zvýrazňování a spuštění výběru ( **F8** ) může mít za následek chyby, takže při spouštění skriptů stiskněte klávesu **F5** .

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Procházení skriptů k vyzkoušení implementace

Nejlepším způsobem, jak porozumět skriptům, je projít si jejich procházením, abyste viděli, co dělají. Podívejte se na zahrnuté **ukázkové** skripty, které představují snadnou kontrolu nad pracovním postupem vysoké úrovně. **Ukázková –** skripty ukazují kroky potřebné k provedení jednotlivých úloh, nastavení zarážek a přechod k podrobnostem o jednotlivých voláních k zobrazení podrobností o implementaci pro různé vzory SaaS.

Tipy pro zkoumání a krokování prostřednictvím skriptů PowerShellu:

- Otevřete **demo-** skripty v prostředí PowerShell ISE.
- Spustit nebo pokračovat s **F5** (použití **klávesy F8** není doporučeno, protože při spuštění výběrů skriptu není vyhodnocena *$PSScriptRoot* ).
- Pokud chcete umístit zarážky, klikněte na řádek nebo ho vyberte a stiskněte klávesu **F9** .
- Funkci nebo volání skriptu můžete vynechat stisknutím klávesy **F10** .
- Na funkci nebo volání skriptu můžete přejít stisknutím klávesy **F11** .
- Aktuální funkci nebo vlání skriptu můžete ukončit stisknutím kombinace **Shift + F11** .


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Prozkoumání databázového schématu a spouštění dotazů SQL pomocí SSMS

K připojení a procházení aplikačních serverů a databází použijte [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) .

Nasazení zpočátku má klienty a servery katalogu, ke kterým se připojuje. Pojmenování serverů závisí na vzoru architektury databáze (podrobnosti najdete níže).

   - **Samostatná aplikace:** servery pro každého tenanta (např. *contosoconcerthall – &lt; User &gt;* Server) a *Catalog-SA- &lt; User &gt;*
   - **Databáze na klienta:** *tenants1-DPT – &lt; Uživatel &gt;* a *katalog – DPT – &lt; uživatelské &gt;* servery
   - **Víceklientské databáze:** *tenants1-MT- &lt; User &gt;* and *Catalog-MT- &lt; User &gt;* Servers

Aby bylo zajištěno úspěšné ukázkové připojení, všechny servery mají [pravidlo brány firewall](firewall-configure.md) , které povoluje všechny IP adresy prostřednictvím.


1. Otevřete *SSMS* a připojte se k tenantovi. Název serveru závisí na vzoru architektury databáze, který jste vybrali (podrobnosti najdete níže):
    - **Samostatná aplikace:** servery jednotlivých tenantů (např. *contosoconcerthall – &lt; User &gt; . Database.Windows.NET* )
    - **Databáze na tenanta:** *tenants1-DPT- &lt; User &gt; . Database.Windows.NET*
    - **Víceklientské databáze:** *tenants1-MT- &lt; User &gt; . Database.Windows.NET*
2. Klikněte na **připojit**  >  **databázový stroj...** :

   ![katalogový server](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Ukázková pověření: login = *Developer* , Password = *P \@ ssword1*

    Následující obrázek ukazuje přihlášení k *databázi pro každý model klienta* .
    ![vázán](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Opakujte kroky 2-3 a připojte se k serveru katalogu (dole najdete konkrétní názvy serverů na základě vybraného vzoru databáze).
    - **Samostatná aplikace:** *Catalog-SA- &lt; User &gt; . Database.Windows.NET*
    - **Databáze na tenanta:** *Catalog-DPT- &lt; User &gt; . Database.Windows.NET*
    - **Víceklientské databáze:** *Catalog-MT- &lt; User &gt; . Database.Windows.NET*


Po úspěšném připojení byste měli vidět všechny servery. Seznam databází se může lišit v závislosti na klientech, které jste zřídili.

Následující obrázek ukazuje, jak se přihlašujete ke vzorům *databáze pro jednotlivé klienty* .

![průzkumník objektů](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Další kroky
- [Nasazení samostatné aplikace SaaS lístky Wingtip Tickets](./saas-standaloneapp-get-started-deploy.md)
- [Nasazení databáze Wingtip Tickets SaaS Database na klientské aplikace](./saas-dbpertenant-get-started-deploy.md)
- [Nasazení aplikace SaaS multi-tenant Database pro víceklientské lístky Wingtip](./saas-multitenantdb-get-started-deploy.md)