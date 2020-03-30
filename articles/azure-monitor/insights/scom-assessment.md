---
title: Posouzení Operations Manager system center pomocí Azure Monitor
description: Pomocí řešení Kontroly stavu nástroje System Center Operations Manager můžete v pravidelných intervalech vyhodnocovat rizika a stav prostředí.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 94251dfa2d9fa732912ed20d825e64f542d79188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055413"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimalizace prostředí s využitím řešení System Center Operations Manager Health Check (Preview)

![Symbol kontroly stavu nástroje Operations Manager nástroje System Center](./media/scom-assessment/scom-assessment-symbol.png)

Pomocí řešení Kontroly stavu nástroje System Center Operations Manager můžete v pravidelných intervalech vyhodnocovat rizika a stav skupiny pro správu nástroje System Center Operations Manager Operations Manager. Tento článek vám pomůže nainstalovat, nakonfigurovat a použít řešení, abyste mohli provést nápravná opatření pro potenciální problémy.

Toto řešení poskytuje seznam doporučení s prioritou specifických pro nasazenou serverovou infrastrukturu. Doporučení jsou rozdělena do kategorií do čtyř oblastí zájmu, které vám pomohou rychle pochopit riziko a přijmout nápravná opatření.

Uvedená doporučení jsou založena na znalostech a zkušenostech získaných inženýry společnosti Microsoft z tisíců návštěv zákazníků. Každé doporučení obsahuje pokyny o tom, proč vám problém může být důležité a jak implementovat navrhované změny.

Můžete zvolit oblasti fokusu, které jsou pro vaši organizaci nejdůležitější, a sledovat svůj pokrok směrem k provozu bezrizikového a zdravého prostředí.

Po přidání řešení a provedení hodnocení se na řídicím panelu **kontroly stavu nástroje System Center Operations Manager** pro vaši infrastrukturu zobrazí souhrnné informace o prioritních oblastech. Následující části popisují použití informací na řídicím panelu **kontroly stavu nástroje System Center Operations Manager,** kde můžete zobrazit a provést doporučené akce pro prostředí nástroje Operations Manager.

![Dlaždice řešení Nástroje pro operations manager systémového centra](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Řídicí panel kontroly stavu nástroje Operations Manager systémového centra](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení

Řešení spolupracuje s aktualizací Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager a Microsoft System Provozní manažer centra 1807. Na každém serveru pro správu musí být nainstalována podporovaná verze rozhraní .NET Framework 4.6.2.

K instalaci a konfiguraci řešení můžete použít následující informace.

- Před použitím řešení kontroly stavu v Log Analytics, musíte mít nainstalované řešení. Nainstalujte řešení z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Po přidání řešení do pracovního prostoru se na dlaždici **Kontrola stavu nástroje System Center Operations Manager** na řídicím panelu zobrazí zpráva s požadovanou další konfigurací. Klikněte na dlaždici a postupujte podle kroků konfigurace uvedených na stránce

  ![Dlaždice řídicího panelu Nástroje řízení nástroje Operations Manager systémového centra](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguraci nástroje System Center Operations Manager lze provést pomocí skriptu podle kroků uvedených na konfigurační stránce řešení v log analytics.

 Chcete-li nakonfigurovat hodnocení prostřednictvím konzoly Operations Console nástroje Operations Console, proveďte následující kroky v následujícím pořadí:
1. [Nastavení účtu Spustit jako pro kontrolu stavu nástroje Operations Manager nástroje System Center](#operations-manager-run-as-accounts-for-log-analytics)  
2. Konfigurace pravidla kontroly stavu nástroje System Center Operations Manager

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Podrobnosti o shromažďování dat pro System Center Operations Manager Health Check

Řešení Kontroly stavu nástroje System Center Operations Manager shromažďuje data z následujících zdrojů:

* Registr
* Windows Management Instrumentation (WMI)
* Protokol událostí
* Data souborů
* Přímo z Operations Manageru pomocí powershellových a SQL dotazů ze serveru pro správu, který jste zadali.  

Data jsou shromažďována na serveru pro správu a předávána do služby Log Analytics každých sedm dní.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty Operations Manager Spustit jako pro Log Analytics

Log Analytics staví na sadách management pack pro úlohy poskytovat služby s přidanou hodnotou. Každá úloha vyžaduje oprávnění specifická pro úlohy ke spuštění sad Management Pack v jiném kontextu zabezpečení, jako je například uživatelský účet domény. Nakonfigurujte účet Operations Manager Run As s privilegovanými pověřeními. Další informace naleznete v [tématu Jak vytvořit účet Spustit jako](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) v dokumentaci nástroje Operations Manager.

Následující informace slouží k nastavení účtu Spustit jako nástroj Operations Manager pro kontrolu stavu nástroje Operations Center Operations Manager.

### <a name="set-the-run-as-account"></a>Nastavení účtu Spustit jako

Účet Spustit jako musí splňovat následující požadavky, než budete pokračovat:

* Uživatelský účet domény, který je členem místní skupiny Administrators na všech serverech podporujících jakoukoli roli nástroje Operations Manager – server pro správu, sql server hostující provozní databázi, databázi datového skladu a služby ACS, vytváření sestav, webovou konzolu a server brány.
* Role správce nástroje Operation Manager pro hodnocenou skupinu pro správu
* Pokud účet nemá práva sql sysadmin, spusťte [skript](#sql-script-to-grant-granular-permissions-to-the-run-as-account) udělit podrobné oprávnění k účtu na každé instanci SQL Server hostování jedné nebo všech databází Operations Manager.

1. V konzole Operations Manager vyberte navigační tlačítko **Správa.**
2. V části **Spustit jako konfigurace**klepněte na **položku Účty**.
3. V Průvodci **vytvořením spustit jako účet** klikněte na stránce **Úvod** na **další**.
4. Na stránce **Obecné vlastnosti** vyberte **Windows** v seznamu **Typ spustit jako účet:** .
5. Do textového pole **Zobrazované jméno** zadejte zobrazovaný název, případně do pole **Popis** zadejte popis a klepněte na tlačítko **Další**.
6. Na stránce **Zabezpečení distribuce** vyberte **možnost Bezpečnější**.
7. Klikněte na **Vytvořit**.  

Nyní, když je vytvořen účet Spustit jako, musí cílit na servery pro správu ve skupině pro správu a je přidružen k předdefinovanému profilu Spustit jako, aby pracovní postupy běžely pomocí pověření.  

1. V části **Spustit jako konfigurace**, **Účty**v podokně výsledků poklikejte na účet, který jste vytvořili dříve.
2. Na kartě **Distribuce** klikněte na **tlačítko Přidat** pro pole **Vybrané počítače** a přidejte server pro správu, do který chcete účet distribuovat.  Chcete-li změny uložit, klepněte dvakrát na **tlačítko OK.**
3. V části **Spustit jako konfigurace**klepněte na **položku Profily**.
4. Vyhledejte *vyhotovení SCOM*.
5. Název profilu by měl být: *Kontrola stavu nástroje Microsoft System Center Operations Manager Spustit jako profil*.
6. Klikněte pravým tlačítkem myši na jeho vlastnosti a aktualizujte je a přidejte nedávno vytvořený účet Spustit jako, který jste vytvořili dříve.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skript SQL pro udělení podrobných oprávnění účtu Spustit jako

Spusťte následující skript SQL, který udělí požadovaná oprávnění účtu Spustit jako na instanci serveru SQL Server, kterou používá nástroj Operations Manager hostující provozní databázi, datový sklad a databázi Služby ACS.

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

Sada Management Pack řešení Kontroly stavu nástroje System Center Operations Manager obsahuje pravidlo s názvem *Pravidlo kontroly stavu nástroje Microsoft System Center Operations Manager Run*. Toto pravidlo je zodpovědný za spuštění kontroly stavu. Chcete-li povolit pravidlo a nakonfigurovat frekvenci, použijte níže uvedené postupy.

Ve výchozím nastavení je pravidlo kontroly stavu spustit spuštění nástroje Microsoft System Center Operations Manager. Chcete-li spustit kontrolu stavu, musíte povolit pravidlo na serveru pro správu. Použijte k tomu následující postup.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Povolení pravidla pro konkrétní server pro správu

1. V pracovním prostoru **vytváření** operační konzole Operations Manager vyhledejte pravidlo, že *nástroj Microsoft System Center Operations Manager spustí pravidlo kontroly stavu* v podokně **Pravidla.**
2. Ve výsledcích hledání vyberte ten, který obsahuje text *Typ: Server pro správu*.
3. Klepněte pravým tlačítkem myši na pravidlo a potom klepněte na příkaz **Přepsat** > **pro určitý objekt třídy: Server pro správu**.
4.  V seznamu dostupných serverů pro správu vyberte server pro správu, na kterém má být pravidlo spuštěno.  Měl by se jedná o stejný server pro správu, který jste nakonfigurovali dříve pro přidružení účtu Spustit jako.
5.  Ujistěte se, že změníte hodnotu přepsání na **Hodnotu True** pro hodnotu parametru **Enabled.**<br><br> ![přepsat parametr](./media/scom-assessment/rule.png)

    Ještě v tomto okně nakonfigurujte frekvenci spuštění pomocí dalšího postupu.

#### <a name="configure-the-run-frequency"></a>Konfigurace frekvence spouštění

Hodnocení je ve výchozím nastavení nakonfigurováno tak, aby se spouštěla každých 10 080 minut (nebo sedm dní). Hodnotu můžete přepsat na minimální hodnotu 1440 minut (nebo jeden den). Hodnota představuje minimální časovou mezeru požadovanou mezi po sobě jdoucími spuštěními hodnocení. Chcete-li interval přepsat, použijte následující postup.

1. V pracovním prostoru **vytváření** konzoly Operations Manager vyhledejte pravidlo, že nástroj *Microsoft System Center Operations Manager spustí pravidlo kontroly stavu* v části **Pravidla.**
2. Ve výsledcích hledání vyberte ten, který obsahuje text *Typ: Server pro správu*.
3. Klepněte pravým tlačítkem myši na pravidlo a potom **klepněte** > na příkaz Přepsat pravidlo**pro všechny objekty třídy: Server pro správu**.
4. Změňte hodnotu parametru **Interval** na požadovanou hodnotu intervalu. V níže uvedeném příkladu je hodnota nastavena na 1440 minut (jeden den).<br><br> ![intervalový parametr](./media/scom-assessment/interval.png)<br>  

    Pokud je hodnota nastavena na méně než 1440 minut, pravidlo běží v intervalu jednoho dne. V tomto příkladu pravidlo ignoruje hodnotu intervalu a běží na frekvenci jednoho dne.


## <a name="understanding-how-recommendations-are-prioritized"></a>Principy určování priority doporučení

Každé provedené doporučení má váhovou hodnotu, která identifikuje relativní důležitost doporučení. Je zobrazeno pouze 10 nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Způsob počítání vah

Váhy jsou agregované hodnoty založené na třech klíčových faktorech:

- *Pravděpodobnost,* že zjištěný problém způsobí problémy. Vyšší pravděpodobnost se rovná větší celkové skóre pro doporučení.
- *Dopad* problému na vaši organizaci, pokud to způsobit problém. Vyšší dopad se rovná větší celkové skóre pro doporučení.
- *Úsilí* potřebné k provedení doporučení. Vyšší úsilí se rovná menší celkové skóre pro doporučení.

Váha každého doporučení je vyjádřena jako procento z celkového skóre, které je k dispozici pro každou zaostřovací oblast. Pokud má například doporučení v oblasti zaměření dostupnosti a kontinuity provozu skóre 5 %, implementace tohoto doporučení zvýší celkové skóre dostupnosti a kontinuity provozu o 5 %.

### <a name="focus-areas"></a>Oblasti zaměření

**Dostupnost a kontinuita provozu** – tato oblast fokus zobrazuje doporučení pro dostupnost služeb, odolnost infrastruktury a ochranu podniku.

**Výkon a škálovatelnost** – tato oblast fokusu zobrazuje doporučení, která pomáhají vaší organizaci růst infrastruktury IT, zajišťují, aby vaše IT prostředí splňovalo aktuální požadavky na výkon a bylo schopno reagovat na měnící se potřeby infrastruktury.

**Upgrade, migrace a nasazení** – tato oblast fokusu zobrazuje doporučení, která vám pomohou upgradovat, migrovat a nasadit SQL Server do stávající infrastruktury.

**Operace a monitorování** – tato oblast fokusu zobrazuje doporučení, která vám pomohou zefektivnit operace IT, implementovat preventivní údržbu a maximalizovat výkon.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Měli byste v každé oblasti zaměření cílit na skóre 100 %?

Ne nutně. Doporučení jsou založena na znalostech a zkušenostech získaných inženýry společnosti Microsoft v tisících návštěv zákazníků. Žádné dvě serverové infrastruktury však nejsou stejné a konkrétní doporučení pro vás mohou být více či méně relevantní. Některá doporučení zabezpečení mohou být například méně relevantní, pokud vaše virtuální počítače nejsou vystaveny Internetu. Některá doporučení týkající se dostupnosti mohou být méně relevantní pro služby, které poskytují shromažďování a vykazování dat ad hoc s nízkou prioritou. Otázky, které jsou důležité pro vyspělý podnik, mohou být pro start-up méně důležité. Možná budete chtít určit, které oblasti zájmu jsou vaše priority a pak se podívat na to, jak se vaše skóre měnit v průběhu času.

Každé doporučení obsahuje pokyny, proč je důležité. Pomocí těchto pokynů můžete vyhodnotit, zda je implementace doporučení vhodná pro vás, vzhledem k povaze vašich IT služeb a obchodním potřebám vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použití doporučení pro kontrolu stavu fokusu

Před použitím řešení kontroly stavu v Log Analytics, musíte mít nainstalované řešení. Další informace o instalaci řešení naleznete [v tématu Instalace řešení pro správu](../../azure-monitor/insights/solutions.md). Po instalaci můžete zobrazit souhrn doporučení pomocí dlaždice Kontrola stavu nástroje System Center Operations Manager na stránce **Přehled** pracovního prostoru na portálu Azure.

Prohlédněte si souhrnné posouzení dodržování předpisů pro vaši infrastrukturu a potom projděte doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Chcete-li zobrazit doporučení pro zaostřovací oblast a přijmout nápravná opatření
1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V podokně Odběry Log Analytics vyberte pracovní prostor a klikněte na položku **nabídky Souhrn pracovního prostoru.**  
4. Na stránce **Přehled** klikněte na dlaždici **Kontrola stavu nástroje System Center Operations Manager.**
5. Na stránce **Kontrola stavu nástroje System Center Operations Manager** zkontrolujte souhrnné informace v jedné z předsunutí a klepnutím na jednu z nich zobrazte doporučení pro tuto oblast fokusu.
6. Na libovolné stránce fokusové oblasti můžete zobrazit doporučení s prioritou pro vaše prostředí. Kliknutím na doporučení v části **Ovlivněné objekty** zobrazíte podrobnosti o tom, proč je doporučení provedeno.<br><br> ![zaostřovací oblast](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Můžete provést nápravná opatření navržená v **části Navrhované akce**. Po vyřešení položky budou pozdější hodnocení zaznamenávat, že byla provedena doporučená opatření, a vaše skóre dodržování předpisů se zvýší. Opravené položky se zobrazí jako **předané objekty**.

## <a name="ignore-recommendations"></a>Ignorování doporučení

Pokud máte doporučení, která chcete ignorovat, můžete vytvořit textový soubor, který používá Log Analytics zabránit doporučení z zobrazí ve výsledcích hodnocení.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Identifikace doporučení, která chcete ignorovat
1. Na portálu Azure na stránce pracovního prostoru Log Analytics pro vybraný pracovní prostor klikněte na položku nabídky **Hledání protokolu.**
2. Pomocí následujícího dotazu můžete zobrazit doporučení, která se nezdařila pro počítače ve vašem prostředí.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [nový dotazovací jazyk Log Analytics](../../azure-monitor/log-query/log-query-overview.md), výše uvedený dotaz by se změnil na následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Zde je snímek obrazovky zobrazující dotaz pro vyhledávání protokolů:<br><br> ![prohledávání protokolů](./media/scom-assessment/scom-log-search.png)<br>

3. Zvolte doporučení, která chcete ignorovat. Budete používat hodnoty pro RecommendationId v dalším postupu.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt

1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložit nebo zadejte každý RecommendationId pro každé doporučení, které chcete Log Analytics ignorovat na samostatném řádku a pak uložit a zavřít soubor.
3. Vložte soubor do následující složky v každém počítači, ve kterém má služba Log Analytics ignorovat doporučení.
4. Na serveru pro správu nástroje Operations Manager - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Ověření, že doporučení jsou ignorována

1. Po spuštění dalšího naplánovaného hodnocení jsou ve výchozím nastavení každých sedm dní označena zadaná doporučení ignorovaná a nezobrazí se na řídicím panelu kontroly stavu.
2. Pomocí následujících dotazů pro vyhledávání protokolů můžete zobrazit všechna ignorovaná doporučení.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [nový dotazovací jazyk Log Analytics](../../azure-monitor/log-query/log-query-overview.md), výše uvedený dotaz by se změnil na následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Pokud se později rozhodnete, že chcete zobrazit ignorovaná doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo z nich můžete odebrat ID doporučení.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Nejčastější dotazy k řešení System Center Operations Manager Health Check

*Do pracovního prostoru Analýzy protokolů jsem přidal řešení kontroly stavu. Ale nevidím doporučení. Proč ne?* Po přidání řešení použijte následující kroky zobrazení doporučení na řídicím panelu Log Analytics.  

- [Nastavení účtu Spustit jako pro kontrolu stavu nástroje Operations Manager nástroje System Center](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurace pravidla kontroly stavu nástroje System Center Operations Manager](#configure-the-health-check-rule)


*Existuje způsob, jak nastavit, jak často se kontrola spustí?* Ano. Viz [Konfigurace frekvence spuštění](#configure-the-run-frequency).

*Pokud je po přidání řešení kontroly stavu nástroje System Center Operations Manager zjištěn jiný server, bude zkontrolován?* Ano, po zjištění je kontrolována od té doby, ve výchozím nastavení každých sedm dní.

*Jaký je název procesu, který provádí shromažďování dat?* AdvisorAssessment.exe

*Kde se spustí proces AdvisorAssessment.exe?* Program AdvisorAssessment.exe je spuštěn v rámci procesu HealthService serveru pro správu, kde je povoleno pravidlo kontroly stavu. Pomocí tohoto procesu je zjišťování celého prostředí dosaženo prostřednictvím vzdáleného shromažďování dat.

*Jak dlouho trvá shromažďování dat?* Sběr dat na serveru trvá přibližně jednu hodinu. Může trvat déle v prostředích, která mají mnoho instancí nebo databází nástroje Operations Manager.

*Co když nastavím interval hodnocení na méně než 1440 minut?* Hodnocení je předem nakonfigurováno tak, aby běželo maximálně jednou denně. Pokud přepíšete hodnotu intervalu na hodnotu menší než 1440 minut, použije hodnocení jako hodnotu intervalu hodnotu 1440 minut.

*Jak zjistit, zda existují předpoklady selhání?* Pokud byla kontrola stavu spuštěna a nevidíte výsledky, je pravděpodobné, že některé požadavky pro kontrolu se nezdařily. Můžete spouštět `Operation Solution=SCOMAssessment` dotazy: a `SCOMAssessmentRecommendation FocusArea=Prerequisites` ve vyhledávání protokolu zobrazíte neúspěšné požadavky.

*V předpokladech `Failed to connect to the SQL Instance (….).` je zpráva. V čem je problém?* AdvisorAssessment.exe, proces, který shromažďuje data, běží v rámci procesu HealthService na serveru pro správu. V rámci kontroly stavu se proces pokusí připojit k serveru SQL Server, kde je k dispozici databáze Nástroje operations manageru. K této chybě může dojít, když pravidla brány firewall blokují připojení k instanci serveru SQL Server.

*Jaký typ dat se shromažďuje?* Shromažďují se následující typy dat: - Data služby WMI – data registru – data eventlogu – data nástroje Operations Manager prostřednictvím prostředí Windows PowerShell, dotazy SQL a kolekcí informací o souborech.

*Proč musím nakonfigurovat účet Spustit jako?* S Operations Manager, různé dotazy SQL jsou spuštěny. Aby mohly být spuštěny, musíte použít spustit jako účet s potřebnými oprávněními. Kromě toho jsou k dotazování služby WMI vyžadována pověření místního správce.

*Proč zobrazit pouze 10 nejlepších doporučení?* Místo toho, abyste vám poskytli vyčerpávající a ohromující seznam úkolů, doporučujeme, abyste se nejprve zaměřili na řešení prioritních doporučení. Po jejich řešení budou k dispozici další doporučení. Pokud dáváte přednost zobrazení podrobného seznamu, můžete zobrazit všechna doporučení pomocí hledání protokolu.

*Existuje způsob, jak ignorovat doporučení?* Ano, viz [Ignorovat doporučení](#ignore-recommendations).


## <a name="next-steps"></a>Další kroky

- [Protokoly hledání se dozvíte,](../../azure-monitor/log-query/log-query-overview.md) jak analyzovat podrobná data a doporučení nástroje System Center Operations Manager health Check.
