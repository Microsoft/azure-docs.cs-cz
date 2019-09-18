---
title: Jak monitorovat dostupnost clusteru pomocí protokolů Ambari a Azure Monitor
description: Naučte se používat protokoly Ambari a Azure Monitor ke sledování stavu a dostupnosti clusteru.
keywords: monitorování, Ambari, monitorování, Log Analytics, výstrahy, dostupnost, stav
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 153c28dc8a06968dc9dd3cfda021496672a094d5
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076555"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Jak monitorovat dostupnost clusteru pomocí protokolů Ambari a Azure Monitor

Clustery HDInsight zahrnují jak Apache Ambari, která poskytuje informace o stavu na první pohled a předdefinované výstrahy, a také Azure Monitor protokoly, které poskytují Queryable metriky a protokoly a také konfigurovatelné výstrahy.

V tomto dokumentu se dozvíte, jak pomocí těchto nástrojů monitorovat cluster a provedou některé příklady konfigurace Ambari výstrahy, monitorování míry dostupnosti uzlu a vytvoření výstrahy Azure Monitor, která se aktivuje při přijetí prezenčního signálu z jednoho nebo více uzlů. za pět hodin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Řídicí panel

Na řídicí panel Ambari se dostanete tak, že kliknete na odkaz **Ambari domů** v části **řídicí panely clusteru** v okně přehled služby HDInsight v Azure Portal, jak je znázorněno níže. Případně můžete k němu přistupovat zadáním následující adresy URL v prohlížeči [https://\<název_clusteru\>. azurehdinsight.NET.](https://clustername.azurehdinsight.net/)

![Zobrazení portálu prostředků HDInsight](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Pak se zobrazí výzva k zadání uživatelského jména a hesla pro přihlášení ke clusteru. Zadejte přihlašovací údaje, které jste zvolili při vytváření clusteru.

Pak přejdete na řídicí panel Ambari, který obsahuje widgety, které znázorňují několik metriky, které vám poskytnou rychlý přehled o stavu clusteru HDInsight. Tyto pomůcky ukazují metriky, jako je počet aktivních datanodes (pracovních uzlů) a dostupných deníkových uzlů (Zookeeper Node), NameNodes (hlavní uzly) v čase a metriky specifické pro určité typy clusterů, jako je například doba provozu PŘÍZového správce pro clustery Spark a Hadoop.

![Apache Ambari použití zobrazení řídicího panelu](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hostitelé – zobrazit stav jednotlivých uzlů

Můžete také zobrazit informace o stavu pro jednotlivé uzly. Kliknutím na kartu **hostitelé** zobrazíte seznam všech uzlů v clusteru a zobrazíte základní informace o jednotlivých uzlech. Zelená šipka vlevo od každého názvu uzlu znamená, že všechny komponenty jsou v uzlu. Pokud je komponenta na uzlu mimo provoz, zobrazí se místo zelené kontroly červený trojúhelník.

![Zobrazení hostitelů pro HDInsight Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Pak můžete kliknout na **název** uzlu a zobrazit tak podrobnější metriky hostitele pro daný uzel. Toto zobrazení ukazuje stav/dostupnost každé jednotlivé součásti.

![Apache Ambari hostuje jedno zobrazení uzlů](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari výstrahy

Ambari také nabízí několik konfigurovatelných výstrah, které mohou poskytnout oznámení o určitých událostech. Když se aktivují výstrahy, zobrazují se v levém horním rohu Ambari červeným znakem, který obsahuje počet výstrah. Po kliknutí na tento odkaz se zobrazí seznam aktuálních výstrah.

![Počet aktuálních výstrah Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Pokud chcete zobrazit seznam definic upozornění a jejich stavů, klikněte na kartu **výstrahy** , jak vidíte níže.

![Zobrazení definic upozornění Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari nabízí mnoho předdefinovaných výstrah souvisejících s dostupností, včetně:

| Název výstrahy                        | Popis                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Shrnutí stavu datauzel           | Tato výstraha na úrovni služby se aktivuje, pokud existují chybné datauzly.                                                                                                                |
| Stav vysoké dostupnosti NameNode | Tato výstraha na úrovni služby se aktivuje v případě, že aktivní NameNode nebo pohotovostní NameNode nejsou spuštěné.                                                                              |
| Procento dostupných dostupných deníkových uzlů    | Tato výstraha se aktivuje, pokud je počet vypnutých dostupných deníkových uzlů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesu JournalNode. |
| Procento dostupných datanode       | Tato výstraha se aktivuje, pokud je počet nefunkčních uzlů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesů datanode.       |

Úplný seznam upozornění Ambari, které vám pomůžou monitorovat dostupnost clusteru, najdete [tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui).

Chcete-li zobrazit podrobnosti výstrahy nebo upravit kritéria, klikněte na **název** výstrahy. Jako příklad Vezměte v úvahu **souhrn stavu datauzel** . Můžete zobrazit popis výstrahy a také specifická kritéria, která aktivují upozornění "upozornění" nebo "kritická" a interval kontroly pro kritéria. Chcete-li upravit konfiguraci, klikněte na tlačítko **Upravit** v pravém horním rohu pole konfigurace.

![Konfigurace upozornění Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Tady můžete popis upravit a důležitější, což je důležitější interval a prahové hodnoty pro upozornění nebo kritické výstrahy.

![Zobrazení pro úpravy konfigurace upozornění Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

V tomto příkladu můžete nastavit 2 chybné datauzly, které aktivují kritickou výstrahu, a 1 špatný datauzel aktivuje pouze upozornění. Po dokončení úprav klikněte na **Uložit** .

### <a name="email-notifications"></a>E-mailová oznámení

Volitelně můžete také nakonfigurovat e-mailová oznámení pro Ambari výstrahy. Pokud to chcete provést, klikněte na kartě **výstrahy** v levém horním rohu na tlačítko **Akce** a potom na **spravovat oznámení.**

![Akce spravovat oznámení Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Otevře se dialogové okno pro správu oznámení výstrah. **+** Klikněte v dolní části dialogového okna a vyplňte požadovaná pole a poskytněte Ambari podrobnosti e-mailového serveru, ze kterých se mají posílat e-maily.

> [!TIP]
> Nastavení e-mailových oznámení Ambari může být dobrým způsobem, jak přijímat výstrahy na jednom místě při správě mnoha clusterů HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor

Protokoly Azure Monitor umožňují shromažďování a agregaci dat vygenerovaných několika prostředky, jako jsou clustery HDInsight, na jednom místě, abyste dosáhli jednotného prostředí pro monitorování.

Za předpokladu budete potřebovat Log Analytics pracovní prostor pro ukládání shromážděných dat. Pokud jste ho ještě nevytvořili, můžete postupovat podle pokynů: [Vytvořte pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Povolit integraci protokolů Azure Monitor HDInsight

Na stránce prostředek clusteru HDInsight na portálu klikněte na okno **Operations Management Suite** . Pak klikněte na **Povolit** a v rozevíracím seznamu vyberte svůj pracovní prostor Log Analytics.

![Okno HDInsight Operations Management Suite](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Dotazování metrik a protokolů do tabulek v okně protokoly

Jakmile je integrace protokolu Azure Monitor povolená (může to trvat několik minut), přejděte do svého prostředku **pracovního prostoru Log Analytics** a klikněte na okno **protokoly** .

![Okno protokolů Log Analytics pracovního prostoru](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

V okně **protokoly** se zobrazuje počet ukázkových dotazů, například:

| Název dotazu                      | Popis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostupnost počítačů v dnešní době    | Graf počtu počítačů odesílajících protokoly, každou hodinu                     |
| Zobrazit prezenční signály                 | Zobrazit seznam všech prezenčních signálů počítače za poslední hodinu                           |
| Poslední prezenční signál každého počítače | Zobrazit poslední prezenční signál odeslaný jednotlivými počítači                             |
| Nedostupné počítače           | Zobrazí seznam všech známých počítačů, které neodeslaly prezenční signál za posledních 5 hodin. |
| Míra dostupnosti               | Vypočítat míru dostupnosti každého připojeného počítače                |

Příklad: Spusťte dotaz ukázka **míry dostupnosti** kliknutím na **Spustit** u tohoto dotazu, jak je znázorněno na snímku obrazovky výše. Tím se v procentech zobrazí míra dostupnosti jednotlivých uzlů v clusteru. Pokud jste povolili více clusterů HDInsight, aby odesílaly metriky do stejného pracovního prostoru Log Analytics, zobrazí se sazba dostupnosti všech uzlů v těchto clusterech.

![Vzorový dotaz sazby dostupnosti v okně Log Analytics v pracovním prostoru](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Míra dostupnosti se měří v průběhu 24 hodin, takže cluster bude muset běžet aspoň 24 hodin, než uvidíte správné sazby dostupnosti.

Tuto tabulku můžete připnout na sdílený řídicí panel tak, že kliknete na **připnout** v pravém horním rohu. Pokud nemáte žádné zapisovatelné řídicí panely, uvidíte, jak ho vytvořit: [Vytváření a sdílení řídicích panelů v Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Výstrahy Azure Monitor

Můžete také nastavit výstrahy Azure Monitor, které se aktivují, když hodnota metriky nebo výsledky dotazu splňují určité podmínky. Můžete například vytvořit upozornění k odeslání e-mailu, když jeden nebo více uzlů neodeslal prezenční signál během 5 hodin (tzn. že se předpokládá, že není k dispozici).

V okně **protokoly** Spusťte ukázkový dotaz **nedostupné počítače** kliknutím na **Spustit** u tohoto dotazu, jak je znázorněno níže.

![Ukázka Log Analytics v pracovním prostoru – ukázka nedostupných počítačů](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Pokud jsou k dispozici všechny uzly, tento dotaz by nyní měl vracet 0 výsledků. Kliknutím na **nové pravidlo výstrahy** zahajte konfiguraci upozornění pro tento dotaz.

![Nové pravidlo výstrahy Log Analytics pracovního prostoru](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Existují tři komponenty výstrahy: *prostředek* , pro který chcete vytvořit pravidlo (Log Analytics pracovní prostor v tomto případě), *podmínku* pro aktivaci výstrahy a *skupiny akcí* , které určují, co se stane, když je výstraha aktivuje.

Klikněte na **název podmínky**, jak je znázorněno níže, a dokončete konfiguraci logiky signálu.

![Podmínka vytvoření pravidla pro upozornění na portál](media/hdinsight-cluster-availability/portal-condition-title.png)

Tím se otevře okno **Konfigurovat logiku signálu** .

Nastavte oddíl **Alert Logic** následujícím způsobem:

*Založené na: Počet výsledků, podmínka: Větší než, prahová hodnota: 0,8.*

Vzhledem k tomu, že tento dotaz vrátí nedostupné uzly jako výsledky, pokud je počet výsledků stále větší než 0, výstraha by se měla aktivovat.

V části **vyhodnocováno na základě** oddílu nastavte **dobu** a **četnost** podle toho, jak často chcete kontrolovat nedostupné uzly.

Všimněte si, že pro účely této výstrahy se chcete ujistit, že **perioda = frekvence.** Další informace o období, četnosti a dalších parametrech výstrahy najdete [tady](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Po dokončení konfigurace logiky signálu klikněte na **Hotovo** .

![Pravidlo výstrahy konfigurace logiky signálů](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Pokud ještě nemáte existující skupinu akcí, klikněte na **vytvořit nový** v části **skupiny akcí** .

![Pravidlo výstrahy vytvořit novou skupinu akcí](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Tím se otevře okno **Přidat skupinu akcí** . Vyberte **název skupiny akcí**, **krátké jméno**, **předplatné**a **skupinu prostředků.** V části **Akce** zvolte **název akce** a jako **typ akce** vyberte **e-mail/SMS/odeslat/hlas** .

> [!NOTE]
> K dispozici je několik dalších akcí, které se můžou aktivovat kromě e-mailu, SMS/nabízeného/hlasu, jako je Azure Functions, LogicApp, Webhook, ITSM a Automation Runbook. [Víc se uč.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Tím se otevře okno **e-mail/SMS/Push/Voice** . Vyberte **jméno** příjemce, **zaškrtněte** políčko **e-mail** a zadejte e-mailovou adresu, na kterou chcete odeslat výstrahu. Klikněte na **OK** v okně **e-mail/SMS/Push/Voice** a potom v okně **Přidat skupinu akcí** dokončete konfiguraci skupiny akcí.

![Pravidlo upozornění vytvořit přidat skupinu akcí](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zavření těchto oken by se měla zobrazit vaše skupina akcí uvedená v části **skupiny akcí** . Nakonec dokončete část **Podrobnosti výstrahy** zadáním názvu a **popisu** **pravidla výstrahy** a zvolením **závažnosti**.
Kliknutím na **vytvořit pravidlo výstrahy** dokončete.

![Dokončit vytvoření pravidla výstrahy na portálu](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Možnost určit **závažnost** je výkonný nástroj, který se dá použít při vytváření více výstrah. Můžete například vytvořit jednu výstrahu, která vyvolá upozornění (závažnost 1) v případě, že dojde k výpadku jednoho hlavního uzlu, a další výstrahu, která vyvolává kritické (závažnost 0) v nepravděpodobném případě, že oba hlavní uzly vycházejí dolů.

Pokud je splněna podmínka této výstrahy, výstraha se aktivuje a obdržíte e-mail s podrobnostmi výstrahy, jako je tato:

![Příklad e-mailu s výstrahou Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Kliknutím na okno **výstrahy** v **pracovním prostoru Log Analytics**můžete zobrazit také všechny výstrahy, které byly aktivovány, seskupené podle závažnosti.

![Výstrahy Log Analytics pracovního prostoru](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Kliknutím na seskupení závažnosti (tj. **závažnost 1, jak je** zvýrazněné výše) se zobrazí záznamy pro všechny výstrahy této závažnosti, které byly aktivovány níže:

![Závažnost upozornění na pracovní prostor Log Analytics 1](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Další postup
- [Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsight](hdinsight-high-availability-linux.md)
