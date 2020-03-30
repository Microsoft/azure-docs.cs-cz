---
title: 'Monitorování: Protokoly Apache Ambari & Azure Monitor – Azure HDInsight'
description: Zjistěte, jak pomocí protokolů Ambari a Azure Monitor monitoru monitorovat stav a dostupnost clusteru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060166"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Jak sledovat dostupnost clusteru pomocí protokolů Apache Ambari a Azure Monitor

HdInsight clustery zahrnují jak Apache Ambari, který poskytuje informace o stavu na první pohled a předdefinované výstrahy, stejně jako integrace protokolů Azure Monitor, který poskytuje dotazovatelné metriky a protokoly, stejně jako konfigurovatelné výstrahy.

Tento dokument ukazuje, jak pomocí těchto nástrojů sledovat cluster a prochází některé příklady pro konfiguraci výstrahy Ambari, sledování rychlosti dostupnosti uzlů a vytvoření výstrahy Azure Monitor, která se aktivuje, když prezenční signál nebyl přijat z jednoho nebo více uzlů za pět hodin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Řídicí panel

K řídicímu panelu Ambari se dostanete tak, že vyberete domovskou linku **Ambari** v části **Řídicí panely clusteru** v přehledu HDInsight na webu Azure Portal, jak je znázorněno níže. Alternativně lze přistupovat na přechod `https://CLUSTERNAME.azurehdinsight.net` v prohlížeči, kde CLUSTERNAME je název clusteru.

![Zobrazení portálu prostředků HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Poté budete vyzváni k zadání uživatelského jména a hesla přihlášení k clusteru. Zadejte přihlašovací údaje, které jste zvolili při vytváření clusteru.

Poté budete převedeni na řídicí panel Ambari, který obsahuje widgety, které zobrazují několik metrik, které vám poskytnou rychlý přehled o stavu clusteru HDInsight. Tyto widgety zobrazují metriky, jako je počet živých datových nodů (pracovníu uzlů) a deníků Nodes (uzel zookeeper), namenodes (hlavní uzly) uptime, stejně jako metriky specifické pro určité typy clusterů, jako je yarn ResourceManager uptime pro clustery Spark a Hadoop.

![Apache Ambari používá displej palubní desky](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hostitelé – zobrazení stavu jednotlivých uzlů

Můžete také zobrazit informace o stavu pro jednotlivé uzly. Výběrem karty **Hosts** zobrazíte seznam všech uzlů v clusteru a zobrazí se základní informace o jednotlivých uzlech. Zelená kontrola nalevo od každého názvu uzlu označuje, že všechny součásti jsou na uzlu. Pokud je komponenta na uzlu dole, zobrazí se místo zelené kontroly červený výstražný trojúhelník.

![HDInsight Apache Ambari hostitelé zobrazení](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Potom můžete vybrat na **název** uzlu pro zobrazení podrobnější metriky hostitele pro tento konkrétní uzel. Toto zobrazení zobrazuje stav/dostupnost jednotlivých komponent.

![Apache Ambari hostuje zobrazení jednoho uzlu](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Upozornění ambari

Ambari také nabízí několik konfigurovatelných výstrah, které mohou poskytnout oznámení o určitých událostech. Když se spustí upozornění, zobrazí se v levém horním rohu Ambari v červeném odznaku obsahujícím počet upozornění. Výběrem tohoto odznaku se zobrazí seznam aktuálních upozornění.

![Počet aktuálních výstrah Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Chcete-li zobrazit seznam definic výstrah a jejich stavů, vyberte kartu **Výstrahy,** jak je znázorněno níže.

![Zobrazení definic výstrah Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari nabízí mnoho předdefinovaných upozornění týkajících se dostupnosti, včetně:

| Název výstrahy                        | Popis   |
|---|---|
| Souhrn stavu datanode           | Tato výstraha na úrovni služby se aktivuje, pokud existují nefunkční datanodes|
| NázevNode Stav vysoké dostupnosti | Tato výstraha na úrovni služby se aktivuje, pokud není spuštěna aktivní název namenode nebo pohotovostní název NameNode.|
| Procento deníkůJsou k dispozici    | Tato výstraha se aktivuje, pokud je počet dolů Deníknodes v clusteru je větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesu JournalNode. |
| Procento datových nodů k dispozici       | Tato výstraha se aktivuje, pokud je počet dolů DataNodes v clusteru je větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesů DataNode.|

Úplný seznam ambariských výstrah, které pomáhají sledovat dostupnost clusteru, naleznete [zde](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Chcete-li zobrazit podrobnosti o výstraze nebo upravit kritéria, vyberte **název** výstrahy. Jako příklad vezměte **souhrn stavu DataNode.** Můžete vidět popis výstrahy, jakož i konkrétní kritéria, která spustí "upozornění" nebo "kritické" výstrahy a kontrolní interval pro kritéria. Chcete-li upravit konfiguraci, vyberte tlačítko **Upravit** v pravém horním rohu pole Konfigurace.

![Konfigurace výstrah Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Zde můžete upravit popis a co je důležitější, interval kontroly a prahové hodnoty pro upozornění nebo kritické výstrahy.

![Zobrazení úprav konfigurací výstrah Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

V tomto příkladu můžete způsobit, že 2 nefunkční datanodes spustí kritickou výstrahu a 1 není v pořádku DataNode pouze aktivovat upozornění. Až budete s úpravami hotovi, vyberte **Uložit.**

### <a name="email-notifications"></a>E-mailová oznámení

Můžete také volitelně konfigurovat e-mailová oznámení pro upozornění Ambari. Chcete-li to provést, když na kartě **Výstrahy** klikněte na tlačítko **Akce** v levém horním rohu a potom **na Spravovat oznámení.**

![Ambari spravovat oznámení akce](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Otevře se dialogové okno pro správu oznámení výstrah. Vyberte **+** v dolní části dialogového okna a vyplňte požadovaná pole, abyste ambari poskytli podrobnosti o e-mailovém serveru, ze kterých chcete odesílat e-maily.

> [!TIP]
> Nastavení e-mailových oznámení Ambari může být dobrým způsobem, jak přijímat upozornění na jednom místě při správě mnoha clusterů HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor

Protokoly Azure Monitor umožňují data generovaná více prostředky, jako jsou clustery HDInsight, shromažďovat a agregovat na jednom místě k dosažení jednotného monitorování.

Jako předpoklad budete potřebovat pracovní prostor Analýzy protokolů k ukládání shromážděných dat. Pokud jste ho ještě nevytvořili, můžete postupovat podle pokynů zde: [Vytvoření pracovního prostoru Analýzy protokolů](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Povolení integrace protokolů HDInsight Azure Monitor

Na stránce prostředků clusteru HDInsight na portálu vyberte **Azure Monitor**. Potom vyberte **povolit** a vyberte pracovní prostor Log Analytics z rozevíracího přehledu.

![Sada správy operací HDInsight](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Metriky dotazů a tabulky protokolů

Po povolení integrace protokolu Azure Monitor (to může trvat několik minut), přejděte na prostředek **pracovního prostoru Analýzy protokolů** a vyberte **protokoly**.

![Protokoly pracovního prostoru Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Protokoly seznam řadu ukázkových dotazů, například:

| Název dotazu                      | Popis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostupnost počítačů dnes    | Graf počtu počítačů odesílajících protokoly za hodinu                     |
| Seznam prezenčních signálů                 | Vypsat všechny prezenční signály počítače za poslední hodinu                           |
| Poslední prezenční signál každého počítače | Zobrazit poslední prezenční signál odeslaný jednotlivými počítači                             |
| Nedostupné počítače           | Seznam všech známých počítačů, které neodeslali prezenční signál za posledních 5 hodin |
| Míra dostupnosti               | Výpočet míry dostupnosti každého připojeného počítače                |

Jako příklad spusťte ukázkový dotaz **Míra dostupnosti** výběrem **spustit** na tento dotaz, jak je znázorněno na obrázku výše. To se zobrazí míra dostupnosti každého uzlu v clusteru jako procento. Pokud jste povolili více clusterů HDInsight k odesílání metrik do stejného pracovního prostoru Log Analytics, zobrazí se míra dostupnosti pro všechny uzly v těchto zobrazených clusterech.

![Ukázkový dotaz protokolu Analytics protokoluje protokoly "míra dostupnosti"](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> Míra dostupnosti se měří po dobu 24 hodin, takže váš cluster bude muset běžet alespoň 24 hodin, než se zobrazí přesné míry dostupnosti.

Tuto tabulku můžete připnout ke sdílenému řídicímu panelu kliknutím na **Připnout** v pravém horním rohu. Pokud nemáte žádné zapisovatelné sdílené řídicí panely, uvidíte, jak ho vytvořit tady: [Vytváření a sdílení řídicích panelů na webu Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Výstrahy Azure Monitoru

Můžete také nastavit výstrahy Azure Monitor, které se spustí, když hodnota metriky nebo výsledky dotazu splňují určité podmínky. Jako příklad vytvoříme upozornění pro odeslání e-mailu, pokud jeden nebo více uzlů neodeslal prezenční signál za 5 hodin (tj. předpokládá se, že není k dispozici).

V **protokolech**spusťte ukázkový dotaz **Nedostupné počítače** výběrem **možnosti Spustit** v tomto dotazu, jak je znázorněno níže.

![Protokolanalytics pracovní prostor protokoly 'nedostupné počítače' ukázka](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Pokud jsou k dispozici všechny uzly, tento dotaz by měl vrátit nulové výsledky pro tuto chvíli. Klepnutím na **tlačítko Nové pravidlo výstrahy** zahájíte konfiguraci výstrahy pro tento dotaz.

![Nové pravidlo výstrahy pracovního prostoru Log Analytics](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Existují tři součásti výstrahy: *prostředek,* pro který chcete vytvořit pravidlo (pracovní prostor Analýzy protokolů v tomto případě), *podmínka* pro aktivaci výstrahy a *skupiny akcí,* které určují, co se stane při aktivaci výstrahy.
Kliknutím na **název podmínky**, jak je znázorněno níže, dokončete konfiguraci logiky signálu.

![Podmínka pravidla vytvoření portálu](media/hdinsight-cluster-availability/portal-condition-title.png)

Otevře **se konfigurace logiky signálu**.

Nastavte část **logiky výstrahy** takto:

*Na základě: Počet výsledků, Podmínka: Větší než, Práh: 0.*

Vzhledem k tomu, že tento dotaz vrátí pouze nedostupné uzly jako výsledky, pokud je počet výsledků stále větší než 0, výstraha by měla být sfire.

V části **Vyhodnocení na základě** nastavte **období** a **frekvenci** na základě toho, jak často chcete kontrolovat nedostupné uzly.

Pro účely této výstrahy se chcete ujistit, **že period=frekvence.** Další informace o období, četnosti a dalších parametrech výstrah naleznete [zde](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Po dokončení konfigurace logiky signálu vyberte **Hotovo.**

![Pravidlo výstrahy konfiguruje logiku signálu](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Pokud ještě nemáte existující skupinu akcí, klikněte v části **Skupiny akcí** na **Vytvořit nový.**

![Pravidlo výstrahy vytvoří novou skupinu akcí](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Otevře se **skupina akcí Přidat**. Zvolte **název skupiny akcí**, Krátký **název**, **Předplatné**a **Skupina prostředků.** V části **Akce** zvolte **název akce** a jako typ akce vyberte **E-mail/SMS/Push/Hlas.** **Action Type.**

> [!NOTE]
> Existuje několik dalších akcí, které může výstraha aktivovat kromě e-mailu/SMS/Push/Hlasové, jako je například funkce Azure, LogicApp, Webhook, ITSM a automatizační sada Runbook. [Víc se uč.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Tím se otevře **e-mail/SMS/Push/Voice**. Vyberte **jméno** příjemce, **zaškrtněte** **políčko E-mail** a zadejte e-mailovou adresu, na kterou chcete upozornění odeslat. Vyberte **OK** v **e-mailu/SMS/Push/Voice**, pak ve **skupině Přidat akce** dokončete konfiguraci skupiny akcí.

![Pravidlo výstrahy vytvoří skupinu akcí přidání.](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zavření těchto nožů byste měli vidět skupinu akcí uvedenou v části **Skupiny akcí.** Nakonec dokončete část **Podrobnosti výstrahy** zadáním názvu a **popisu** **pravidla výstrahy** a výběrem **závažnosti**. **Chcete-li dokončit,** klepněte na tlačítko Vytvořit pravidlo výstrahy.

![Portál vytvoří dokončení pravidla výstrahy.](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Možnost zadat **závažnost** je výkonný nástroj, který lze použít při vytváření více výstrah. Můžete například vytvořit jednu výstrahu pro zvýšení upozornění (Sev 1), pokud jeden hlavní uzel přejde dolů a další výstraha, která vyvolá kritické (Sev 0) v nepravděpodobném případě, že oba hlavní uzly přejít dolů.

Pokud je splněna podmínka pro tuto výstrahu, výstraha se vyznítí a obdržíte e-mail s podrobnostmi upozornění, jako je tento:

![Příklad e-mailu s výstrahami Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Můžete také zobrazit všechny výstrahy, které byly aktivovány, seskupené podle závažnosti, přejdete do **výstrahy** v **pracovním prostoru Analýzy protokolů**.

![Výstrahy pracovního prostoru Analýzy protokolů](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Výběrem na seskupování závažnosti (tj. **sev 1,** jak je zvýrazněno výše) se zobrazí záznamy pro všechny výstrahy této závažnosti, které mají vystřelil jako níže:

![Protokol Analytics pracovní ho sv jednu výstrahu](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Další kroky

- [Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsightu](hdinsight-high-availability-linux.md)
