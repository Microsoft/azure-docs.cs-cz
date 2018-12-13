---
title: Optimalizujete si prostředí System Center Operations Manageru pomocí služby Azure Log Analytics | Dokumentace Microsoftu
description: Můžete použít řešení kontroly systému System Center Operations Manager Health k vyhodnocení v pravidelných intervalech rizika a stav různých prostředí.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.openlocfilehash: fc625192464dce174b4c2a6d8a2a98343519699f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186119"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimalizujete si prostředí díky řešení System Center Operations Manager kontroly stavu (Preview)

![Systém System Center Operations Manager Health Zkontrolujte symbol](./media/scom-assessment/scom-assessment-symbol.png)

Můžete použít řešení kontroly systému System Center Operations Manager Health k posuzuje rizika a stav skupiny pro správu System Center Operations Manager v pravidelných intervalech. Tento článek pomůže nainstalovat, nakonfigurovat a používat řešení tak, že můžete provést opravné akce pro potenciální problémy.

Toto řešení poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury. Doporučení jsou rozdělené mezi čtyři oblasti zaměření, které vám pomohou rychle vědomi rizika a provést opravné akce.

Doporučení jsou založeny na znalosti a zkušenosti získané z návštěvě tisíců zákaznických odborníky z Microsoftu. Každé doporučení obsahuje pokyny k proč k problému může být na kterých vám záleží a jak implementovat navrhované změny.

Můžete vybrat konkrétní oblasti, které jsou nejdůležitější pro vaši organizaci a sledovat svůj postup směrem ke spuštění prostředí pro riziko free a v dobrém stavu.

Poté, co jste přidali řešení a posouzení se provádí, souhrnné informace pro konkrétní oblasti se zobrazí na **kontroly systému System Center Operations Manager Health** řídicího panelu pro vaši infrastrukturu. Následující části popisují, jak pomocí informací na **kontroly systému System Center Operations Manager Health** řídicí panel, kde můžete zobrazit a pak provést doporučené akce pro vaše prostředí nástroje Operations Manager.

![Dlaždice řešení System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Systém System Center Operations Manager Health zkontrolujte řídicí panel](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení

Toto řešení spolupracuje s Microsoft nástrojem Operations Manager 2012 Service Pack (SP) 1 a 2012 R2.

K instalaci a konfiguraci řešení můžete použít následující informace.

 - Než ve službě Log Analytics můžete použít řešení kontroly stavu, musíte mít nainstalované řešení. Nainstalujte řešení od [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - Po přidání řešení do pracovního prostoru, **kontroly systému System Center Operations Manager Health** dlaždice na řídicím panelu zobrazí zprávu vyžaduje další konfiguraci. Klikněte na dlaždici a postupujte podle kroků konfigurace uvedených na stránce

 ![Dlaždice řídicího panelu systému System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfigurace nástroje System Center Operations Manager lze provést pomocí skriptu pomocí následujícího postupu uvedeného na stránce konfigurace řešení v Log Analytics.

 Hodnocení můžete nakonfigurovat prostřednictvím Operations console Operations Manageru, proveďte následující kroky v uvedeném pořadí:
1. [Nastavení účtu spustit jako pro kontroly systému System Center Operations Manager Health](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Konfigurace pravidla kontroly systému System Center Operations Manager Health](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Podrobnosti o systému System Center Operations Manager assessment dat kolekce

Hodnocení System Center Operations Manager shromažďuje data z následujících zdrojů:

* Registr
* Windows Management Instrumentation (WMI)
* Protokol událostí
* Data souborů
* Přímo z nástroje Operations Manager pomocí prostředí PowerShell a dotazů SQL, ze serveru pro správu, kterou jste zadali.  

Data se shromažďují na serveru pro správu a předávají do Log Analytics každých sedm dní.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty spustit jako nástroje Operations Manager ke službě Log Analytics

Log Analytics sestavení na sadách management Pack pro úlohy zadejte hodnotu přidat služby. Každá úloha vyžaduje specifické úlohy oprávnění ke spuštění sady management Pack v kontextu zabezpečení, jako je například účet uživatele domény. Nakonfigurujte Operations Manageru účet Spustit jako pomocí privilegovaných přihlašovacích údajů. Další informace najdete v tématu [způsob vytvoření účtu spustit jako](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) v dokumentaci k nástroji Operations Manager.

Pomocí následujících informací můžete nastavit Operations Manageru účet Spustit jako pro kontroly systému System Center Operations Manager Health.

### <a name="set-the-run-as-account"></a>Nastavte účet Spustit jako

Účet Spustit jako, musí splňovat následující požadavky před pokračováním:

* Účet uživatele domény, který je členem místní skupiny Administrators na všech serverech podporujících jakákoli role nástroje Operations Manager – server pro správu, serveru SQL Server hostující operační databázi, datového skladu a databáze služby ACS, generování sestav a webové konzole a server brány.
* Role správce operace správce skupiny pro správu je použit k vyhodnocení
* Pokud účet nemá práva správce systému SQL, provádějí [skript](#sql-script-to-grant-granular-permissions-to-the-run-as-account) k udělovat různě odstupňovaná oprávnění k účtu pro každou instanci serveru SQL Server hostující jednu nebo všechny databáze nástroje Operations Manager.

1. V konzole nástroje Operations Manager, vyberte **správu** navigační tlačítko.
2. V části **konfigurace spustit jako**, klikněte na tlačítko **účty**.
3. V **vytvořit účet Spustit jako** Průvodce na **ÚVOD** stránce klikněte na **Další**.
4. Na **obecné vlastnosti** stránce **Windows** v **typ účtu spustit jako:** seznamu.
5. Zadejte zobrazovaný název v **zobrazovaného názvu** textové pole a volitelně zadejte popis do **popis** pole a potom klikněte na tlačítko **Další**.
6. Na **zabezpečení distribuce** stránce **bezpečnější**.
7. Klikněte na možnost **Vytvořit**.  

Teď, když se vytvoří účet Spustit jako, musí cílové servery pro správu ve skupině pro správu a přidružené předem definovaného profilu spustit jako, pracovní postupy se spustí pomocí přihlašovacích údajů.  

1. V části **konfigurace spustit jako**, **účty**, v podokně výsledků klikněte dvakrát na účtu, který jste vytvořili dříve.
2. Na **distribuce** klikněte na tlačítko **přidat** pro **vybrané počítače** a přidejte k distribuci účtu na serveru pro správu.  Klikněte na tlačítko **OK** dvakrát tím uložíte provedené změny.
3. V části **konfigurace spustit jako**, klikněte na tlačítko **profily**.
4. Hledat *profilu posouzení SCOM*.
5. By měl být název profilu: *Posouzení SCOM Microsoft System Center Advisor profil spustit jako*.
6. Klikněte pravým tlačítkem myši a aktualizovat jeho vlastnosti a přidat naposledy vytvořený účet Spustit jako jste vytvořili dříve.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skript SQL pro udělovat různě odstupňovaná oprávnění k účtu spustit jako

Spusťte následující skript SQL udělit požadovaná oprávnění k účtu spustit jako v instanci SQL serveru hostující operační databázi nástroje Operations Manager, datový sklad a databáze služby ACS.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Konfigurace pravidla kontroly stavu

Sada management pack řešení kontroly systému System Center Operations Manager Health obsahují pravidlo s názvem *Microsoft System Center Advisor SCOM posouzení spustit posouzení pravidlo*. Toto pravidlo je zodpovědný za spouštění kontroly stavu. Následující postupy použijte, pokud chcete povolit pravidlo a nastavte četnost.

Microsoft System Center Advisor SCOM posouzení spustit posouzení pravidlo je ve výchozím nastavení zakázána. Pokud chcete spustit kontrolu stavu, je nutné povolit pravidlo na serveru pro správu. Pomocí následujícího postupu.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Povolení pravidla pro konkrétní server pro správu

1. V **Authoring** pracovního prostoru na konzole Operations Manageru, vyhledejte pravidlo *Microsoft System Center Advisor SCOM posouzení spustit posouzení pravidlo* v **pravidla** podokně.
2. Ve výsledcích hledání vyberte ten, který obsahuje text *typu: Server pro správu*.
3. Klikněte pravým tlačítkem na pravidlo a pak klikněte na tlačítko **přepíše** > **pro konkrétní objekt třídy: Server pro správu**.
4.  V seznamu serverů pro správu k dispozici vyberte ve kterém se má pravidlo spustit server pro správu.  To by měl být stejný server pro správu, který jste nakonfigurovali dříve přidružit účet Spustit jako s.
5.  Ujistěte se, že změníte hodnotu pro přepisování na **True** pro **povoleno** hodnotu parametru.<br><br> ![Přepište parametr](./media/scom-assessment/rule.png)

    Stále v tomto okně Konfigurovat frekvenci běhu pomocí dalšího postupu.

#### <a name="configure-the-run-frequency"></a>Konfigurovat frekvenci běhu

Hodnocení je ve výchozím nastavení nakonfigurované ke spuštění každých 10 080 minut (nebo sedm dní). Můžete přepsat hodnotu na minimální hodnotu 1 440 minut (nebo jeden den). Hodnota představuje minimální časové prodlevy mezi po sobě jdoucích posouzení spuštění vyžaduje. K přepsání intervalu, použijte následující postup.

1. V **Authoring** pracovního prostoru na konzole nástroje Operations Manager, vyhledejte pravidlo *Microsoft System Center Advisor SCOM posouzení spustit posouzení pravidlo* v **pravidla** oddíl.
2. Ve výsledcích hledání vyberte ten, který obsahuje text *typu: Server pro správu*.
3. Klikněte pravým tlačítkem na pravidlo a pak klikněte na tlačítko **přepsat pravidlo** > **pro všechny objekty třídy: Server pro správu**.
4. Změnit **Interval** hodnota parametru hodnotě požadované intervalu. V následujícím příkladu je hodnota nastavena na 1 440 minut (jeden den).<br><br> ![Parametr interval](./media/scom-assessment/interval.png)<br>  

    Pokud je hodnota nastavena na méně než 1 440 minut, se pravidlo spustí v intervalech jeden den. V tomto příkladu pravidlo ignoruje hodnota intervalu a běží s frekvencí jeden den.


## <a name="understanding-how-recommendations-are-prioritized"></a>Vysvětlení, jak mají určenou prioritu doporučení

Každé doporučení je přiřazena hodnota váhu, která identifikuje relativní důležitost doporučení. Zobrazí jenom 10 nejdůležitější doporučení.

### <a name="how-weights-are-calculated"></a>Jak se počítají váhy

Váhy jsou agregované hodnoty, které jsou založené na tři klíčové faktory:

- *Pravděpodobnost* , způsobí chyby zjištěné potíže. Vyšší pravděpodobnost rovná větší celkové skóre pro doporučení.
- *Dopad* problému ve vaší organizaci, pokud ho způsobovat problémy. Vyšší dopad odpovídá větší celkové skóre pro doporučení.
- *Úsilí* potřebnou k implementaci doporučení. Vyšší úsilí odpovídá menší celkové skóre pro doporučení.

Váhu pro jednotlivá doporučení je vyjádřené jako procentní podíl celkové skóre, které jsou k dispozici pro každou oblast zaměření. Například pokud doporučení v oblasti zaměření dostupnosti a kontinuity obchodních procesů má skóre % 5, implementaci tohoto doporučení zvýší vaše celkové skóre podle 5 % dostupnosti a kontinuity obchodních procesů.

### <a name="focus-areas"></a>Konkrétní oblasti

**Dostupnost a plynulost obchodních procesů** -tuto oblast zaměření ukazuje doporučení pro služby dostupnost, odolnost vaší infrastruktury a ochrany firmy.

**Výkon a škálovatelnost** – zobrazuje tuto oblast zaměření doporučení, která vaší organizaci pomoct IT infrastruktury růst, ujistěte se, že splňuje aktuální požadavky na výkon vašeho IT prostředí a je schopný reagovat na měnící se infrastruktury potřebuje.

**Upgrade, migrace a nasazení** – zobrazuje tuto oblast zaměření doporučení, která vám pomohou upgrade, migrace a nasazení systému SQL Server do vaší stávající infrastruktury.

**Operace a monitorování** – zobrazuje tuto oblast zaměření doporučení, která pomůžou zefektivnit vaše IT oddělení, implementovat preventivní údržby a maximalizovat výkon.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>By měl být cílem stanovení skóre 100 % v každé oblasti zaměření?

Ne nutně. Doporučení jsou založeny na znalosti a zkušenosti získané v Microsoftu přes návštěvě tisíců zákaznických. Ale žádné serverové infrastruktury jsou stejné, a konkrétní doporučení může být vyšší nebo nižší pro vás relevantní. Některá doporučení zabezpečení může být například méně relevantní, pokud vaše virtuální počítače nejsou zveřejněné na Internetu. Některá doporučení dostupnosti může být méně důležité pro služby, které poskytují shromažďování dat ad hoc s nízkou prioritou a generování sestav. Problémy, které jsou důležité pro až po zralé podnikání, může být méně důležité spouštěcí. Můžete určit, které konkrétní oblasti mají vašich priorit a podívejte se na tom, jak v průběhu času měnit skóre.

Každé doporučení obsahuje pokyny o tom, proč je důležité. Tyto pokyny slouží k vyhodnocení, zda implementace doporučení je vhodné, vzhledem k povaze služeb IT a obchodní potřeby vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Kontroly stavu použijte doporučení oblasti zaměření

Než ve službě Log Analytics můžete použít řešení kontroly stavu, musíte mít nainstalované řešení. Další informace o instalaci řešení, najdete v článku [nainstalovat řešení pro správu](../../azure-monitor/insights/solutions.md). Po jeho instalaci, můžete zobrazit souhrn doporučení s využitím kontroly systému System Center Operations Manager Health dlaždici na **přehled** stránku vašeho pracovního prostoru na webu Azure Portal.

Zobrazení posouzení souhrnné dodržování předpisů pro infrastrukturu a pak přejít k podrobnostem doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Zobrazit doporučení pro oblast zaměření a provedení nápravné akce
1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V podokně předplatná Log Analytics vyberte pracovní prostor a pak klikněte na tlačítko **shrnutí pracovního prostoru** položky nabídky.  
4. Na **přehled** stránky, klikněte na tlačítko **kontroly systému System Center Operations Manager Health** dlaždici.
5. Na **kontroly systému System Center Operations Manager Health** stránky, zkontrolujte souhrnné informace u některého okna oblasti zaměření a klikněte na z nich se má zobrazit doporučení pro tuto oblast zaměření.
6. Na žádném z oblasti stránek fokus můžete zobrazit prioritizovaných doporučení pro vaše prostředí. Kliknutím na doporučení v části **vliv na objekty** zobrazíte podrobnosti o tom, proč je provedeny doporučení.<br><br> ![oblast zaměření](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Můžete provést opravné akce navržený v **doporučené akce**. Pokud položka byla určena, zaznamená novější posouzení, které doporučené akce byly provedeny a zvýší vaše skóre dodržování předpisů. Opravené položky se zobrazí jako **předaný objekty**.

## <a name="ignore-recommendations"></a>Ignorujte doporučení

Pokud máte doporučení, která má být ignorována, můžete vytvořit textový soubor, který využívá Log Analytics, která brání doporučení povolí, nebude vaše výsledky posouzení.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>K identifikaci doporučení, která má být ignorována
1. Na portálu Azure na stránce pracovního prostoru Log Analytics pro váš vybraný pracovní prostor, klikněte na tlačítko **prohledávání protokolů** položky nabídky.
2. Následující dotaz tak, aby seznam doporučení, které se nepodařilo použijte pro počítače se ve vašem prostředí.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [dotazovací jazyk Log Analytics nové](../../azure-monitor/log-query/log-query-overview.md), pak se změní výše uvedeném dotazu následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Zde je snímek obrazovky zobrazující z dotazu prohledávání protokolu:<br><br> ![prohledávání protokolů](./media/scom-assessment/scom-log-search.png)<br>

3. Zvolte doporučení, která má být ignorována. V dalším postupu budete používat hodnoty pro ID doporučení.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt

1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložte nebo zadejte ID jednotlivých doporučení pro jednotlivá doporučení, který má Log Analytics ignorovat na samostatném řádku a potom uložte a zavřete soubor.
3. Uložte soubor v následující složce na každém počítači místo, kam chcete Log Analytics pro ignorování doporučení.
4. Na serveru pro správu nástroje Operations Manager - *systemdrive % musí být*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Chcete-li ověřit, že jsou ignorovány doporučení

1. Až dalšího naplánovaného spuštění posouzení ve výchozím nastavení každých sedm dní, zadaný doporučení jsou označeny ignorováno a nezobrazí se na řídicím panelu stavu zaškrtnutí.
2. Do seznamu ignorovaných doporučení můžete použít následující dotazy prohledávání protokolů.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [dotazovací jazyk Log Analytics nové](../../azure-monitor/log-query/log-query-overview.md), pak se změní výše uvedeném dotazu následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Pokud se později rozhodnete, že chcete zobrazit ignorované doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo RecommendationIDs můžete odebrat z nich.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Řešení System Center Operations Manager Health zkontrolujte systému – nejčastější dotazy

*Kontroly stavu řešení po přidání do pracovního prostoru Log Analytics. Ale nevidím žádná doporučení. Proč ne?* Po přidání řešení, použijte následující postup zobrazení doporučení na řídicím panelu Log Analytics.  

- [Nastavení účtu spustit jako pro kontroly systému System Center Operations Manager Health](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurace pravidla kontroly systému System Center Operations Manager Health](#configure-the-health-check-rule)


*Existuje způsob, jak konfigurovat, jak často neproběhne kontrola?* Ano. Zobrazit [konfigurovat frekvenci běhu](#configure-the-run-frequency).

*Pokud jiný server je zjištěno, po přidali jsme řešení System Center Operations Manager Assessment, budou se kontrolovat?* Ano, po zjištění, které od té chvíle se kontroluje ve výchozím nastavení každých sedm dní.

*Jaký je název procesu, která provádí sběr dat?* AdvisorAssessment.exe

*Pokud proces AdvisorAssessment.exe spustit?* AdvisorAssessment.exe běží v rámci procesu služby stavu serveru pro správu, kde je aktivováno pravidlo kontroly stavu. Pomocí tohoto procesu se prostřednictvím vzdálené shromažďování dat dosahuje zjišťování celého prostředí.

*Jak dlouho trvá shromažďování dat?* Shromažďování dat na serveru trvá přibližně jednu hodinu. Může trvat déle, v prostředích, která mají mnoho instancí nástroje Operations Manager nebo databáze.

*Co když mám nastavit interval posouzení na menší než 1 440 minut?* Hodnocení je nakonfigurovaná tak, aby spustit maximálně jednou za den. Pokud přepíšete hodnota intervalu na hodnotu menší než 1 440 minut, posouzení používá 1440 minut jako hodnota intervalu.

*Jak zjistit, pokud jsou požadované selhání?* Pokud se nezobrazí výsledky spuštění kontroly stavu, je pravděpodobné, že některé z požadovaných součástí pro kontrolu se nezdařilo. Při spouštění dotazů: `Operation Solution=SCOMAssessment` a `SCOMAssessmentRecommendation FocusArea=Prerequisites` v prohledávání protokolu zobrazíte neúspěšné požadavky.

*Je `Failed to connect to the SQL Instance (….).` zprávy požadavků selhání. V čem je problém?* AdvisorAssessment.exe, proces, který shromažďuje data, běží v rámci procesu služby Health Service na serveru pro správu. V rámci kontroly stavu se proces pokusí připojit k SQL serveru, kde je k dispozici databáze nástroje Operations Manager. Této chybě může dojít, když se pravidla brány firewall zablokovat připojení k instanci systému SQL Server.

*Jaký typ dat se shromažďují?* Následující typy dat se shromažďují: - dat služby WMI - registru dat – data protokolu událostí – dat nástroje Operations Manager pomocí prostředí Windows PowerShell, dotazů SQL a soubor informace kolekcí.

*Proč je nutné nakonfigurovat účet Spustit jako?* S nástrojem Operations Manager se spouštějí různé dotazy SQL. V pořadí jejich spuštění musíte použít účet Spustit jako s potřebnými oprávněními. Kromě toho je třeba dotaz WMI přihlašovací údaje místního správce.

*Proč zobrazit pouze prvních 10 doporučení?* Místo získáte vyčerpávající, náročný seznam úkolů, doporučujeme vám zaměřit nejprve adresování prioritizovaných doporučení. Po jejich řešení, budou k dispozici další doporučení. Pokud chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení, pomocí prohledávání protokolů.

*Existuje způsob, jak ignorujte doporučení?* Ano, najdete v článku [ignorujte doporučení](#Ignore-recommendations).


## <a name="next-steps"></a>Další postup

- [Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) informace o analýze podrobných dat o stavu systému System Center Operations Manager zkontrolujte a doporučení.
