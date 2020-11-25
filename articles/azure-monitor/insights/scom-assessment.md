---
title: Posouzení System Center Operations Manager s využitím Azure Monitor
description: Řešení System Center Operations Manager Health Check můžete použít k vyhodnocení rizik a stavu prostředí v pravidelných intervalech.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 97d7d21374062462248e1b86f2bde2fef2d25331
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004904"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimalizace prostředí s využitím řešení System Center Operations Manager Health Check (Preview)

![Symbol System Center Operations Manager Health Check](./media/scom-assessment/scom-assessment-symbol.png)

Řešení System Center Operations Manager Health Check můžete použít k vyhodnocení rizik a stavu skupiny pro správu System Center Operations Manager v pravidelných intervalech. Tento článek vám pomůže s instalací, konfigurací a používáním řešení, abyste mohli provádět nápravné akce pro potenciální problémy.

Toto řešení poskytuje prioritní seznam doporučení specifických pro vaši nasazenou serverovou infrastrukturu. Doporučení jsou rozdělená do kategorií v rámci čtyř oblastí fokusu, které vám pomůžou rychle pochopit rizika a provádět nápravná opatření.

Doporučení jsou založená na znalostech a zkušenostech získaných odborníky Microsoftu z tisíců návštěv zákazníků. Každé doporučení poskytuje pokyny týkající se toho, proč k problému může dojít, a postup implementace navrhovaných změn.

Můžete vybrat oblasti, které jsou pro vaši organizaci nejdůležitější, a sledovat svůj pokrok směrem k provozu bezplatného a zdravého prostředí.

Po přidání řešení a provedení posouzení se souhrnné informace pro oblasti výběru zobrazí na řídicím panelu **System Center Operations Manager Health Check** pro vaši infrastrukturu. Následující části popisují, jak používat informace na řídicím panelu **System Center Operations Manager Health Check** , kde můžete zobrazit a následně provést doporučené akce pro vaše Operations Manager prostředí.

![Dlaždice řešení System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Řídicí panel System Center Operations Manager Health Check](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení

Řešení spolupracuje s Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager a Microsoft System Center Operations Manager 1807. V každém management server musí být nainstalovaná podporovaná verze .NET Framework 4.6.2.

K instalaci a konfiguraci řešení můžete použít následující informace.

- Než budete moct použít řešení pro kontrolu stavu v Log Analytics, musíte mít nainstalované řešení. Nainstalujte řešení z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Po přidání řešení do pracovního prostoru se v dlaždici **System Center Operations Manager Health Check** na řídicím panelu zobrazí další zpráva požadovaná pro konfiguraci. Klikněte na dlaždici a postupujte podle kroků konfigurace uvedených na stránce.

  ![Dlaždice řídicího panelu System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguraci System Center Operations Manager můžete provést pomocí skriptu podle kroků uvedených na stránce konfigurace řešení v Log Analytics.

 Při konfiguraci posouzení prostřednictvím konzoly Operations Manager Operations Console proveďte následující kroky v tomto pořadí:
1. [Nastavte účet Spustit jako pro System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. Konfigurace pravidla System Center Operations Manager Health Check

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Podrobnosti o shromažďování dat pro System Center Operations Manager Health Check

Řešení System Center Operations Manager Health Check shromažďuje data z následujících zdrojů:

* Registr
* Windows Management Instrumentation (WMI)
* Protokol událostí
* Data souborů
* Přímo z Operations Manager pomocí dotazů PowerShell a SQL ze management server, kterou jste určili.  

Data se shromažďují na management server a předají se Log Analytics každých 7 dní.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Účty Operations Manager Spustit jako pro Log Analytics

Log Analytics vytváří sady Management Pack pro úlohy, které poskytují služby přidávající hodnoty. Každé zatížení vyžaduje oprávnění pro spouštění sad Management Pack v jiném kontextu zabezpečení, jako je například účet uživatele domény. Nakonfigurujte Operations Manager účet Spustit jako s privilegovanými přihlašovacími údaji. Další informace najdete v tématu [Postup vytvoření účtu Spustit jako](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) v dokumentaci k Operations Manager.

Pomocí následujících informací nastavte pro System Center Operations Manager Health Check Operations Manager účet Spustit jako.

### <a name="set-the-run-as-account"></a>Nastavení účtu Spustit jako

Než budete pokračovat, musí účet Spustit jako splňovat následující požadavky:

* Účet uživatele domény, který je členem místní skupiny Administrators na všech serverech, které podporují jakýkoli Operations Manager Server pro správu rolí, SQL Server hostující provozní, datový sklad a databázi služby ACS, vytváření sestav, webovou konzolu a server brány.
* Role správce Operations Manageru pro skupinu pro správu, která se vyhodnocuje
* Pokud účet nemá oprávnění správce systému SQL, potom spusťte [skript](#sql-script-to-grant-granular-permissions-to-the-run-as-account) pro udělení podrobných oprávnění účtu v každé instanci SQL Server hostující jednu nebo všechny databáze Operations Manager.

1. V konzole Operations Manager vyberte navigační tlačítko **pro správu** .
2. V části **Konfigurace spustit jako** klikněte na **účty**.
3. V průvodci **vytvořením účtu Spustit jako** na **úvodní** stránce klikněte na **Další**.
4. Na stránce **Obecné vlastnosti** vyberte možnost **Windows** v seznamu **účet Spustit jako typ:** .
5. Do textového pole **Zobrazovaný název** zadejte zobrazovaný název a volitelně zadejte popis do pole **Popis** a pak klikněte na **Další**.
6. Na stránce **zabezpečení distribuce** vyberte **bezpečnější**.
7. Klikněte na **Vytvořit**.  

Teď, když je vytvořený účet Spustit jako, musí být cílovým serverem pro správu ve skupině pro správu a přidružený k předdefinovanému profilu spustit jako, aby se pracovní postupy spouštěly pomocí přihlašovacích údajů.  

1. V části **Konfigurace spustit jako** klikněte na **účty** v podokně výsledků dvakrát klikněte na účet, který jste vytvořili dříve.
2. Na kartě **distribuce** klikněte na **Přidat** pro **vybrané počítače** a přidejte Management Server k distribuci účtu do.  Dvakrát klikněte na **OK** , aby se změny uložily.
3. V části **Konfigurace spustit jako** klikněte na **profily**.
4. Vyhledejte *profil posouzení SCOM*.
5. Název profilu by měl být: *Microsoft System Center Operations Manager Health Check profil spustit jako*.
6. Klikněte pravým tlačítkem a aktualizujte jeho vlastnosti a přidejte nedávno vytvořený účet Spustit jako, který jste vytvořili dříve.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skript SQL pro udělení podrobných oprávnění účtu Spustit jako

Spuštěním následujícího skriptu SQL udělte požadovaná oprávnění účtu Spustit jako v instanci SQL Server, kterou používá Operations Manager hostování provozní databáze, datového skladu a databáze služby ACS.

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

Management Pack System Center Operations Manager Health Check řešení zahrnuje pravidlo s názvem *Microsoft System Center Operations Manager spustit pravidlo kontroly stavu*. Toto pravidlo zodpovídá za spuštění kontroly stavu. Pokud chcete pravidlo Povolit a nakonfigurovat frekvenci, použijte následující postupy.

Ve výchozím nastavení je pravidlo kontroly stavu spouštění Microsoft System Center Operations Manager zakázané. Chcete-li spustit kontrolu stavu, je nutné povolit pravidlo na management server. Použijte k tomu následující postup.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Povolení pravidla pro konkrétní server pro správu

1. V pracovním prostoru **vytváření obsahu** konzoly Operations Manager Operations Console vyhledejte pravidlo pravidla *kontroly stavu Microsoft System Center Operations Manager spustit* v podokně **pravidla** .
2. Ve výsledcích hledání vyberte jednu, která obsahuje *typ text: Server pro správu*.
3. Klikněte pravým tlačítkem na pravidlo a potom klikněte na **přepsání**  >  **pro konkrétní objekt třídy: Server pro správu**.
4.  V seznamu dostupné servery pro správu vyberte management server, kde se má pravidlo spustit.  Mělo by se jednat o stejný management server, který jste dříve nakonfigurovali pro přidružení účtu Spustit jako k.
5.  Zajistěte, aby byla hodnota přepsání změněna na hodnotu **true** pro hodnotu **povoleného** parametru.<br><br> ![přepsat parametr](./media/scom-assessment/rule.png)

    Stále v tomto okně nakonfigurujte četnost spouštění pomocí následujícího postupu.

#### <a name="configure-the-run-frequency"></a>Konfigurace frekvence spouštění

Vyhodnocení je nakonfigurované tak, aby se ve výchozím nastavení spouštělo každých 10 080 minut (nebo sedmi dní). Hodnotu můžete přepsat na minimální hodnotu 1440 minut (nebo jeden den). Hodnota představuje minimální časovou mezeru nutnou pro běhy po úspěšném vyhodnocení. K přepsání intervalu použijte následující postup.

1. V pracovním prostoru **vytváření obsahu** konzoly Operations Manager vyhledejte pravidlo pravidla *kontroly stavu Microsoft System Center Operations Manager spustit* v části **pravidla** .
2. Ve výsledcích hledání vyberte jednu, která obsahuje *typ text: Server pro správu*.
3. Klikněte pravým tlačítkem na pravidlo a pak klikněte na **přepsat pravidlo**  >  **pro všechny objekty třídy: Server pro správu**.
4. Změňte hodnotu parametru **interval** na hodnotu požadovaného intervalu. V následujícím příkladu je hodnota nastavena na 1440 minut (jeden den).<br><br> ![parametr intervalu](./media/scom-assessment/interval.png)<br>  

    Pokud je hodnota nastavená na méně než 1440 minut, pak se pravidlo spustí na jeden den. V tomto příkladu pravidlo ignoruje hodnotu intervalu a spustí se v frekvence jednoho dne.


## <a name="understanding-how-recommendations-are-prioritized"></a>Principy určování priority doporučení

Každé provedené doporučení je předána hodnota váhy, která identifikuje relativní důležitost doporučení. Zobrazuje se jenom 10 nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Způsob počítání vah

Váhy jsou agregované hodnoty založené na třech klíčových faktorech:

- *Pravděpodobnost* , že zjištěná chyba způsobí problémy. Vyšší pravděpodobnost odpovídá většímu celkovému skóre pro doporučení.
- *Dopad* problému ve vaší organizaci, pokud dojde k problému. Vyšší dopad odpovídá většímu celkovému skóre pro doporučení.
- *Úsilí* potřebné k implementaci doporučení. Vyšší úsilí se rovná menšímu celkovému skóre pro doporučení.

Váha pro každé doporučení se vyjádří jako procento z celkového skóre dostupného pro každou oblast zaměření. Pokud má například doporučení v oblasti dostupnosti a zaměření na provozní kontinuitu skóre 5%, implementace tohoto doporučení zvyšuje celkovou dostupnost a skóre kontinuity podnikových prostředí o 5%.

### <a name="focus-areas"></a>Oblasti zaměření

**Dostupnost a provozní kontinuita** – tato oblast pro výběr zobrazuje doporučení pro dostupnost služby, odolnost vaší infrastruktury a obchodní ochranu.

**Výkon a škálovatelnost** – tato oblast pro výběr obsahuje doporučení, která vám pomohou zvýšit infrastrukturu IT vaší organizace, zajistit, že vaše IT prostředí splňuje aktuální požadavky na výkon a je schopné reagovat na měnící se potřeby infrastruktury.

**Upgrade, migrace a nasazení** – tato oblast pro výběr vám ukáže doporučení, která vám pomůžou při upgradu, migraci a nasazování SQL Server do vaší stávající infrastruktury.

**Operace a monitorování** – tato oblast pro výběr obsahuje doporučení, která vám pomůžou zjednodušit vaše IT operace, implementovat preventivní údržbu a maximalizovat výkon.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Měli byste v každé oblasti zaměření cílit na skóre 100 %?

Ne nutně. Doporučení jsou založená na znalostech a zkušenostech získaných odborníky Microsoftu v různých tisících návštěv zákazníků. Nicméně žádné dvě serverové infrastruktury nejsou stejné a specifická doporučení můžou být pro vás méně důležitá. Některá doporučení zabezpečení mohou být například méně důležitá, pokud vaše virtuální počítače nejsou vystaveny pro Internet. Některá doporučení k dostupnosti mohou být méně důležitá pro služby, které poskytují nízkou prioritu shromažďování a generování dat ad hoc. Problémy, které jsou důležité pro vyspělou firmu, můžou být pro spuštění méně důležité. Možná budete chtít určit, které oblasti fokusu jsou vašimi prioritami, a pak se podívat, jak se vaše skóre v průběhu času mění.

Každé doporučení obsahuje pokyny k tomu, proč je důležité. Pomocí těchto pokynů můžete vyhodnotit, jestli je implementace doporučení vhodná pro vás, a to s ohledem na povahu vašich IT služeb a obchodních potřeb vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použít doporučení oblasti pro kontrolu stavu

Než budete moct v Log Analytics použít řešení kontroly stavu, musíte mít nainstalované řešení. Další informace o instalaci řešení najdete v tématu [instalace řešení pro správu](./solutions.md). Po instalaci můžete zobrazit souhrn doporučení pomocí dlaždice System Center Operations Manager Health Check na stránce **Přehled** pro váš pracovní prostor v Azure Portal.

Podívejte se na souhrnná vyhodnocení dodržování předpisů pro vaši infrastrukturu a pak na doporučení pro přechod k podrobnostem.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Chcete-li zobrazit doporučení pro oblast zaměření a provést nápravná opatření
1. Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com) .
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V podokně předplatná Log Analytics vyberte pracovní prostor a pak klikněte na položku nabídky **Souhrn pracovního prostoru** .  
4. Na stránce **Přehled** klikněte na dlaždici **System Center Operations Manager Health Check** .
5. Na stránce **System Center Operations Manager Health Check** zkontrolujte souhrnné informace v jednom z oken detailní oblasti a potom klikněte na jednu pro zobrazení doporučení pro tuto oblast výběru.
6. Na kterékoli stránce oblasti pro zaměření si můžete prohlédnout doporučení s prioritou vytvořená pro vaše prostředí. Kliknutím na doporučení v části **Ovlivněné objekty** zobrazíte podrobnosti o tom, proč se doporučení udělalo.<br><br> ![oblast zaměření](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. V **navrhovaných akcích** můžete provést opravné akce. Po vyřešení této položky budou později vyhodnoceny záznamy o tom, že byly provedeny doporučené akce a že se bude zvyšovat skóre dodržování předpisů. Opravené položky se zobrazí jako **předané objekty**.

## <a name="ignore-recommendations"></a>Ignorování doporučení

Pokud máte doporučení, která chcete ignorovat, můžete vytvořit textový soubor, který Log Analytics používá k tomu, aby se předešlo zobrazování doporučení ve výsledcích hodnocení.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Určení doporučení, která chcete ignorovat
1. V Azure Portal na stránce pracovního prostoru Log Analytics pro vybraný pracovní prostor klikněte na položku nabídky **hledání protokolu** .
2. K vypsání doporučení, která se pro počítače ve vašem prostředí nezdařila, použijte následující dotaz.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [Nový dotazovací jazyk Log Analytics](../log-query/log-query-overview.md), pak se výše uvedený dotaz změní na následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Tady je snímek obrazovky, který ukazuje dotaz na hledání protokolu:<br><br> ![prohledávání protokolů](./media/scom-assessment/scom-log-search.png)<br>

3. Vyberte doporučení, která chcete ignorovat. V dalším postupu použijete hodnoty pro RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt

1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložte nebo zadejte jednotlivé RecommendationIdy pro každé doporučení, které chcete, Log Analytics ignorovat na samostatném řádku a pak soubor uložte a zavřete.
3. Uložte soubor do následující složky na každém počítači, na kterém chcete ignorovat doporučení Log Analytics.
4. Na Operations Manager management server- *systémová_jednotka*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Ověření, že se doporučení ignorují

1. Po dalším naplánovaném spuštění hodnocení se ve výchozím nastavení každých 7 dnů budou uvedená doporučení ignorovat a nezobrazí se na řídicím panelu pro kontrolu stavu.
2. Pomocí následujících vyhledávacích dotazů protokolu můžete zobrazit seznam všech ignorovaných doporučení.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Pokud byl váš pracovní prostor upgradován na [Nový dotazovací jazyk Log Analytics](../log-query/log-query-overview.md), pak se výše uvedený dotaz změní na následující.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Pokud se později rozhodnete, že chcete zobrazit ignorovaná doporučení, odeberte všechny IgnoreRecommendations.txt soubory nebo z nich můžete odebrat RecommendationIDs.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Nejčastější dotazy k řešení System Center Operations Manager Health Check

*Do pracovního prostoru Log Analytics jsem přidal (a) řešení kontroly stavu. Ale doporučení se nezobrazuje. Proč ne?* Po přidání řešení použijte následující postup zobrazení doporučení na řídicím panelu Log Analytics.  

- [Nastavte účet Spustit jako pro System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurace pravidla System Center Operations Manager Health Check](#configure-the-health-check-rule)


*Existuje způsob, jak nakonfigurovat četnost spouštění kontroly?* Ano. Viz [Konfigurace frekvence spouštění](#configure-the-run-frequency).

*Pokud je po přidání System Center Operations Manager Health Check řešení zjištěn jiný server, bude zkontrolován?* Ano, po zjištění, že je zjišťování zaškrtnuto, je ve výchozím nastavení každých 7 dní.

*Jaký je název procesu, který provádí shromažďování dat?* AdvisorAssessment.exe

*Kde se proces AdvisorAssessment.exe spouští?* AdvisorAssessment.exe běží v rámci procesu HealthService management server, kde je pravidlo kontroly stavu povolené. Pomocí tohoto procesu se dosahuje zjišťování celého prostředí prostřednictvím vzdáleného shromažďování dat.

*Jak dlouho trvá shromažďování dat?* Shromažďování dat na serveru trvá přibližně jednu hodinu. Může trvat déle v prostředích, která mají mnoho instancí Operations Manager nebo databází.

*Co když nastavím interval hodnocení na méně než 1440 minut?* Posouzení je předem nakonfigurované tak, aby běželo maximálně jednou za den. Pokud přepíšete hodnotu interval na hodnotu menší než 1440 minut, pak hodnocení použije jako hodnotu intervalu 1440 minut.

*Jak zjistit, jestli došlo k selhání požadavků?* Pokud při kontrole stavu došlo k chybě, ale nevidíte výsledky, je pravděpodobný, že některé z požadavků pro tuto kontrolu selhaly. Můžete spustit dotazy: `Operation Solution=SCOMAssessment` a `SCOMAssessmentRecommendation FocusArea=Prerequisites` v hledání v protokolu zobrazíte neúspěšné požadavky.

*`Failed to connect to the SQL Instance (….).`V případě selhání požadavků se zobrazí zpráva. Jaký je problém?* AdvisorAssessment.exe proces, který shromažďuje data, běží v procesu HealthService na management server. V rámci kontroly stavu se proces pokusí připojit k SQL Server, kde se nachází databáze Operations Manager. K této chybě může dojít, když pravidla brány firewall zablokují připojení k instanci SQL Server.

*Jaký typ dat se shromáždí?* Shromažďují se tyto typy dat:-data služby WMI data-EventLog – data-Operations Manager data prostřednictvím prostředí Windows PowerShell, dotazy SQL a kolekce informací o souborech.

*Proč musím nakonfigurovat účet Spustit jako?* V Operations Manager se spouští různé dotazy SQL. Aby je bylo možné spustit, je nutné použít účet Spustit jako s potřebnými oprávněními. Kromě toho jsou pro dotazování rozhraní WMI vyžadovány přihlašovací údaje místního správce.

*Proč zobrazit jenom 10 nejčastějších doporučení?* Místo toho, abyste vám poskytli vyčerpávající a nenáročný seznam úkolů, doporučujeme nejprve zaměřit se na doporučení s upřednostněním adres. Po vyřešení těchto adres budou k dispozici další doporučení. Pokud chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení pomocí prohledávání protokolů.

*Existuje způsob, jak ignorovat doporučení?* Ano, přečtěte si [doporučení ignorovat](#ignore-recommendations).


## <a name="next-steps"></a>Další kroky

- V [protokolech hledání](../log-query/log-query-overview.md) zjistíte, jak analyzovat podrobná System Center Operations Manager Health Checká data a doporučení.

