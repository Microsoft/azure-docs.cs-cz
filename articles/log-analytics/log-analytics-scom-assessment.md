---
title: Optimalizace prostředí System Center Operations Manager s nástrojem Azure Log Analytics | Microsoft Docs
description: Řešení System Center Operations Manager Health kontrolovat můžete vyhodnotit stav prostředí a riziko v pravidelných intervalech.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 493fb0295c1d760bb7de13ae1b703b3636750612
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018708"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimalizace prostředí s nástrojem System Center Operations Manager Health kontrolovat (Preview) řešení

![System Center Operations Manager Health kontrolovat symbol](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

Řešení System Center Operations Manager Health kontrolovat můžete použít k vyhodnocení rizik a stavu skupiny pro správu System Center Operations Manager v pravidelných intervalech. Tento článek vám umožňuje instalovat, konfigurovat a používat řešení, takže můžete provést nápravné akce pro potenciální problémy.

Toto řešení poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serverů seřazený podle priority. Doporučení jsou rozdělené mezi čtyři konkrétní oblasti, které vám pomůžou rychle pochopit riziko a proveďte opravné akce.

Doporučení, která jsou založené na znalosti a zkušenosti technici Microsoft z tisíce zákazníka návštěvách. Každé doporučení obsahuje informace, proč může pro vás důležitá problém a implementaci navrhované změny.

Můžete vybrat konkrétní oblasti, které jsou důležité pro vaši organizaci a sledovat průběh směrem k spuštění prostředí riziko volné a v pořádku.

Poté, co jste přidali řešení a posouzení se provádí, souhrnné informace pro konkrétní oblasti je zobrazena na **System Center Operations Manager Health kontrolovat** řídicí panel pro vaši infrastrukturu. Následující části popisují, jak používat informace na **System Center Operations Manager Health kontrolovat** řídicí panel, kde můžete zobrazit a pak proveďte doporučené akce pro vaše prostředí nástroje Operations Manager.

![Dlaždice řešení System Center Operations Manager](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Health kontrolovat řídicí panel](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení

Toto řešení funguje s Microsoft systému Operations Manager 2012 Service Pack (SP) 1 a 2012 R2.

K instalaci a konfiguraci řešení můžete použít následující informace.

 - Než v analýzy protokolů můžete použít kontrolu stavu řešení, musíte mít nainstalován řešení. Instalaci řešení z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - Po přidání řešení do pracovního prostoru, **System Center Operations Manager Health kontrolovat** dlaždice na řídicím panelu zobrazí zprávu vyžaduje další konfiguraci. Klikněte na dlaždici a postupujte podle kroků konfigurace uvedených na stránce

 ![Dlaždice řídicího panelu System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguraci nástroje System Center Operations Manager lze provést pomocí skriptu podle kroků uvedených na stránce konfigurace řešení v analýzy protokolů.

 Konfigurace assessment prostřednictvím konzoly Operations Manager Operations, proveďte následující postup v následujícím pořadí:
1. [Nastavit účet Spustit jako pro System Center Operations Manager Health kontrolovat](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Konfigurace pravidla System Center Operations Manager Health kontrolovat](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Informace o System Center Operations Manager assessment datových kolekce

Hodnocení nástroje System Center Operations Manager shromažďuje data z následujících zdrojů:

* Registr
* Windows Management Instrumentation (WMI)
* V protokolu událostí
* Data souborů
* Přímo z nástroje Operations Manager pomocí prostředí PowerShell a SQL dotazů, ze serveru pro správu, který jste zadali.  

Data jsou shromažďována na serveru pro správu a předávány k analýze protokolů každých sedm dní.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty spustit jako nástroje Operations Manager pro analýzy protokolů

Log Analytics staví na sady management Pack pro úlohy zajistit přidanou hodnotou services. Každé zatížení vyžaduje určeného pro konkrétní úlohy oprávnění ke spouštění sad management Pack v odlišném kontextu zabezpečení, jako je například účet uživatele domény. Nakonfigurujte účet Operations Manager spustit jako s privilegovaným přihlašovacím údajům. Další informace najdete v tématu [jak vytvořit účet Spustit jako](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) v dokumentaci k nástroji Operations Manager.

K nastavení Operations Manager účet Spustit jako pro System Center Operations Manager Health kontrolovat, použijte následující informace.

### <a name="set-the-run-as-account"></a>Nastavit účet Spustit jako

Účet Spustit jako, musí splňovat následující požadavky, než budete pokračovat:

* Uživatelský účet domény, který je členem místní skupiny Administrators na všech serverech podporujících žádné roli nástroje Operations Manager – server pro správu, SQL Server, který je hostitelem provozní, datového skladu a databáze služby ACS, vytváření sestav, webovou konzolu a server brány.
* Role správce Manager operace pro skupinu pro správu probíhá vyhodnocení
* Pokud účet nemá oprávnění správce systému SQL, potom spusťte [skriptu](#sql-script-to-grant-granular-permissions-to-the-run-as-account) udělit oprávnění na podrobné úrovni k účtu na každou instanci serveru SQL, který je hostitelem jednoho nebo všech databází nástroje Operations Manager.

1. V konzole nástroje Operations Manager, vyberte **správy** navigační tlačítko.
2. V části **konfigurace spustit jako**, klikněte na tlačítko **účty**.
3. V **vytvořit účet Spustit jako** Průvodce na **ÚVOD** klikněte na stránce **Další**.
4. Na **obecné vlastnosti** vyberte **Windows** v **typ účtu spustit jako:** seznamu.
5. Zadejte zobrazovaný název v **zobrazovaného názvu** textové pole a volitelně zadejte popis do **popis** pole a pak klikněte na **Další**.
6. Na **zabezpečení distribuce** vyberte **bezpečnější**.
7. Klikněte na možnost **Vytvořit**.  

Teď, když je vytvořen účet Spustit jako, potřebuje k cílovým serverům správy ve skupině pro správu a přidružené předem definovaného profilu spustit jako, spustí pracovní postupy, pomocí přihlašovacích údajů.  

1. V části **konfigurace spustit jako**, **účty**, v podokně výsledků dvakrát klikněte na účet, který jste vytvořili dříve.
2. Na **distribuční** , klikněte na **přidat** pro **vybrané počítače** a přidejte pro tento účet distribuovat do serveru pro správu.  Klikněte na tlačítko **OK** dvakrát a uložit provedené změny.
3. V části **konfigurace spustit jako**, klikněte na tlačítko **profily**.
4. Vyhledejte *SCOM Assessment profil*.
5. Název profilu musí být: *Microsoft System Center Advisor SCOM Assessment profilu spustit jako*.
6. Klikněte pravým tlačítkem myši a aktualizujte jeho vlastnosti a přidejte naposledy vytvořený účet Spustit jako jste vytvořili dříve.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skript SQL k udělení oprávnění na podrobné úrovni k účtu spustit jako

Spusťte následující skript SQL udělit potřebná oprávnění k účtu spustit jako v instanci systému SQL Server používané Operations Manager, který je hostitelem provozní, datového skladu a databáze služby ACS.

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

Sada management pack řešení System Center Operations Manager Health kontrolovat zahrnuje na pravidlo s názvem *Microsoft System Center Advisor SCOM Assessment spustit Assessment pravidlo*. Toto pravidlo je zodpovědná za spuštění kontroly stavu. Následující postupy použijte, pokud chcete povolit pravidlo a nastavte četnost.

Microsoft System Center Advisor SCOM Assessment spustit Assessment pravidlo je ve výchozím nastavení zakázaná. Pokud chcete spustit kontrolu stavu, je nutné povolit pravidlo na serveru pro správu. Pomocí následujících kroků.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Povolení pravidla pro konkrétní management server

1. V **vytváření** prostoru Operations Manager Operations console, vyhledejte pravidlo *Microsoft System Center Advisor SCOM Assessment spustit Assessment pravidlo* v **pravidla** podokně.
2. Ve výsledcích hledání vyberte ten, který obsahuje text *typu: Server pro správu*.
3. Klikněte pravým tlačítkem na pravidlo a pak klikněte na **přepsání** > **pro konkrétní objekt třídy: Server pro správu**.
4.  V seznamu serverů pro správu k dispozici vyberte server pro správu kde se má pravidlo spustit.  To by měl být stejný server pro správu, které jste dříve nakonfigurovali pro přidružení k účtu spustit jako s.
5.  Ujistěte se, že změníte hodnotu přepsání **True** pro **povoleno** hodnota parametru.<br><br> ![Přepište parametr](./media/log-analytics-scom-assessment/rule.png)

    Během tohoto okna nakonfigurujte spuštění četnost pomocí dalšího postupu.

#### <a name="configure-the-run-frequency"></a>Nastavte četnost, spuštění

Hodnocení je ve výchozím nastavení nakonfigurované ke spuštění každých 10 080 minut (nebo sedm dní). Hodnota, která má minimální hodnotu 1 440 minut (nebo jeden den), můžete přepsat. Hodnota představuje minimální časové prodlevy požadované mezi následných vyhodnocení běží. K přepsání intervalu, použijte následující postup.

1. V **vytváření** prostoru konzoly nástroje Operations Manager, vyhledejte pravidlo *Microsoft System Center Advisor SCOM Assessment spustit Assessment pravidlo* v **pravidla** oddíl.
2. Ve výsledcích hledání vyberte ten, který obsahuje text *typu: Server pro správu*.
3. Klikněte pravým tlačítkem na pravidlo a pak klikněte na **přepsat pravidlo** > **pro všechny objekty třídy: Server pro správu**.
4. Změna **Interval** hodnota parametru hodnotu požadovaného intervalu pro. V následujícím příkladu je hodnota nastavena na 1 440 minut (jeden den).<br><br> ![Parametr interval](./media/log-analytics-scom-assessment/interval.png)<br>  

    Pokud je hodnota nastavena na méně než 1 440 minut, pravidlo spustí v intervalu za jeden den. V tomto příkladu pravidlo ignoruje hodnota intervalu a spustí frekvencí jeden den.


## <a name="understanding-how-recommendations-are-prioritized"></a>Pochopení, jak budou doporučení mít vyšší prioritu

Každé doporučení je zadána hodnota vyvážení, která určuje relativní důležitost doporučení. Jsou zobrazeny pouze 10 nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Jak jsou vypočítávány váhu

Váhy jsou agregovaných hodnot založena na tři klíčové faktory:

- *Pravděpodobnosti* způsobí, že problém identifikovat problémy. Vyšší pravděpodobnost rovná větší celkové skóre pro doporučení.
- *Dopad* problému na vaší organizaci, pokud ji způsobovat problémy. Vyšší dopad rovná větší celkové skóre pro doporučení.
- *Úsilí* potřebnou k implementaci doporučení. Vyšší úsilí rovná menší celkové skóre pro doporučení.

Vyvážení pro každé doporučení je vyjádřený jako procentní podíl celkové skóre, které jsou k dispozici pro každou oblast fokus. Například pokud doporučení v oblasti fokus dostupnost a provozní kontinuita je skóre % 5, implementace tímto doporučením zvyšuje celkové skóre podle 5 % dostupnost a provozní kontinuita.

### <a name="focus-areas"></a>Konkrétní oblasti

**Dostupnost a provozní kontinuita** – v tomto poli fokus zobrazí doporučení pro dostupnost služeb, odolnost vaší infrastruktury a obchodní ochrany.

**Výkon a škálovatelnost** – v tomto poli fokus zobrazí doporučení, která pomůžou vaší organizace IT infrastruktury růst, zajistěte, aby vaše IT prostředí splňuje aktuální požadavky na výkon a schopné reagovat na měnící se infrastruktury potřebuje.

**Nasazení, upgrade a migrace** – v tomto poli fokus zobrazí doporučení, která vám pomohou upgradovat, migrace a nasazení systému SQL Server k vaší stávající infrastruktury.

**Operace a monitorování** – v tomto poli fokus zobrazí doporučení, která pomůžou zjednodušení operací IT a implementaci preventivní údržby, maximalizovat výkon.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Mají usilovat o stanovení skóre 100 % v každé oblasti fokus?

Ne nutně. Doporučení jsou založené na prostředí, které nebyly získány prostřednictvím Microsoft technici napříč tisíce návštěvy zákazníka a znalostní báze. Ale žádné serverové infrastruktury jsou stejné, a konkrétní doporučení může být vyšší nebo nižší relevantní pro vás. Například může být několik doporučení zabezpečení méně důležité, pokud vaše virtuální počítače nejsou vystaveny v Internetu. Některá doporučení, jaké dostupnosti může být méně důležité pro služby, které poskytují kolekce s nízkou prioritou ad hoc dat a vytváření sestav. Problémy, které jsou důležité, abyste vyspělých firmy může být méně důležité Startup. Můžete určit, které konkrétní oblasti mají vašich priorit a podívejte se na tom, jak se vaše skóre časem změnit.

Každé doporučení obsahuje pokyny o tom, proč je důležité. Tyto pokyny slouží k vyhodnocení, zda implementace doporučení je vhodné pro vás, daná povaze vašich IT služeb a obchodním potřebám vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použití stavu zkontrolujte fokus oblasti doporučení

Než v analýzy protokolů můžete použít řešení kontrolu stavu, musíte mít nainstalován řešení. Další informace o instalaci řešení, najdete v části [instalaci řešení správy](log-analytics-add-solutions.md). Po instalaci, můžete zobrazit souhrn doporučení pomocí System Center Operations Manager Health kontrolovat dlaždice **přehled** stránky vašeho pracovního prostoru na portálu Azure.

Zobrazte vyhodnocování souhrnné dodržování předpisů pro infrastrukturu a potom přejít k podrobnostem doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Proveďte opravné akce a zobrazit doporučení pro oblastí zájmu
1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V podokně odběry analýzy protokolů vyberte pracovní prostor a klikněte **prostoru Souhrn** položku nabídky.  
4. Na **přehled** klikněte na tlačítko **System Center Operations Manager Health kontrolovat** dlaždici.
5. Na **System Center Operations Manager Health kontrolovat** zkontrolujte souhrnné informace v jednom z okna oblasti fokus a pak klikněte na jednu zobrazíte doporučení pro tuto oblast fokus.
6. Na všech stránkách oblasti fokus můžete zobrazit seřazený podle priority doporučení, která se pro vaše prostředí. Klikněte na tlačítko doporučení v části **vliv na objekty** Chcete-li zobrazit podrobnosti, proč se provádí doporučení.<br><br> ![oblastí zájmu](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Můžete provést nápravné akce navržený v **doporučované akce**. Když položka vyřeší, bude záznam novější vyhodnocování které doporučené akce provedené a zvýší se vaše skóre dodržování předpisů. Opravené položky se zobrazí jako **předán objekty**.

## <a name="ignore-recommendations"></a>Ignorovat doporučení

Pokud máte doporučení, které chcete ignorovat, můžete vytvořit textový soubor, který používá analýzy protokolů, aby doporučení ze storu ve výsledky hodnocení.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>K identifikaci doporučení, které chcete ignorovat
1. Na stránce pracovní prostor analýzy protokolů pro vaši vybranou pracovní prostor na portálu Azure, klikněte na tlačítko **hledání protokolů** položku nabídky.
2. Pro počítače ve vašem prostředí použijte následující dotaz, který seznam doporučení, které selhaly.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud pracovní prostor byl upgradován na verzi [nové analýzy protokolů dotazu jazyka](log-analytics-log-search-upgrade.md), pak výše uvedeném dotazu by změnit na následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Zde je snímek obrazovky zobrazující protokolu vyhledávací dotaz:<br><br> ![prohledávání protokolů](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. Zvolte doporučení, které chcete ignorovat. Hodnoty pro RecommendationId budete používat v dalším postupu.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytváření a používání textový soubor s IgnoreRecommendations.txt

1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložit nebo zadejte každou RecommendationId pro jednotlivá doporučení, které chcete analýzy protokolů ignorovat na samostatném řádku a potom uložte a zavřete soubor.
3. Uložte soubor v následující složce na každém počítači, kam chcete analýzy protokolů ignorovat doporučení.
4. Na serveru pro správu nástroje Operations Manager - *SystemDrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Chcete-li ověřit, že se ignorovat doporučení

1. Po na další naplánované vyhodnocení běží ve výchozím nastavení každých sedm dní, zadaný doporučení jsou označeny ignorovaná a nezobrazí se na řídicím panelu kontroly stavu.
2. Následující dotazy hledání protokolů můžete použít k zobrazení seznamu všech ignorováno doporučení.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Pokud pracovní prostor byl upgradován na verzi [nové analýzy protokolů dotazu jazyka](log-analytics-log-search-upgrade.md), pak výše uvedeném dotazu by změnit na následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Pokud se později rozhodnete, zda chcete zobrazit ignorováno doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo RecommendationIDs můžete odebrat z nich.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager Health kontrolovat řešení – nejčastější dotazy

*Kontrola stavu řešení po přidání do pracovního prostoru analýzy protokolů. Ale doporučení se nezobrazí. Proč ne?* Po přidání řešení, použijte následující postup zobrazení doporučení na řídicím panelu analýzy protokolů.  

- [Nastavit účet Spustit jako pro System Center Operations Manager Health kontrolovat](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurace pravidla System Center Operations Manager Health kontrolovat](#configure-the-health-check-rule)


*Existuje způsob, jak nakonfigurovat, jak často neproběhne kontrola?* Ano. V tématu [nakonfigurovat spuštění četnost](#configure-the-run-frequency).

*Pokud po byly přidány řešení System Center Operations Manager hodnocení je zjistit jiný server, budou se kontrolovat?* Ano, po zjišťování, který je ve výchozím nastavení každých sedm dní od toho zkontrolován.

*Jaký je název procesu, který nemá shromažďování dat?* AdvisorAssessment.exe

*Kde proces AdvisorAssessment.exe spustit?* AdvisorAssessment.exe spuštěna pod proces služby stavu serveru pro správu, kde je povolené pravidlo kontroly stavu. Pomocí tohoto procesu, zjišťování celé prostředí je dosaženo pomocí vzdálených dat kolekce.

*Jak dlouho trvá pro shromažďování dat?* Shromažďování dat na serveru trvá asi jednu hodinu. Může trvat déle v prostředích, která mají mnoho instancí nástroje Operations Manager nebo databáze.

*Co když lze nastavit interval hodnocení na méně než 1 440 minut?* Hodnocení je předem nakonfigurovaný pro spouštění na maximálně jednou za den. Pokud přepíšete hodnota intervalu na hodnotu menší než 1 440 minut, pak hodnocení používá jako hodnota intervalu 1 440 minut.

*Jak potřebujete vědět, pokud jsou požadované selhání?* Pokud byla spuštěna kontrola stavu, která se nezobrazí výsledky, je pravděpodobné, že některé požadované součásti pro kontrolu se nezdařilo. Můžete provést dotazy: `Operation Solution=SCOMAssessment` a `SCOMAssessmentRecommendation FocusArea=Prerequisites` v protokolu hledat neúspěšné požadavky.

*Je `Failed to connect to the SQL Instance (….).` zprávy v požadovaných selhání. Co je problém?* AdvisorAssessment.exe, proces, který shromažďuje data, běží v procesu služby Health Service na serveru pro správu. Jako součást kontrolou stavu proces pokusí připojit k systému SQL Server, kde databáze nástroje Operations Manager nachází. Této chybě může dojít, když pravidla brány firewall zablokovat připojení k instanci systému SQL Server.

*Jaký typ dat shromažďovaných?* Se shromažďují následující typy dat: - dat služby WMI – registru data – data protokolu událostí – Operations Manager data prostřednictvím prostředí Windows PowerShell, dotazy SQL a soubor informace kolekce.

*Proč je nutné nakonfigurovat účet Spustit jako?* S nástrojem Operations Manager se spouštějí různé dotazy SQL. Aby mohly spustit musíte použít účet Spustit jako s potřebnými oprávněními. Kromě toho je potřeba dotaz WMI přihlašovací údaje místního správce.

*Proč zobrazit pouze prvních 10 doporučení?* Namísto udělení vyčerpávající, čtenáře seznam úloh, doporučujeme, byste se zaměřit na první adresování seřazený podle priority doporučení. Po jejich řešení, bude k dispozici další doporučení. Pokud ji chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení pomocí hledání protokolů.

*Existuje způsob, jak ignorovat doporučení?* Ano, najdete v článku [ignorovat doporučení](#Ignore-recommendations).


## <a name="next-steps"></a>Další postup

- [V protokolech Hledat](log-analytics-log-searches.md) se dozvíte, jak analyzovat doporučení a podrobné údaje o stavu System Center Operations Manager kontrolovat.
