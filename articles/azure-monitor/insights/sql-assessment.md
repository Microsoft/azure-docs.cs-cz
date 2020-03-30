---
title: Optimalizace prostředí SQL Serveru pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Pomocí Azure Monitoru můžete použít řešení SQL Health Check k posouzení rizika a stavu vašeho prostředí v pravidelných intervalech.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/28/2019
ms.openlocfilehash: ceaed0800df01bf2c44fee13d98b01b6e726200d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662480"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimalizace prostředí SQL pomocí řešení KONTROLY stavu serveru SQL Server v Azure Monitoru

![Symbol kontroly stavu SQL](./media/sql-assessment/sql-assessment-symbol.png)

Řešení SQL Health Check můžete použít k posouzení rizika a stavu prostředí serveru v pravidelných intervalech. Tento článek vám pomůže nainstalovat řešení, takže můžete provést nápravná opatření pro potenciální problémy.

Toto řešení poskytuje seznam doporučení s prioritou specifických pro nasazenou serverovou infrastrukturu. Doporučení jsou rozdělena do šesti oblastí zájmu, které vám pomohou rychle pochopit riziko a přijmout nápravná opatření.

Uvedená doporučení jsou založena na znalostech a zkušenostech získaných inženýry společnosti Microsoft z tisíců návštěv zákazníků. Každé doporučení obsahuje pokyny o tom, proč vám problém může být důležité a jak implementovat navrhované změny.

Můžete zvolit oblasti fokusu, které jsou pro vaši organizaci nejdůležitější, a sledovat svůj pokrok směrem k provozu bezrizikového a zdravého prostředí.

Po přidání řešení a dokončení hodnocení se na řídicím panelu **kontroly stavu SQL** pro infrastrukturu ve vašem prostředí zobrazí souhrnné informace o prioritních oblastech. Následující části popisují, jak používat informace na řídicím panelu **kontroly stavu SQL,** kde můžete zobrazit a pak provést doporučené akce pro infrastrukturu serveru SQL Server.

![Obrázek dlaždice Kontrola stavu SQL](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![Obrázek řídicího panelu kontroly stavu SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Požadavky

* Řešení KONTROLY stavu SQL vyžaduje podporovanou verzi rozhraní .NET Framework 4.6.2 nainstalovanou v každém počítači s nainstalovaným agentem MMA (Microsoft Monitoring Agent).  Agent MMA používá System Center 2016 – Operations Manager a Operations Manager 2012 R2 a Azure Monitor.  
* Řešení podporuje SQL Server verze 2012, 2014 a 2016.
* Pracovní prostor Log Analytics pro přidání řešení SQL Health Check z webu Azure Marketplace na webu Azure Portal.  Chcete-li nainstalovat řešení, musíte být správcenebo přispěvatel v předplatném Azure.

  > [!NOTE]
  > Po přidání řešení je soubor AdvisorAssessment.exe přidán na servery s agenty. Konfigurační data se přečtou a pak se odesílají do Azure Monitoru v cloudu ke zpracování. Na přijatá data se aplikuje logika a cloudová služba data zaznamená.
  >
  >

Chcete-li provést kontrolu stavu na serverech SQL Server, vyžadují agenta a připojení k Azure Monitorpomocí jedné z následujících podporovaných metod:

1. Pokud server ještě není monitorován system center 2016 – Operations Manager nebo Operations Manager 2012 R2, nainstalujte [microsoft monitoring agent (MMA).](../../azure-monitor/platform/agent-windows.md)
2. Pokud je monitorován pomocí System Center 2016 - Operations Manager nebo Operations Manager 2012 R2 a skupina pro správu není integrovaná s Azure Monitor, server může být multi-homed s Log Analytics shromažďovat data a předávat do služby a stále být společností Operations Manager.  
3. V opačném případě, pokud je vaše skupina pro správu nástroje Operations Manager integrována se službou, je třeba po povolení řešení v pracovním prostoru přidat řadiče domény pro shromažďování dat službou podle kroků uvedených v části [Přidání počítačů spravovaných agenty.](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor)  

Agent na serveru SQL Server, který je podřízen skupině správy nástroje Operations Manager, shromažďuje data, předává na přiřazený server pro správu a pak se odesílá přímo ze serveru pro správu do služby Azure Monitor.  Data nejsou zapsána do databází nástroje Operations Manager.  

Pokud je SQL Server sledován nástrojoperations managerem, je třeba nakonfigurovat účet Operations Manager Run As. Další informace najdete níže v [tématu Run-as účty Operations Manager pro Azure Monitor.](#operations-manager-run-as-accounts-for-log-analytics)

## <a name="sql-health-check-data-collection-details"></a>Podrobnosti o shromažďování dat pro kontrolu stavu SQL
Kontrola stavu SQL shromažďuje data z následujících zdrojů pomocí agenta, který jste povolili:

* Windows Management Instrumentation (WMI)
* Registr
* Čítače výkonu
* Výsledky zobrazení dynamické správy serveru SQL Server

Data jsou shromažďována na serveru SQL Server a předávána do analýzy protokolů každých sedm dní.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty Operations Manager Spustit jako pro Log Analytics
Log Analytics používá agenta operations managera a skupinu pro správu ke shromažďování a odesílání dat do služby Log Analytics. Log Analytics staví na sadách management pack pro úlohy poskytovat služby s přidanou hodnotou. Každá úloha vyžaduje oprávnění specifická pro úlohy ke spuštění sad Management Pack v jiném kontextu zabezpečení, jako je například uživatelský účet domény. Je třeba zadat informace o pověření konfigurací účtu Spustit jako nástroj Operations Manager.

Následující informace slouží k nastavení účtu Spustit jako nástroj Operations Manager pro kontrolu stavu SQL.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Nastavení účtu Spustit jako pro kontrolu stavu SQL
 Pokud již používáte sadu management Pack serveru SQL Server, měli byste použít tuto konfiguraci Spustit jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Konfigurace účtu SPUSTIT Jako SQL v konzole Operations Console
> [!NOTE]
> Ve výchozím nastavení jsou pracovní postupy v rámci sady Management Pack spuštěny v kontextu zabezpečení účtu Místní systém. Pokud používáte agenta Microsoft Monitoring Agent připojený přímo ke službě, nikoli přímo do skupiny pro správu nástroje Operations Manager, přeskočte kroky 1-5 níže a spusťte ukázku T-SQL nebo PowerShell a zadejte nt authority\system jako uživatelské jméno.
>
>

1. V nástroji Operations Manager otevřete konzolu Operations Console a klepněte na tlačítko **Správa**.
2. V části **Spustit jako konfigurace**klepněte na **položku Profily**a otevřete **příkaz Sql Assessment Run As Profile**.
3. Na stránce **Účty Spustit jako** klikněte na tlačítko **Přidat**.
4. Vyberte účet Windows Run As, který obsahuje pověření potřebná pro SQL Server, nebo ho vytvořte klepnutím na **tlačítko Nový.**

   > [!NOTE]
   > Typ účtu Spustit jako musí být systém Windows. Účet Spustit jako musí být také součástí skupiny Místní správci na všech serverech Windows hostujících instance serveru SQL Server.
   >
   >
5. Klikněte na **Uložit**.
6. Upravte a potom spusťte následující ukázku T-SQL v každé instanci serveru SQL Server, abyste udělili minimální oprávnění požadovaná pro spuštění jako účet k provedení kontroly stavu. Však není nutné provést, pokud spustit jako účet je již součástí role serveru sysadmin na instancích serveru SQL Server.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Konfigurace účtu SPUSTIT Jako sql pomocí prostředí Windows PowerShell
Otevřete okno PowerShellu a po aktualizaci s informacemi spusťte následující skript:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Principy určování priority doporučení
Každé provedené doporučení má váhovou hodnotu, která identifikuje relativní důležitost doporučení. Je zobrazeno pouze deset nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Způsob počítání vah
Váhy jsou agregované hodnoty založené na třech klíčových faktorech:

* *Pravděpodobnost,* že zjištěný problém způsobí problémy. Vyšší pravděpodobnost se rovná větší celkové skóre pro doporučení.
* *Dopad* problému na vaši organizaci, pokud to způsobit problém. Vyšší dopad se rovná větší celkové skóre pro doporučení.
* *Úsilí* potřebné k provedení doporučení. Vyšší úsilí se rovná menší celkové skóre pro doporučení.

Váha každého doporučení je vyjádřena jako procento z celkového skóre, které je k dispozici pro každou zaostřovací oblast. Pokud má například doporučení v oblasti fokusu zabezpečení a dodržování předpisů skóre 5 %, implementace tohoto doporučení zvýší celkové skóre zabezpečení a dodržování předpisů o 5 %.

### <a name="focus-areas"></a>Oblasti zaměření
**Zabezpečení a dodržování předpisů** – tato oblast fokusu zobrazuje doporučení pro potenciální bezpečnostní hrozby a porušení, podnikové zásady a technické, právní a regulační požadavky na dodržování předpisů.

**Dostupnost a kontinuita provozu** – tato oblast fokus zobrazuje doporučení pro dostupnost služeb, odolnost infrastruktury a ochranu podniku.

**Výkon a škálovatelnost** – tato oblast fokusu zobrazuje doporučení, která pomáhají vaší organizaci růst infrastruktury IT, zajišťují, aby vaše IT prostředí splňovalo aktuální požadavky na výkon a bylo schopno reagovat na měnící se potřeby infrastruktury.

**Upgrade, migrace a nasazení** – tato oblast fokusu zobrazuje doporučení, která vám pomohou upgradovat, migrovat a nasadit SQL Server do stávající infrastruktury.

**Operace a monitorování** – tato oblast fokusu zobrazuje doporučení, která vám pomohou zefektivnit operace IT, implementovat preventivní údržbu a maximalizovat výkon.

**Správa změn a konfigurace** – tato oblast fokusu zobrazuje doporučení, která pomáhají chránit každodenní provoz, zajišťují, aby změny neměly negativní vliv na vaši infrastrukturu, zavedly postupy řízení změn a sledovaly a auditovaly konfigurace systému.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Měli byste v každé oblasti zaměření cílit na skóre 100 %?
Ne nutně. Doporučení jsou založena na znalostech a zkušenostech získaných inženýry společnosti Microsoft v tisících návštěv zákazníků. Žádné dvě serverové infrastruktury však nejsou stejné a konkrétní doporučení pro vás mohou být více či méně relevantní. Některá doporučení zabezpečení mohou být například méně relevantní, pokud vaše virtuální počítače nejsou vystaveny Internetu. Některá doporučení týkající se dostupnosti mohou být méně relevantní pro služby, které poskytují shromažďování a vykazování dat ad hoc s nízkou prioritou. Otázky, které jsou důležité pro vyspělý podnik, mohou být pro start-up méně důležité. Možná budete chtít určit, které oblasti zájmu jsou vaše priority a pak se podívat na to, jak se vaše skóre měnit v průběhu času.

Každé doporučení obsahuje pokyny, proč je důležité. Tyto pokyny byste měli použít k vyhodnocení, zda je implementace doporučení vhodná pro vás, vzhledem k povaze vašich it služeb a obchodním potřebám vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Využití doporučení k oblastem zaměření kontroly stavu
Než budete moci použít řešení hodnocení ve službě Azure Monitor, musíte mít nainstalované řešení.  Po instalaci můžete zobrazit souhrn doporučení pomocí dlaždice Kontrola stavu SQL na stránce **Přehled** pro Azure Monitor na webu Azure Portal.

Prohlédněte si souhrnné posouzení dodržování předpisů pro vaši infrastrukturu a potom projděte doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Chcete-li zobrazit doporučení pro zaostřovací oblast a přijmout nápravná opatření
1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Monitor**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Monitor**.
3. V části **Přehledy** v nabídce vyberte **Další**.  
4. Na stránce **Přehled** klikněte na dlaždici **Kontrola stavu SQL.**
5. Na stránce **Kontrola stavu** zkontrolujte souhrnné informace v jednom z fokusů a potom klepnutím na jednu z nich zobrazte doporučení pro tuto oblast fokusu.
6. Na libovolné stránce fokusové oblasti můžete zobrazit doporučení s prioritou pro vaše prostředí. Kliknutím na doporučení v části **Ovlivněné objekty** zobrazíte podrobnosti o tom, proč je doporučení provedeno.<br><br> ![Obrázek doporučení kontroly stavu SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Můžete provést nápravná opatření navržená v **části Navrhované akce**. Po vyřešení položky budou pozdější hodnocení zaznamenávat, že byla provedena doporučená opatření, a vaše skóre dodržování předpisů se zvýší. Opravené položky se zobrazí jako **předané objekty**.

## <a name="ignore-recommendations"></a>Ignorování doporučení
Pokud máte doporučení, která chcete ignorovat, můžete vytvořit textový soubor, který Azure Monitor použije, aby se zabránilo zobrazení doporučení ve výsledcích hodnocení.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Chcete-li identifikovat doporučení, která budete ignorovat
1. V nabídce Azure Monitor klikněte na **protokoly**.
2. Pomocí následujícího dotazu můžete zobrazit doporučení, která se nezdařila pro počítače ve vašem prostředí.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Zde je snímek obrazovky zobrazující dotaz protokolu:<br><br> ![neúspěšná doporučení](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Zvolte doporučení, která chcete ignorovat. Budete používat hodnoty pro RecommendationId v dalším postupu.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt
1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložte nebo zadejte každé DoporučeníId pro každé doporučení, které chcete Azure Monitor ignorovat na samostatném řádku a pak uložit a zavřít soubor.
3. Vložte soubor do následující složky v každém počítači, kde chcete Azure Monitor ignorovat doporučení.
   * V počítačích s agentem Microsoft Monitoring Agent (připojeným přímo nebo prostřednictvím nástroje Operations Manager) – *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Na serveru pro správu nástroje Operations Manager – *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serveru pro správu nástroje Operations Manager 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Ověření, že doporučení jsou ignorována
1. Po spuštění dalšího naplánovaného vyhodnocení jsou ve výchozím nastavení každých 7 dní označena zadaná doporučení ignorovaná a nezobrazí se na řídicím panelu hodnocení.
2. Pomocí následujících dotazů pro vyhledávání protokolů můžete zobrazit všechna ignorovaná doporučení.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Pokud se později rozhodnete, že chcete zobrazit ignorovaná doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo z nich můžete odebrat ID doporučení.

## <a name="sql-health-check-solution-faq"></a>Nejčastější dotazy k řešení kontroly stavu SQL

*Jaké kontroly provádí řešení SQL Assessment?*

* Následující dotaz zobrazuje popis všech aktuálně provedených kontrol:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Výsledky pak lze exportovat do aplikace Excel k dalšímu přezkoumání.


*Jak často se kontrola stavu běží?*

* Šek probíhá každých sedm dní.

*Existuje způsob, jak nastavit, jak často se kontrola spustí?*

* V tuto chvíli to není možné.

*Pokud je po přidání řešení kontroly stavu SQL zjištěn jiný server, bude zkontrolován?*

* Ano, jakmile je zjištěno, že je kontrolována od té doby, každých sedm dní.

*Pokud je server vyřazen z provozu, kdy bude odebrán z kontroly stavu?*

* Pokud server neodešle data po dobu 3 týdnů, je odebrán.

*Jaký je název procesu, který provádí shromažďování dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá, než budou údaje shromažďovány?*

* Skutečné shromažďování dat na serveru trvá přibližně 1 hodinu. Může trvat déle na serverech, které mají velký počet instancí SQL nebo databází.

*Jaký typ dat se shromažďuje?*

* Jsou shromažďovány následující typy dat:
  * Rozhraní WMI
  * Registr
  * Čítače výkonu
  * Zobrazení dynamické správy SQL (DMV).

*Existuje způsob, jak nastavit, kdy jsou data shromažďována?*

* V tuto chvíli to není možné.

*Proč musím nakonfigurovat účet Spustit jako?*

* Pro SQL Server je spuštěn malý počet dotazů SQL. Aby mohly být spuštěny, musí být použit a Spustit jako účet s oprávněními STAVU SERVERU VIEW PRO SQL.  Kromě toho jsou pro dotazování služby WMI vyžadována pověření místního správce.

*Proč zobrazit pouze 10 nejlepších doporučení?*

* Místo toho, abyste vám poskytli vyčerpávající ohromující seznam úkolů, doporučujeme, abyste se nejprve zaměřili na řešení prioritních doporučení. Po jejich řešení budou k dispozici další doporučení. Pokud dáváte přednost zobrazení podrobného seznamu, můžete zobrazit všechna doporučení pomocí vyhledávání protokolu Analytics.

*Existuje způsob, jak ignorovat doporučení?*

* Ano, viz [Ignorovat doporučení](#ignore-recommendations) část výše.

## <a name="next-steps"></a>Další kroky
* [Protokolujte dotazy,](../log-query/log-query-overview.md) abyste se dozvěděli, jak analyzovat podrobná data a doporučení kontroly stavu SQL.
