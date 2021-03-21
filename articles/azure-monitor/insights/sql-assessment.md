---
title: Optimalizujte SQL Server prostředí pomocí Azure Monitor | Microsoft Docs
description: Pomocí Azure Monitor můžete řešení pro kontrolu stavu SQL použít k vyhodnocení rizik a stavu prostředí v pravidelných intervalech.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 0f2319ea6ba314c08a67651667837f05df5765a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723227"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimalizujte prostředí SQL pomocí řešení pro kontrolu stavu SQL Server v Azure Monitor

![Symbol kontroly stavu SQL](./media/sql-assessment/sql-assessment-symbol.png)

Pomocí řešení pro kontrolu stavu SQL můžete vyhodnotit rizika a stav prostředí serveru v pravidelných intervalech. Tento článek vám pomůže s instalací řešení, abyste mohli provádět nápravné akce pro potenciální problémy.

Toto řešení poskytuje prioritní seznam doporučení specifických pro vaši nasazenou serverovou infrastrukturu. Doporučení jsou rozdělená do šesti oblastí s fokusem, které vám pomůžou rychle pochopit rizika a provádět nápravná opatření.

Doporučení jsou založená na znalostech a zkušenostech získaných odborníky Microsoftu z tisíců návštěv zákazníků. Každé doporučení poskytuje pokyny týkající se toho, proč k problému může dojít, a postup implementace navrhovaných změn.

Můžete vybrat oblasti, které jsou pro vaši organizaci nejdůležitější, a sledovat svůj pokrok směrem k provozu bezplatného a zdravého prostředí.

Po přidání řešení a dokončení posouzení se souhrnné informace pro oblasti výběru zobrazí na řídicím panelu **kontroly stavu SQL** pro infrastrukturu ve vašem prostředí. Následující části popisují, jak používat informace na řídicím panelu pro **kontrolu stavu SQL** , kde můžete zobrazit a následně provést doporučené akce pro infrastrukturu SQL Server.

![Obrázek dlaždice kontroly stavu SQL](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![Obrázek řídicího panelu pro kontrolu stavu SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Předpoklady

* Řešení kontroly stavu SQL vyžaduje, aby na každém počítači s nainstalovanou Microsoft Monitoring Agent (MMA) byla nainstalovaná podporovaná verze .NET Framework 4.6.2.  MMA agent používá System Center 2016-Operations Manager a Operations Manager 2012 R2 a Azure Monitor.  
* Řešení podporuje SQL Server verze 2012, 2014, 2016, 2017 a 2019.
* Pracovní prostor Log Analytics pro přidání řešení kontroly stavu SQL z webu Azure Marketplace v Azure Portal. Abyste mohli řešení nainstalovat, musíte být správce nebo přispěvatel v rámci předplatného Azure.

  > [!NOTE]
  > Po přidání řešení se soubor AdvisorAssessment.exe přidá na servery s agenty. Konfigurační data se čtou a pak se odesílají do Azure Monitor v cloudu ke zpracování. Na přijatá data se aplikuje logika a cloudová služba data zaznamená.
  >
  >

Chcete-li provést kontrolu stavu na serverech SQL Server, vyžadují agenta a připojení k Azure Monitor pomocí jedné z následujících podporovaných metod:

1. Nainstalujte [Microsoft Monitoring Agent (MMA)](../agents/agent-windows.md) , pokud server ještě není monitorovaný pomocí nástroje System Center 2016-Operations Manager nebo Operations Manager 2012 R2.
2. Pokud je monitorovaná pomocí nástroje System Center 2016-Operations Manager nebo Operations Manager 2012 R2 a skupina pro správu není integrována s Azure Monitor, může být server s využitím více domovských Log Analytics pro shromažďování dat a přeposílání do služby a nadále je monitorován pomocí Operations Manager.  
3. V opačném případě, pokud je vaše skupina pro správu Operations Manager integrovaná se službou, je nutné přidat řadiče domény pro shromažďování dat službou podle kroků v části [Přidání počítačů spravovaných agentem](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) po povolení řešení ve vašem pracovním prostoru.  

Agent na vašem SQL Server, který hlásí skupinu pro správu Operations Manager, shromažďuje data, přechází na přiřazenou management server a pak se pošle přímo z management server do Azure Monitor.  Data nejsou zapsána do databází Operations Manager.  

Pokud je SQL Server monitorovaná pomocí Operations Manager, je třeba nakonfigurovat účet Spustit jako Operations Manager. Další informace najdete v tématu [Operations Manager účty Spustit jako pro Azure monitor](#operations-manager-run-as-accounts-for-log-analytics) níže.

## <a name="sql-health-check-data-collection-details"></a>Podrobnosti o shromažďování dat pro kontrolu stavu SQL
Při kontrole stavu SQL se shromažďují data z následujících zdrojů pomocí agenta, který jste povolili:

* Windows Management Instrumentation (WMI)
* Registr
* Čítače výkonu
* Výsledky zobrazení dynamické správy SQL Server

Data se shromažďují na SQL Server a předají se Log Analytics každých 7 dní.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty Operations Manager Spustit jako pro Log Analytics
Log Analytics používá agenta Operations Manager a skupinu pro správu ke shromažďování a odesílání dat do služby Log Analytics. Log Analytics sestaví na sady Management Pack pro úlohy, aby poskytovaly služby přidaných hodnot. Každé zatížení vyžaduje oprávnění pro spouštění sad Management Pack v jiném kontextu zabezpečení, jako je například účet uživatele domény. Přihlašovací údaje musíte zadat konfigurací účtu Spustit jako Operations Manager.

Pomocí následujících informací nastavte účet Operations Manager spustit jako pro kontrolu stavu SQL.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Nastavení účtu Spustit jako pro kontrolu stavu SQL
 Pokud již používáte Management Pack SQL Server, měli byste použít tuto konfiguraci spustit jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Konfigurace účtu Spustit jako pro SQL v konzole Operations Console
> [!NOTE]
> Ve výchozím nastavení jsou pracovní postupy ve Management Pack spouštěny v kontextu zabezpečení místního systémového účtu. Pokud místo vytváření sestav přímo do skupiny pro správu Operations Manager používáte Microsoft Monitoring Agent připojené přímo k této službě, přeskočte kroky 1-5 níže a spusťte buď ukázku T-SQL nebo PowerShell, jako uživatelské jméno určete NT AUTHORITY\SYSTEM.
>
>

1. V Operations Manager otevřete konzoli Operations Console a klikněte na **Správa**.
2. V části **Konfigurace spustit jako** klikněte na **profily** a otevřete **SQL Assessment profil spustit jako**.
3. Na stránce **Účty Spustit jako** klikněte na tlačítko **Přidat**.
4. Vyberte účet Spustit jako pro Windows, který obsahuje přihlašovací údaje potřebné pro SQL Server, nebo klikněte na **Nový** a vytvořte si ho.

   > [!NOTE]
   > Typ účtu Spustit jako musí být Windows. Účet Spustit jako musí být také součástí místní skupiny správců na všech serverech se systémem Windows, které jsou hostiteli SQL Server instance.
   >
   >
5. Klikněte na **Uložit**.
6. Úpravou a následným spuštěním následující ukázky T-SQL na každé instanci SQL Server udělte minimální oprávnění požadovaná pro účet Spustit jako, aby bylo možné provést kontrolu stavu. Nemusíte to ale dělat, pokud už účet Spustit jako je součástí role serveru sysadmin na instancích SQL Server.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Konfigurace účtu Spustit jako pro SQL pomocí Windows PowerShellu
Otevřete okno PowerShellu a po jeho aktualizaci s vašimi informacemi spusťte následující skript:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Principy určování priority doporučení
Každé provedené doporučení je předána hodnota váhy, která identifikuje relativní důležitost doporučení. Zobrazují se jenom deset nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Způsob počítání vah
Váhy jsou agregované hodnoty založené na třech klíčových faktorech:

* *Pravděpodobnost* , že zjištěná chyba způsobí problémy. Vyšší pravděpodobnost odpovídá většímu celkovému skóre pro doporučení.
* *Dopad* problému ve vaší organizaci, pokud dojde k problému. Vyšší dopad odpovídá většímu celkovému skóre pro doporučení.
* *Úsilí* potřebné k implementaci doporučení. Vyšší úsilí se rovná menšímu celkovému skóre pro doporučení.

Váha pro každé doporučení se vyjádří jako procento z celkového skóre dostupného pro každou oblast zaměření. Pokud například doporučení v oblasti zaměření zabezpečení a dodržování předpisů má skóre 5%, bude implementace tohoto doporučení zvyšovat celkové skóre zabezpečení a dodržování předpisů o 5%.

### <a name="focus-areas"></a>Oblasti zaměření
**Zabezpečení a dodržování předpisů** – tato oblast pro výběr obsahuje doporučení pro potenciální bezpečnostní hrozby a porušení zabezpečení, podnikové zásady a požadavky na dodržování předpisů v souladu s právními předpisy.

**Dostupnost a provozní kontinuita** – tato oblast pro výběr zobrazuje doporučení pro dostupnost služby, odolnost vaší infrastruktury a obchodní ochranu.

**Výkon a škálovatelnost** – tato oblast pro výběr obsahuje doporučení, která vám pomohou zvýšit infrastrukturu IT vaší organizace, zajistit, že vaše IT prostředí splňuje aktuální požadavky na výkon a je schopné reagovat na měnící se potřeby infrastruktury.

**Upgrade, migrace a nasazení** – tato oblast pro výběr vám ukáže doporučení, která vám pomůžou při upgradu, migraci a nasazování SQL Server do vaší stávající infrastruktury.

**Operace a monitorování** – tato oblast pro výběr obsahuje doporučení, která vám pomůžou zjednodušit vaše IT operace, implementovat preventivní údržbu a maximalizovat výkon.

**Správa změn a konfigurace** – tato oblast pro výběr obsahuje doporučení, která vám pomůžou chránit každodenní operace. Zajistěte, aby změny nepříznivě ovlivnily vaši infrastrukturu, navázaly řízení změn a sledovaly a ověřovaly konfigurace systému.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Měli byste v každé oblasti zaměření cílit na skóre 100 %?
Ne nutně. Doporučení jsou založená na znalostech a zkušenostech získaných odborníky Microsoftu v různých tisících návštěv zákazníků. Nicméně žádné dvě serverové infrastruktury nejsou stejné a specifická doporučení můžou být pro vás méně důležitá. Některá doporučení zabezpečení mohou být například méně důležitá, pokud vaše virtuální počítače nejsou vystaveny pro Internet. Některá doporučení k dostupnosti mohou být méně důležitá pro služby, které poskytují nízkou prioritu shromažďování a generování dat ad hoc. Problémy, které jsou důležité pro vyspělou firmu, můžou být pro spuštění méně důležité. Možná budete chtít určit, které oblasti fokusu jsou vašimi prioritami, a pak se podívat, jak se vaše skóre v průběhu času mění.

Každé doporučení obsahuje pokyny k tomu, proč je důležité. Tyto pokyny byste měli použít k vyhodnocení, jestli je implementace doporučení vhodná pro vás, a to s ohledem na povahu vašich IT služeb a obchodních potřeb vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Využití doporučení k oblastem zaměření kontroly stavu
Než budete moct použít řešení posouzení v Azure Monitor, musíte mít nainstalované řešení.  Po instalaci můžete zobrazit souhrn doporučení pomocí dlaždice pro kontrolu stavu SQL na stránce **Přehled** pro Azure Monitor v Azure Portal.

Podívejte se na souhrnná vyhodnocení dodržování předpisů pro vaši infrastrukturu a pak na doporučení pro přechod k podrobnostem.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Chcete-li zobrazit doporučení pro oblast zaměření a provést nápravná opatření
1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Monitor**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Monitor**.
3. V části **přehledy** v nabídce vyberte **Další**.  
4. Na stránce **Přehled** klikněte na dlaždici **kontroly stavu SQL** .
5. Na stránce **Kontrola stavu** zkontrolujte souhrnné informace v jednom z oken detailní oblasti a potom klikněte na jednu pro zobrazení doporučení pro tuto oblast výběru.
6. Na kterékoli stránce oblasti pro zaměření si můžete prohlédnout doporučení s prioritou vytvořená pro vaše prostředí. Kliknutím na doporučení v části **Ovlivněné objekty** zobrazíte podrobnosti o tom, proč se doporučení udělalo.<br><br> ![Obrázek doporučení pro kontrolu stavu SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. V **navrhovaných akcích** můžete provést opravné akce. Po vyřešení této položky budou později vyhodnoceny záznamy o tom, že byly provedeny doporučené akce a že se bude zvyšovat skóre dodržování předpisů. Opravené položky se zobrazí jako **předané objekty**.

## <a name="ignore-recommendations"></a>Ignorování doporučení
Pokud máte doporučení, která chcete ignorovat, můžete vytvořit textový soubor, který Azure Monitor použít k tomu, aby se předešlo zobrazování doporučení ve výsledcích hodnocení.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Určení doporučení, která budete ignorovat
1. V nabídce Azure Monitor klikněte na **protokoly**.
2. K vypsání doporučení, která se pro počítače ve vašem prostředí nezdařila, použijte následující dotaz.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Tady je snímek obrazovky, který ukazuje dotaz protokolu:<br><br> ![neúspěšná doporučení](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Vyberte doporučení, která chcete ignorovat. V dalším postupu použijete hodnoty pro RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt
1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložte nebo zadejte jednotlivé RecommendationIdy pro každé doporučení, které chcete, Azure Monitor ignorovat na samostatném řádku a pak soubor uložte a zavřete.
3. Uložte soubor do následující složky na každém počítači, na kterém chcete ignorovat doporučení Azure Monitor.
   * V počítačích s Microsoft Monitoring Agent (připojené přímo nebo prostřednictvím Operations Manager) – *systémová_jednotka*: \Program Files\Microsoft monitoring Agent\Agent
   * Na Operations Manager management server- *systémová_jednotka*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * V Operations Manager 2016 management server- *systémová_jednotka*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Ověření, že se doporučení ignorují
1. Po dalším naplánovaném spuštění hodnocení se ve výchozím nastavení každých 7 dnů budou uvedená doporučení ignorovat a nezobrazí se na řídicím panelu hodnocení.
2. Pomocí následujících vyhledávacích dotazů protokolu můžete zobrazit seznam všech ignorovaných doporučení.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Pokud se později rozhodnete, že chcete zobrazit ignorovaná doporučení, odeberte všechny IgnoreRecommendations.txt soubory nebo z nich můžete odebrat RecommendationIDs.

## <a name="sql-health-check-solution-faq"></a>Nejčastější dotazy k řešení kontroly stavu SQL

*Jaké kontroly provádí řešení SQL Assessment?*

* Následující dotaz ukazuje popis všech aktuálně prováděných kontrol:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Výsledky pak lze exportovat do aplikace Excel pro další kontrolu.


*Jak často se spouští kontroly stavu?*

* Tato kontroler se spustí každých 7 dní.

*Existuje způsob, jak nakonfigurovat četnost spouštění kontroly?*

* V tuto chvíli to není možné.

*Pokud je po přidání řešení pro kontrolu stavu SQL zjištěn jiný server, bude zkontrolován?*

* Ano, jakmile je zjištěno, že se bude kontrolovat, každých 7 dní.

*Pokud je server vyřazený z provozu, když ho odeberete z kontroly stavu?*

* Pokud server neodesílá data po dobu 3 týdnů, dojde k jejímu odebrání.

*Jaký je název procesu, který provádí shromažďování dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá shromažďování dat?*

* Skutečná shromažďování dat na serveru trvá přibližně 1 hodinu. Může to trvat déle na serverech, které mají velký počet instancí SQL nebo databází.

*Jaký typ dat se shromáždí?*

* Shromažďují se tyto typy dat:
  * Rozhraní WMI
  * Registr
  * Čítače výkonu
  * Zobrazení dynamické správy SQL (DMV).

*Existuje způsob, jak nakonfigurovat, kdy se data shromažďují?*

* V tuto chvíli to není možné.

*Proč musím nakonfigurovat účet Spustit jako?*

* Pro SQL Server se spustí malý počet dotazů SQL. Aby je bylo možné spustit, musí být použit účet Spustit jako s oprávněním zobrazit stav serveru pro SQL.  Kromě toho se vyžaduje, aby se pro dotazování rozhraní WMI vyžadovaly přihlašovací údaje místního správce.

*Proč zobrazit jenom 10 nejčastějších doporučení?*

* Místo toho, abyste vám pomohli vyčerpávající úplný seznam úkolů, doporučujeme nejprve se zaměřit na doporučení přednostních doporučení. Po vyřešení těchto adres budou k dispozici další doporučení. Pokud chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení pomocí Log Analytics prohledávání protokolu.

*Existuje způsob, jak ignorovat doporučení?*

* Ano, podívejte se na část [Ignorovat doporučení](#ignore-recommendations) výše.

## <a name="next-steps"></a>Další kroky
* [Dotazy protokolu](../logs/log-query-overview.md) pro informace o tom, jak analyzovat podrobné údaje o kontrole stavu SQL a doporučení.

