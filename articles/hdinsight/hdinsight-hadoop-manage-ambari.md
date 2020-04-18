---
title: Sledování a správa Azure HDInsight pomocí webového uživatelského rozhraní Ambari
description: Přečtěte si, jak pomocí rozhraní Apache Ambari monitorovat a spravovat clustery HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 2ed3d6b1088315b580ab8ddc4f12d8d61434ec53
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606554"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari zjednodušuje správu a monitorování clusteru Apache Hadoop. Toto zjednodušení se provádí poskytnutím snadno použitelné webové uživatelské rozhraní a ROZHRANÍ REST API. Ambari je součástí clusterů HDInsight a používá se ke sledování clusteru a provádění změn konfigurace.

V tomto dokumentu se dozvíte, jak používat webové uživatelské rozhraní Ambari s clusterem HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Co je Apache Ambari?

[Apache Ambari](https://ambari.apache.org) zjednodušuje správu Hadooptím, že poskytuje snadno použitelné webové uživatelské uzly. Ambari můžete použít ke správě a sledování clusterů Hadoop. Vývojáři mohou integrovat tyto funkce do svých aplikací pomocí [rozhraní API Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Připojení

Webové uživatelské rozhraní Ambari je k dispozici `https://CLUSTERNAME.azurehdinsight.net`v `CLUSTERNAME` clusteru HDInsight na adrese , kde je název clusteru.

> [!IMPORTANT]  
> Připojení k Ambari na HDInsight vyžaduje HTTPS. Po zobrazení výzvy k ověření použijte název účtu správce a heslo, které jste zadali při vytvoření clusteru. Pokud se nezobrazí výzva k zadání přihlašovacích údajů, zkontrolujte nastavení sítě a potvrďte, že mezi klientem a clustery Azure HDInsight není žádný problém s připojením.

## <a name="ssh-tunnel-proxy"></a>Tunel SSH (proxy)

Zatímco Ambari pro váš cluster je přístupný přímo přes internet, některé odkazy z Ambari Web UI (například jobtracker) nejsou vystaveny na internetu. Chcete-li získat přístup k těmto službám, musíte vytvořit tunelové propojení SSH. Další informace naleznete [v tématu Použití tunelového propojení SSH s rozhraním HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Webové uživatelské uzlina Ambari

> [!WARNING]  
> V rozhraní HDInsight nejsou podporovány všechny funkce webového uživatelského rozhraní Ambari. Další informace naleznete v části [Nepodporované operace](#unsupported-operations) tohoto dokumentu.

Při připojování k webovému uživatelskému uživatelskému uživatelskému panelu Ambari budete vyzváni k ověření na stránce. Použijte uživatele správce clusteru (výchozísprávce) a heslo, které jste použili při vytváření clusteru.

Po otevření stránky si poznamenejte pruh nahoře. Tento panel obsahuje následující informace a ovládací prvky:

![Přehled řídicího panelu Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Položka |Popis |
|---|---|
|Logo Ambari|Otevře řídicí panel, který lze použít ke sledování clusteru.|
|Název clusteru # ops|Zobrazuje počet probíhajících operací Ambari. Výběrem názvu clusteru nebo **# ops** zobrazí seznam operací na pozadí.|
|# upozornění|Zobrazí upozornění nebo kritické výstrahy, pokud existuje, pro cluster.|
|Řídicí panel|Zobrazí řídicí panel.|
|Služby|Informace a nastavení konfigurace pro služby v clusteru.|
|Hostitelé|Informace a nastavení konfigurace uzlů v clusteru.|
|Výstrahy|Protokol informací, upozornění a kritických výstrah.|
|Správce|Zásobník/služby softwaru, které jsou nainstalovány v clusteru, informace o účtu služby a zabezpečení protokolu Kerberos.|
|Tlačítko Správce|Ambari management, uživatelská nastavení a odhlášení.|

## <a name="monitoring"></a>Monitorování

### <a name="alerts"></a>Výstrahy

Následující seznam obsahuje společné stavy výstrah používané společností Ambari:

* **OK**
* **Upozornění**
* **Kritické**
* **Neznámý**

Výstrahy jiné než **OK** způsobit **# výstrahy** položky v horní části stránky zobrazit počet výstrah. Výběrem této položky se zobrazí výstrahy a jejich stav.

Výstrahy jsou uspořádány do několika výchozích skupin, které lze zobrazit na stránce **Výstrahy.**

![Apache Ambari upozornění shrnutí stránky](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Skupiny můžete spravovat pomocí nabídky **Akce** a výběrem **možnosti Spravovat skupiny výstrah**.

![Apache Ambari spravovat skupiny výstrah](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Můžete spravovat metody výstrah a vytvářet oznámení výstrah z nabídky **Akce** výběrem __možnosti Spravovat oznámení__. Zobrazí se všechna aktuální oznámení. Vytvořte oznámení odtud. Oznámení lze odesílat prostřednictvím **e-mailu** nebo **protokolu SNMP,** pokud dojde k určitým kombinacím výstraha/závažnost. Můžete například odeslat e-mailovou zprávu, pokud je některá výstraha ve výchozí skupině **YARN** nastavena na **hodnotu Kritická**.

![Apache Ambari vytvořit upozornění](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Nakonec výběr __možnosti Spravovat nastavení výstrah__ z nabídky __Akce__ umožňuje nastavit počet, kolikrát musí výstraha proběhnout před odesláním oznámení. Toto nastavení lze zabránit oznámení o přechodné chyby.

Kurz upozornění pomocí bezplatného [účtu SendGrid](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)najdete v [tématu Konfigurace e-mailových oznámení Apache Ambari v Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Cluster

Karta **Metriky** na řídicím panelu obsahuje řadu widgetů, které usnadňují sledování stavu clusteru na první pohled. Několik widgetů, například **Využití procesoru**, poskytuje po klepnutí další informace.

![Řídicí panel Apache Ambari s metrikami](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Karta **Heatmaps** zobrazuje metriky jako barevné heatmapy, které přejdou ze zelené na červenou.

![Řídicí panel Apache Ambari s heatmapami](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Další informace o uzlech v clusteru získáte výběrem **možnosti Hostitelé**. Pak vyberte konkrétní uzel, který vás zajímá.

![Souhrnné podrobnosti hostitele Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Služby

Postranní panel **Služby** na řídicím panelu poskytuje rychlý přehled o stavu služeb spuštěných v clusteru. Různé ikony se používají k označení stavu nebo akce, které by měly být přijata. Například žlutý symbol recyklace se zobrazí, pokud je třeba službu recyklovat.

![Boční lišta služeb Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Zobrazené služby se liší mezi typy clusterů HDInsight a verzemi. Zde zobrazené služby se mohou lišit od služeb zobrazených pro váš cluster.

Výběr služby zobrazí podrobnější informace o službě.

![Souhrnné informace o službě Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Rychlé odkazy

Některé služby zobrazují odkaz **Rychlé odkazy** v horní části stránky. Tento odkaz lze použít pro přístup k webovým unám specifickým pro službu, například:

* **Historie úloh** – mapaZmenit historii úloh.
* **Správce prostředků** - UI Správce prostředků příze.
* **NameNode** - Hadoop Distributed File System (HDFS) NameNode UI.
* **Oozie Web UI** - Oozie uživatelské hod.

Výběrem některého z těchto odkazů se v prohlížeči otevře nová karta, která zobrazí vybranou stránku.

> [!NOTE]  
> Výběr položky **Rychlé odkazy** pro službu může vrátit chybu "server nebyl nalezen". Pokud narazíte na tuto chybu, je nutné použít tunelové propojení SSH při použití položky **rychlé odkazy** pro tuto službu. Další informace naleznete v tématu [Použití tunelového propojení SSH s rozhraním HDInsight.](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Správa

### <a name="ambari-users-groups-and-permissions"></a>Uživatelé, skupiny a oprávnění ambari

Práce s uživateli, skupinami a oprávněními je podporována při použití clusteru HDInsight [přilehlém k doméně.](./domain-joined/hdinsight-security-overview.md) Informace o používání ustálení ambari v clusteru přilehlém k doméně naleznete v [tématu Správa clusterů HDInsight přilehlých k doméně](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Neměňte heslo hlídacího psa Ambari (hdinsightwatchdog) ve vašem clusteru HDInsight založeném na Linuxu. Změna hesla přeruší možnost použití akcí skriptu nebo provádění operací škálování s clusterem.

### <a name="hosts"></a>Hostitelé

Na stránce **Hosts** jsou uvedeni všichni hostitelé v clusteru. Chcete-li spravovat hostitele, postupujte takto.

![Apache Ambari hostí přehled stránek](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Přidání, vyřazení z provozu a opětovné uvedení hostitele z provozu by nemělo být používáno s clustery HDInsight.

1. Vyberte hostitele, kterého chcete spravovat.

2. Pomocí nabídky **Akce** vyberte akci, kterou chcete provést:

    |Položka |Popis |
    |---|---|
    |Spuštění všech součástí|Spusťte všechny součásti na hostiteli.|
    |Zastavit všechny součásti|Zastavte všechny součásti na hostiteli.|
    |Restartovat všechny součásti|Zastavte a spusťte všechny součásti na hostiteli.|
    |Zapnutí režimu údržby|Potlačí výstrahy pro hostitele. Tento režim by měl být povolen, pokud provádíte akce, které generují výstrahy. Například zastavení a spuštění služby.|
    |Vypnutí režimu údržby|Vrátí hostitele k normálnímu upozorňování.|
    |Zastavit|Zastaví DataNode nebo NodeManagers na hostiteli.|
    |Spustit|Spustí DataNode nebo NodeManagers na hostiteli.|
    |Restartování|Zastaví a spustí DataNode nebo NodeManagers na hostiteli.|
    |Vyřazení|Odebere hostitele z clusteru. **Tuto akci nepoužívejte v clusterech HDInsight.**|
    |Opětovné uvedení do provozu|Přidá do clusteru dříve vyřazeného hostitele. **Tuto akci nepoužívejte v clusterech HDInsight.**|

### <a name="services"></a><a id="service"></a>Služby

Na stránce **Řídicí panel** nebo **Služby** můžete zastavit a spustit všechny služby pomocí tlačítka **Akce** v dolní části seznamu služeb.

![Seznam akcí služby Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Při **přidání služby** je uveden v této nabídce, by neměl být používán k přidání služeb do clusteru HDInsight. Nové služby by měly být přidány pomocí akce skriptu během zřizování clusteru. Další informace o používání akcí skriptů naleznete v [tématu Přizpůsobení clusterů HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

Zatímco tlačítko **Akce** můžete restartovat všechny služby, často chcete spustit, zastavit nebo restartovat konkrétní službu. K provedení akcí v jednotlivých službách použijte následující kroky:

1. Na stránce **Řídicí panel** nebo **Služby** vyberte službu.

2. V horní části karty **Souhrn** použijte tlačítko **Akce servisu** a vyberte akci, kterou chcete provést. Tato akce restartuje službu ve všech uzlech.

    ![Apache Ambari individuální servisní akce](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Restartování některých služeb v době, kdy je spuštěný cluster, může generovat výstrahy. Chcete-li se vyhnout výstrahám, můžete před restartováním povolit **režim údržby** služby pomocí tlačítka **Akce služby.**

3. Jakmile je akce byla vybrána, **# op** položka v horní části stránky přírůstky ukázat, že operace na pozadí dochází. Pokud je nakonfigurován pro zobrazení, zobrazí se seznam operací na pozadí.

   > [!NOTE]  
   > Pokud jste pro službu **povolili režim údržby,** nezapomeňte jej po dokončení operace zakázat pomocí tlačítka **Akce servisu.**

Chcete-li službu nakonfigurovat, postupujte takto:

1. Na stránce **Řídicí panel** nebo **Služby** vyberte službu.

2. Vyberte kartu **Konfigurace.** Zobrazí se aktuální konfigurace. Zobrazí se také seznam předchozích konfigurací.

    ![Konfigurace služby Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Pomocí zobrazených polí upravte konfiguraci a pak vyberte **Uložit**. Nebo vyberte předchozí konfiguraci a pak vyberte **Vytvořit aktuální,** chcete-li vrátit zpět k předchozím ustavičům.

## <a name="ambari-views"></a>Ambari pohledy

Ambari Zobrazení umožňují vývojářům připojit prvky uživatelského rozhraní do Ambari web UI pomocí [Apache Ambari zobrazení framework .](https://cwiki.apache.org/confluence/display/AMBARI/Views) HDInsight poskytuje následující zobrazení s typy clusterů Hadoop:

* Zobrazení hive: Zobrazení Hive umožňuje spouštět dotazy Hive přímo z webového prohlížeče. Můžete ukládat dotazy, zobrazovat výsledky, ukládat výsledky do úložiště clusteru nebo stahovat výsledky do místního systému. Další informace o používání zobrazení Hive najdete v [tématu Použití zobrazení Hive Apache s HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez View: Tez View umožňuje lépe pochopit a optimalizovat úlohy. Můžete zobrazit informace o tom, jak jsou úlohy Tez spouštěny a jaké prostředky se používají.

## <a name="unsupported-operations"></a>Nepodporované operace

Následující operace Ambari nejsou v HDInsightu podporovány:

* __Přesunutí služby kolektoru metrik__. Při zobrazení informací o službě Sběratel metriky je jednou z akcí dostupných v nabídce Akce __služby kolektor přesunout metriky__. Tato akce není podporována pomocí HDInsight.

## <a name="next-steps"></a>Další kroky

* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) s HDInsight.
* [Použití Apache Ambari k optimalizaci konfigurací clusterů HDInsight](./hdinsight-changing-configs-via-ambari.md)
* [Škálování clusterů Azure HDInsight](./hdinsight-scaling-best-practices.md)
