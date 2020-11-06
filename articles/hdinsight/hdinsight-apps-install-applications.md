---
title: Instalace aplikací třetích stran ve službě Azure HDInsight
description: Naučte se instalovat aplikace Apache Hadoop třetích stran v Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 3c1c7ac8fe8b7adf287bcde30a054df5ebaa63d7
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337436"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalace aplikací Apache Hadoop třetích stran v Azure HDInsight

Naučte se instalovat aplikaci [Apache Hadoop](https://hadoop.apache.org/) třetí strany do Azure HDInsight. Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

An HDInsight aplikace je aplikace, kterou uživatelé můžou instalovat v clusteru HDInsight. Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

Publikované aplikace jsou uvedeny v následujícím seznamu:

|Aplikace |Typy clusteru | Popis |
|---|---|---|
|[Platforma AtScale Intelligence](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale zapíná cluster HDInsight do serveru OLAP se škálováním na více instancí, což vám umožní interaktivně dotazovat se na miliardy řádků dat pomocí nástrojů BI, které už znáte, vlastníte a máte rádi – od Microsoft Excelu, Power BI, Tableau software až QlikView. |
|[CDAP pro HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP je první sjednocená integrační platforma pro velké objemy dat, která zrychluje dobu na hodnotu Hadoop a umožňuje IT oddělení poskytovat data samoobslužných služeb. Open Source a rozšiřitelné CDAP odstraňují překážky při inovacích. Požadavky: 4 uzly oblasti, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameerá škálovatelná platforma pro přípravu, prozkoumávání a řízení vašich dat pro účely analýzy urychluje převádění složitých zdrojů dat na cenné informace připravené pro firmy. díky rychlejšímu poskytování lepších informací na podnikové úrovni. |
|[Dataiku DSS ve službě HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS pro podnikovou datovou vědeckou platformu, která umožňuje odborníkům přes data a analytikům v oblasti IT efektivněji navrhovat a spouštět nové datové produkty a služby a přepínat nezpracovaná data na ovlivněné předpovědi. |
|[Aplikace HDI pro WANdisco Fusion](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark, HBA, více Kafka |Udržování dat konzistentních v distribuovaném prostředí je náročné na zpracování datových operací. WANdisco fúze, softwarová platforma na podnikové úrovni, řeší tento problém tím, že umožňuje nestrukturovaná konzistenci dat napříč jakýmkoli prostředím. |
|[SparklingWater pro HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |Sparková voda podporuje následující distribuované algoritmy: GLM, Naive Bayes, distribuovaná náhodná doménová struktura, počítač s hloubkovou neuronové sítí, obsáhlý Learning, K-znamená, DPS, zobecněné modely nízké klasifikace, detekce anomálií, autoencoders. |
|[Striim pro integraci dat Real-Time do HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop, HBA, Kafka, Spark, |Striim ("Stream") je ucelená platforma pro integraci dat a datovou sadu, která umožňuje průběžné přijímání, zpracování a analýzu různorodých datových proudů. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Jumbune pomáhá podnikům na vysoké úrovni – 1. Urychlení tez, MapReduce &ý podregistr založená na modulech Spark, Java a Scala úlohy. 2. Proaktivní monitorování clusteru Hadoop, 3. Vytváření správy kvality dat v systému souborů DFS. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBA, Spark |Kyligence Enterprise, který využívá Apache Kylin, umožňuje BI na velkých objemech dat. Jako podnikový modul OLAP v systému Hadoop Kyligence Enterprise umožňuje obchodním analytikům architekt BI v systému Hadoop pomocí standardních datových skladů a metodologie BI. |
|[Hvězdicový Presto pro Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto je rychlý a Škálovatelný modul SQL pro distribuované dotazy. Presto je ideální pro oddělení úložiště a výpočetních prostředků, které slouží k dotazování na data v Azure Data Lake Storage, databázích Azure Blob Storage, SQL a NoSQL a dalších zdrojích dat. |
|[Kolekce dat StreamSets pro Cloud HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBA, Spark, Kafka |StreamSets data collector je jednoduchý, výkonný modul, který streamuje data v reálném čase. Pomocí kolekce dat můžete směrovat a zpracovávat data v datových proudech. Obsahuje 30denní licenci na zkušební verzi. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark, HBA |Trifacta Wrangler Enterprise pro HDInsight podporuje datové tahání v podnikové síti pro libovolná škálování dat. Cena za provozování Trifacta v Azure je kombinací nákladů na předplatné Trifacta a náklady na infrastrukturu Azure pro virtuální počítače. |
|[Datová platforma sjednocuje](https://unifisoftware.com/platform/) |Hadoop, HBA, vyplavení, Spark |Datová platforma sjednocuje je bezproblémově integrovaná sada nástrojů samoobslužných dat, která slouží k tomu, aby firemnímu uživateli bylo možné řešit výzvy k datům, které jsou zaměřeny na přírůstkové výnosy, snižovat náklady nebo provozní složitost. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel data App pro cluster HDInsight Spark. |
|[Ponor AI-Driven Data Catalog](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Vyčíslení katalogů, uspořádání a řízení dat pomocí AI k automatickému označení dat pomocí obchodních podmínek. Katalog obchodních přepisů na vodoryskou je nepostradatelná komponenta, která je úspěšná pro samoobslužné analýzy, dodržování předpisů a zásady správného řízení a iniciativy správy IT. |

Pokyny uvedené v tomto článku se týkají webu Azure Portal. Můžete také exportovat šablonu Azure Resource Manager z portálu nebo získat kopii Správce prostředků šablony od dodavatelů a pomocí Azure PowerShell a Azure Classic CLI šablonu nasadit.  Přečtěte si téma [vytvoření Apache Hadoop clusterů ve službě HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete instalovat aplikace HDInsight na stávající cluster HDInsight, musí mít cluster služby HDInsight. Chcete-li jeden vytvořit, prostudujte si část [Tvorba clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md). Aplikace HDInsight můžete také nainstalovat při vytváření clusteru HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalace aplikací do existujících clusterů
Následující postup ukazuje, jak můžete instalovat aplikace HDInsight do existujícího clusteru HDInsight.

**Instalace aplikace HDInsight**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo přejděte na **všechny služby**  >  **Analytics**  >  **clustery HDInsight**.
3. Ze seznamu vyberte cluster HDInsight.  Pokud ho ještě nemáte, vytvořte ho.  Viz článek [Vytvoření clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. V kategorii **Nastavení** vyberte **aplikace**. V hlavním okně uvidíte seznam nainstalovaných aplikací. 
   
    ![Nabídka portálu pro aplikace HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. V nabídce vyberte **+ Přidat** . Můžete zobrazit seznam dostupných aplikací.  Pokud je pole **+ Přidat** šedé, znamená to, že pro tuto verzi clusteru HDInsight nejsou k dispozici žádné aplikace.
   
    ![Dostupné aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Vyberte jednu z dostupných aplikací a podle pokynů přijměte právní podmínky.

Stav instalace můžete zobrazit z oznámení na portálu (vyberte ikonu zvonku v horní části portálu). Po instalaci aplikace se aplikace zobrazí v seznamu nainstalované aplikace.

## <a name="install-applications-during-cluster-creation"></a>Instalace aplikací při vytváření clusteru

Během vytváření clusteru máte možnost instalace aplikací HDInsight. Během tohoto procesu se aplikace HDInsight instalují po vytvoření clusteru a jeho přechodu do spuštěného stavu. Pokud chcete během vytváření clusteru instalovat aplikace pomocí Azure Portal, na kartě **Konfigurace + ceny** vyberte **+ Přidat aplikaci**.

![Azure Portal aplikace konfigurace clusteru](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Zobrazení seznamu nainstalovaných aplikací HDInsight a jejich vlastností
Portál zobrazuje seznam nainstalovaných aplikací HDInsight pro cluster a vlastnosti jednotlivých nainstalovaných aplikací.

**Výpis vlastností aplikace a zobrazení HDInsight**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo přejděte na **všechny služby**  >  **Analytics**  >  **clustery HDInsight**.
3. Ze seznamu vyberte cluster HDInsight.
4. V kategorii **Nastavení** vyberte **aplikace**. V hlavním okně uvidíte seznam nainstalovaných aplikací. 
   
    ![Nainstalované aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Vyberte jednu z nainstalovaných aplikací pro zobrazení vlastnosti. Seznam vlastností:

    |Vlastnost | Popis |
    |---|---|
    |Název aplikace |Název aplikace |
    |Status |Stav aplikace |
    |Stránku |Adresa URL webové aplikace, kterou jste nasadili na hraničním uzlu. Přihlašovací údaje jsou stejné jako přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster. |
    |Koncový bod SSH |Pomocí SSH se můžete připojit k hraničnímu uzlu. Přihlašovací údaje SSH jsou stejné jako přihlašovací údaje uživatele SSH, které jste nakonfigurovali pro cluster. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Popis | Popis aplikace |

6. Pokud chcete aplikaci odstranit, klikněte pravým tlačítkem na aplikaci a potom v místní nabídce klikněte na **Odstranit** .

## <a name="connect-to-the-edge-node"></a>Připojení k hraničnímu uzlu
K hraničnímu uzlu se můžete připojit pomocí protokolu HTTP a SSH. Informace o koncových bodech najdete na [portálu](#list-installed-hdinsight-apps-and-properties). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Přihlašovací údaje koncového bodu protokolu HTTP jsou přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster HDInsight. Přihlašovací údaje koncového bodu SSH jsou přihlašovací údaje SSH, které jste nakonfigurovali pro cluster HDInsight.

## <a name="troubleshoot"></a>Řešení potíží
Viz článek [Řešení potíží instalace](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Další kroky
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se, jak nasadit nepublikovanou aplikaci HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](/rest/api/hdinsight/hdinsight-application): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvoření clusterů Apache Hadoop se systémem Linux v HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Naučte se volat šablony Správce prostředků pro vytváření clusterů HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.