---
title: Sledování a správě Azure HDInsight pomocí Ambari webového uživatelského rozhraní | Microsoft Docs
description: Další informace o použití Ambari ke sledování a správě clusterů HDInsight se systémem Linux. V tomto dokumentu zjistěte, jak pomocí uživatelského rozhraní Ambari Web součástí clusterů HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: larryfr
ms.openlocfilehash: 922dfd475f27f182d8958887087fc2f4945cc43a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Správa clusterů HDInsight pomocí Ambari webového uživatelského rozhraní

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari zjednodušuje správu a sledování clusteru Hadoop tím, že poskytuje snadno použít webového uživatelského rozhraní a rozhraní REST API. Ambari je zahrnuta v clusterech HDInsight se systémem Linux a slouží ke sledování clusteru a udělat změny konfigurace.

V tomto dokumentu zjistěte, jak pomocí uživatelského rozhraní Ambari Web pomocí clusteru služby HDInsight.

## <a id="whatis"></a>Co je Ambari?

[Apache Ambari](http://ambari.apache.org) zjednodušuje správu Hadoop, tím, že poskytuje snadno použitelné webového uživatelského rozhraní. Ambari slouží ke správě a sledování clusterů systému Hadoop. Vývojářům můžete integrovat tyto funkce do svých aplikací pomocí [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Webové uživatelské rozhraní Ambari je dostupné ve výchozím nastavení s clustery HDInsight, které používají operační systém Linux.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Připojení

Webové uživatelské rozhraní Ambari je k dispozici v clusteru HDInsight na HTTPS://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název clusteru.

> [!IMPORTANT]
> Připojení k Ambari v HDInsight vyžaduje protokol HTTPS. Po zobrazení výzvy k ověření, použijte název účtu správce a heslo, které jste zadali při vytvoření clusteru.

## <a name="ssh-tunnel-proxy"></a>Tunelové propojení SSH (proxy)

Při Ambari pro váš cluster je přístupná přímo přes Internet, nejsou některé odkazy z webové uživatelské rozhraní Ambari, (například za účelem jako JobTracker) zveřejněné na Internetu. Pro přístup k těmto službám, je nutné vytvořit tunelového propojení SSH. Další informace najdete v tématu [používání tunelového propojení SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]
> Ne všechny funkce webové uživatelské rozhraní Ambari jsou podporovány v HDInsight. Další informace najdete v tématu [Nepodporovaná operace](#unsupported-operations) část tohoto dokumentu.

Při připojování k webové uživatelské rozhraní Ambari, zobrazí se výzva k ověření na stránku. Pomocí Správce clusteru (výchozí správce) a heslo použité při vytváření clusteru.

Po otevření stránky, poznamenejte si panelu nahoře. Tento panel obsahuje následující informace a ovládací prvky:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari logo** -otevře řídicí panel, který můžete použít ke sledování clusteru.

* **Název ops # clusteru** -zobrazí počet probíhajících operacích Ambari. Výběr názvu clusteru nebo **# ops** zobrazí seznam operace na pozadí.

* **výstrahy #** -zobrazí varování nebo kritické výstrahy, pokud existuje, které pro cluster.

* **Řídicí panel** -zobrazí řídicí panel.

* **Služby** – informace a nastavení konfigurace pro služby v clusteru.

* **Hostitelé** – informace a konfigurace nastavení pro uzly v clusteru.

* **Výstrahy** -protokolu informace, upozornění a kritické výstrahy.

* **Správce** -softwaru zásobníku nebo služby, které jsou nainstalované v clusteru, informace o účtu služby a zabezpečení protokolu Kerberos.

* **Tlačítko Správce** -Ambari správy, uživatelská nastavení a odhlášení.

## <a name="monitoring"></a>Monitorování

### <a name="alerts"></a>Výstrahy

Následující seznam obsahuje společné výstrahy stavy, které jsou používané Ambari:

* **OK**
* **Upozornění**
* **CRITICAL**
* **NEZNÁMÝ**

Výstrahy jiné než **OK** způsobit, že **# výstrahy** položku v horní části stránky a zobrazuje počet výstrah. Výběrem této položky zobrazí výstrahy a jejich stav.

Výstrahy jsou uspořádány do několik výchozích skupin, které si můžete prohlížet **výstrahy** stránky.

![stránka výstrah](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Skupiny můžete spravovat pomocí **akce** nabídky a výběrem **Spravovat výstrahy skupiny**.

![Dialogové okno upozornění skupin spravovat](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Můžete také spravovat výstrahy metody a vytvořit oznámení o výstrahách z **akce** nabídky výběrem __Spravovat výstrahy oznámení__. Zobrazí se všechny aktuální oznámení. Odsud můžete také vytvořit oznámení. Může být zasláno oznámení **e-MAILU** nebo **SNMP** dojít, když konkrétní závažnost výstrahy nebo kombinace. Například můžete odesílat e-mailové zprávy při jejich výstrahy v **YARN výchozí** skupina nastavení **kritický**.

![Vytvoření dialogového okna výstrah](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Nakonec výběr __spravovat nastavení upozornění__ z __akce__ nabídky můžete nastavit počet opakování výstrahy se musí vyskytovat předtím, než odešle oznámení. Toto nastavení lze zabránit oznámení pro přechodné chyby.

### <a name="cluster"></a>Cluster

**Metriky** řídicího panelu obsahuje řadu pomůcek, které usnadňují monitorovat stav clusteru na první pohled. Několik widgetů, například **využití procesoru**, poskytují další informace, při kliknutí na.

![řídicí panel se metriky](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**Heatmaps** karta zobrazuje jako barevnou heatmaps, budete z zelená na červený metriky.

![řídicí panel se heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Další informace o uzly v clusteru, vyberte **hostitele**. Pak vyberte konkrétní uzlu, které vás zajímají.

![Podrobnosti o hostiteli](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Služby

**Služby** bočním panelu na řídicí panel poskytuje rychlý přehled o stavu služeb spuštěných v clusteru. Jsou různé ikony slouží k určení stavu nebo akce, které by měla být provedena. Symbol žlutý recyklaci se například zobrazí, pokud služba vyžaduje provedení recyklace.

![straně řádku služeb](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Služby, zobrazí se liší mezi typy clusteru HDInsight a verze. Služby zobrazí tady může být jiný než služby zobrazené pro váš cluster.

Výběr služby zobrazí podrobnější informace ve službě.

![souhrnné informace o služby](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Rychlé odkazy

Zobrazení některé služby **rychlé odkazy** odkaz v horní části stránky. To lze použít pro přístup k webové specifickou pro službu uživatelská rozhraní, jako například:

* **Historie úlohy** -historie úlohy MapReduce.
* **Správce prostředků** -YARN ResourceManager uživatelského rozhraní.
* **NameNode** -Hadoop Distributed soubor System (HDFS) NameNode uživatelského rozhraní.
* **Uživatelské rozhraní webu Oozie** -Oozie uživatelského rozhraní.

V prohlížeči, který zobrazí stránku vybrané výběrem libovolné z těchto odkazů otevře novou kartu.

> [!NOTE]
> Výběr **rychlé odkazy** záznam pro službu může vrátit chyba "server nebyl nalezen". Pokud dojde k této chybě, musíte použít tunelového propojení SSH při použití **rychlé odkazy** položka pro tuto službu. Informace najdete v tématu [používání tunelového propojení SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Správa

### <a name="ambari-users-groups-and-permissions"></a>Ambari uživatele, skupiny a oprávnění

Práce se uživatelé, skupiny a oprávnění jsou podporovány při použití [připojený k doméně](./domain-joined/apache-domain-joined-introduction.md) clusteru HDInsight. Informace o používání rozhraní Ambari správy na cluster připojený k doméně, najdete v části [Správa clusterů HDInsight připojený k doméně](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]
> Neměňte heslo Ambari sledovací zařízení (hdinsightwatchdog) v clusteru HDInsight se systémem Linux. Změna hesla se dělí možnost pomocí skriptových akcí nebo provádět operace škálování k vašemu clusteru.

### <a name="hosts"></a>Hostitelé

**Hostitele** stránka obsahuje seznam všech hostitelích v clusteru. Ke správě hostitelů, postupujte podle těchto kroků.

![stránka hostitele](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> Přidání, vyřazení z provozu a recommissioning hostitele nepoužívejte s clustery HDInsight.

1. Vyberte hostitele, který chcete spravovat.

2. Použití **akce** nabídce vyberte akci, kterou chcete provést:

   * **Spustit všechny součásti** – spustit všechny součásti na hostiteli.

   * **Zastavit všechny součásti** -ukončit všechny součásti na hostiteli.

   * **Restartujte všechny součásti** – zastavení a spuštění všech součástí na hostiteli.

   * **Zapnout režim údržby** -potlačí výstrah pro hostitele. Tento režim by měl povolit, pokud jsou provádění akcí, které generují výstrahy. Například ukončení a opětovném spuštění služby.

   * **Zapnutí režimu údržby** -vrátí hostitele do normální výstrahy.

   * **Zastavit** -zastaví DataNode nebo NodeManagers na hostiteli.

   * **Spustit** -spustí DataNode nebo NodeManagers na hostiteli.

   * **Restartujte** -zastaví a spustí DataNode nebo NodeManagers na hostiteli.

   * **Vyřadit z provozu** – odebere hostitele z clusteru.

     > [!NOTE]
     > Nepoužívejte tuto akci v clusterech prostředí HDInsight.

   * **Recommission** -přidá dříve vyřazení hostitele do clusteru.

     > [!NOTE]
     > Nepoužívejte tuto akci v clusterech prostředí HDInsight.

### <a id="service"></a>Služby

Z **řídicí panel** nebo **služby** stránky, použijte **akce** tlačítko v dolní části seznamu služeb zastavení a spuštění všech služeb.

![Akce služby](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Při **přidat službu** je uveden v této nabídce neměl by být použit na přidání služeb na clusteru HDInsight. Nové služby musí být přidaní pomocí akce skriptu při zřizování clusteru. Další informace o použití akce skriptu najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

Když **akce** tlačítko můžete restartovat všechny služby, často chcete spustit, zastavit nebo restartovat určitou službu. Použijte následující postup k provádění akcí na jednotlivé služby:

1. Z **řídicí panel** nebo **služby** vyberte možnost služby.

2. Z horní části **Souhrn** pomocí **služby akce** tlačítko a vyberte akci provést. To restartuje službu na všech uzlech.

    ![Akce služby](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]
   > Restartování některé služby spuštěného clusteru může generovat výstrahy. Abyste se vyhnuli výstrahy, můžete použít **služby akce** tlačítko umožníte **režimu údržby** pro službu před provedením restartování.

3. Jakmile akci byla vybrána, **# op** položku v horní části stránky přírůstcích zobrazíte probíhající operaci na pozadí. Pokud nakonfigurované pro zobrazení, zobrazí se seznam operace na pozadí.

   > [!NOTE]
   > Pokud jste povolili **režimu údržby** pro službu, nezapomeňte ho vypnout pomocí **služby akce** tlačítko po dokončení operace.

Ke konfiguraci služby, použijte následující kroky:

1. Z **řídicí panel** nebo **služby** vyberte možnost služby.

2. Vyberte **konfigurací** kartě. Zobrazí se aktuální konfigurace. Zobrazí se také seznam předchozí konfigurace.

    ![Konfigurace](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Používání polí zobrazí konfiguraci upravit, a potom vyberte **Uložit**. Nebo vyberte předchozí konfiguraci a pak vyberte **změnit na aktuální** se vrátit zpět předchozí nastavení.

## <a name="ambari-views"></a>Ambari views

Zobrazení Ambari umožňují vývojářům připojte prvky uživatelského rozhraní pomocí webové uživatelské rozhraní Ambari [Framework zobrazení Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight poskytuje následující zobrazení s typy clusteru Hadoop:

* Správce front yarn: správce front poskytuje jednoduché uživatelského rozhraní pro zobrazení a úprava fronty YARN.

* Zobrazení Hive: Zobrazení Hive umožňuje spouštět dotazy Hive přímo z webového prohlížeče. Můžete uložit dotazy, zobrazení výsledků, uložte výsledky do úložiště clusteru nebo stáhnout výsledky do místního systému. Další informace o používání Hive zobrazení najdete v tématu [použijte zobrazení Hive s HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Zobrazení tez: Tez zobrazení umožňuje lépe pochopit a optimalizovat úlohy. Můžete zobrazit informace na tom, jak jsou vykonány úlohách Tez a jaké prostředky jsou používány.

## <a name="unsupported-operations"></a>Nepodporovaná operace

Následující operace Ambari nejsou podporovány v HDInsight:

* __Přesunutí služba Collector metriky__. Při zobrazení informací ve službě Collector metriky jednu z akcí, které jsou k dispozici v nabídce Akce služby je __přesunout metriky kolekce__. Tato možnost není podporována v prostředí HDInsight.

## <a name="next-steps"></a>Další postup

Další informace o použití [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) s HDInsight.