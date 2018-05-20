---
title: Instalace aplikací Hadoop jiných výrobců v Azure HDInsight | Dokumentace Microsoftu
description: Přečtěte si, jak v Azure HDInsight instalovat aplikace Hadoop jiných výrobců.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 3ad112544a703a9b6ec37fa07cbd6df6976d5e26
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Instalace aplikací Hadoop jiných výrobců v Azure HDInsight

Zjistěte, jak nainstalovat aplikaci Hadoop třetích stran v Azure HDInsight. Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight. Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

V následujícím seznamu jsou publikované aplikace:

* **Platforma Intelligence AtScale** změní na clusteru HDInsight serveru se Škálováním na více systémů OLAP. Aplikace umožňuje dotazu až miliardy řádků dat interaktivně pomocí nástroje BI z aplikace Microsoft Excel, PowerBI, Tableau softwaru pro QlikView.
* **Obalového souboru CDAP pro HDInsight** poskytuje první integrace jednotnou platformu pro velké objemy dat, která snižuje čas do produkčního prostředí pro data aplikací a dat jezera 80 %. Tato aplikace podporuje pouze clustery Standard HBase 3.4.
* **DATAIKU DDS v HDInsight** umožňuje Odborníci v oblasti dat na prototypu, vytvoření a nasazení vysoce specifických služeb, které transformace nezpracovaná data do zvládat obchodní předpovědi.
* **H2O umělé Intelligence pro HDInsight (Beta)** H2O šumivého horních podporuje následující algoritmy distribuovaného: GLM, Naïve Bayes, distribuované náhodných doménové struktury, přechodu počítač zvyšovat skóre, hluboké Neuronové sítě, přímým učení K-means, PCA, Zobecněný nízkou Rank modely, detekce anomálií a Autoencoders.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (KAP) je připravené pro podniky datového skladu, který používá technologii Apache Kylin a Apache Hadoop, poskytuje dílčí sekundu dotazování latence pro datovou sadu masivním měřítku a zjednodušuje analýza dat pro Podnikoví uživatelé a analytikům. 
* **Příprava dat Paxata samoobslužných**
* **Serveru úloh Spark pro KNIME Spark vykonavatele** serveru úloh Spark pro KNIME Spark vykonavatele slouží k připojení ke clusterům HDInsight KNIME Analytics Platform.
* **Kolekce dat Streamsets pro HDInsight** poskytuje plně vybavené integrované vývojové prostředí (IDE) umožňující navrhovat, testovat, nasazovat a spravovat kanály ingestace typu any-to-any, které zachytávají streamovaná data a data dávek a zahrnují celou řadu transformací v průběhu streamování – to vše bez nutnosti psát vlastní kód. 
* **[Trifacta](http://www.trifacta.com/)**  technici dat a analytici umožňuje efektivněji prozkoumat a připravit data různých dneška s využitím strojového učení a poskytuje revoluční uživatelské prostředí, pracovní postup a architektura.
* **Aplikace HDI Fusion WANdisco** umožňuje nepřetržité konzistentní připojení k datům změnách bez ohledu na se nachází. Poskytuje přístup k datům kdykoli a kdekoli se žádné výpadky a bez přerušení.

Pokyny uvedené v tomto článku se týkají webu Azure Portal. Šablonu Azure Resource Manageru můžete exportovat z portálu nebo získat její kopii od dodavatelů, a poté k jejímu nasazení můžete použít Azure PowerShell a rozhraní příkazového řádku Azure.  V tématu [vytvoření Hadoop clusterů v HDInsight pomocí šablony Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

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
6. Klikněte na jednu z dostupných aplikací a potom postupujte podle pokynů přijměte právní podmínky.

Stav instalace aplikace můžete vidět v oznámeních portálu (klikněte na ikonu zvonku v horní části portálu). Po instalaci aplikace aplikace se zobrazí v seznamu nainstalované aplikace.

## <a name="install-applications-during-cluster-creation"></a>Instalace aplikací při vytváření clusteru
Během vytváření clusteru máte možnost instalace aplikací HDInsight. Během tohoto procesu se aplikace HDInsight instalují po vytvoření clusteru a jeho přechodu do spuštěného stavu. K instalaci aplikací při vytváření clusteru pomocí portálu Azure, použijete – vlastní – možnost místo výchozího – rychlé vytvoření – možnost.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Zobrazení seznamu nainstalovaných aplikací HDInsight a jejich vlastností
Portál zobrazuje seznam nainstalovaných aplikací HDInsight pro cluster a vlastnosti jednotlivých nainstalovaných aplikací.

**Seznam aplikací HDInsight a zobrazit vlastnosti**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **Clustery HDInsight** v levé nabídce. 
3. Klikněte na cluster HDInsight.
4. Z **nastavení**, klikněte na tlačítko **aplikace** pod **konfigurace** kategorie. Nainstalované aplikace jsou uvedeny na pravé straně. 
   
    ![Nainstalované aplikace aplikací HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Kliknutím na jednu z nainstalovaných aplikací zobrazíte její vlastnosti. Seznamy vlastností:
   
   * Název aplikace: Název aplikace.
   * Stav: Stav aplikace. 
   * Webová stránka: Adresa URL webové aplikace, které jste nasadili, k uzlu edge. Přihlašovací údaje jsou stejné jako přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster.
   * Koncový bod HTTP: Přihlašovací údaje jsou stejné jako přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster. 
   * Koncový bod SSH: SSH můžete použít pro připojení k uzlu edge. Přihlašovací údaje SSH jsou stejné jako přihlašovací údaje uživatele SSH, které jste nakonfigurovali pro cluster. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Aplikaci odstranit, klikněte pravým tlačítkem na aplikaci a pak klikněte na tlačítko **odstranit** v místní nabídce.

## <a name="connect-to-the-edge-node"></a>Připojení k hraničnímu uzlu
K hraničnímu uzlu se můžete připojit pomocí protokolu HTTP a SSH. Informace o koncových bodech najdete na [portálu](#list-installed-hdinsight-apps-and-properties). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Přihlašovací údaje koncového bodu protokolu HTTP jsou přihlašovací údaje uživatele protokolu HTTP, které jste nakonfigurovali pro cluster HDInsight. Přihlašovací údaje koncového bodu SSH jsou přihlašovací údaje SSH, které jste nakonfigurovali pro cluster HDInsight.

## <a name="troubleshoot"></a>Řešení potíží
Viz článek [Řešení potíží instalace](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Další postup
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): naučit se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytváření clusterů Hadoop na systému Linux v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Zjistěte, jak voláním šablon Resource Manageru vytvoříte clustery HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.

