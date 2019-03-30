---
title: Jak monitorovat dostupnost clusteru pomocí protokolů Ambari a Azure Monitor
description: Další informace o použití protokoly Ambari a Azure Monitor k monitorování stavu clusteru a dostupnost.
keywords: ambari, monitorování, log analytics, upozornění, dostupnost, stav monitorování
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 9bf0c0f45007f81312ac08230d571b727220bcd9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671225"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Jak monitorovat dostupnost clusteru pomocí protokolů Ambari a Azure Monitor

Clustery HDInsight zahrnují Apache Ambari, který poskytuje informace o stavu na první pohled a předdefinované výstrahy, i integraci protokolů Azure Monitor, která poskytuje dotazovatelné metriky a protokoly a konfigurovatelných upozornění.

Tento dokument ukazuje, jak pomocí těchto nástrojů pro monitorování clusteru a provede několik příkladů, pro konfiguraci Ambari upozornění, monitorování míry dostupnosti uzlu a vytvoření výstrahy monitorování Azure, který se aktivuje při z jednoho nebo více uzlů nebyl přijat prezenční signál za pět hodin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Řídicí panel

Řídicí panel Ambari je přístupná po kliknutí **Ambari domácí** odkaz v **řídicí panely clusteru** část HDInsight okno Přehled na webu Azure portal, jak je znázorněno níže. Alternativně můžete přistupovat zadáním následující adresy URL v prohlížeči [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Zobrazení portálu prostředků HDInsight](media/hdinsight-cluster-availability/portal-overview.png)

Pak zobrazí výzva pro uživatelské jméno přihlášení clusteru a heslo. Zadejte přihlašovací údaje jste zvolili při vytváření clusteru.

Pak přejdete na řídicí panel Ambari, který obsahuje pomůcek, které ukazují na několik metrik získáte rychlý přehled o stavu vašeho clusteru HDInsight. Tyto pomůcky zobrazit metriky, jako je počet skutečných DataNodes (pracovní uzly) a JournalNodes (uzel zookeeper), dostupnost NameNodes (hlavní uzly), jako zdroj metriky, které jsou specifické pro určité typy clusterů, jako jsou YARN ResourceManager dostupnost clusterů Spark a Hadoop.

![Řídicí panel Ambari](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hostitelé – zobrazení stavu jednotlivých uzlů

Můžete také zobrazit informace o stavu pro jednotlivé uzly. Klikněte na tlačítko **hostitele** kartu k zobrazení seznamu všech uzlů v clusteru a si zobrazili základní informace o každém uzlu. Zelená značka zaškrtnutí nalevo od názvu každého uzlu označuje, že jsou všechny součásti na uzlu. Pokud komponenta nefunguje v uzlu, zobrazí se červený výstrah trojúhelník místo zelená značka zaškrtnutí.

![Hostitelé zobrazení Ambari](media/hdinsight-cluster-availability/ambari-hosts.png)

Potom můžete kliknout na **název** uzlu, chcete-li zobrazit podrobnější metriky hostitele pro tento konkrétní uzel. Toto zobrazení uvádí stav a dostupnost jednotlivých součástí.

![Hostitelé Ambari jednotné zobrazení uzlů](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari výstrahy

Ambari také nabízí několik konfigurovatelných upozornění, které poskytují oznámení o určité události. Když se aktivují upozornění, zobrazí se v levém horním rohu Ambari v red znaku obsahující počet výstrah. Kliknutím na toto oznámení "BADGE" zobrazí seznam aktuální výstrahy.

![Počet výstrah Ambari](media/hdinsight-cluster-availability/ambari-alerts.png)

Chcete-li zobrazit seznam definic upozornění a jejich stavy, klikněte na tlačítko **výstrahy** kartu, jak je znázorněno níže.

![Zobrazení definice upozornění Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari nabízí mnoho předdefinovaných výstrah souvisejících s dostupností, včetně:

| Název výstrahy                        | Popis                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Souhrn stavu DataNode           | Tato úroveň služby výstraha se aktivuje, pokud není v pořádku DataNodes                                                                                                                |
| Stav NameNode vysoké dostupnosti | Tato úroveň služby výstraha se aktivuje, pokud nejsou spuštěné na aktivní NameNode nebo NameNode pohotovostní režim.                                                                              |
| Procento JournalNodes k dispozici    | Tato výstraha se aktivuje, pokud počet dolů JournalNodes v clusteru je větší než nastavená prahová hodnota důležité. Agreguje výsledky kontrol JournalNode procesu. |
| Procento DataNodes k dispozici       | Tato výstraha se aktivuje, pokud počet dolů DataNodes v clusteru je větší než nastavená prahová hodnota důležité. Agreguje výsledky kontrol DataNode procesu.       |

Úplný seznam Ambari výstrahy monitoru pomoc najdete dostupnost clusteru [tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Chcete-li zobrazit podrobnosti o výstraze nebo upravit kritéria, klikněte na tlačítko **název** výstrahy. Využijte **souhrn stavu DataNode** jako příklad. Zobrazí se popis výstrahy a také konkrétní kritéria, která se aktivuje upozornění na "varování" nebo "kritický" a intervalu kontroly pro kritéria. Chcete-li upravit konfiguraci, klikněte na tlačítko **upravit** tlačítko v pravém horním rohu konfigurační pole.

![Konfigurace výstrah Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Tady můžete upravit popis a důležitější je, kontrola interval a prahové hodnoty pro upozornění nebo kritickou výstrahu.

![Zobrazení pro úpravy konfigurace výstrah Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

V tomto příkladu může provést 2 není v pořádku DataNodes aktivují kritickou výstrahu a není v pořádku DataNode pouze aktivačních událostí: 1 upozornění. Klikněte na tlačítko **Uložit** po dokončení úprav.

### <a name="email-notifications"></a>E-mailová oznámení

Také v případě potřeby můžete nakonfigurovat e-mailová oznámení pro výstrahy Ambari. K tomu, kdy na **výstrahy** klikněte na tlačítko **akce** tlačítko v levém horním rohu, pak **spravovat oznámení.**

![Ambari spravovat akce oznámení](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Otevře se dialogové okno pro správu oznámení výstrah. Klikněte na tlačítko **+** v dolní části dialogového okna a zadejte požadovaná pole k poskytování Ambari pomocí e-mailu podrobnosti o serveru, ze kterého chcete odeslat e-mailů.

> [!TIP]
> Nastavení Ambari e-mailová oznámení může být dobrým způsobem, jak dostávat upozornění na jednom místě, při správě mnoha clusterů HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor

Azure Monitor protokolů umožňuje dat vygenerovaných vašimi více zdrojů, jako je například clustery HDInsight, shromažďování a agregovat na jednom místě pro zajištění unifikovaného prostředí monitorování.

Předpokladem je budete potřebovat pracovní prostor Log Analytics pro ukládání shromážděných dat. Pokud jste jednu ještě nevytvořili, můžete postupujte podle pokynů tady: [Vytvořit pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Povolit integraci protokolů HDInsight Azure Monitor

Na stránce prostředků clusteru HDInsight na portálu klikněte na tlačítko **Operations Management Suite** okno. Potom klikněte na **povolit** a vyberte pracovní prostor Log Analytics z rozevíracího seznamu.

![Okno HDInsight Operations Management Suite](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Dotazování tabulky metrik a protokolů v okně protokoly

Po povolení integrace protokolů Azure Monitor (to může trvat několik minut), přejděte do vaší **pracovní prostor Log Analytics** prostředek a klikněte na kartu **protokoly** okno

![Okno protokoly pracovního prostoru log Analytics](media/hdinsight-cluster-availability/portal-logs.png)

**Protokoly** okno uvádí několik ukázkových dotazů, jako například:

| Název dotazu                      | Popis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostupnost počítače ještě dnes    | Graf počtu počítačů odesílajících protokoly, každou hodinu                     |
| Seznam prezenční signály                 | Seznam všech počítačů prezenční signály poslední hodina                           |
| Poslední prezenční signál jednotlivých počítačů | Zobrazit poslední prezenční signál odesílaných každý počítač                             |
| Není k dispozici počítače           | Seznam všech známých počítačů, které se nepovedlo odeslat prezenční signál za posledních 5 hodin |
| Míra dostupnosti               | Vypočítat sazbu dostupnost všech připojených počítačů                |

Jako příklad, spusťte **míry dostupnosti** ukázkový dotaz kliknutím **spustit** na daný dotaz, jak je znázorněno na snímku obrazovky výše. Míry dostupnosti každého uzlu v clusteru zobrazí v procentech. Pokud jste povolili více clusterů HDInsight k odeslání metrik do stejného pracovního prostoru Log Analytics, zobrazí se míra dostupnosti pro všechny uzly na těchto clusterech zobrazí.

![Analytics pracovní prostor protokolů okno "sazby dostupnosti" Ukázkový dotaz protokolu](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Dostupnost rychlost se měří po dobu 24 hodin, tak váš cluster potřebuje ke spuštění pro nejméně 24 hodin, než uvidíte přesný dostupnost sazby.

V této tabulce na sdílený řídicí panel můžete připnout kliknutím **Pin** v pravém horním rohu. Pokud nemáte žádné zapisovatelné sdílené řídicí panely, můžete zjistit, jak ho vytvořit tady: [Vytvářejte a sdílejte řídicí panely na webu Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control).

### <a name="azure-monitor-alerts"></a>Azure Monitor výstrah

Můžete také nastavit výstrahy monitorování Azure, které se aktivuje při hodnota metriky nebo výsledky dotazu splnění určitých podmínek. Jako příklad, Pojďme vytvořit výstrahu pro odeslání e-mailu, když jeden nebo více uzlů neodeslal prezenční signál za 5 hodin (například se předpokládá, že nebudou k dispozici).

Z **protokoly** okno, spusťte **nedostupnými počítači** ukázkový dotaz kliknutím **spustit** na daný dotaz, jak je znázorněno níže.

![Analytics pracovní prostor protokolů okno "není k dispozici počítače" Ukázkový dotaz protokolu](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Pokud jsou k dispozici všechny uzly, tento dotaz by měl vrátit 0 výsledků teď. Klikněte na tlačítko **nové pravidlo upozornění** chcete začít konfigurovat upozornění pro tento dotaz.

![Log Analytics pracovní prostor nové pravidlo upozornění](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Existují tři komponenty na upozornění služby: *prostředků* pro který chcete vytvořit pravidlo (pracovnímu prostoru Log Analytics v tomto případě), *podmínku* pro aktivaci upozornění a *skupiny akcí*  , které definují, co se stane, když se aktivuje upozornění.

Klikněte na tlačítko **podmínka vyhodnocena jako nadpis**, jak je znázorněno níže, k dokončení konfigurace logiku signálů.

![Podmínka pro pravidlo výstrahy](media/hdinsight-cluster-availability/portal-condition-title.png)

Tím se otevře **konfigurovat logiku signálů** okno.

Nastavte **upozornění logiky** části následujícím způsobem:

*Na základě: Počet výsledků, podmínka: Větší než prahová hodnota: 0.*

Vzhledem k tomu, že tento dotaz vrátí pouze uzly není k dispozici jako výsledky, pokud počet výsledků, které je stále větší než 0, by měly aktivovat upozornění.

V **Evaluated na základě** nastavte **období** a **frekvence** podle jak často chcete vyhledávat uzly není k dispozici.

Všimněte si, že pro účely této výstrahy chcete Ujistěte se, že **období = četnost.** Najdete další informace o období, četnost a další výstrahy parametry [tady](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Klikněte na tlačítko **provádí** po dokončení konfigurace logiku signálů.

![Pravidlo výstrahy konfigurovat logiku signálů](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Pokud již nemáte existující skupiny akcí, klikněte na tlačítko **vytvořit nový** pod **skupiny akcí** oddílu.

![Pravidlo výstrahy nová skupina akcí](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Tím se otevře **přidat skupinu akcí** okno. Zvolte **název skupiny akcí**, **krátký název**, **předplatné**, a **skupinu prostředků.** V části **akce** zvolte **název akce** a vyberte **e-mailu/SMS nebo nabízená/hlasové** jako **typ akce.**

> [!NOTE]
> Nejsou k dispozici několik akcí, které kromě e-mailu/SMS/nabízených oznámení/Voice, třeba funkce Azure Functions, aplikaci LogicApp, Webhooku, ITSM a Automation Runbook můžete aktivovat výstrahu. [Víc se uč.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Tím se otevře **e-mailu/SMS nebo nabízená/hlasové** okno. Zvolte **název** pro příjemce, **zkontrolujte** **e-mailu** a zadejte e-mailovou adresu, na kterou chcete výstrahu odesílat. Klikněte na tlačítko **OK** v **e-mailu/SMS nebo nabízená/hlasové** okno, pak v **přidat skupinu akcí** okno a dokončete konfiguraci vaší skupiny akcí.

![Pravidlo výstrahy přidat skupinu akcí](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zavření těchto oknech, měli byste vidět vaše skupiny akcí, které jsou uvedené v části **skupiny akcí** oddílu. Nakonec dokončí **podrobnosti výstrahy** zadáním **název pravidla upozornění** a **popis** a zvolíte **závažnost**.
Klikněte na tlačítko **vytvořit pravidlo upozornění** na dokončení.

![Vytvořit pravidlo upozornění dokončit](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Možnost určit **závažnost** je výkonný nástroj, který se dá použít při vytváření více výstrah. Například můžete vytvořit jednu výstrahu pro vyvolání upozornění (závažnost 1) Pokud se nepovede jeden hlavní uzel dolů a další výstrahu, která vyvolá kritické (záv. 0) v nepravděpodobném případě, že oba hlavním uzlům, přestanou fungovat.

Když je splněna podmínka pro tuto výstrahu, výstraha se aktivuje a obdržíte e-mailu v detailech výstrahy takto:

![Azure Monitor e-mailové upozornění](media/hdinsight-cluster-availability/alert-email.png)

Můžete také zobrazit všechny výstrahy, které mají aktivováno, seskupené podle závažnosti, tak, že přejdete **výstrahy** okna ve vaší **pracovní prostor Log Analytics**.

![Upozornění pracovní prostor analýzy protokolů](media/hdinsight-cluster-availability/portal-alerts.png)

Kliknutím na seskupení závažnosti (to znamená **závažnost 1** jako zvýrazněné výše) se zobrazí záznamy pro všechny výstrahy, které tento závažnost, které se mají aktivována jako níže:

![Upozornění závažnost 1 pracovní prostor analýzy protokolů](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Další postup
- [Dostupnost a spolehlivost clusterů systému Apache Hadoop v HDInsight](hdinsight-high-availability-linux.md)