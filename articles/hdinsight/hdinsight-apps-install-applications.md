---
title: Instalace aplikací jiných výrobců v Azure HDInsight
description: Přečtěte si, jak v Azure HDInsight instalovat aplikace Hadoop jiných výrobců.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: b916cbf690544d5887569e052a0456ca952140c8
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409541"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalace aplikací třetích stran Apache Hadoop v Azure HDInsight

Informace o instalaci jiných výrobců [Apache Hadoop](https://hadoop.apache.org/) aplikaci v Azure HDInsight. Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

Aplikace HDInsight je aplikace, které uživatelé můžou nainstalovat na clusteru HDInsight. Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

Následující seznam uvádí publikované aplikace:

|Aplikace |Typy clusteru | Popis |
|---|---|---|
|[Platforma AtScale informací](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale se změní váš cluster HDInsight do serveru OLAP horizontální navýšení kapacity, umožňují dotazu miliard řádků dat interaktivně pomocí nástrojů BI, které už znáte, vlastní a máte rádi – a to z Microsoft Excelu, Power BI, Tableau Software QlikView. |
|[CDAP pro HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP je první jednotnou integrační platformu pro velké objemy dat, které zrychluje času na hodnotu pro Hadoop a umožňuje IT k poskytování samoobslužných služeb data. Opensourcový a rozšiřitelné, CDAP odstraňuje bariéry inovace. Požadavky: 4 uzly oblasti, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Datameer pro samoobslužné škálovatelností pro přípravu, zkoumat a kterými se řídí vaše data pro analýzu zrychluje zapnutí komplexní s více zdroji dat do cenné informace připravené pro potřeby doručování rychlejší a chytřejší vytváření přehledy v podnikovém měřítku. |
|[Dataiku DSS v HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS v enterprise platforma pro datovou vědu, která umožňuje datoví vědci a analytiky dat. spolupráce pro návrh a účinnějšímu nových dat produktů a služeb, zapnutí nezpracovaná data do zvládat předpovědi. |
|[WANdisco Fusion HDI aplikace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Konzistenci dat. v distribuovaném prostředí výzvou je i velké objemy dat operace. WANdisco Fusion, softwarové podnikové platformy tento problém řeší tím, že konzistence nestrukturovaných dat v jakémkoli prostředí. |
|[H2O SparklingWater pro HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water podporuje následující distribuované algoritmy: GLM, Naive Bayes, distribuované Random doménové struktury, přechod počítače zvýšení skóre, hluboké Neuronové sítě, obsáhlý learning K-means, PCA, zobecněný modely řazení s nízkou, detekce anomálií, Autoencoders. |
|[Striim pro integraci dat v reálném čase pro HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |Striim (čteno "stream") je ukončení až do konce streamování integrace dat + platforma informací, povolení průběžného příjem, zpracování a analýzy různorodé datové proudy. |
|[Zrychlení podnikových Jumbune BigData, Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Na vysoké úrovni Jumbune pomáhá podnikům o 1. Urychlení Tez, modul MapReduce a Spark na základě Hive, Java, Scala výkon úloh. 2. Proaktivní Cluster Hadoop, monitorování, 3. Vytvářejí se řízení kvality dat v systému souborů DFS. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Spark |Používá technologii analytické jádro Apache Kylin, umožňuje Kyligence Enterprise BI velkých objemů dat. Jako organizace modul OLAP v Hadoop Kyligence Enterprise dává obchodní analytička se navrhovat BI na systému Hadoop pomocí standardní datový sklad a metodologie BI. |
|[Server úloh Spart pro prováděcí modul KNIME Spark](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |Spark |Server úloh Spart pro prováděcí modul KNIME Spark se používá pro připojení ke clusterům HDInsight KNIME analytická platforma. |
|[Hvězdice Presto pro Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto je rychlé a škálovatelné distribuované modul dotazů SQL. Navržen k oddělení úložiště a výpočetního výkonu, je ideální pro dotazování na data v Azure Data Lake Storage, Azure Blob Storage, SQL a NoSQL databáze a další zdroje dat Presto. |
|[Kolekce dat StreamSets pro HDInsight Cloud](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hdinsight Hadoop, HBase, Spark, Kafka |StreamSets Data Collector je jednoduchý, výkonný stroj, který datové proudy dat v reálném čase. Pomocí kolekcí dat trasy a zpracování dat v datových proudů. Obsahuje 30denní zkušební licenci. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise pro HDInsight podporuje tahání pro libovolné škálování dat dat na podnikové úrovni. Náklady na provozování Trifacta v Azure je kombinací Trifacta předplatné náklady a náklady na infrastrukturu Azure pro virtuální počítače. |
|[Unifi datová platforma](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-platform) |Hadoop, HBase, Storm, Spark |Datová platforma Unifi je bezproblémově integrovaná sada samoobslužné dat nástrojů pro posílení produktivity uživatelů business řešit problémy dat přírůstkové výnosy tuto jednotku, snížit náklady a složitost. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel Data aplikace pro cluster HDInsight Spark. |
|[Vodoryskou řízené AI Data Catalog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Vodoryskou katalogů slouží k uspořádání a řídí data auto-tag dat s podmínkami podnikání pomocí AI. Vodoryskou vaší firmy chtějí katalogu je zásadní, součástí úspěch pro samoobslužné analýzy, dodržování předpisů a zásad správného řízení a iniciativy správy IT. |

Pokyny uvedené v tomto článku se týkají webu Azure Portal. Můžete také exportovat šablonu Azure Resource Manageru z portálu nebo získejte kopii souboru šablony Resource Manageru od dodavatelů a pomocí Azure Powershellu a rozhraní příkazového řádku Azure Classic k nasazení šablony.  Zobrazit [vytvořit Apache Hadoop clusterů v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete instalovat aplikace HDInsight na stávající cluster HDInsight, musí mít cluster služby HDInsight. Chcete-li jeden vytvořit, prostudujte si část [Tvorba clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Aplikace HDInsight můžete také nainstalovat při vytváření clusteru HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalace aplikací do existujících clusterů
Následující postup ukazuje, jak můžete instalovat aplikace HDInsight do existujícího clusteru HDInsight.

**Instalace aplikace HDInsight**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo přejděte na **všechny služby** > **Analytics** > **clustery HDInsight**.
3. Ze seznamu vyberte HDInsight cluster.  Pokud ho ještě nemáte, vytvořte ho.  Viz článek [Vytvoření clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. V části **nastavení** vyberte **aplikací**. Zobrazí se seznam nainstalovaných aplikací v hlavním okně. 
   
    ![Nabídka portálu pro aplikace HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Vyberte **+ přidat** z nabídky. Zobrazí se seznam dostupných aplikací.  Pokud **+ přidat** zašedlé, že znamená, že se žádné aplikace pro tuto verzi clusteru HDInsight.
   
    ![Dostupné aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Vyberte jednu z dostupných aplikací a pak postupujte podle pokynů přijměte právní podmínky.

Zobrazí se stav instalace v oznámeních portálu (vyberte ikonu zvonku v horní části portálu). Po instalaci aplikace aplikace se zobrazí v seznamu nainstalované aplikace.

## <a name="install-applications-during-cluster-creation"></a>Instalace aplikací při vytváření clusteru
Během vytváření clusteru máte možnost instalace aplikací HDInsight. Během tohoto procesu se aplikace HDInsight instalují po vytvoření clusteru a jeho přechodu do spuštěného stavu. Instalace aplikací při vytváření clusteru pomocí webu Azure portal, můžete použít **vlastní** možnost místo výchozího **rychlé vytvoření** možnost.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Zobrazení seznamu nainstalovaných aplikací HDInsight a jejich vlastností
Portál zobrazuje seznam nainstalovaných aplikací HDInsight pro cluster a vlastnosti jednotlivých nainstalovaných aplikací.

**Seznam aplikací HDInsight a zobrazení vlastností**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo přejděte na **všechny služby** > **Analytics** > **clustery HDInsight**.
3. Ze seznamu vyberte HDInsight cluster.
4. V části **nastavení** vyberte **aplikací**. Zobrazí se seznam nainstalovaných aplikací v hlavním okně. 
   
    ![Nainstalované aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Vyberte jednu z nainstalovaných aplikací zobrazíte její vlastnosti. Seznamy vlastností:

    |Vlastnost | Popis |
    |---|---|
    |Název aplikace |Název aplikace. |
    |Status |Stav aplikace. |
    |Webová stránka |Adresa URL webové aplikace, kterou jste nasadili do hraničního uzlu. Přihlašovací údaje jsou stejné jako přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster. |
    |Koncový bod SSH |SSH můžete použít pro připojení k hraničnímu uzlu. Přihlašovací údaje SSH jsou stejné jako přihlašovací údaje uživatele SSH, které jste nakonfigurovali pro cluster. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Popis | Popis aplikace. |

6. Pokud chcete odstranit aplikaci, klikněte pravým tlačítkem na aplikaci a pak klikněte na **odstranit** v místní nabídce.

## <a name="connect-to-the-edge-node"></a>Připojení k hraničnímu uzlu
K hraničnímu uzlu se můžete připojit pomocí protokolu HTTP a SSH. Informace o koncových bodech najdete na [portálu](#list-installed-hdinsight-apps-and-properties). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Přihlašovací údaje koncového bodu protokolu HTTP jsou přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster HDInsight. Přihlašovací údaje koncového bodu SSH jsou přihlašovací údaje SSH, které jste nakonfigurovali pro cluster HDInsight.

## <a name="troubleshoot"></a>Řešení potíží
Viz článek [Řešení potíží instalace](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Další postup
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Zjistěte, jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvářet clustery založené na Linuxu Apache Hadoop v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Zjistěte, jak voláním šablon Resource Manageru vytvoříte clustery HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.

