---
title: Instalace aplikací třetích stran na Azure HDInsight
description: Přečtěte si, jak nainstalovat aplikace Apache Hadoop třetích stran do Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366338"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalace aplikací Apache Hadoop od jiných výrobců na Azure HDInsight

Přečtěte si, jak nainstalovat aplikaci [Apache Hadoop](https://hadoop.apache.org/) od jiného výrobce do Azure HDInsight. Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

Aplikace HDInsight je aplikace, kterou mohou uživatelé nainstalovat do clusteru HDInsight. Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

V následujícím seznamu jsou uvedeny publikované aplikace:

|Aplikace |Typ clusteru | Popis |
|---|---|---|
|[Inteligentní platforma Atscale](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale změní váš hdinsight cluster na škálovatelný server OLAP, což vám umožní interaktivně vyhledávat miliardy řádků dat pomocí nástrojů BI, které už znáte, vlastníte a milujete – od Microsoft Excelu, Power BI, Tableau Software až po QlikView. |
|[CDAP pro HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP je první sjednocená integrační platforma pro velká data, která urychluje dobu od hodnoty pro Hadoop a umožňuje IT poskytovat samoobslužná data. Open source a rozšiřitelný, CDAP odstraňuje překážky pro inovace. Požadavky: 4 Uzly regionu, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Samoobslužná škálovatelná platforma datameeru pro přípravu, zkoumání a správě dat pro analýzy urychluje přeměnu složitých vícezdrojových dat na cenné informace připravené pro podnikání a poskytuje rychlejší a chytřejší přehledy v podnikovém měřítku. |
|[Dataiku DSS na HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Jiskra |Dataiku DSS v podnikové datové vědecké platformě, která umožňuje datovým vědcům a datovým analytikům spolupracovat na efektivnějším navrhování a spouštění nových datových produktů a služeb a přeměnit nezpracovaná data na působivé předpovědi. |
|[APLIKACE WANdisco Fusion HDI](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Jiskra, HBase, Bouře, Kafka |Udržování konzistentních dat v distribuovaném prostředí je obrovskou výzvou pro operace s daty. WANdisco Fusion, softwarová platforma podnikové třídy, tento problém řeší tím, že umožňuje nestrukturovanou konzistenci dat v jakémkoli prostředí. |
|[H2O SparklingWater pro HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water podporuje následující distribuované algoritmy: GLM, Naivní Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection, Autoencoders. |
|[Striim pro integraci dat v reálném čase do HDInsightu](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Bouře, Jiskra, Kafka |Striim (vyslovuje se "stream") je komplexní streamovaná datová integrace + inteligentní platforma, která umožňuje nepřetržité ingestování, zpracování a analýzu různorodých datových proudů. |
|[Jumbune BigData Analytics, který zrychluje podnikání](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Jiskra |Na vysoké úrovni, Jumbune pomáhá podnikům, 1. Urychlení Tez, MapReduce & Spark engine založené Hive, Java, Scala zatížení výkon. 2. Proaktivní monitorování hadoopového clusteru, 3. Vytvoření správy kvality dat v distribuovaném systému souborů. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase,Jiskra |Kyligence Enterprise, poháněná Apache Kylinem, umožňuje BI na velkých datech. Jako podnikový motor OLAP na Hadoopu umožňuje Kyligence Enterprise obchodnímu analytikovi architektovi BI na Hadoopu standardní datový sklad a metodiku BI. |
|[Třesk Presto pro Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto je rychlý a škálovatelný distribuovaný sql dotazovací stroj. Presto, které je navrženo pro oddělení úložiště a výpočetních prostředků, je ideální pro dotazování na data v Azure Data Lake Storage, Azure Blob Storage, SQL a NoSQL databázích a dalších zdrojích dat. |
|[Kolekce dat datových sad streamsetů pro HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Jiskra, Kafka |StreamSets Data Collector je lehký, výkonný modul, který streamuje data v reálném čase. Pomocí funkce Shromažďování dat můžete směrovat a zpracovávat data v datových tocích. Dodává se s 30 denní zkušební licencí. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop, Jiskra,HBase |Trifacta Wrangler Enterprise for HDInsight podporuje celopodnikové tahanice dat pro libovolné měřítko dat. Náklady na spuštění Trifacta v Azure je kombinací nákladů na předplatné Trifacta plus náklady na infrastrukturu Azure pro virtuální počítače. |
|[Datová platforma Unifi](https://unifisoftware.com/platform/) |Hadoop, HBase, Bouře, Jiskra |Datová platforma Unifi je bezproblémově integrovaná sada samoobslužných datových nástrojů, které jsou navrženy tak, aby podnikovému uživateli zvládaly problémy s daty, které vedou k přírůstkovým příjmům, snižují náklady nebo provozní složitost. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Rozluštit datovou aplikaci pro cluster HDInsight Spark. |
|[Katalog dat řízený umělou avodrtou](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Katalogy vodličů, uspořádá a řídí data pomocí ai k automatickému označování dat obchodními podmínkami. Obchodní gramotný katalog Waterline je kritickou součástí úspěchu pro samoobslužnou analýzu, dodržování předpisů a řízení a iniciativy v oblasti správy IT. |

Pokyny uvedené v tomto článku se týkají webu Azure Portal. Šablonu Azure Resource Manager můžete také exportovat z portálu nebo získat kopii šablony Správce prostředků od dodavatelů a použít Azure PowerShell a Azure Classic CLI k nasazení šablony.  Viz [Vytvoření clusterů Apache Hadoop na HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete instalovat aplikace HDInsight na stávající cluster HDInsight, musí mít cluster služby HDInsight. Chcete-li jeden vytvořit, prostudujte si část [Tvorba clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md). Aplikace HDInsight můžete také nainstalovat při vytváření clusteru HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalace aplikací do existujících clusterů
Následující postup ukazuje, jak můžete instalovat aplikace HDInsight do existujícího clusteru HDInsight.

**Instalace aplikace HDInsight**

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levém menu přejděte do clusterů **Všechny služby** > **Analytics** > **HDInsight**.
3. Vyberte cluster HDInsight ze seznamu.  Pokud ho ještě nemáte, vytvořte ho.  Viz článek [Vytvoření clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. V kategorii **Nastavení** vyberte **Aplikace**. Seznam nainstalovaných aplikací můžete zobrazit v hlavním okně. 
   
    ![Nabídka portálu pro aplikace HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. V nabídce vyberte **+Přidat.** Můžete zobrazit seznam dostupných aplikací.  Pokud **+Add** je šedě, to znamená, že neexistují žádné aplikace pro tuto verzi clusteru HDInsight.
   
    ![Dostupné aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Vyberte jednu z dostupných aplikací a podle pokynů přijměte zákonné podmínky.

Stav instalace můžete zobrazit z oznámení portálu (v horní části portálu vyberte ikonu zvonku). Po instalaci aplikace se aplikace zobrazí v seznamu Nainstalované aplikace.

## <a name="install-applications-during-cluster-creation"></a>Instalace aplikací při vytváření clusteru

Během vytváření clusteru máte možnost instalace aplikací HDInsight. Během tohoto procesu se aplikace HDInsight instalují po vytvoření clusteru a jeho přechodu do spuštěného stavu. Pokud chcete instalovat aplikace během vytváření clusteru pomocí portálu Azure, na kartě **Konfigurace + ceny** vyberte + Přidat **aplikaci**.

![Konfigurační aplikace portálového clusteru Azure](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Zobrazení seznamu nainstalovaných aplikací HDInsight a jejich vlastností
Portál zobrazuje seznam nainstalovaných aplikací HDInsight pro cluster a vlastnosti jednotlivých nainstalovaných aplikací.

**Seznam vlastností aplikace HDInsight a zobrazení**

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levém menu přejděte do clusterů **Všechny služby** > **Analytics** > **HDInsight**.
3. Vyberte cluster HDInsight ze seznamu.
4. V kategorii **Nastavení** vyberte **Aplikace**. Seznam nainstalovaných aplikací můžete zobrazit v hlavním okně. 
   
    ![Nainstalované aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Vyberte jednu z nainstalovaných aplikací, která má být vlastnost zobrazována. Seznam vlastností:

    |Vlastnost | Popis |
    |---|---|
    |App name (Název aplikace) |Název aplikace. |
    |Status |Stav aplikace. |
    |Webové stránky |Adresa URL webové aplikace, kterou jste nasadili do hraničního uzlu. Přihlašovací údaje jsou stejné jako přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster. |
    |Koncový bod SSH |SSH můžete použít pro připojení k hraničnímu uzlu. Přihlašovací údaje SSH jsou stejné jako přihlašovací údaje uživatele SSH, které jste nakonfigurovali pro cluster. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Popis | Popis aplikace. |

6. Pokud chcete aplikaci odstranit, klikněte na ni pravým tlačítkem myši a potom z kontextové nabídky klikněte na **Odstranit.**

## <a name="connect-to-the-edge-node"></a>Připojení k hraničnímu uzlu
K hraničnímu uzlu se můžete připojit pomocí protokolu HTTP a SSH. Informace o koncových bodech najdete na [portálu](#list-installed-hdinsight-apps-and-properties). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Přihlašovací údaje koncového bodu protokolu HTTP jsou přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster HDInsight. Přihlašovací údaje koncového bodu SSH jsou přihlašovací údaje SSH, které jste nakonfigurovali pro cluster HDInsight.

## <a name="troubleshoot"></a>Řešení potíží
Viz článek [Řešení potíží instalace](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Další kroky
* [Instalace vlastních aplikací HDInsight:](hdinsight-apps-install-custom-applications.md)Přečtěte si, jak nasadit nepublikovanou aplikaci HDInsight do HDInsightu.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvářejte clustery Apache Hadoop založené na Linuxu v HDInsightpomocí šablon Resource Manageru:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)přečtěte si, jak volat šablony Správce prostředků k vytvoření clusterů HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.

