---
title: Jak monitorovat dostupnost clusteru pomocí protokolů Azure Monitor v HDInsight
description: Naučte se používat protokoly Azure Monitor k monitorování stavu a dostupnosti clusteru.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 299a17e23ca3eb2d954bae7335571ae1f645152e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867147"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Jak monitorovat dostupnost clusteru pomocí protokolů Azure Monitor v HDInsight

Clustery HDInsight zahrnují integraci protokolů Azure Monitor, která poskytuje metriky a protokoly Queryable a také konfigurovatelné výstrahy. V tomto článku se dozvíte, jak pomocí Azure Monitor monitorovat cluster.

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor

Protokoly Azure Monitor umožňují shromažďování a agregaci dat vygenerovaných několika prostředky, jako jsou clustery HDInsight, a jejich shromáždění a agregace na jednom místě, abyste dosáhli sjednoceného prostředí monitorování.

Za předpokladu budete potřebovat Log Analytics pracovní prostor pro ukládání shromážděných dat. Pokud jste ho ještě nevytvořili, můžete postupovat podle pokynů zde: [Vytvoření pracovního prostoru Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Povolit integraci protokolů Azure Monitor HDInsight

Na stránce prostředek clusteru HDInsight na portálu vyberte **Azure monitor**. Pak vyberte **Povolit** a v rozevíracím seznamu vyberte svůj pracovní prostor Log Analytics.

:::image type="content" source="media/cluster-availability-monitor-logs/azure-portal-monitoring.png" alt-text="Sada HDInsight Operations Management Suite":::

Ve výchozím nastavení se nainstaluje agent OMS na všechny uzly clusteru s výjimkou hraničních uzlů. Protože na hraničních uzlech clusteru není nainstalovaný žádný agent OMS, na hraničních uzlech, které jsou ve výchozím nastavení Log Analytics, se nevyskytují žádné telemetrie.

## <a name="query-metrics-and-logs-tables"></a>Tabulky metrik a protokolů dotazů

Jakmile je integrace protokolu Azure Monitor povolená (může to trvat několik minut), přejděte do prostředku **pracovního prostoru Log Analytics** a vyberte **protokoly**.

:::image type="content" source="media/cluster-availability-monitor-logs/hdinsight-portal-logs.png" alt-text="Protokoly Log Analytics pracovního prostoru":::

Protokoluje seznam několika ukázkových dotazů, například:

| Název dotazu                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostupnost počítačů v dnešní době    | Graf počtu počítačů odesílajících protokoly, každou hodinu                     |
| Zobrazit prezenční signály                 | Zobrazit seznam všech prezenčních signálů počítače za poslední hodinu                           |
| Poslední prezenční signál každého počítače | Zobrazit poslední prezenční signál odeslaný jednotlivými počítači                             |
| Nedostupné počítače           | Zobrazí seznam všech známých počítačů, které neodeslaly prezenční signál za posledních 5 hodin. |
| Míra dostupnosti               | Vypočítat míru dostupnosti každého připojeného počítače                |

Jako příklad spusťte dotaz ukázka **míry dostupnosti** tak, že v tomto dotazu vyberete **Spustit** , jak je znázorněno na snímku obrazovky výše. Tím se v procentech zobrazí míra dostupnosti jednotlivých uzlů v clusteru. Pokud jste povolili více clusterů HDInsight, aby odesílaly metriky do stejného pracovního prostoru Log Analytics, zobrazí se v těchto clusterech frekvence dostupnosti všech uzlů (kromě hraničních uzlů).

:::image type="content" source="media/cluster-availability-monitor-logs/portal-availability-rate.png" alt-text="Vzorový dotaz k protokolu dostupnosti v pracovním prostoru Log Analytics":::

> [!NOTE]  
> Míra dostupnosti se měří v průběhu 24 hodin, takže cluster bude muset běžet aspoň 24 hodin, než uvidíte správné sazby dostupnosti.

Tuto tabulku můžete připnout na sdílený řídicí panel tak, že kliknete na **připnout** v pravém horním rohu. Pokud nemáte žádné zapisovatelné řídicí panely, uvidíte, jak ho vytvořit: [vytváření a sdílení řídicích panelů v Azure Portal](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Výstrahy Azure Monitor

Můžete také nastavit výstrahy Azure Monitor, které se aktivují, když hodnota metriky nebo výsledky dotazu splňují určité podmínky. Můžete například vytvořit upozornění k odeslání e-mailu, když jeden nebo více uzlů neodeslal prezenční signál během 5 hodin (tzn. že se předpokládá, že není k dispozici).

V části **protokoly** Spusťte ukázkový dotaz **nedostupných počítačů** , a to tak, že v tomto dotazu vyberete **Spustit** , jak je znázorněno níže.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-unavailable-computers.png" alt-text="Ukázka Log Analytics v pracovním prostoru zaznamenává nedostupné počítače":::

Pokud jsou k dispozici všechny uzly, tento dotaz by nyní měl vracet nulový výsledek. Kliknutím na **nové pravidlo výstrahy** zahajte konfiguraci upozornění pro tento dotaz.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png" alt-text="Nové pravidlo výstrahy Log Analytics pracovního prostoru":::

Existují tři komponenty výstrahy: *prostředek* , pro který chcete vytvořit pravidlo (Log Analytics pracovní prostor v tomto případě), *podmínku* pro aktivaci výstrahy a *skupiny akcí* , které určují, co se stane, když se výstraha aktivuje.
Klikněte na **název podmínky**, jak je znázorněno níže, a dokončete konfiguraci logiky signálu.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-condition-title.png" alt-text="Podmínka vytvoření pravidla pro upozornění na portál":::

Tím se otevře **Konfigurace signálu**.

Nastavte oddíl **Alert Logic** následujícím způsobem:

*Podle: počet výsledků, podmínka: větší než, prahová hodnota: 0.*

Vzhledem k tomu, že tento dotaz vrátí nedostupné uzly jako výsledky, pokud je počet výsledků stále větší než 0, výstraha by se měla aktivovat.

V části **vyhodnocováno na základě** oddílu nastavte **dobu** a **četnost** podle toho, jak často chcete kontrolovat nedostupné uzly.

Pro účely této výstrahy se chcete ujistit, že **perioda = frekvence.** Další informace o období, četnosti a dalších parametrech výstrahy najdete [tady](../azure-monitor/alerts/alerts-unified-log.md#alert-logic-definition).

Vyberte **Hotovo** , až budete hotovi s konfigurací logiky signálu.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-configure-signal-logic.png" alt-text="Pravidlo výstrahy konfiguruje logiku signálu":::

Pokud ještě nemáte existující skupinu akcí, klikněte na **vytvořit nový** v části **skupiny akcí** .

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-new-action-group.png" alt-text="Pravidlo výstrahy vytvoří novou skupinu akcí.":::

Otevře se okno **Přidat skupinu akcí**. Vyberte **název skupiny akcí**, **krátké jméno**, **předplatné** a **skupinu prostředků.** V části **Akce** zvolte **název akce** a jako **typ akce** vyberte **e-mail/SMS/odeslat/hlas** .

> [!NOTE]
> K dispozici je několik dalších akcí, které se můžou aktivovat kromě e-mailu, SMS/nabízeného/hlasu, jako je Azure Functions, LogicApp, Webhook, ITSM a Automation Runbook. [Víc se uč.](../azure-monitor/alerts/action-groups.md#action-specific-information)

Tím se otevře **e-mail/SMS/nabízený/hlas**. Vyberte **jméno** příjemce, **zaškrtněte** políčko **e-mail** a zadejte e-mailovou adresu, na kterou chcete odeslat výstrahu. V **e-mailu/SMS/Push/Voice** vyberte **OK** a potom do **Přidat skupinu akcí** dokončete konfiguraci skupiny akcí.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-add-action-group.png" alt-text="Pravidlo výstrahy vytvoří přidat skupinu akcí.":::

Po zavření těchto oken by se měla zobrazit vaše skupina akcí uvedená v části **skupiny akcí** . Nakonec dokončete část **Podrobnosti výstrahy** zadáním názvu a **popisu** **pravidla výstrahy** a zvolením **závažnosti**. Kliknutím na **vytvořit pravidlo výstrahy** dokončete.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png" alt-text="Portál vytvoří dokončení pravidla výstrahy.":::

> [!TIP]
> Možnost určit **závažnost** je výkonný nástroj, který se dá použít při vytváření více výstrah. Můžete například vytvořit jednu výstrahu, která vyvolá upozornění (závažnost 1) v případě, že dojde k výpadku jednoho hlavního uzlu, a další výstrahu, která vyvolává kritické (závažnost 0) v nepravděpodobném případě, že oba hlavní uzly vycházejí dolů.

Pokud je splněna podmínka této výstrahy, výstraha se aktivuje a obdržíte e-mail s podrobnostmi výstrahy, jako je tato:

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alert-email.png" alt-text="Příklad e-mailu s výstrahou Azure Monitor":::

Kliknutím na **výstrahy** v **pracovním prostoru Log Analytics** můžete zobrazit také všechny výstrahy, které byly aktivovány, seskupené podle závažnosti.

:::image type="content" source="media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png" alt-text="Výstrahy Log Analytics pracovního prostoru":::

Výběr u seskupení závažnosti (tj. **závažnost 1, jak je** zvýrazněný výše) zobrazí záznamy pro všechny výstrahy této závažnosti, které byly aktivovány níže:

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png" alt-text="Log Analytics pracovní prostor závažnost jednu výstrahu":::

## <a name="next-steps"></a>Další kroky

* [Dostupnost clusteru – Apache Ambari](./hdinsight-cluster-availability.md)
* [Použití protokolů Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)
