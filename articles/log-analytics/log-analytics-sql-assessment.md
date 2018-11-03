---
title: Optimalizace prostředí SQL serveru pomocí služby Azure Log Analytics | Dokumentace Microsoftu
description: S Azure Log Analytics vám pomůže řešení SQL Health Check posuzuje rizika a stav prostředí v pravidelných intervalech.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: be2fd826ffa658ec04bd937036e37bf55cbf65a4
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50964131"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Optimalizujete si prostředí SQL s řešením kontroly stavu SQL serveru ve službě Log Analytics

![Kontrola stavu SQL symbol](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Vám pomůže řešení SQL Health Check posuzuje rizika a stav prostředí vašeho serveru v pravidelných intervalech. Tento článek vám pomůže nainstalovat řešení, takže můžete provést opravné akce pro potenciální problémy.

Toto řešení poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury. Doporučení jsou zařazené do kategorií v šesti oblastech zaměření, které vám pomohou rychle vědomi rizika a provést opravné akce.

Doporučení jsou založeny na znalosti a zkušenosti získané z návštěvě tisíců zákaznických odborníky z Microsoftu. Každé doporučení obsahuje pokyny k proč k problému může být na kterých vám záleží a jak implementovat navrhované změny.

Můžete vybrat konkrétní oblasti, které jsou nejdůležitější pro vaši organizaci a sledovat svůj postup směrem ke spuštění prostředí pro riziko free a v dobrém stavu.

Po přidání řešení a posouzení hotové, souhrnné informace pro konkrétní oblasti se zobrazí na **kontroly stavu SQL** řídicí panel infrastruktury ve vašem prostředí. Následující části popisují, jak pomocí informací na **kontroly stavu SQL** řídicí panel, kde můžete zobrazit a pak provést doporučené akce pro vaši infrastrukturu systému SQL Server.

![snímek dlaždice kontroly stavu SQL](./media/log-analytics-sql-assessment/sql-healthcheck-summary-tile.png)

![Obrázek řídicího panelu kontroly stavu SQL](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Požadavky

* Řešení SQL Health Check vyžaduje podporovanou verzi rozhraní .NET Framework 4 nainstalovaný na každém počítači, který má Microsoft Monitoring Agent (MMA) nainstalovaný.  MMA agent používá System Center 2016 – Operations Manager a Operations Manageru 2012 R2 a služby Log Analytics.  
* Řešení podporuje SQL Server verze 2012, 2014 a 2016.
* Pracovní prostor Log Analytics můžete přidat řešení SQL Health Check z Azure marketplace na webu Azure Portal.  Pokud chcete řešení nainstalovat, musíte být správce nebo přispěvatele v předplatném Azure.

  > [!NOTE]
  > Po přidání řešení, se přidá soubor AdvisorAssessment.exe na servery s agenty. Konfigurační data je čtení a pak posílají do služby Log Analytics v cloudu pro zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data.
  >
  >

Provádění kontroly stavu na serverech systému SQL Server, vyžadují agenta a připojení ke službě Log Analytics pomocí jedné z následujících podporovaných metod:

1. Nainstalujte [Microsoft Monitoring Agent (MMA)](log-analytics-agent-windows.md) Pokud server není již monitorovaná System Center 2016 – Operations Manager nebo Operations Manager 2012 R2.
2. Pokud je monitorovat pomocí nástroje System Center 2016 – Operations Manager nebo Operations Manager 2012 R2 a skupině pro správu není integrovaná se službou Log Analytics, je možné serveru s více adresami pomocí Log Analytics pro shromažďování dat a předat službě a stále sledování Operations Managerem.  
3. Jinak, pokud vaší skupině pro správu Operations Manageru je integrovaná se službou, budete muset přidat řadiče domény pro shromažďování dat podle pokynů v části služby [přidat počítače spravované bez agenta](log-analytics-om-agents.md#connecting-operations-manager-to-log-analytics) po povolení řešení ve vašem pracovním prostoru.  

Agent na serveru SQL Server, které sestavy pro skupinu pro správu nástroje Operations Manager shromažďuje data, předává do serveru pro správu přiřazené a pak se odešle přímo ze serveru pro správu služby Log Analytics.  Data není zapsána do databáze nástroje Operations Manager.  

Pokud SQL Server je sledování Operations Managerem, budete muset nakonfigurovat Operations Manageru účet Spustit jako. Zobrazit [účty nástroje Operations Manager spustit jako pro Log Analytics](#operations-manager-run-as-accounts-for-log-analytics) níže pro další informace.

## <a name="sql-health-check-data-collection-details"></a>Kontrola stavu SQL podrobnosti kolekce dat
Kontrola stavu SQL shromažďuje data z následujících zdrojů pomocí agenta, který jste povolili:

* Windows Management Instrumentation (WMI)
* Registr
* Čítače výkonu
* Výsledky zobrazení dynamické správy SQL serveru

Data se shromažďují na SQL serveru a předávají do Log Analytics každých sedm dní.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty spustit jako nástroje Operations Manager ke službě Log Analytics
Log Analytics používá skupiny management a agent nástroje Operations Manager ke shromažďování a odesílání dat do služby Log Analytics. Log Analytics vychází z sady management Pack pro úlohy zadejte hodnotu přidat služby. Každá úloha vyžaduje specifické úlohy oprávnění ke spuštění sady management Pack v kontextu zabezpečení, jako je například účet uživatele domény. Budete muset zadat přihlašovací údaje tím, že nakonfigurujete Operations Manageru účet Spustit jako.

Chcete-li nastavit Operations Manageru účet Spustit jako pro kontroly stavu SQL použijte následující informace.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Nastavení účtu spustit jako pro kontroly stavu SQL
 Pokud už používáte sadu management pack systému SQL Server, měli byste použít, že konfigurace spustit jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Ke konfiguraci účtu spustit jako SQL v konzoli Operations console
> [!NOTE]
> Ve výchozím nastavení spouští pracovní postupy v sadě management pack v kontextu zabezpečení účtu místního systému. Pokud používáte Microsoft Monitoring Agent připojí přímo ke službě namísto vytváření sestav přímo do skupiny pro správu Operations Manageru, přeskočte kroky 1 až 5 níže a spuštění T-SQL nebo ukázku prostředí PowerShell, určení NT AUTHORITY\SYSTEM jako uživatelské jméno.
>
>

1. V nástroji Operations Manager, otevřete konzoli Operations console a klikněte na **správu**.
2. V části **konfigurace spustit jako**, klikněte na tlačítko **profily**a otevřete **SQL Assessment profilu spustit jako**.
3. Na **účty spustit jako** klikněte na **přidat**.
4. Vyberte účet Spustit jako Windows, který obsahuje přihlašovacích údajů potřebných pro SQL Server, nebo klikněte na tlačítko **nový** k jejímu vytvoření.

   > [!NOTE]
   > Typ účtu spustit jako musí být Windows. Účet Spustit jako musí být také součástí místní skupiny Administrators na všechny servery Windows, které hostují instance systému SQL Server.
   >
   >
5. Klikněte na **Uložit**.
6. Upravit a potom spusťte následující příklad jazyka T-SQL pro každou instanci serveru SQL Server udělit minimální oprávnění potřebná pro účet Spustit jako k provádění kontroly stavu. Ale není potřeba udělat, pokud účet Spustit jako je již součástí role serveru sysadmin v instancích systému SQL Server.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Konfigurace účtu spustit jako SQL pomocí prostředí Windows PowerShell
Otevřete okno Powershellu a spusťte následující skript po aktualizaci s informacemi o:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Vysvětlení, jak mají určenou prioritu doporučení
Každé doporučení je přiřazena hodnota váhu, která identifikuje relativní důležitost doporučení. Zobrazují se jenom deset nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Jak se počítají váhy
Váhy jsou agregované hodnoty, které jsou založené na tři klíčové faktory:

* *Pravděpodobnost* , způsobí chyby zjištěné potíže. Vyšší pravděpodobnost rovná větší celkové skóre pro doporučení.
* *Dopad* problému ve vaší organizaci, pokud ho způsobovat problémy. Vyšší dopad odpovídá větší celkové skóre pro doporučení.
* *Úsilí* potřebnou k implementaci doporučení. Vyšší úsilí odpovídá menší celkové skóre pro doporučení.

Váhu pro jednotlivá doporučení je vyjádřené jako procentní podíl celkové skóre, které jsou k dispozici pro každou oblast zaměření. Například pokud doporučení v oblasti zabezpečení a dodržování předpisů zaměření má skóre % 5, implementaci tohoto doporučení zvýší vaše celkové skóre podle 5 % zabezpečení a dodržování předpisů.

### <a name="focus-areas"></a>Konkrétní oblasti
**Zabezpečení a dodržování předpisů** – zobrazuje tuto oblast zaměření doporučení ohledně potenciálních bezpečnostních hrozeb a porušení, podnikové zásady a technických, právních i regulačních požadavků.

**Dostupnost a plynulost obchodních procesů** -tuto oblast zaměření ukazuje doporučení pro služby dostupnost, odolnost vaší infrastruktury a ochrany firmy.

**Výkon a škálovatelnost** – zobrazuje tuto oblast zaměření doporučení, která vaší organizaci pomoct IT infrastruktury růst, ujistěte se, že splňuje aktuální požadavky na výkon vašeho IT prostředí a je schopný reagovat na měnící se infrastruktury potřebuje.

**Upgrade, migrace a nasazení** – zobrazuje tuto oblast zaměření doporučení, která vám pomohou upgrade, migrace a nasazení systému SQL Server do vaší stávající infrastruktury.

**Operace a monitorování** – zobrazuje tuto oblast zaměření doporučení, která pomůžou zefektivnit vaše IT oddělení, implementovat preventivní údržby a maximalizovat výkon.

**Konfigurace správy změn a** -zobrazí doporučení pomáhají chránit každodenní operace, ujistěte se, že změny není mít negativní vliv na vaši infrastrukturu, vytvořit procedury řízení změn a sledovat a auditovat tuto oblast zaměření Konfigurace systému.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>By měl být cílem stanovení skóre 100 % v každé oblasti zaměření?
Ne nutně. Doporučení jsou založeny na znalosti a zkušenosti získané v Microsoftu přes návštěvě tisíců zákaznických. Ale žádné serverové infrastruktury jsou stejné, a konkrétní doporučení může být vyšší nebo nižší pro vás relevantní. Některá doporučení zabezpečení může být například méně relevantní, pokud vaše virtuální počítače nejsou zveřejněné na Internetu. Některá doporučení dostupnosti může být méně důležité pro služby, které poskytují shromažďování dat ad hoc s nízkou prioritou a generování sestav. Problémy, které jsou důležité pro až po zralé podnikání, může být méně důležité spouštěcí. Můžete určit, které konkrétní oblasti mají vašich priorit a podívejte se na tom, jak v průběhu času měnit skóre.

Každé doporučení obsahuje pokyny o tom, proč je důležité. Měli byste použít tento návod k vyhodnocení, jestli implementace doporučení je vhodné, vzhledem k povaze služeb IT a obchodní potřeby vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použití kontroly stavu doporučení oblasti zaměření
Před použitím řešení pro posouzení ve službě Log Analytics, musíte mít nainstalované řešení.  Po instalaci, zobrazí se přehled doporučení pomocí kontroly stavu SQL dlaždice na stránce řešení na webu Azure Portal.

Zobrazení posouzení souhrnné dodržování předpisů pro infrastrukturu a pak přejít k podrobnostem doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Zobrazit doporučení pro oblast zaměření a provedení nápravné akce
1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V podokně předplatná Log Analytics vyberte pracovní prostor a pak klikněte na tlačítko **přehled** dlaždici.  
4. Na **přehled** stránky, klikněte na tlačítko **kontroly stavu SQL** dlaždici.
5. Na **kontroly stavu** stránky, zkontrolujte souhrnné informace u některého okna oblasti zaměření a klikněte na z nich se má zobrazit doporučení pro tuto oblast zaměření.
6. Na žádném z oblasti stránek fokus můžete zobrazit prioritizovaných doporučení pro vaše prostředí. Kliknutím na doporučení v části **vliv na objekty** zobrazíte podrobnosti o tom, proč je provedeny doporučení.<br><br> ![Obrázek kontroly stavu SQL doporučení](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Můžete provést opravné akce navržený v **doporučené akce**. Pokud položka byla určena, zaznamená novější posouzení, které doporučené akce byly provedeny a zvýší vaše skóre dodržování předpisů. Opravené položky se zobrazí jako **předaný objekty**.

## <a name="ignore-recommendations"></a>Ignorujte doporučení
Pokud máte doporučení, která má být ignorována, můžete vytvořit textový soubor, který brání doporučení povolí, nebude vaše výsledky posouzení pomocí Log Analytics.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>K identifikaci doporučení, která se bude ignorovat.
1. Na portálu Azure na stránce pracovního prostoru Log Analytics pro váš vybraný pracovní prostor, klikněte na tlačítko **prohledávání protokolů** dlaždici.
2. Následující dotaz tak, aby seznam doporučení, které se nepodařilo použijte pro počítače se ve vašem prostředí.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [dotazovací jazyk Log Analytics nové](log-analytics-log-search.md), pak se změní výše uvedeném dotazu následující.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Zde je snímek obrazovky zobrazující z dotazu prohledávání protokolu:<br><br> ![doporučení se nezdařilo](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Zvolte doporučení, která má být ignorována. V dalším postupu budete používat hodnoty pro ID doporučení.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt
1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložte nebo zadejte ID jednotlivých doporučení pro jednotlivá doporučení, který má Log Analytics ignorovat na samostatném řádku a potom uložte a zavřete soubor.
3. Uložte soubor v následující složce na každém počítači místo, kam chcete Log Analytics pro ignorování doporučení.
   * Na počítačích s Microsoft Monitoring Agent (připojené přímo nebo prostřednictvím Operations managera) - *systemdrive % musí být*: \Program Files\Microsoft Monitoring Agent\Agent
   * Na serveru pro správu nástroje Operations Manager - *systemdrive % musí být*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serveru pro správu Operations Manageru 2016 - *systemdrive % musí být*: \Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Chcete-li ověřit, že jsou ignorovány doporučení
1. Až dalšího naplánovaného spuštění posouzení ve výchozím nastavení každých 7 dní, zadaný doporučení jsou označeny ignorováno a nezobrazí se na řídicím panelu posouzení.
2. Do seznamu ignorovaných doporučení můžete použít následující dotazy prohledávání protokolů.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [dotazovací jazyk Log Analytics nové](log-analytics-log-search.md), pak se změní výše uvedeném dotazu následující.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Pokud se později rozhodnete, že chcete zobrazit ignorované doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo RecommendationIDs můžete odebrat z nich.

## <a name="sql-health-check-solution-faq"></a>Řešení SQL Health Check – nejčastější dotazy
*Jak často Kontrola stavu spouštět?*

* Kontrola proběhne každých sedm dní.

*Existuje způsob, jak konfigurovat, jak často neproběhne kontrola?*

* V tuto chvíli to není možné.

*Pokud po přidali jsme řešení SQL Health Check je zjistit další server, budou se kontrolovat?*

* Ano, jakmile je zjištěna zkontroluje z té, každých sedm dní.

*Pokud dojde k deaktivaci na serveru, když ho se odebere z kontroly stavu?*

* Pokud server není odesílání dat po dobu 3 týdnů, bude odebrán.

*Jaký je název procesu, která provádí sběr dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá shromažďování dat?*

* Skutečná data kolekce na serveru trvá přibližně 1 hodinu. Může trvat delší dobu na serverech, které mají velký počet instancí SQL nebo databází.

*Jaký typ dat se shromažďují?*

* Jsou shromažďovány následující typy dat:
  * ROZHRANÍ WMI
  * Registr
  * Čítače výkonu
  * Zobrazení dynamické správy SQL (DMV Sys.DM_OPERATION_STATUS).

*Existuje způsob, jak konfigurovat, když se shromažďují data?*

* V tuto chvíli to není možné.

*Proč je nutné nakonfigurovat účet Spustit jako?*

* Pro SQL Server se spouštějí menšímu množství dotazů SQL. V pořadí jejich spuštění musíte použít účet Spustit jako s oprávnění VIEW SERVER STATE pro SQL.  Kromě toho aby bylo možné dotazovat služby WMI, jsou požadovány přihlašovací údaje místního správce.

*Proč zobrazit pouze prvních 10 doporučení?*

* Místo získáte vyčerpávající seznam náročné úkoly, doporučujeme vám zaměřit nejprve adresování prioritizovaných doporučení. Po jejich řešení, budou k dispozici další doporučení. Pokud chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení, pomocí prohledávání protokolu Log Analytics.

*Existuje způsob, jak ignorujte doporučení?*

* Ano, naleznete v tématu [ignorujte doporučení](#ignore-recommendations) výše uvedené části.

## <a name="next-steps"></a>Další postup
* [Hledání protokolů](log-analytics-log-search.md) informace o analýze podrobných dat o kontrolu stavu SQL a doporučení.
