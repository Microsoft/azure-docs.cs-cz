---
title: Monitorování a Správa Azure HDInsight pomocí webového uživatelského rozhraní Ambari
description: Naučte se používat uživatelské rozhraní Apache Ambari k monitorování a správě clusterů HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/16/2020
ms.openlocfilehash: f21b72cd519c1615d8273bf316a8d0ccad039672
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546156"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari zjednodušuje správu a monitorování Apache Hadoopho clusteru. Toto zjednodušení se provádí díky snadnému používání webového uživatelského rozhraní a REST API. Ambari je součástí clusterů HDInsight a používá se k monitorování clusteru a provádění změn konfigurace.

V tomto dokumentu se dozvíte, jak používat webové uživatelské rozhraní Ambari s clusterem HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Co je Apache Ambari?

[Apache Ambari](https://ambari.apache.org) zjednodušuje správu Hadoop tím, že nabízí snadno použitelné webové uživatelské rozhraní. Pomocí Ambari můžete spravovat a monitorovat clustery Hadoop. Vývojáři můžou tyto možnosti integrovat do svých aplikací pomocí [rozhraní AMBARI REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Připojení

Webové uživatelské rozhraní Ambari je k dispozici v clusteru HDInsight v `https://CLUSTERNAME.azurehdinsight.net` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

> [!IMPORTANT]  
> Připojení k Ambari v HDInsight vyžaduje protokol HTTPS. Po zobrazení výzvy k ověření použijte název účtu správce a heslo, které jste zadali při vytvoření clusteru. Pokud se vám nezobrazí výzva k zadání přihlašovacích údajů, zkontrolujte nastavení sítě a potvrďte, že mezi klientem a clustery Azure HDInsight nedochází k potížím s připojením.

## <a name="ssh-tunnel-proxy"></a>Tunel SSH (proxy)

I když je Ambari pro váš cluster přístupný přímo přes Internet, některé odkazy z webového uživatelského rozhraní Ambari (například na JobTracker) nejsou zveřejněné na internetu. Chcete-li získat přístup k těmto službám, je nutné vytvořit tunel SSH. Další informace najdete v tématu [použití tunelového propojení SSH se službou HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Webové uživatelské rozhraní Ambari

> [!WARNING]  
> Ve službě HDInsight nejsou podporovány všechny funkce webového uživatelského rozhraní Ambari. Další informace najdete v části [nepodporované operace](#unsupported-operations) v tomto dokumentu.

Při připojování k webovému uživatelskému rozhraní Ambari se zobrazí výzva k ověření na stránce. Použijte uživatele Správce clusteru (výchozí správce) a heslo, které jste použili při vytváření clusteru.

Po otevření stránky si poznamenejte pruh v horní části. Tento panel obsahuje následující informace a ovládací prvky:

![Přehled řídicího panelu Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Položka |Popis |
|---|---|
|Logo Ambari|Otevře řídicí panel, který se dá použít k monitorování clusteru.|
|Název clusteru # OPS|Zobrazí počet probíhajících operací Ambari. Když vyberete název clusteru nebo **# OPS** , zobrazí se seznam operací na pozadí.|
|počet výstrah|Zobrazí upozornění nebo kritická upozornění, pokud existují, pro cluster.|
|Řídicí panel|Zobrazí řídicí panel.|
|Služby|Informace a konfigurační nastavení pro služby v clusteru.|
|Hostitelé|Informace a nastavení konfigurace pro uzly v clusteru.|
|Výstrahy|Protokol informací, upozornění a kritických výstrah.|
|Správce|Softwarový zásobník/služby, které jsou nainstalovány v clusteru, informace o účtu služby a zabezpečení protokolu Kerberos.|
|Tlačítko Správce|Správa Ambari, uživatelské nastavení a odhlášení.|

## <a name="monitoring"></a>Monitorování

### <a name="alerts"></a>Výstrahy

Následující seznam obsahuje běžné stavy výstrah, které používá Ambari:

* **OK**
* **Upozornění**
* **KRITICKÉ**
* **Neznámý**

Jiné výstrahy než **OK** způsobí, že položka **# výstrahy** v horní části stránky zobrazuje počet výstrah. Výběrem této položky se zobrazí upozornění a jejich stav.

Výstrahy jsou uspořádány do několika výchozích skupin, které lze zobrazit na stránce s **výstrahami** .

![Shrnutí stránky s upozorněními Apache Ambari](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Skupiny můžete spravovat pomocí nabídky **Akce** a výběrem **možnosti spravovat skupiny výstrah** .

![Správa skupin výstrah Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Můžete spravovat metody upozorňování a vytvářet oznámení výstrah z nabídky **Akce** výběrem __možnosti spravovat oznámení__ . Zobrazí se všechna aktuální oznámení. Tady můžete vytvářet oznámení. Oznámení je možné odeslat **e-mailem** nebo **protokolem SNMP** , když dojde k určitým kombinacím výstrah a závažnosti. Můžete například odeslat e-mailovou zprávu, pokud se některá z výstrah ve **výchozí skupině příze** nastaví na **kritickou** .

![Oznámení o vytvoření výstrahy pro Apache Ambari](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Nakonec vyberte __možnost spravovat nastavení výstrah__ z nabídky __Akce__ a nastavte počet pokusů, které se musí objevit před odesláním oznámení. Toto nastavení se dá použít k tomu, aby se zabránilo oznámením přechodným chybám.

Kurz oznámení o výstrahách pomocí bezplatného [účtu SendGrid](../sendgrid-dotnet-how-to-send-email.md)najdete v tématu [Konfigurace e-mailových oznámení Apache Ambari ve službě Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Cluster

Karta **metriky** řídicího panelu obsahuje řadu widgetů, které usnadňují sledování stavu vašeho clusteru na první pohled. Několik widgetů, jako je například **využití procesoru** , poskytují další informace při kliknutí.

![Řídicí panel Apache Ambari s metrikami](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Karta **Heat mapy** zobrazuje metriky jako barevné Heat mapyy z zelené na červenou.

![Řídicí panel Apache Ambari s Heat mapy](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Chcete-li získat další informace o uzlech v clusteru, vyberte možnost **hostitelé** . Pak vyberte konkrétní uzel, na který vás zajímáte.

![Souhrnné informace o Ambari hostitele Apache](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Služby

Postranní panel **služby** na řídicím panelu poskytuje rychlý přehled o stavu služeb spuštěných v clusteru. K označení stavu nebo akcí, které by měly být provedeny, se používají různé ikony. Například pokud je potřeba recyklovat službu, zobrazí se žlutý symbol recyklace.


![Boční panel služeb Apache Ambari Services](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Zobrazené služby se v různých typech a verzích clusteru HDInsight liší. Zobrazené služby se můžou lišit od služeb zobrazených pro váš cluster.

Výběr služby zobrazí podrobnější informace o službě.

![Souhrnné informace o službě Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Rychlé odkazy

Některé služby zobrazí v horní části stránky odkaz **Rychlé odkazy** . Tento odkaz se dá použít pro přístup k webovému uživatelská rozhraní pro konkrétní služby, jako je například:

* **Historie úlohy** – historie úloh MapReduce
* **Správce prostředků** -nitě správce prostředků UI.
* **NameNode** -Hadoop systém souborů DFS (DISTRIBUTED File System) (HDFS) NameNode UI.
* **Webové uživatelské rozhraní Oozie** – uživatelské rozhraní Oozie.

Výběrem kteréhokoli z těchto odkazů otevřete v prohlížeči novou kartu, která zobrazí vybranou stránku.

> [!NOTE]  
> Výběr položky **Rychlé odkazy** pro službu může vracet chybu "Server nebyl nalezen". Pokud k této chybě dojde, musíte při použití položky **Rychlé odkazy** pro tuto službu použít tunel SSH. Informace najdete v tématu [použití tunelového propojení SSH se službou HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) .

## <a name="management"></a>Správa

### <a name="ambari-users-groups-and-permissions"></a>Ambari uživatelé, skupiny a oprávnění

Práce s uživateli, skupinami a oprávněními je podporována. Místní správa najdete v tématu [autorizace uživatelů pro zobrazení Apache Ambari](./hdinsight-authorize-users-to-ambari.md). Clustery připojené k doméně najdete v tématu [Správa clusterů HDInsight připojených k doméně](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Neměňte heslo sledovacího zařízení Ambari (hdinsightwatchdog) v clusteru HDInsight se systémem Linux. Změna hesla zruší schopnost používat akce skriptu nebo provádět operace škálování s vaším clusterem.

### <a name="hosts"></a>Hostitelé

Na stránce **hostitelé** jsou uvedeni všichni hostitelé v clusteru. Chcete-li spravovat hostitele, postupujte podle těchto kroků.

![Přehled stránek hostitelů Apache Ambari](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Přidání, vyřazení z provozu a reřazování z provozu hostitele by se nemělo používat s clustery HDInsight.

1. Vyberte hostitele, kterého chcete spravovat.

2. Pomocí nabídky **Akce** vyberte akci, kterou chcete provést:

    |Položka |Popis |
    |---|---|
    |Spustit všechny součásti|Spusťte všechny součásti na hostiteli.|
    |Zastavit všechny komponenty|Zastavte všechny součásti na hostiteli.|
    |Restartovat všechny součásti|Zastavte a spusťte všechny součásti na hostiteli.|
    |Zapnout režim údržby|Potlačí výstrahy pro hostitele. Tento režim by měl být povolen, pokud provádíte akce, které generují výstrahy. Například zastavení a spuštění služby.|
    |Vypnout režim údržby|Vrátí hostitele do normálního upozorňování.|
    |Zastavit|Zastaví na hostiteli datanode nebo NodeManagers.|
    |Spustit|Spustí na hostiteli datanode nebo NodeManagers.|
    |Restartovat|Zastaví a spustí na hostiteli datanode nebo NodeManagers.|
    |Vyřazení|Odebere hostitele z clusteru. **Tuto akci nepoužívejte u clusterů HDInsight.**|
    |Reprovize|Přidá dříve vyřazeného hostitele do clusteru. **Tuto akci nepoužívejte u clusterů HDInsight.**|

### <a name="services"></a><a id="service"></a>Služby

Na stránce **řídicí panel** nebo **služby** pomocí tlačítka **Akce** v dolní části seznamu služeb zastavte a spusťte všechny služby.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Seznam akcí služby Apache Ambari" border="true":::

> [!WARNING]  
> Nové služby by se měly přidat pomocí akce skriptu během zřizování clusteru. Další informace o použití akcí skriptů najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

Zatímco tlačítko **Akce** může restartovat všechny služby, často chcete spustit, zastavit nebo restartovat konkrétní službu. Pomocí následujících kroků můžete provádět akce v jednotlivých službách:

1. Na stránce **řídicí panel** nebo **služby** vyberte službu.

2. V horní části karty **Souhrn** použijte tlačítko **Akce služby** a vyberte akci, která se má provést. Tato akce restartuje službu na všech uzlech.

    ![Akce jednotlivých služeb Apache Ambari](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Restartování některých služeb, když cluster běží, může generovat výstrahy. Abyste se vyhnuli výstrahám, můžete před provedením restartování Povolit pro službu **režim údržby** pomocí tlačítka **Akce služby** .

3. Po výběru akce se položka **# op** v horní části stránky zvýší a ukáže, že se vyskytne operace na pozadí. Pokud je nakonfigurováno pro zobrazení, zobrazí se seznam operací na pozadí.

   > [!NOTE]  
   > Pokud jste pro službu povolili **režim údržby** , nezapomeňte ji po dokončení operace zakázat pomocí tlačítka **Akce služby** .

Ke konfiguraci služby použijte následující postup:

1. Na stránce **řídicí panel** nebo **služby** vyberte službu.

2. Vyberte kartu **Konfigurace** . Zobrazí se aktuální konfigurace. Zobrazí se také seznam předchozích konfigurací.

    ![Konfigurace služby Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Pomocí zobrazených polí Upravte konfiguraci a potom vyberte **Uložit** . Nebo vyberte předchozí konfiguraci a pak vyberte **nastavit jako aktuální** a vraťte se k předchozímu nastavení.

## <a name="ambari-views"></a>Zobrazení Ambari

Ambari zobrazení umožňují vývojářům napojit prvky uživatelského rozhraní do webového uživatelského rozhraní Ambari pomocí architektury zobrazení Apache Ambari. HDInsight poskytuje následující zobrazení s typy clusterů Hadoop:

* Zobrazení podregistru: zobrazení podregistru umožňuje spouštět dotazy na podregistr přímo z webového prohlížeče. Můžete ukládat dotazy, zobrazovat výsledky, ukládat výsledky do úložiště clusteru nebo stahovat výsledky do místního systému. Další informace o používání zobrazení podregistru najdete v tématu [použití zobrazení Apache Hive se službou HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Zobrazení tez: tez zobrazení vám umožní lépe pochopit a optimalizovat úlohy. Můžete si prohlédnout informace o tom, jak se úlohy tez spouštějí a jaké prostředky se používají.

## <a name="unsupported-operations"></a>Nepodporované operace

Následující operace Ambari nejsou podporovány v HDInsight:

* Probíhá __Přesun služby sběrače metrik__ . Při zobrazení informací o službě sběrače metrik je jednou z akcí, které jsou k dispozici v nabídce Akce služby, __kolekce metriky přesunutí__ . Tato akce není podporována v HDInsight.

## <a name="next-steps"></a>Další kroky

* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) se službou HDInsight.
* [Použití Apache Ambari k optimalizaci konfigurací clusterů HDInsight](./hdinsight-changing-configs-via-ambari.md)
* [Škálování clusterů Azure HDInsight](./hdinsight-scaling-best-practices.md)