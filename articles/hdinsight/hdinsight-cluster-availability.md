---
title: 'Monitorování: Apache Ambari & Azure Monitor logs – Azure HDInsight'
description: Naučte se používat protokoly Ambari a Azure Monitor ke sledování stavu a dostupnosti clusteru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060166"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Jak monitorovat dostupnost clusteru pomocí protokolů Apache Ambari a Azure Monitor

Clustery HDInsight zahrnují jak Apache Ambari, která poskytuje informace o stavu na první pohled a předdefinované výstrahy, a také Azure Monitor protokoly, které poskytují Queryable metriky a protokoly a také konfigurovatelné výstrahy.

V tomto dokumentu se dozvíte, jak pomocí těchto nástrojů monitorovat cluster a provedou některé příklady konfigurace Ambari výstrahy, monitorování míry dostupnosti uzlu a vytvoření výstrahy Azure Monitor, která se aktivuje při přijetí prezenčního signálu z jednoho nebo více uzlů. za pět hodin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Řídicí panel

K řídicímu panelu Ambari se dostanete tak, že v části **řídicí panely clusteru** v tématu Přehled služby HDInsight v Azure Portal, jak vidíte níže, vyberete odkaz **Domů Ambari** . K němu taky můžete přejít tak, že přejdete na `https://CLUSTERNAME.azurehdinsight.net` v prohlížeči, kde název_clusteru je název vašeho clusteru.

![Zobrazení portálu prostředků HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Pak se zobrazí výzva k zadání uživatelského jména a hesla pro přihlášení ke clusteru. Zadejte přihlašovací údaje, které jste zvolili při vytváření clusteru.

Pak přejdete na řídicí panel Ambari, který obsahuje widgety, které znázorňují několik metriky, které vám poskytnou rychlý přehled o stavu clusteru HDInsight. Tyto pomůcky ukazují metriky, jako je počet aktivních datanodes (pracovních uzlů) a dostupných deníkových uzlů (Zookeeper Node), NameNodes (hlavní uzly) v čase a metriky specifické pro určité typy clusterů, jako je například doba provozu PŘÍZového správce pro clustery Spark a Hadoop.

![Apache Ambari použití zobrazení řídicího panelu](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hostitelé – zobrazit stav jednotlivých uzlů

Můžete také zobrazit informace o stavu pro jednotlivé uzly. Výběrem karty **hostitelé** zobrazíte seznam všech uzlů v clusteru a zobrazíte základní informace o jednotlivých uzlech. Zelená šipka vlevo od každého názvu uzlu znamená, že všechny komponenty jsou v uzlu. Pokud je komponenta na uzlu mimo provoz, zobrazí se místo zelené kontroly červený trojúhelník.

![Zobrazení hostitelů pro HDInsight Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Pak můžete vybrat **název** uzlu a zobrazit tak podrobnější metriky hostitele pro daný uzel. Toto zobrazení ukazuje stav/dostupnost každé jednotlivé součásti.

![Apache Ambari hostuje jedno zobrazení uzlů](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari výstrahy

Ambari také nabízí několik konfigurovatelných výstrah, které mohou poskytnout oznámení o určitých událostech. Když se aktivují výstrahy, zobrazují se v levém horním rohu Ambari červeným znakem, který obsahuje počet výstrah. Výběrem této příznaku se zobrazí seznam aktuálních výstrah.

![Počet aktuálních výstrah Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Chcete-li zobrazit seznam definic výstrah a jejich stavů, vyberte kartu **výstrahy** , jak je uvedeno níže.

![Zobrazení definic upozornění Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari nabízí mnoho předdefinovaných výstrah souvisejících s dostupností, včetně:

| Název výstrahy                        | Popis   |
|---|---|
| Shrnutí stavu datauzel           | Tato výstraha na úrovni služby se aktivuje, pokud existují chybné datauzly.|
| Stav vysoké dostupnosti NameNode | Tato výstraha na úrovni služby se aktivuje, pokud není spuštěný aktivní NameNode nebo pohotovostní NameNode.|
| Procento dostupných dostupných deníkových uzlů    | Tato výstraha se aktivuje, pokud je počet vypnutých dostupných deníkových uzlů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesu JournalNode. |
| Procento dostupných datanode       | Tato výstraha se aktivuje, pokud je počet nefunkčních uzlů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesů datanode.|

Úplný seznam upozornění Ambari, které vám pomůžou monitorovat dostupnost clusteru, najdete [tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui).

Chcete-li zobrazit podrobnosti výstrahy nebo upravit kritéria, vyberte **název** výstrahy. Jako příklad Vezměte v úvahu **souhrn stavu datauzel** . Můžete zobrazit popis výstrahy a také specifická kritéria, která aktivují upozornění "upozornění" nebo "kritická" a interval kontroly pro kritéria. Chcete-li upravit konfiguraci, vyberte tlačítko **Upravit** v pravém horním rohu pole konfigurace.

![Konfigurace upozornění Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Tady můžete popis upravit a důležitější, což je důležitější interval a prahové hodnoty pro upozornění nebo kritické výstrahy.

![Zobrazení úprav konfigurace upozornění Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

V tomto příkladu můžete nastavit 2 chybné datauzly, které aktivují kritickou výstrahu, a 1 špatný datauzel aktivuje pouze upozornění. Po dokončení úprav vyberte **Uložit** .

### <a name="email-notifications"></a>E-mailová oznámení

Volitelně můžete také nakonfigurovat e-mailová oznámení pro Ambari výstrahy. Pokud to chcete provést, klikněte na kartě **výstrahy** v levém horním rohu na tlačítko **Akce** a potom na **spravovat oznámení.**

![Akce spravovat oznámení Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Otevře se dialogové okno pro správu oznámení výstrah. Vyberte **+** v dolní části dialogového okna a vyplňte požadovaná pole a poskytněte Ambari e-mailem podrobnosti serveru, ze kterých se mají odesílat e-maily.

> [!TIP]
> Nastavení e-mailových oznámení Ambari může být dobrým způsobem, jak přijímat výstrahy na jednom místě při správě mnoha clusterů HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor

Protokoly Azure Monitor umožňují shromažďování a agregaci dat vygenerovaných několika prostředky, jako jsou clustery HDInsight, a jejich shromáždění a agregace na jednom místě, abyste dosáhli sjednoceného prostředí monitorování.

Za předpokladu budete potřebovat Log Analytics pracovní prostor pro ukládání shromážděných dat. Pokud jste ho ještě nevytvořili, můžete postupovat podle pokynů zde: [Vytvoření pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Povolit integraci protokolů Azure Monitor HDInsight

Na stránce prostředek clusteru HDInsight na portálu vyberte **Azure monitor**. Pak vyberte **Povolit** a v rozevíracím seznamu vyberte svůj pracovní prostor Log Analytics.

![Sada HDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Tabulky metrik a protokolů dotazů

Jakmile je integrace protokolu Azure Monitor povolená (může to trvat několik minut), přejděte do prostředku **pracovního prostoru Log Analytics** a vyberte **protokoly**.

![Protokoly Log Analytics pracovního prostoru](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Protokoluje seznam několika ukázkových dotazů, například:

| Název dotazu                      | Popis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostupnost počítačů v dnešní době    | Graf počtu počítačů odesílajících protokoly, každou hodinu                     |
| Zobrazit prezenční signály                 | Zobrazit seznam všech prezenčních signálů počítače za poslední hodinu                           |
| Poslední prezenční signál každého počítače | Zobrazit poslední prezenční signál odeslaný jednotlivými počítači                             |
| Nedostupné počítače           | Zobrazí seznam všech známých počítačů, které neodeslaly prezenční signál za posledních 5 hodin. |
| Míra dostupnosti               | Vypočítat míru dostupnosti každého připojeného počítače                |

Jako příklad spusťte dotaz ukázka **míry dostupnosti** tak, že v tomto dotazu vyberete **Spustit** , jak je znázorněno na snímku obrazovky výše. Tím se v procentech zobrazí míra dostupnosti jednotlivých uzlů v clusteru. Pokud jste povolili více clusterů HDInsight, aby odesílaly metriky do stejného pracovního prostoru Log Analytics, zobrazí se v těchto clusterech míra dostupnosti pro všechny uzly.

![Vzorový dotaz k protokolu dostupnosti v pracovním prostoru Log Analytics](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> Míra dostupnosti se měří v průběhu 24 hodin, takže cluster bude muset běžet aspoň 24 hodin, než uvidíte správné sazby dostupnosti.

Tuto tabulku můžete připnout na sdílený řídicí panel tak, že kliknete na **připnout** v pravém horním rohu. Pokud nemáte žádné zapisovatelné řídicí panely, uvidíte, jak ho vytvořit: [vytváření a sdílení řídicích panelů v Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Výstrahy Azure Monitor

Můžete také nastavit výstrahy Azure Monitor, které se aktivují, když hodnota metriky nebo výsledky dotazu splňují určité podmínky. Můžete například vytvořit upozornění k odeslání e-mailu, když jeden nebo více uzlů neodeslal prezenční signál během 5 hodin (tzn. že se předpokládá, že není k dispozici).

V části **protokoly**Spusťte ukázkový dotaz **nedostupných počítačů** , a to tak, že v tomto dotazu vyberete **Spustit** , jak je znázorněno níže.

![Ukázka Log Analytics v pracovním prostoru zaznamenává nedostupné počítače](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Pokud jsou k dispozici všechny uzly, tento dotaz by nyní měl vracet nulový výsledek. Kliknutím na **nové pravidlo výstrahy** zahajte konfiguraci upozornění pro tento dotaz.

![Nové pravidlo výstrahy Log Analytics pracovního prostoru](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Existují tři komponenty výstrahy: *prostředek* , pro který chcete vytvořit pravidlo (Log Analytics pracovní prostor v tomto případě), *podmínku* pro aktivaci výstrahy a *skupiny akcí* , které určují, co se stane, když se výstraha aktivuje.
Klikněte na **název podmínky**, jak je znázorněno níže, a dokončete konfiguraci logiky signálu.

![Podmínka vytvoření pravidla pro upozornění na portál](media/hdinsight-cluster-availability/portal-condition-title.png)

Tím se otevře **Konfigurace signálu**.

Nastavte oddíl **Alert Logic** následujícím způsobem:

*Podle: počet výsledků, podmínka: větší než, prahová hodnota: 0.*

Vzhledem k tomu, že tento dotaz vrátí nedostupné uzly jako výsledky, pokud je počet výsledků stále větší než 0, výstraha by se měla aktivovat.

V části **vyhodnocováno na základě** oddílu nastavte **dobu** a **četnost** podle toho, jak často chcete kontrolovat nedostupné uzly.

Pro účely této výstrahy se chcete ujistit, že **perioda = frekvence.** Další informace o období, četnosti a dalších parametrech výstrahy najdete [tady](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Vyberte **Hotovo** , až budete hotovi s konfigurací logiky signálu.

![Pravidlo výstrahy konfiguruje logiku signálu](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Pokud ještě nemáte existující skupinu akcí, klikněte na **vytvořit nový** v části **skupiny akcí** .

![Pravidlo výstrahy vytvoří novou skupinu akcí.](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Otevře se okno **Přidat skupinu akcí**. Vyberte **název skupiny akcí**, **krátké jméno**, **předplatné**a **skupinu prostředků.** V části **Akce** zvolte **název akce** a jako **typ akce** vyberte **e-mail/SMS/odeslat/hlas** .

> [!NOTE]
> K dispozici je několik dalších akcí, které se můžou aktivovat kromě e-mailu, SMS/nabízeného/hlasu, jako je Azure Functions, LogicApp, Webhook, ITSM a Automation Runbook. [Víc se uč.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Tím se otevře **e-mail/SMS/nabízený/hlas**. Vyberte **jméno** příjemce, **zaškrtněte** políčko **e-mail** a zadejte e-mailovou adresu, na kterou chcete odeslat výstrahu. V **e-mailu/SMS/Push/Voice**vyberte **OK** a potom do **Přidat skupinu akcí** dokončete konfiguraci skupiny akcí.

![Pravidlo výstrahy vytvoří přidat skupinu akcí.](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zavření těchto oken by se měla zobrazit vaše skupina akcí uvedená v části **skupiny akcí** . Nakonec dokončete část **Podrobnosti výstrahy** zadáním názvu a **popisu** **pravidla výstrahy** a zvolením **závažnosti**. Kliknutím na **vytvořit pravidlo výstrahy** dokončete.

![Portál vytvoří dokončení pravidla výstrahy.](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Možnost určit **závažnost** je výkonný nástroj, který se dá použít při vytváření více výstrah. Můžete například vytvořit jednu výstrahu, která vyvolá upozornění (závažnost 1) v případě, že dojde k výpadku jednoho hlavního uzlu, a další výstrahu, která vyvolává kritické (závažnost 0) v nepravděpodobném případě, že oba hlavní uzly vycházejí dolů.

Pokud je splněna podmínka této výstrahy, výstraha se aktivuje a obdržíte e-mail s podrobnostmi výstrahy, jako je tato:

![Příklad e-mailu s výstrahou Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Kliknutím na **výstrahy** v **pracovním prostoru Log Analytics**můžete zobrazit také všechny výstrahy, které byly aktivovány, seskupené podle závažnosti.

![Výstrahy Log Analytics pracovního prostoru](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Výběr u seskupení závažnosti (tj. **závažnost 1, jak je** zvýrazněný výše) zobrazí záznamy pro všechny výstrahy této závažnosti, které byly aktivovány níže:

![Log Analytics pracovní prostor závažnost jednu výstrahu](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Další kroky

- [Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsight](hdinsight-high-availability-linux.md)
