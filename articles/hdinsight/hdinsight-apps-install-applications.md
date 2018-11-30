---
title: Instalace aplikací jiných výrobců v Azure HDInsight
description: Přečtěte si, jak v Azure HDInsight instalovat aplikace Hadoop jiných výrobců.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e1a6b453bb92ca57a1c9be9bff958ac0780ea12e
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498045"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalace aplikací třetích stran Apache Hadoop v Azure HDInsight

Informace o instalaci jiných výrobců [Apache Hadoop](https://hadoop.apache.org/) aplikaci v Azure HDInsight. Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

Aplikace HDInsight je aplikace, které uživatelé můžou nainstalovat na clusteru HDInsight. Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

Následující seznam uvádí publikované aplikace:

* **Platforma Intelligence AtScale** změní váš cluster HDInsight do serveru OLAP horizontální navýšení kapacity. Aplikace lze dotaz až miliardy řádků dat interaktivně pomocí nástrojů BI z Microsoft Excelu, Power BI, Tableau Software k QlikView.
* **Cask CDAP pro HDInsight** poskytuje první jednotnou integrační platformu pro velké objemy dat, která snižuje čas do produkčního prostředí pro data aplikací a datových jezer o 80 %. Tato aplikace podporuje pouze clustery Standard HBase 3.4.
* **DATAIKU DDS v HDInsight** umožňuje odborníkům v oblasti dat vytvářet prototypy, sestavovat a nasazovat vysoce specifické služby, které transformují nezpracovaná data na účinné obchodní předpovědi.
* **Datameer** je samoobslužné škálovatelná platforma pro přípravu, zkoumání, a kterými se řídí vaše data pro analýzu zrychluje zapnutí komplexní s více zdroji dat do cenné informace připravené pro potřeby doručování rychlejší a chytřejší vytváření přehledů na podnikové úrovni.
* **H2O umělé inteligence pro HDInsight (beta verze)** H2O Sparkling Water podporuje následující distribuované algoritmy: GLM, Naive Bayes, distribuované Random doménové struktury, přechod počítače zvýšení skóre, hluboké Neuronové sítě, obsáhlý learning K-means, PCA, Využívajících modely s nízkou řadit, detekce anomálií a Autoencoders.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (CAPE) je připravená pro podnikové datový sklad s využitím analytické jádro Apache Kylin a Apache Hadoop, nabízí subsecond dotazování čekací doba na datové sadě škálování ve velkém rozsahu a zjednodušuje analýzu dat pro Podnikoví uživatelé a analytiky. 
* **Adaptive samoobslužné přípravu dat**
* **Server úloh Spart pro prováděcí modul Sparku KNIME** serveru úloh Spark pro prováděcí modul KNIME Spark se používá k připojení analytická platforma KNIME do clusterů HDInsight.
* **Presto hvězdice** Presto je rychlé a škálovatelné distribuované modul dotazů SQL. Navržen k oddělení úložiště a výpočetního výkonu, je ideální pro dotazování na data v Azure Data Lake Storage, Azure Blob Storage, SQL a NoSQL databáze a další zdroje dat Presto.
* **Kolekce dat Streamsets pro HDInsight** poskytuje plně vybavené integrované vývojové prostředí (IDE) umožňující navrhovat, testovat, nasazovat a spravovat kanály ingestace typu any-to-any, které zachytávají streamovaná data a data dávek a zahrnují celou řadu transformací v průběhu streamování – to vše bez nutnosti psát vlastní kód. 
* **Striim** (vyslovováno "datový proud stream") je začátku do konce streamování integrace dat + platforma informací, povolení průběžného příjem, zpracování a analýzy různorodé datové proudy.
* **[Trifacta](http://www.trifacta.com/)**  umožňuje datovými architekty a analytici efektivněji a příprava různorodých dnes s využitím strojového učení k zajištění převratných uživatelské prostředí, pracovní postup a architektury.
* **Datová platforma Unifi** je bezproblémově integrovaná sada nástrojů pro samoobslužné dat navržená tak, aby obchodní uživatelům řešit problémy dat této jednotky přírůstkové výnosy, snížit náklady a složitost. 
* **WANdisco Fusion HDI aplikace** umožňuje nepřetržité konzistentní připojení k datům, protože změny bez ohledu na to se nachází. Poskytuje přístup k vašim datům kdykoli a kdekoli bez výpadku a bez přerušení.
* **Vodoryskou** katalogů slouží k uspořádání a řídí data automatického tagování dat s podmínkami podnikání pomocí AI. Vodoryskou vaší firmy chtějí katalogu je zásadní, součástí úspěch pro samoobslužné analýzy, dodržování předpisů a zásad správného řízení a iniciativy správy IT.

Pokyny uvedené v tomto článku se týkají webu Azure Portal. Můžete také exportovat šablonu Azure Resource Manageru z portálu nebo získejte kopii souboru šablony Resource Manageru od dodavatelů a pomocí Azure Powershellu a rozhraní příkazového řádku Azure Classic k nasazení šablony.  Zobrazit [vytvořit Apache Hadoop clusterů v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete instalovat aplikace HDInsight na stávající cluster HDInsight, musí mít cluster služby HDInsight. Chcete-li jeden vytvořit, prostudujte si část [Tvorba clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Aplikace HDInsight můžete také nainstalovat při vytváření clusteru HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalace aplikací do existujících clusterů
Následující postup ukazuje, jak můžete instalovat aplikace HDInsight do existujícího clusteru HDInsight.

**Instalace aplikace HDInsight**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **Clustery HDInsight** v levé nabídce.
3. Klikněte na cluster HDInsight.  Pokud ho ještě nemáte, vytvořte ho.  Viz článek [Vytvoření clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Klikněte na **Aplikace** v kategorii **Konfigurace**. Zobrazí se seznam nainstalovaných aplikací. Pokud nemůžete najít aplikace, znamená to, že žádné aplikace pro tuto verzi clusteru HDInsight neexistují.
   
    ![Nabídka portálu pro aplikace HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Klikněte na tlačítko **přidat** z nabídky. Zobrazí se seznam existujících aplikací HDInsight.
   
    ![Dostupné aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Klikněte na jednu z dostupných aplikací a pak postupujte podle pokynů přijměte právní podmínky.

Stav instalace aplikace můžete vidět v oznámeních portálu (klikněte na ikonu zvonku v horní části portálu). Po instalaci aplikace aplikace se zobrazí v seznamu nainstalované aplikace.

## <a name="install-applications-during-cluster-creation"></a>Instalace aplikací při vytváření clusteru
Během vytváření clusteru máte možnost instalace aplikací HDInsight. Během tohoto procesu se aplikace HDInsight instalují po vytvoření clusteru a jeho přechodu do spuštěného stavu. Instalace aplikací při vytváření clusteru pomocí webu Azure portal, použijete možnost--vlastní – místo výchozího – rychlé vytvoření – možnost.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Zobrazení seznamu nainstalovaných aplikací HDInsight a jejich vlastností
Portál zobrazuje seznam nainstalovaných aplikací HDInsight pro cluster a vlastnosti jednotlivých nainstalovaných aplikací.

**Seznam aplikací HDInsight a zobrazení vlastností**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **Clustery HDInsight** v levé nabídce. 
3. Klikněte na cluster HDInsight.
4. Z **nastavení**, klikněte na tlačítko **aplikací** pod **konfigurace** kategorie. Nainstalované aplikace jsou uvedeny na pravé straně. 
   
    ![Nainstalované aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Kliknutím na jednu z nainstalovaných aplikací zobrazíte její vlastnosti. Seznamy vlastností:
   
   * Název aplikace: Název aplikace.
   * Stav: Stav aplikace. 
   * Webová stránka: Adresa URL webové aplikace, kterou jste nasadili do hraničního uzlu. Přihlašovací údaje jsou stejné jako přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster.
   * Koncový bod HTTP: Přihlašovací údaje jsou stejné jako přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster. 
   * Koncový bod SSH: SSH můžete použít pro připojení k hraničnímu uzlu. Přihlašovací údaje SSH jsou stejné jako přihlašovací údaje uživatele SSH, které jste nakonfigurovali pro cluster. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Pokud chcete odstranit aplikaci, klikněte pravým tlačítkem na aplikaci a pak klikněte na **odstranit** v místní nabídce.

## <a name="connect-to-the-edge-node"></a>Připojení k hraničnímu uzlu
K hraničnímu uzlu se můžete připojit pomocí protokolu HTTP a SSH. Informace o koncových bodech najdete na [portálu](#list-installed-hdinsight-apps-and-properties). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Přihlašovací údaje koncového bodu protokolu HTTP jsou přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster HDInsight. Přihlašovací údaje koncového bodu SSH jsou přihlašovací údaje SSH, které jste nakonfigurovali pro cluster HDInsight.

## <a name="troubleshoot"></a>Řešení potíží
Viz článek [Řešení potíží instalace](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Další postup
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvářet clustery založené na Linuxu Apache Hadoop v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Zjistěte, jak voláním šablon Resource Manageru vytvoříte clustery HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.

