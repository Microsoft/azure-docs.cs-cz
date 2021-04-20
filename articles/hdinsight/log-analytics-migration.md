---
title: Migrace dat Log Analytics pro Azure HDInsight
description: Přečtěte si o změnách Azure Monitor integrace a osvědčených postupech pro použití nových tabulek.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741566"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Průvodce migrací Log Analytics pro clustery Azure HDInsight

 Azure HDInsight je Clusterová služba, která je připravená pro podnikové prostředí. Tato služba spouští Open Source analytické architektury, jako jsou Apache Spark, Hadoop, HBA a Kafka v Azure. Azure HDInsight se integruje s dalšími službami Azure, aby mohli zákazníci lépe spravovat svoje aplikace pro analýzu velkých objemů dat.

Log Analytics poskytuje nástroj v Azure Portal pro úpravy a spouštění dotazů protokolu. Dotazy pocházejí z dat shromažďovaných Azure Monitor protokoly a interaktivně analyzují výsledky. Zákazníci můžou pomocí Log Analytics dotazů načíst záznamy, které odpovídají konkrétním kritériím. Můžou také používat dotazy k identifikaci trendů, analýze vzorů a poskytování přehledů o jejich datech.

Integrace s Azure HDInsight je povolená s Log Analytics v 2017. Zákazníci HDInsight tuto funkci rychle přijali, aby mohli monitorovat své clustery HDInsight a dotazovat se na protokoly v clusterech. Zatímco přijetí této funkce se zvýšilo, zákazníci získali zpětnou vazbu o integraci:

- Zákazníci se nemůžou rozhodnout, které protokoly se mají ukládat, a uložit všechny protokoly můžou být nákladné.
- Aktuální protokoly schémat HDInsight nedodržují konzistentní konvence pojmenování a některé tabulky se opakují.
- Zákazníci chtějí mít předem připravený řídicí panel, který snadno monitoruje klíčový ukazatel výkonu svých clusterů HDInsight.
- Aby mohli zákazníci spustit jednoduché dotazy, musí přejít na Log Analytics.

## <a name="solution-overview"></a>Přehled řešení

V úvahách o zpětné vazbě zákazníků se tým Azure HDInsight investoval do integrace s Azure Monitor. Tato integrace umožňuje:

- Nová sada tabulek v pracovním prostoru Customers Log Analytics. Nové tabulky se dodávají pomocí nového kanálu Log Analytics.
- Vyšší spolehlivost
- Rychlejší doručování protokolů
- Seskupení tabulek založené na prostředku a výchozí dotazy

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Výhody nové Integrace Azure Monitor

Tento dokument popisuje změny v integraci Azure Monitor a poskytuje osvědčené postupy pro používání nových tabulek.

Přepracované **schémata**: formátování schématu pro novou integraci Azure monitor je lépe organizované a snadno pochopitelné. Existují dva třetiny méně schémat, aby bylo možné odebrat co nejvíc nejednoznačnosti ve starších schématech.

**Selektivní protokolování (brzy** vydáváme): k dispozici jsou protokoly a metriky, které jsou dostupné prostřednictvím Log Analytics. Abychom vám pomohli ušetřit náklady na monitorování, uvolňujeme novou funkci selektivního protokolování. Pomocí této funkce můžete zapnout a vypnout různé protokoly a zdroje metrik. S touto funkcí budete muset platit jenom za to, co využijete.

**Zaznamená integraci portálu clusteru**: podokno **protokoly** je nového na portálu clusteru HDInsight. Každý, kdo má přístup ke clusteru, může přejít do tohoto podokna a dotazovat se na jakoukoli tabulku, do které prostředek clusteru odesílá záznamy. Uživatelé už nepotřebují mít přístup k pracovnímu prostoru Log Analytics, aby viděli záznamy pro určitý prostředek clusteru.

**Integrace portálu clusteru Insights**: podokno **přehledů** je také nového na portálu clusteru HDInsight. Po povolení nové Integrace Azure Monitor můžete vybrat podokno **přehledy** a integrované protokoly a řídicí panel metriky, které jsou specifické pro typ clusteru, budou automaticky vyplněny. Tyto řídicí panely se přepracované z předchozích řešení Azure. Poskytují podrobné přehledy o výkonu a stavu vašeho clusteru.

**Přehledy** v rámci škálování: pomocí nového sešitu **na úrovni přehledů** na portálu **Azure monitor** můžete monitorovat stav a výkon svých clusterů v různých předplatných.

## <a name="customer-scenarios"></a>Scénáře zákazníků

Následující části popisují, jak můžou zákazníci používat novou integraci Azure Monitor v různých scénářích. Část [Aktivace nové integrace Azure monitor](#activate-a-new-azure-monitor-integration) popisuje, jak aktivovat a používat novou integraci Azure monitor. [Migrace z Azure monitor na nový oddíl Azure Monitor Integration Classic](#migrate-to-the-new-azure-monitor-integration) obsahuje další informace pro uživatele, kteří závisejí na staré integraci Azure monitor.

> [!NOTE]
> Pro novou integraci Azure Monitoring jsou vhodné jenom clustery vytvořené v 2020. září a potom.

## <a name="activate-a-new-azure-monitor-integration"></a>Aktivovat novou integraci Azure Monitor 

> [!NOTE]
> Než povolíte novou integraci, musíte mít vytvořený pracovní prostor Log Analytics v předplatném, ke kterému máte přístup. Další informace o tom, jak vytvořit pracovní prostor Log Analytics, najdete [v tématu Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Novou integraci aktivujete tak, že přejdete na stránku portálu vašeho clusteru a posunete nabídku vlevo dolů, dokud se nedostanete do části **monitorování** . V části **monitorování** vyberte **monitorovat integraci**. Pak vyberte **Povolit** a můžete zvolit Log Analytics pracovní prostor, do kterého chcete protokoly odesílat. Po zvolení pracovního prostoru vyberte **Uložit** . 

### <a name="access-the-new-tables"></a>Přístup k novým tabulkám

Existují dva způsoby, jak můžete získat přístup k novým tabulkám. 

### <a name="approach-1"></a>1. přístup:
První způsob, jak získat přístup k novým tabulkám, je prostřednictvím pracovního prostoru Log Analytics. 

1. Pokud jste povolili integraci, přejdete do pracovního prostoru Log Analytics, který jste vybrali. 
2. V nabídce na levé straně obrazovky přejděte dolů a vyberte **protokoly**. Editor dotazů v protokolech se zobrazí se seznamem všech tabulek v pracovním prostoru. 
3. Pokud jsou tabulky seskupené podle **řešení**, nové tabulky HDI se nacházejí v části **Správa protokolů** . 
4. Pokud seskupete tabulky podle **typu prostředku**, tabulky se nacházejí v části **clustery HDInsight** , jak je znázorněno na následujícím obrázku. 

> [!NOTE]
> Tento postup popisuje, jak byly k protokolům přistupované ve staré integraci. K tomu je potřeba, aby uživatel měl přístup k pracovnímu prostoru.

### <a name="approach-2"></a>2. přístup:

Druhým způsobem, jak získat přístup k novým tabulkám, je přístup k portálu clusteru.
 
1. Přejděte na stránku portálu vašeho clusteru a posuňte se dolů v nabídce na levé straně, dokud se nezobrazí část **monitorování** . V této části se zobrazí podokno **protokoly** . 
2. Vyberte **protokoly** a zobrazí se Editor dotazů protokolů. Editor obsahuje všechny protokoly, které jsou přidruženy k prostředku clusteru. Protokoly jste odeslali do pracovního prostoru Log Analytics, když jste povolili integraci. Tyto protokoly poskytují přístup na základě prostředků (RBAC). Pomocí RBAC můžou uživatelé, kteří mají přístup ke clusteru, ale ne k pracovnímu prostoru, zobrazit protokoly přidružené ke clusteru.

Pro porovnání zobrazí následující snímky obrazovky starší zobrazení pracovního prostoru integrace a nové zobrazení pracovního prostoru integrace:

**Starší zobrazení pracovního prostoru integrace**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Snímek obrazovky zobrazující starší verzi zobrazení pracovního prostoru integrace" border="false":::

**Nové zobrazení pracovního prostoru integrace**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Snímek obrazovky zobrazující nové zobrazení pracovního prostoru integrace" border="false":::

### <a name="use-the-new-tables"></a>Použití nových tabulek

Tyto integrace vám můžou pomáhat s používáním nových tabulek:

#### <a name="default-queries-to-use-with-new-tables"></a>Výchozí dotazy pro použití s novými tabulkami

V editoru dotazů na protokoly nastavte přepínač na **dotazy** nad seznamem tabulky. Ujistěte se, že jste seskupovat dotazy podle **typu prostředku** a že není nastaven žádný filtr pro jiný typ prostředku než **clustery HDInsight**. Na následujícím obrázku vidíte, jak vypadají výsledky po seskupení podle **typu prostředku** a filtrované pro **clustery HDInsight**. Stačí vybrat jednu z nich, která se zobrazí v editoru dotazů v protokolech. Nezapomeňte si přečíst komentáře obsažené v dotazech, protože některé vyžadují, abyste zadali nějaké informace, jako je název clusteru, aby se dotaz úspěšně spouštěl.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Snímek obrazovky, který zobrazuje výchozí výsledky dotazu seskupený typ prostředku." border="true":::


#### <a name="create-your-own-queries"></a>Vytváření vlastních dotazů

V editoru dotazů protokolů můžete zadat vlastní dotazy. Dotazy použité u starých tabulek nebudou v nových tabulkách platné, protože v mnoha nových tabulkách jsou nová, rafinovaná schémata. Výchozí dotazy jsou skvělé odkazy na tvarování dotazů v nových tabulkách.

#### <a name="insights"></a>Insights

Přehledy jsou řídicí panely vizualizace specifické pro clustery vytvořené pomocí [sešitů Azure](../azure-monitor/platform/workbooks-overview.md). Tyto řídicí panely poskytují podrobné grafy a vizualizace, jak cluster běží. Řídicí panely obsahují oddíly pro každý typ clusteru, PŘÍZi, systémové metriky a protokoly součástí. Přístup k řídicímu panelu clusteru získáte tak, že na portálu navštívíte stránku vašeho clusteru, posunete se dolů do části **monitorování** a vyberete podokno **přehledy** . Řídicí panel se načte automaticky, pokud jste povolili novou integraci. Počkejte několik sekund, než se grafy načtou při dotazování protokolů.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Snímek obrazovky, který zobrazuje řídicí panel vizualizace.":::

#### <a name="custom-azure-workbooks"></a>Vlastní sešity Azure

Můžete vytvářet vlastní sešity Azure pomocí vlastních grafů a vizualizací. Na stránce portálu clusteru přejděte dolů k části **monitorování** a v nabídce na levé straně vyberte podokno **sešity** . Můžete buď začít používat prázdnou šablonu, nebo použít jednu ze šablon v části **clustery HDInsight** . Pro každý typ clusteru je šablona. Šablony jsou užitečné, pokud chcete uložit konkrétní vlastní nastavení, které výchozí HDInsight Insights neposkytuje. Pokud se domníváte, že se o nové funkce v HDInsight Insights nestanou pracovat, můžete je zdarma posílat v žádostech o nové funkce.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Škálování v sešitech pro nové Integrace Azure Monitor

Využijte náš nový sešit ve velkém měřítku a získejte prostředí pro monitorování více clusterů pro vaše clustery. Náš pracovní sešit vám ukáže, na kterých clusterech je povolený kanál monitorování. Sešit také nabízí jednoduchý způsob, jak kontrolovat stav více clusterů najednou. Postup zobrazení tohoto sešitu:

1. Přejít na stránku **Azure monitor** v části na domovské stránce Azure Portal
2. Jednou na stránce **Azure monitor** v části **přehledy** vyberte **centrum Insights** .
3. V části **Analýza** vyberte **clustery HDInsight** .

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Snímek obrazovky, který zobrazuje sešit ve velkém měřítku." border="false":::

#### <a name="alerts"></a>Výstrahy

Do svých clusterů a pracovních prostorů můžete přidat vlastní upozornění v editoru dotazů protokolu. V editoru dotazů protokolů vyberte podokno **protokoly** ze svého clusteru nebo portálu pracovního prostoru. Spusťte dotaz a pak vyberte **nové pravidlo výstrahy** , jak je znázorněno na následujícím snímku obrazovky. Další informace najdete v článku o [konfiguraci výstrah](../azure-monitor/platform/alerts-log.md).

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Snímek obrazovky zobrazující upozornění nového pravidla" border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Migrace na novou integraci Azure Monitor

Pokud používáte integraci Azure Monitor Classic, musíte po přepnutí na novou integraci Azure Monitor provést některé úpravy nových formátů tabulky.

Pokud chcete povolit novou integraci Azure Monitor, postupujte podle kroků popsaných v části [Aktivace nové Azure monitor integrace](#activate-a-new-azure-monitor-integration) .

### <a name="run-queries-in-log-analytics"></a>Spouštění dotazů v Log Analytics

Vzhledem k tomu, že se nový formát tabulky liší od předchozí, je nutné dotazy znovu dopracovat, abyste mohli používat naše nové tabulky. Jakmile povolíte novou integraci Azure Monitor, můžete procházet tabulky a schémata a identifikovat pole, která se používají ve vašich starých dotazech.

Poskytujeme [tabulku mapování](#appendix-table-mapping) mezi starou tabulkou a novou tabulkou, která vám pomůže rychle najít nová pole, která potřebujete použít k migraci řídicích panelů a dotazů.

**Výchozí dotazy**: vytvořili jsme výchozí dotazy, které ukazují, jak používat nové tabulky pro běžné situace. Výchozí dotazy také ukazují, jaké informace jsou v každé tabulce k dispozici. Pomocí pokynů v části [výchozí dotazy pro použití s novými tabulkami](#default-queries-to-use-with-new-tables) v tomto článku můžete získat přístup k výchozím dotazům.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Aktualizace řídicích panelů pro clustery HDInsight

Pokud jste vytvořili více řídicích panelů pro monitorování clusterů HDInsight, musíte po povolení nové Integrace Azure Monitor upravit dotaz za tabulkou. Název tabulky nebo pole se mohou v nové integraci změnit, ale všechny informace, které jste nastavili v původní integraci, jsou zahrnuté.

Chcete-li aktualizovat dotaz za řídicími panely, přečtěte si [tabulku mapování](#appendix-table-mapping) mezi starou tabulkou nebo schématem do nové tabulky nebo schématu.

#### <a name="out-of-box-dashboards"></a>Připravené řídicí panely 

Vylepšili jsme také připravené řídicí panely na úrovni clusteru. V pravém horním rohu každého grafu je tlačítko, které umožňuje zobrazit základní dotaz, který vytváří informace. Graf je skvělým způsobem, jak se seznámit s tím, jak se můžou nové tabulky efektivně dotazovat. K předpřipraveným řídicím panelům získáte přístup podle pokynů, které najdete v oddílech [přehledů](#insights) a v [sešitech pro nové Integrace Azure monitor](#at-scale-workbooks-for-new-azure-monitor-integrations) .

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>Použití řídicího panelu pro monitorování ve škálování HDInsight

Pokud používáte řídicí panel, který je k dispozici pro clustery HDInsight, jako je monitorování HDInsight Spark a interaktivní monitorování HDInsight, pracujeme na tom, abychom vám na Azure Monitor portálu poskytovali stejné možnosti.

V Azure Monitor se zobrazí možnost clustery HDInsight.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Snímek obrazovky, který zobrazuje možnost HDInsight v Azure Monitor." border="false":::

Centrum Insights Azure Monitorového portálu poskytuje možnost monitorování více clusterů HDInsight na jednom místě. Clustery uspořádáme na základě typu úlohy, takže se zobrazí typy, jako je Spark, HBA a podregistr. Místo přechodu na více řídicích panelů teď můžete v tomto zobrazení monitorovat všechny clustery HDInsight.

> [!NOTE]
> Další informace najdete v částech [přehledy](#insights) a [ve velkém měřítku pro nové Integrace Azure monitor](#at-scale-workbooks-for-new-azure-monitor-integrations) v tomto článku.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Povolit obě integrace k urychlení migrace

V clusteru můžete aktivovat jak Classic, tak novou Azure Monitor integrace současně s nárokem na obě integrace pro migraci na novou integraci Azure Monitor. Nová integrace je k dispozici pro všechny clustery vytvořené po polovině září 2020.

Tímto způsobem můžete snadno provést souběžné porovnání pro dotazy, které používáte.

### <a name="enabling-the-classic-integration"></a>Povolení klasické integrace

Pokud používáte cluster vytvořený po polovině 2020, zobrazí se nové prostředí portálu na portálu vašeho clusteru. Pokud chcete nový kanál povolit, můžete postupovat podle kroků uvedených v části [Aktivace nové integrace Azure monitor](#activate-a-new-azure-monitor-integration) . Pokud chcete aktivovat klasickou integraci na tomto clusteru, navštivte stránku portálu vašeho clusteru. V nabídce na levé straně stránky portálu clusteru vyberte podokno **monitorovat integraci** v části **monitorování** . Vyberte **konfigurace Azure monitor pro integraci clusterů HDInsight (Classic)**. Zobrazí se vedlejší kontext s přepínačem, který můžete použít k povolení a zakázání Integrace Azure Monitoring Classic. 

> [!NOTE]
> Z klasické integrace na stránce protokoly a přehledy na portálu clusteru neuvidíte žádné protokoly ani metriky. V těchto umístěních se budou vyskytovat jenom nové protokoly Integration a metriky.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Snímek obrazovky, který zobrazuje odkaz pro přístup k klasické integraci." border="false":::

Vytváření nových clusterů s integrací Classic Azure Monitor není k dispozici po 1. červenci 2021.

## <a name="release-and-support-timeline"></a>Časová osa vydání a podpory

- Integrace monitorování Azure Classic nebude po 15. říjnu 2021 k dispozici. Po tomto datu nemůžete povolit integraci monitorování Azure Classic.
- Stávající integrace monitorování Azure Classic budou fungovat i nadále. Pro integraci monitorování Azure Classic bude omezená podpora. 
  - Jakmile zákazníci odešlou lístek podpory, budou prověřeny problémy.
  - Pokud řešení vyžaduje změnu obrázku, zákazníci by se měli přesunout na novou integraci.
  - Klasické clustery monitorování Azure Classic nebudeme opravovat s výjimkou důležitých problémů se zabezpečením.

## <a name="appendix-table-mapping"></a>Příloha: mapování tabulky

Následující grafy znázorňují mapování tabulek z klasické integrace monitorování Azure na náš nový. Sloupec **úlohy** popisuje, ke kterému úlohám je každá tabulka přidružená. **Nový řádek tabulky** zobrazuje název nové tabulky. Řádek **Popis** popisuje typ protokolů nebo metrik, které budou v této tabulce k dispozici. **Starý řádek tabulky** je seznam všech tabulek z integrace Classic Azure monitor, jejichž data budou nyní k dispozici v tabulce uvedené na **novém řádku tabulky** .

> [!NOTE]
> Některé tabulky jsou nové a nejsou založené na starých tabulkách.

## <a name="general-workload-tables"></a>Obecné tabulky úloh

| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Popis**: Tato tabulka obsahuje systémové metriky shromážděné z Ambari. Metriky teď pocházejí z každého uzlu v clusteru (s výjimkou hraničních uzlů), nikoli jenom z obou hlavních. Každá metrika je nyní sloupec a každá metrika je hlášena jednou pro každý záznam.</li><li>**Stará tabulka**: metriky \_ CPU – \_ Skvělé \_ CL, metriky \_ CPU \_ System \_ CL, metriky \_ CPU \_ User \_ CL, metriky \_ paměti \_ \_ CL, s metrikami \_ \_ přehození paměti \_ CL, metriky \_ paměti celkem CLmetrics vyrovnávací paměti CL, metriky zatížení \_ \_ \_ \_ \_ \_ \_ 1 minuta \_ CL, metriky \_ zatížení \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ v \_ CL \_ \_ \_ , metriky načtení uzlů CL, metriky zatížení CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Popis**: Tato tabulka obsahuje výstrahy clusteru Ambari z každého uzlu v clusteru (s výjimkou hraničních uzlů). Každé upozornění je záznam v této tabulce.</li><li>**Stará tabulka**: metriky \_ clusteru s \_ výstrahami \_ CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Popis**: Tato tabulka obsahuje záznamy z protokolů auditu a ověření Ambari.</li><li>**Stará tabulka**: log \_ Ambari \_ audit \_ CL, log \_ auth \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Popis**: Tato tabulka obsahuje všechny záznamy z protokolu auditu Ranger pro clustery ESP.</li><li>**Stará tabulka**: \_ protokoly auditu Ranger. \_ \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Popis**: Tato tabulka obsahuje informace o auditu uzlů brány. Má stejný formát jako tabulka ve sloupci staré tabulky. **Pořád se nachází v části vlastní protokoly.**</li><li>**Stará tabulka**: log \_ Gateway \_ audit \_ CL</li></ul>|

## <a name="spark-workload"></a>Úlohy Spark

> [!NOTE]
> Tabulky související s aplikacemi Spark byly nahrazeny 11 novými tabulkami Spark (počínaje HDInsightSpark *), které poskytnou podrobnější informace o úlohách Sparku.


| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Popis**: Tato tabulka obsahuje všechny protokoly týkající se Sparku a související součásti: Livy a Jupyter.</li><li>**Stará tabulka**: log \_ Livy, \_ CL, log \_ Jupyter \_ CL, log \_ Spark \_ CL, log \_ sparkappsexecutors \_ CL, log \_ sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech pro aplikace Spark, včetně času odeslání a dokončení, ID aplikace a APPNAME. Je vhodné sledovat, kdy aplikace běží a jsou dokončeny. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech týkajících se správce bloků Spark. Obsahuje informace, jako je například využití paměti prováděcího modulu.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech týkajících se prostředí, ve kterém se aplikace spouští, v režimu nasazení Spark, hlavní a informace o prováděcím modulu.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech použití prováděcího modulu Spark pro aplikaci.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech, které neodpovídají žádné jiné tabulce Spark. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o úlohách Sparku, včetně jejich počátečního a koncového času, výsledku a přidružených fází.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech na dotazech Spark SQL, včetně informací o plánu a popisu a počátečního a koncového času.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech pro fáze Sparku, včetně jejich počátečních a dokončovacích časů, stavu selhání a podrobných informací o spuštění.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Popis**: Tato tabulka obsahuje metriky výkonu pro fáze a úlohy.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech pro úlohy Spark, včetně času zahájení a dokončení, přidružených fází, stavu provádění a typu úkolu.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Popis**: Tato tabulka obsahuje informace o událostech pro poznámkové bloky Jupyter.</li></ul>|

## <a name="hadoopyarn-workload"></a>Zatížení Hadoop/PŘÍZe

| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Popis**: Tato tabulka obsahuje metriky JMX z rozhraní HADOOP a příze. Obsahuje všechny stejné metriky JMX jako staré tabulky vlastních protokolů a navíc další metriky, které jsme považovali za důležité. Přidali jsme Server Timeline, správce uzlů a metriky serveru historie úloh. Obsahuje jednu metriku na záznam.</li><li>**Stará tabulka**: metriky \_ ResourceManager \_ clustermetrics \_ CL, metriky \_ ResourceManager \_ JVM \_ CL, metriky \_ ResourceManager \_ \_ root \_ CL, metriky \_ ResourceManager, \_ \_ root \_ joblauncher \_ CL, metriky ResourceManager. \_ \_ \_ výchozí adresář \_ \_ \_ \_ \_ \_ fronty \_</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Popis**: Tato tabulka obsahuje všechny protokoly vygenerované z rozhraní HADOOP a příze.</li><li>**Stará tabulka**: log \_ mrjobsummary \_ CL, log \_ ResourceManager \_ CL, log \_ timelineserver \_ CL, log \_ NodeManager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Podregistr/LLAP úlohy 

| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Popis**: Tato tabulka obsahuje metriky JMX z podregistru a architektury LLAP. Obsahuje všechny stejné metriky JMX jako staré tabulky vlastních protokolů. Obsahuje jednu metriku na záznam.</li><li>**Stará tabulka**: llap \_ metriky \_ hiveserver2 \_ CL, llap \_ metriky \_ HS2 \_ metriky subsystemllap \_ \_ \_ \_ CL, JVM \_ metriky llap \_ pro \_ démona \_ \_ , llap \_ metriky \_ přítel pro \_ přidělování \_ údajů \_ CL, llap \_ metriky \_ llap \_ démonu \_ CL, JVM \_ metriky v \_ / \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ v v/v, llap – metrika v mezipaměti CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Popis**: Tato tabulka obsahuje protokoly vygenerované z podregistru, LLAP a jejich souvisejících komponent: WebHCat a Zeppelin.</li><li>**Stará tabulka**: log \_ hivemetastore \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log \_ Zeppelin \_ Zeppelin \_ CL</li></ul>|


## <a name="kafka-workload"></a>Kafka úlohy

| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Popis**: Tato tabulka obsahuje metriky JMX z Kafka. Obsahuje všechny stejné metriky JMX jako staré tabulky vlastních protokolů a navíc další metriky, které jsme považovali za důležité. Obsahuje jednu metriku na záznam.</li><li>**Stará tabulka**: metriky \_ Kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Popis**: Tato tabulka obsahuje všechny protokoly vygenerované zprostředkovateli Kafka.</li><li>**Stará tabulka**: log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>Zatížení HBA

| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Popis**: Tato tabulka obsahuje metriky JMX z adaptérů HBA. Obsahuje všechny stejné metriky JMX z tabulek uvedených ve sloupci starého schématu. Na rozdíl od starých tabulek každý řádek obsahuje jednu metriku.</li><li>**Stará tabulka**: metriky \_ regionserver \_ CL, metriky \_ regionserver \_ Wal \_ CL, metriky \_ regionserver \_ IPC \_ CL, metriky \_ regionserver \_ OS \_ CL, metriky \_ regionserver \_ Replication \_ CL, metriky \_ restserver \_ CL, metriky \_ restserver \_ JVM \_ CL, metriky \_ hmaster \_ assignmentmanager \_ CL, metriky hmaster \_ IPC \_ \_ CL \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ , metriky hmaser. cl, metriky hmaster FS CL, metriky hmaster. cl</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Popis**: Tato tabulka obsahuje protokoly z HBA a její související součásti: Phoenix a HDFS.</li><li>**Stará tabulka**: log \_ regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamenode \_ CL, log \_ uvolňování paměti \_ \_ CL</li></ul>|

## <a name="storm-workload"></a>Úloha zatížení

| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Popis**: Tato tabulka obsahuje stejné metriky JMX jako tabulky v části staré tabulky. Jeho řádky obsahují jednu metriku na záznam.</li><li>**Stará tabulka**: metriky \_ stormnimbus \_ CL, metriky \_ stormsupervisor \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Popis**: Tato tabulka obsahuje metriky úrovně topologie z přestupnosti. Je to stejný tvar jako tabulka uvedená v části staré tabulky.</li><li>**Stará tabulka**: metriky \_ stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Popis**: Tato tabulka obsahuje všechny protokoly vygenerované ze zaplavení.</li><li>**Stará tabulka**: \_ správce protokolu \_ CL, log \_ Nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Oozie úlohy

| Nová tabulka | Podrobnosti |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Popis**: Tato tabulka obsahuje všechny protokoly vygenerované z Oozie Frameworku.</li><li>**Stará tabulka**: log \_ Oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Další kroky
[Dotazování protokolů Azure Monitor pro monitorování clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
