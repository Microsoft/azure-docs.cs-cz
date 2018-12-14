---
title: Monitorování a správa Azure HDInsight pomocí webového uživatelského rozhraní Ambari
description: Další informace o použití Ambari monitorovat a spravovat clustery HDInsight založené na Linuxu. V tomto dokumentu zjistíte, jak pomocí webového uživatelského rozhraní Ambari součástí clusterů HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: eee37f5a77331b78aa587ba2e3eb61132d7d6adc
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385563"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari zjednodušuje správu a monitorování tím, že poskytuje snadno pomocí webového uživatelského rozhraní a rozhraní REST API pro cluster Apache Hadoop. Ambari je zahrnutá v clusterech HDInsight založených na Linuxu a slouží k monitorování clusteru a provést změny konfigurace.

V tomto dokumentu se dozvíte, jak pomocí webového uživatelského rozhraní Ambari clusteru služby HDInsight.

## <a id="whatis"></a>Co je Apache Ambari?

[Apache Ambari](http://ambari.apache.org) zjednodušuje správu Hadoop tím, že poskytuje snadno použitelné webové uživatelské rozhraní. Ambari slouží ke správě a monitorování clusterů systému Hadoop. Vývojářům můžete integrovat tyto funkce do svých aplikací s použitím [rozhraní Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Webové uživatelské rozhraní Ambari je dostupné ve výchozím nastavení s clustery HDInsight, které používají operační systém Linux.

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Připojení

Webové uživatelské rozhraní Ambari je k dispozici v clusteru HDInsight na HTTPS://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název vašeho clusteru.

> [!IMPORTANT]  
> Připojení k Ambari na HDInsight vyžaduje protokol HTTPS. Po zobrazení výzvy k ověření, použijte název účtu správce a heslo, které jste zadali při vytváření clusteru.

## <a name="ssh-tunnel-proxy"></a>Tunel SSH (proxy)

I když Ambari pro váš cluster dostupné přímo přes Internet, některé odkazy z webového rozhraní Ambari, (například za účelem JobTracker) nejsou zveřejněné na Internetu. Pro přístup k těmto službám, je nutné vytvořit tunel SSH. Další informace najdete v tématu [použití tunelování SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> Ne všechny funkce webové uživatelské rozhraní Ambari, jsou podporovány v HDInsight. Další informace najdete v tématu [Nepodporovaná operace](#unsupported-operations) část tohoto dokumentu.

Při připojování k webovému uživatelskému rozhraní Ambari, zobrazí se výzva k ověření na stránku. Pomocí Správce clusteru (výchozí správce) a heslo, které jste použili při vytváření clusteru.

Po otevření stránky, Všimněte si panelu v horní části. Tento panel obsahuje následující informace a ovládací prvky:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari logo** -otevře řídicí panel, který slouží k monitorování clusteru.

* **Cluster název ops #** – zobrazuje počet probíhajících operacích Ambari. Výběr názvu clusteru nebo **# ops** zobrazí seznam operací na pozadí.

* **# upozornění** -zobrazí varování nebo kritické výstrahy, pokud existuje, pro cluster.

* **Řídicí panel** – zobrazí řídicí panel.

* **Služby** – informace a nastavení konfigurace pro služby v clusteru.

* **Hostitelé** – informace a nastavení konfigurace pro uzly v clusteru.

* **Výstrahy** – protokol informace, upozornění a kritické výstrahy.

* **Správce** – Software zásobníku/služeb, které jsou nainstalované v clusteru, informace o účtu služby a zabezpečení protokolu Kerberos.

* **Tlačítko Správce** -Ambari správy, uživatelská nastavení a odhlášení.

## <a name="monitoring"></a>Monitorování

### <a name="alerts"></a>Výstrahy

Následující seznam obsahuje společné výstrah stavy používané Ambari:

* **OK**
* **Upozornění**
* **KRITICKÁ**
* **NEZNÁMÝ**

Upozorní, jiné než **OK** způsobit, že **# upozornění** položky v horní části stránky a zobrazit počet výstrah. Výběrem této položky se zobrazí upozornění a jejich stav.

Výstrahy jsou uspořádané do několik výchozích skupin, které si můžete prohlížet **výstrahy** stránky.

![Stránky s upozorněními](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Skupiny můžete spravovat pomocí **akce** nabídky a vyberete **Spravovat výstrahy skupiny**.

![Správa výstrah skupiny dialogového okna](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Můžete také spravovat upozornění metody a vytvořit oznámení o výstrahách z **akce** nabídek výběrem __spravovat oznámení výstrah__. Zobrazí se všechny aktuální oznámení. Odsud můžete také vytvořit oznámení. Můžete odesílat oznámení prostřednictvím **e-MAILU** nebo **SNMP** dojít, když konkrétní závažnost výstrahy nebo kombinace. Například můžete odesílat e-mailové zprávy při jejich výstrahy v **YARN výchozí** skupina nastavena **kritický**.

![Vytvoření dialogového okna výstrah](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

A konečně výběr __spravovat nastavení upozornění__ z __akce__ nabídka umožňuje nastavit počet opakování výstrahy se musí vyskytovat před odesláním oznámení. Toto nastavení je možné zabránit oznámení pro přechodné chyby.

### <a name="cluster"></a>Cluster

**Metriky** karty řídicí panel obsahuje řadu pomůcek, které usnadňují sledování stavu clusteru na první pohled. Několik widgetů, například **využití procesoru**, uveďte další důležité informace při kliknutí na.

![řídicí panel s metrikami](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**Heat mapy** karta zobrazuje metriky jako barevný Heat mapy, přechodu od zelené na červenou.

![řídicí panel s Heat mapy](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Další informace o uzly v clusteru, vyberte **hostitele**. Vyberte konkrétní uzel, které vás zajímají.

![Podrobnosti hostitele](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Služby

**Služby** bočního panelu na řídicí panel poskytuje rychlý přehled o stavu služby spuštěné v clusteru. Různé ikony se používají k označení stav nebo akcích, které se má vzít. Například žlutý recyklace symbol se zobrazí, pokud služba vyžaduje provedení recyklace.

![postranního panelu služby](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> Služby, zobrazí se liší mezi typy clusterů HDInsight a verze. Tady zobrazí služby může být jiný než služby zobrazené pro váš cluster.

Výběrem služby zobrazí podrobnější informace ve službě.

![souhrnné informace o službě](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Rychlé odkazy

Některé služby zobrazení **rychlé odkazy** odkazu v horní části stránky. To umožňuje přístup ke konkrétní služby webovým uživatelským rozhraním, jako například:

* **Historie úlohy** -historie úloh MapReduce.
* **Resource Manager** – uživatelské rozhraní správce prostředků YARN.
* **NameNode** -Hadoop Distributed File System (HDFS) NameNode uživatelského rozhraní.
* **Oozie webového uživatelského rozhraní** -Oozie uživatelského rozhraní.

Výběrem některé z těchto odkazů se otevře na nové kartě v prohlížeči, který zobrazuje vybrané stránky.

> [!NOTE]  
> Výběr **rychlé odkazy** záznam pro službu může vrátit chybu "Nepodařilo se najít server". Pokud dojde k této chybě, je nutné použít tunel SSH, při použití **rychlé odkazy** záznam pro tuto službu. Informace najdete v tématu [použití tunelování SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Správa

### <a name="ambari-users-groups-and-permissions"></a>Ambari uživatele, skupiny a oprávnění

Práce s uživatele, skupiny a oprávnění jsou podporovány při použití [připojených k doméně](./domain-joined/apache-domain-joined-introduction.md) clusteru HDInsight. Informace o použití rozhraní Ambari, Uživatelskému správy v clusteru připojeném k doméně najdete v tématu [Správa clusterů HDInsight připojených k doméně](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]  
> Neměňte heslo Ambari sledovacích (hdinsightwatchdog) ve vašem clusteru HDInsight se systémem Linux. Změna hesla dělí schopnost pomocí skriptových akcí nebo provádění operací škálování s vaším clusterem.

### <a name="hosts"></a>Hostitelé

**Hostitele** stránce uvedeny všichni hostitelé v clusteru. Ke správě hostitelů, postupujte podle těchto kroků.

![stránky hostitelé](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> Přidání, vyřazení z provozu a recommissioning hostitele se nemá používat s clustery HDInsight.

1. Vyberte hostitele, kterou chcete spravovat.

2. Použití **akce** nabídce vyberte akci, kterou chcete provést:

   * **Spustit všechny součásti** – spustit všechny součásti na hostiteli.

   * **Zastavit všechny komponenty** -zastavit všechny komponenty na hostiteli.

   * **Restartovat všechny komponenty** – zastavení a spuštění všech součástí na hostiteli.

   * **Zapnout režim údržby** – potlačí výstrahy pro hostitele. Tento režim musí být povolené, pokud provádíte akce, která generují výstrahy. Například, zastavení a spuštění služby.

   * **Vypnout režim údržby** – vrátí hostitele tak, aby normální výstrahy.

   * **Zastavit** -DataNode zastaví nebo NodeManagers na hostiteli.

   * **Spustit** – spustí DataNode nebo NodeManagers na hostiteli.

   * **Restartujte** -zastaví a spustí DataNode nebo NodeManagers na hostiteli.

   * **Vyřazení z provozu** – odebere hostitele z clusteru.

     > [!NOTE]
     > Nepoužívejte tuto akci na clusterech HDInsight.

   * **Recommission** – přidá dříve Vyřazená z provozu hostitele do clusteru.

     > [!NOTE]
     > Nepoužívejte tuto akci na clusterech HDInsight.

### <a id="service"></a>Služby

Z **řídicí panel** nebo **služby** stránky, použijte **akce** tlačítko v dolní části seznamu služeb k zastavení a spuštění všech služeb.

![Akce služby](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> Zatímco **přidat službu** je uvedený v této nabídce není vhodné jej použít k přidání služby do clusteru HDInsight. Pomocí akce skriptu při zřizování clusteru byste přidávat nové služby. Další informace o použití akce skriptu, naleznete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

Zatímco **akce** tlačítko restartovat všechny služby, často chcete spustit, zastavit nebo restartovat určitou službu. Použijte následující postup k provádění akcí na jednotlivé služby:

1. Z **řídicí panel** nebo **služby** vyberte možnost služby.

2. Od začátku **Souhrn** kartu, použijte **akce služby** tlačítko a vyberte akci přijmout. Tím se znovu spustí službu na všech uzlech.

    ![Akce služby](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Když cluster běží restartování některých služeb mohou generovat výstrahy. Pokud chcete vyhnout, můžete použít **akce služby** tlačítko povolíte **režimu údržby** pro službu, než se pustíte do restartování.

3. Po akci byla vybrána, **# op** položky v horní části stránky navyšuje k zobrazení probíhající operaci na pozadí. Pokud jsou nakonfigurovány pro zobrazení, zobrazí se seznam operací na pozadí.

   > [!NOTE]  
   > Pokud jste povolili **režimu údržby** pro službu, nezapomeňte ji vypnout pomocí **akce služby** tlačítka po dokončení operace.

Ke konfiguraci služby použijte následující kroky:

1. Z **řídicí panel** nebo **služby** vyberte možnost služby.

2. Vyberte **Configs** kartu. Zobrazí se aktuální konfigurace. Zobrazí se také seznam předchozí konfigurace.

    ![Konfigurace](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Použití polí zobrazených konfiguraci upravit, a potom vyberte **Uložit**. Nebo vyberte předchozí konfiguraci a pak vyberte **nastavit jako aktuální** chcete vrátit zpět na předchozí nastavení.

## <a name="ambari-views"></a>Ambari Views

Zobrazení Ambari umožňuje vývojářům pružný prvky uživatelského rozhraní pomocí webového uživatelského rozhraní Ambari [Framework zobrazení Apache Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight poskytuje následující zobrazení s typy clusterů Hadoop:


* Zobrazení Hive: Zobrazení Hive můžete spouštět dotazy Hive přímo z webového prohlížeče. Můžete ukládat dotazy, zobrazit výsledky, uložte výsledky do úložiště clusteru nebo stáhnout výsledky do místního systému. Další informace o použití zobrazení Hivu najdete v tématu [použití Apache Hive zobrazení s HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez zobrazení: Zobrazení Tez umožňuje lépe vyhodnotit a optimalizovat výkon úloh. Zobrazí se informace o tom, jak jsou spuštěny úlohy Tez a jaké prostředky se používají.

## <a name="unsupported-operations"></a>Nepodporovaná operace

Následující operace Ambari nejsou podporovány v HDInsight:

* __Přesunutí služba Collector metriky__. Při zobrazení informací na službě Collector metriky, jednu z akcí, které jsou k dispozici v nabídce Akce služby je __přesunout metriky kolekcí__. To není podporováno s HDInsight.

## <a name="next-steps"></a>Další postup

Další informace o použití [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) s HDInsight.
