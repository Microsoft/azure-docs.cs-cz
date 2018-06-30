---
title: Optimalizace prostředí systému SQL Server s nástrojem Azure Log Analytics | Microsoft Docs
description: S Azure Log Analytics můžete řešení kontroly stavu SQL pro vyhodnocení rizik a stavu prostředí v pravidelných intervalech.
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
ms.component: na
ms.openlocfilehash: e83a722ee58f49c20871faaca6aae7d7a5678361
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130603"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Optimalizace prostředí SQL s řešením kontroly stavu serveru SQL v analýzy protokolů

![Kontrola stavu SQL – symbol](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Řešení SQL stavu zkontrolujte můžete použít k vyhodnocení rizik a stavu serveru prostředí v pravidelných intervalech. Tento článek vám pomůže nainstalovat řešení tak, že lze provádět opravné akce pro potenciální problémy.

Toto řešení poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serverů seřazený podle priority. Doporučení jsou rozdělené mezi šest konkrétní oblasti, které vám pomůžou rychle pochopit riziko a proveďte opravné akce.

Doporučení, která jsou založené na znalosti a zkušenosti technici Microsoft z tisíce zákazníka návštěvách. Každé doporučení obsahuje informace, proč může pro vás důležitá problém a implementaci navrhované změny.

Můžete vybrat konkrétní oblasti, které jsou důležité pro vaši organizaci a sledovat průběh směrem k spuštění prostředí riziko volné a v pořádku.

Když jste přidali řešení a posouzení je dokončené, souhrnné informace pro konkrétní oblasti je zobrazena na **SQL stavu zkontrolujte** řídicí panel infrastruktury ve vašem prostředí. Následující části popisují, jak používat informace na **SQL stavu zkontrolujte** řídicí panel, kde můžete zobrazit a pak proveďte doporučené akce pro vaši infrastrukturu systému SQL Server.

![Obrázek kontroly stavu SQL dlaždice](./media/log-analytics-sql-assessment/sql-healthcheck-summary-tile.png)

![Obrázek SQL stavu zkontrolujte řídicí panel](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Požadavky

* Kontrola stavu SQL řešení vyžaduje podporovanou verzi rozhraní .NET Framework 4 nainstalován na každý počítač, který má monitorování agenta MMA (Microsoft) nainstalována.  MMA agent používá System Center 2016 - Operations Manager a Operations Manager 2012 R2 a službu analýzy protokolů.  
* Podporuje řešení systému SQL Server verze 2012, 2014 a 2016.
* Pracovní prostor analýzy protokolů pro přidání SQL zkontrolujte stav řešení z Azure marketplace na portálu Azure.  Abyste mohli nainstalovat řešení, musíte být správce nebo Přispěvatel v rámci předplatného Azure.

  > [!NOTE]
  > Po přidání řešení, soubor AdvisorAssessment.exe je přidán na servery s agenty. Konfigurační data je čtení a pak se odešle službě Analýza protokolů v cloudu pro zpracování. Logika se použije pro přijatá data a cloudové služby zaznamenává data.
  >
  >

K provedení kontroly stavu na serverech systému SQL Server, vyžadují agenta a připojení k analýze protokolů pomocí jedné z následujících podporovaných metod:

1. Nainstalujte [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) Pokud server není již monitorován pomocí System Center 2016 - Operations Manager nebo Operations Manager 2012 R2.
2. Pokud je monitorovaný pomocí System Center 2016 - Operations Manager nebo Operations Manager 2012 R2 a skupině pro správu není integrované se službou analýzy protokolů, je možné serveru vícedomé s analýzy protokolů pro shromažďování dat a předání do služby a je stále sledovány serverem Operations Manager.  
3. Jinak, pokud skupině pro správu nástroje Operations Manager je integrovaná se službou, je nutné přidat řadiče domény pro shromažďování dat pomocí služby podle kroků v části [přidat počítače spravované agentem](log-analytics-om-agents.md#connecting-operations-manager-to-log-analytics) po povolení řešení v pracovním prostoru.  

Agent na serveru SQL Server, které sestavy ke skupině pro správu nástroje Operations Manager shromažďuje data, předává do serveru pro správu přiřazené, se pak odešlou přímo ze serveru pro správu ke službě Analýza protokolů.  Data není zapsán do databáze nástroje Operations Manager.  

Pokud je nástrojem Operations Manager serveru SQL Server, musíte nakonfigurovat Operations Manager účet Spustit jako. V tématu [účty nástroje Operations Manager spustit jako pro analýzy protokolů](#operations-manager-run-as-accounts-for-log-analytics) níže Další informace.

## <a name="sql-health-check-data-collection-details"></a>Kontrola stavu SQL podrobnosti kolekce dat
Kontrola stavu SQL shromažďuje data z následujících zdrojů pomocí agenta, který jste povolili:

* Windows Management Instrumentation (WMI)
* Registr
* Čítače výkonu
* Výsledky zobrazení dynamické správy SQL Server

Data jsou shromažďována na serveru SQL a předávat k analýze protokolů každých sedm dní.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty spustit jako nástroje Operations Manager pro analýzy protokolů
Analýzy protokolů skupinu pro správu a agent nástroje Operations Manager používá ke shromažďování a odesílání dat do služby analýzy protokolů. Sestavení Analytics protokolu na sady management Pack pro úlohy zajistit hodnotu – Přidání služeb. Každé zatížení vyžaduje určeného pro konkrétní úlohy oprávnění ke spouštění sad management Pack v odlišném kontextu zabezpečení, jako je například účet uživatele domény. Budete muset zadat přihlašovací údaje nakonfigurováním Operations Manager účet Spustit jako.

K nastavení Operations Manager účet Spustit jako pro kontrolu stavu SQL použijte následující informace.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Nastavit účet Spustit jako pro kontrolu stavu SQL
 Pokud už používáte sadu management pack systému SQL Server, měli byste použít tato konfigurace spustit jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Jak nakonfigurovat účet Spustit jako SQL v konzoli Operations console
> [!NOTE]
> Ve výchozím nastavení spouští pracovní postupy v sadě management pack v kontextu zabezpečení účtu místního systému. Pokud používáte službu Microsoft Monitoring Agent připojení přímo ke službě namísto vytváření sestav přímo do skupiny správy nástroje Operations Manager, přeskočit kroky 1 až 5 níže a spustit buď T-SQL nebo prostředí PowerShell, zadání NT AUTHORITY\SYSTEM jako uživatelské jméno.
>
>

1. V nástroji Operations Manager, otevřete konzoli Operations console a pak klikněte na tlačítko **správy**.
2. V části **konfigurace spustit jako**, klikněte na tlačítko **profily**a otevřete **OMS SQL Assessment profilu spustit jako**.
3. Na **účty spustit jako** klikněte na tlačítko **přidat**.
4. Vyberte účet Spustit jako systému Windows, který obsahuje přihlašovacích údajů nezbytných pro systém SQL Server, nebo klikněte na **nový** k jeho vytvoření.

   > [!NOTE]
   > Typ účtu spustit jako musí být Windows. Účet Spustit jako musí být také součástí místní skupiny Administrators na všech serverech Windows, který je hostitelem instance serveru SQL.
   >
   >
5. Klikněte na **Uložit**.
6. Upravit a potom spusťte následující ukázka T-SQL na každou instanci serveru SQL udělte minimální oprávnění potřebná pro účet Spustit jako k provedení kontroly stavu. Ale nemusíte to dělat, když se účet Spustit jako už je součástí role serveru sysadmin na instance systému SQL Server.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Jak nakonfigurovat účet Spustit jako SQL pomocí prostředí Windows PowerShell
Otevřete okno prostředí PowerShell a spusťte následující skript po aktualizaci s vaše informace:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Pochopení, jak budou doporučení mít vyšší prioritu
Každé doporučení je zadána hodnota vyvážení, která určuje relativní důležitost doporučení. Zobrazí se jenom deset nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Jak jsou vypočítávány váhu
Váhy jsou agregovaných hodnot založena na tři klíčové faktory:

* *Pravděpodobnosti* způsobí, že problém identifikovat problémy. Vyšší pravděpodobnost rovná větší celkové skóre pro doporučení.
* *Dopad* problému na vaší organizaci, pokud ji způsobovat problémy. Vyšší dopad rovná větší celkové skóre pro doporučení.
* *Úsilí* potřebnou k implementaci doporučení. Vyšší úsilí rovná menší celkové skóre pro doporučení.

Vyvážení pro každé doporučení je vyjádřený jako procentní podíl celkové skóre, které jsou k dispozici pro každou oblast fokus. Například pokud doporučení v oblasti zabezpečení a dodržování předpisů fokus je skóre % 5, implementace tímto doporučením zvýší celkové skóre podle 5 % zabezpečení a dodržování předpisů.

### <a name="focus-areas"></a>Konkrétní oblasti
**Zabezpečení a dodržování předpisů** – v tomto poli fokus zobrazí doporučení pro potenciální bezpečnostní hrozby a narušení, podnikové zásady a dodržování předpisů technických, právních i regulačních požadavků.

**Dostupnost a provozní kontinuita** – v tomto poli fokus zobrazí doporučení pro dostupnost služeb, odolnost vaší infrastruktury a obchodní ochrany.

**Výkon a škálovatelnost** – v tomto poli fokus zobrazí doporučení, která pomůžou vaší organizace IT infrastruktury růst, zajistěte, aby vaše IT prostředí splňuje aktuální požadavky na výkon a schopné reagovat na měnící se infrastruktury potřebuje.

**Upgrade, nasazení a migrace** – v tomto poli fokus zobrazí doporučení, která vám pomohou upgradovat, migrace a nasazení systému SQL Server k vaší stávající infrastruktury.

**Operace a monitorování** – v tomto poli fokus zobrazí doporučení, která pomůžou zjednodušení operací IT a implementaci preventivní údržby, maximalizovat výkon.

**Správa změn a konfigurací** – v tomto poli fokus zobrazí doporučení pomáhají chránit každodenní operace, zkontrolujte, zda nemáte změny negativně ovlivnit vaši infrastrukturu, vytvořit procedury řízení změn a ke sledování a monitorování Konfigurace systému.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Mají usilovat o stanovení skóre 100 % v každé oblasti fokus?
Ne nutně. Doporučení jsou založené na prostředí, které nebyly získány prostřednictvím Microsoft technici napříč tisíce návštěvy zákazníka a znalostní báze. Ale žádné serverové infrastruktury jsou stejné, a konkrétní doporučení může být vyšší nebo nižší relevantní pro vás. Například může být několik doporučení zabezpečení méně důležité, pokud vaše virtuální počítače nejsou vystaveny v Internetu. Některá doporučení, jaké dostupnosti může být méně důležité pro služby, které poskytují kolekce s nízkou prioritou ad hoc dat a vytváření sestav. Problémy, které jsou důležité, abyste vyspělých firmy může být méně důležité Startup. Můžete určit, které konkrétní oblasti mají vašich priorit a podívejte se na tom, jak se vaše skóre časem změnit.

Každé doporučení obsahuje pokyny o tom, proč je důležité. Měli byste použít tyto pokyny k vyhodnocení, zda implementace doporučení je vhodné pro vás, daná povaze vašich IT služeb a obchodním potřebám vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použít kontrolu stavu fokus oblasti doporučení
Než v analýzy protokolů můžete použít řešení pro vyhodnocení, musíte mít nainstalován řešení.  Po instalaci, zobrazí se souhrn doporučení pomocí dlaždice SQL stavu zkontrolujte na stránce řešení na portálu Azure.

Zobrazte vyhodnocování souhrnné dodržování předpisů pro infrastrukturu a potom přejít k podrobnostem doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Proveďte opravné akce a zobrazit doporučení pro oblastí zájmu
1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V podokně odběry analýzy protokolů vyberte pracovní prostor a klikněte **přehled** dlaždici.  
4. Na **přehled** klikněte na tlačítko **SQL stavu zkontrolujte** dlaždici.
5. Na **kontroly stavu** zkontrolujte souhrnné informace v jednom z okna oblasti fokus a pak klikněte na jednu zobrazíte doporučení pro tuto oblast fokus.
6. Na všech stránkách oblasti fokus můžete zobrazit seřazený podle priority doporučení, která se pro vaše prostředí. Klikněte na tlačítko doporučení v části **vliv na objekty** Chcete-li zobrazit podrobnosti, proč se provádí doporučení.<br><br> ![Obrázek kontroly stavu SQL doporučení](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Můžete provést nápravné akce navržený v **doporučované akce**. Když položka vyřeší, bude záznam novější vyhodnocování které doporučené akce provedené a zvýší se vaše skóre dodržování předpisů. Opravené položky se zobrazí jako **předán objekty**.

## <a name="ignore-recommendations"></a>Ignorovat doporučení
Pokud máte doporučení, které chcete ignorovat, můžete vytvořit textový soubor, který analýzy protokolů použije k zabránění doporučení ze storu ve výsledky hodnocení.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>K identifikaci doporučení, které se budou ignorovat.
1. Na stránce pracovní prostor analýzy protokolů pro vaši vybranou pracovní prostor na portálu Azure, klikněte na tlačítko **hledání protokolů** dlaždici.
2. Pro počítače ve vašem prostředí použijte následující dotaz, který seznam doporučení, které selhaly.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud pracovní prostor byl upgradován na verzi [nové analýzy protokolů dotazu jazyka](log-analytics-log-search-upgrade.md), pak výše uvedeném dotazu by změnit na následující.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Zde je snímek obrazovky zobrazující protokolu vyhledávací dotaz:<br><br> ![doporučení se nezdařilo](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Zvolte doporučení, které chcete ignorovat. Hodnoty pro RecommendationId budete používat v dalším postupu.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytváření a používání textový soubor s IgnoreRecommendations.txt
1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložit nebo zadejte každou RecommendationId pro jednotlivá doporučení, které chcete analýzy protokolů ignorovat na samostatném řádku a potom uložte a zavřete soubor.
3. Uložte soubor v následující složce na každém počítači, kam chcete analýzy protokolů ignorovat doporučení.
   * Na počítačích s Microsoft Monitoring Agent (připojené přímo nebo prostřednictvím nástroje Operations Manager) - *SystemDrive*: \Program Files\Microsoft Agent\Agent monitorování
   * Na serveru pro správu nástroje Operations Manager - *SystemDrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serveru pro správu nástroje Operations Manager 2016 - *SystemDrive*: \Program Files\Microsoft Manager\Server 2016\Operations System Center

### <a name="to-verify-that-recommendations-are-ignored"></a>Chcete-li ověřit, že se ignorovat doporučení
1. Po na další naplánované vyhodnocení běží ve výchozím nastavení každých 7 dní, zadaný doporučení jsou označeny ignorovaná a nezobrazí se na řídicím panelu hodnocení.
2. Následující dotazy hledání protokolů můžete použít k zobrazení seznamu všech ignorováno doporučení.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud pracovní prostor byl upgradován na verzi [nové analýzy protokolů dotazu jazyka](log-analytics-log-search-upgrade.md), pak výše uvedeném dotazu by změnit na následující.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Pokud se později rozhodnete, zda chcete zobrazit ignorováno doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo RecommendationIDs můžete odebrat z nich.

## <a name="sql-health-check-solution-faq"></a>Kontrola stavu SQL řešení – nejčastější dotazy
*Jak často kontrolu stavu spustit?*

* Kontrola spouští každých sedm dní.

*Existuje způsob, jak nakonfigurovat, jak často neproběhne kontrola?*

* V tuto chvíli to není možné.

*Pokud jiný server je zjištěno po byly přidány SQL zkontrolujte stav řešení, budou se kontrolovat?*

* Ano, jakmile se zjistí, že je zaškrtnuté z pak, každých sedm dní.

*Pokud dojde k deaktivaci serveru, když ho se odebere z kontrolou stavu?*

* Pokud server není odesílání dat 3 týdny, bude odebrán.

*Jaký je název procesu, který nemá shromažďování dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá na shromáždění dat?*

* Kolekce skutečná data na serveru trvá asi 1 hodina. Může trvat déle na serverech, které mají velký počet instancí SQL nebo databáze.

*Jaký typ dat shromažďovaných?*

* Se shromažďují následující typy dat:
  * ROZHRANÍ WMI
  * Registr
  * Čítače výkonu
  * Zobrazení dynamické správy SQL (DMV).

*Existuje způsob, jak nakonfigurovat, když jsou shromažďována data?*

* V tuto chvíli to není možné.

*Proč je nutné nakonfigurovat účet Spustit jako?*

* Pro systém SQL Server se spouštějí na malý počet dotazů SQL. Aby, aby se spouštěly musíte použít účet Spustit jako s oprávnění VIEW SERVER STATE do systému SQL.  Kromě toho aby bylo možné odeslat dotaz rozhraní WMI, jsou požadované přihlašovací údaje místního správce.

*Proč zobrazit pouze prvních 10 doporučení?*

* Namísto udělení vyčerpávající čtenáře seznam úloh, doporučujeme můžete soustředit na první adresování seřazený podle priority doporučení. Po jejich řešení, bude k dispozici další doporučení. Pokud ji chcete zobrazit podrobné seznamu, můžete zobrazit všechna doporučení pomocí vyhledávání protokolu analýzy protokolů.

*Existuje způsob, jak ignorovat doporučení?*

* Ano, najdete v části [ignorovat doporučení](#ignore-recommendations) část výše.

## <a name="next-steps"></a>Další postup
* [V protokolech Hledat](log-analytics-log-searches.md) se dozvíte, jak analyzovat doporučení a podrobné údaje o kontrolu stavu SQL.
