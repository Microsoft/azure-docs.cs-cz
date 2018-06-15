---
title: Nasazení a správa topologií Apache Storm v HDInsight | Microsoft Docs
description: Zjistěte, jak pro nasazení, monitorování a správa topologií Apache Storm pomocí řídicího panelu Storm v HDInsight. Pomocí nástroje Hadoop pro sadu Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: conceptual
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 55c92e6408522b8a96a37dbedd99d929af1e49fb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414979"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Nasazení a správa topologií Apache Storm v HDInsight se systémem Windows

Řídicí panel Storm můžete snadno nasadit a spustit topologií Apache Storm ke svému clusteru HDInsight pomocí webového prohlížeče. Řídicí panel můžete použít také ke sledování a správě spuštěných topologií. Pokud používáte Visual Studio, nástroje HDInsight pro Visual Studio poskytují podobné funkce v sadě Visual Studio.

Řídicí panel Storm a funkce Storm v HDInsight nástroje využívají Storm REST API, které lze použít k vytvoření vlastního monitorování a řešení pro správu.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují Storm v clusteru HDInsight se systémem Windows jako operační systém. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Informace o nasazení a správa topologií Storm pomocí clusteru služby HDInsight, který používá Linux najdete v tématu [nasazení a správa topologií Apache Storm v HDInsight se systémem Linux](apache-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Požadavky

* **Apache Storm v HDInsight** -najdete v části [začít pracovat s Apache Storm v HDInsight](apache-storm-tutorial-get-started-linux.md) pokyny týkající se vytvoření clusteru.

* Pro **řídicí panel Storm**: moderní webový prohlížeč, který podporuje HTML5.

* Pro **Visual Studio** -Azure SDK 2.5.1 nebo novější a nástroje HDInsight pro Visual Studio. V tématu [Začínáme pomocí nástrojů HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) k instalaci a konfiguraci nástroje HDInsight pro Visual Studio.

    Jedna z následujících verzí sady Visual Studio:

  * Visual Studio 2012 s aktualizací 4

  * Visual Studio 2013 s aktualizací 4 nebo Visual Studio 2013 Community

  * Visual Studio 2015 (všechny edice)

  * Visual Studio 2017 (všechny edice)

## <a name="storm-dashboard"></a>Řídicí panel Storm

Řídicí panel Storm je webová stránka, k dispozici v clusteru Storm. Adresa URL je **https://&lt;clustername >.azurehdinsight.net/**, kde **clustername** je název vaší cluster Storm v HDInsight.

Z horní části řídicího panelu Storm, vyberte **odeslání topologie**. Postupujte podle pokynů na stránce ke spuštění ukázkové topologie nebo odeslání a spusťte topologie, kterou jste vytvořili.

![odeslání stránky topologie][storm-dashboard-submit]

### <a name="storm-ui"></a>Storm uživatelského rozhraní

Na řídicím panelu Storm, vyberte **uživatelské rozhraní Storm** odkaz. Zobrazí informace o clusteru, kromě všech spuštěných topologií.

![uživatelské rozhraní storm][storm-dashboard-ui]

> [!NOTE]
> U některých verzí aplikace Internet Explorer můžete zjistit, že uživatelské rozhraní Storm neaktualizuje po jste ji nejprve navštívili. Například se nemusí zobrazit nové topologie odeslání, nebo se může zobrazit topologii jako aktivní, když je dříve deaktivováno. Společnost Microsoft si je vědoma tento problém a pracuje na řešení.

#### <a name="main-page"></a>Hlavní stránka

Hlavní stránka uživatelského rozhraní Storm poskytuje následující informace:

* **Souhrn clusteru**: základní informace o clusteru Storm.

* **Souhrn topologie**: seznam spuštěných topologií. Chcete-li zobrazit další informace o konkrétní topologie pomocí odkazů v této části.

* **Souhrn nadřízeného**: informace o nadřízeného Storm.

* **Konfigurace nimbus**: Nimbus konfiguraci pro daný cluster.

#### <a name="topology-summary"></a>Souhrn topologie

Výběr odkazu z **souhrn topologie** části zobrazí následující informace o topologii:

* **Souhrn topologie**: základní informace o topologii.

* **Topologie akce**: akce správy, které můžete provést pro topologii.

  * **Aktivovat**: obnoví zpracování deaktivované topologie.

  * **Deaktivovat**: Pozastaví spuštěné topologie.

  * **Znovu vyvážit**: upraví paralelismus topologii. Po změně počtu uzlů v clusteru musíte znovu vyvážit spuštěné topologie. To umožňuje topologii upravovat paralelismus za účelem kompenzace pro vyšší nebo ke snížení počtu uzlů v clusteru.

      Další informace najdete v tématu [pochopení paralelismu topologie Storm (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Příkaz kill**: ukončí topologii Storm po zadaný časový limit.

* **Statistiky topologie**: Statistika týkající se topologie. Použijte odkazy v **okno** sloupec nastavit časový rámec pro zbývající záznamy na stránce.

* **Spouts**: funkcích spouts používané topologii. Chcete-li zobrazit další informace o konkrétních funkcích spouts pomocí odkazů v této části.

* **Bolts**: funkce bolts používané topologii. Použijte odkazy v této části zobrazíte další informace o konkrétních funkcích bolts.

* **Topologie konfigurace**: konfiguraci vybrané topologie.

#### <a name="spout-and-bolt-summary"></a>Souhrn funkcí Bolt a spout

Výběr funkcí spout z **Spouts** nebo **Bolts** části zobrazí následující informace o vybrané položce:

* **Souhrn součást**: základní informace o funkcích spout nebo bolt.

* **Statistiky funkcí spout/Bolt**: statistiky o funkcích spout nebo bolt. Použijte odkazy v **okno** sloupec nastavit časový rámec pro zbývající záznamy na stránce.

* **Statistiky vstupu** (pouze funkce bolt): informace o vstupní datové proudy využívaná funkcí bolt.

* **Statististiky výstupu**: informace o datové proudy vygenerované tímto objektem spout nebo funkce bolt.

* **Vykonavatelů**: informace o funkcích spout nebo bolt instance. Vyberte **Port** vytváří záznam pro konkrétní vykonavatele chcete zobrazit protokol diagnostické informace pro tuto instanci.

* **Chyby**: všechny informace o chybě pro tento spout nebo funkce bolt.

## <a name="hdinsight-tools-for-visual-studio"></a>Nástroje HDInsight pro Visual Studio

Nástroje HDInsight slouží k odeslání jazyka C# nebo hybridní topologie pro váš cluster Storm. Následující postup použijte ukázkovou aplikaci. Informace o vytváření vlastního topologie pomocí nástrojů HDInsight naleznete v tématu [vývoj topologií C# pomocí nástrojů HDInsight pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Použijte následující kroky k nasazení ukázkového pro váš cluster Storm v HDInsight, pak zobrazení a Správa topologie.

1. Pokud jste ještě nenainstalovali nejnovější verzi nástroje HDInsight pro Visual Studio, najdete v části [Začínáme pomocí nástrojů HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Otevřete Visual Studio, vyberte **soubor** > **nový** > **projektu**.

3. V **nový projekt** dialogové okno, rozbalte seznam **nainstalovaná** > **šablony**a potom vyberte **HDInsight**. V seznamu šablon vyberte **Storm ukázka**. V dolní části dialogových oken zadejte název aplikace.

    ![Bitové kopie](./media/apache-storm-deploy-monitor-topology/sample.png)

4. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **odeslání do Storm v HDInsight**.

   > [!NOTE]
   > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se k ta, která obsahuje váš cluster Storm v HDInsight.

5. Vyberte váš cluster Storm v HDInsight z **Storm Cluster** rozevíracího seznamu a potom vyberte **odeslání**. Můžete sledovat, jestli je úspěšně odesílání pomocí **výstup** okno.

6. Když topologii byl úspěšně odeslán, **topologie Storm** pro cluster by se měla objevit. Vyberte topologii ze seznamu a zobrazit informace o spuštěné topologie.

    ![monitorování v sadě Visual studio](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Můžete také zobrazit **topologie Storm** z **Průzkumníka serveru** rozšířením **Azure** > **HDInsight**a potom kliknete pravým tlačítkem cluster Storm v HDInsight a výběr **topologie Storm zobrazení**.

    Vyberte tvar funkcích spouts nebo funkce bolts zobrazíte informace o těchto součástí. Otevře se nové okno pro každé vybrané položky.

   > [!NOTE]
   > Název topologie je název třídy topologie (v tomto případě `HelloWord`,) s časovým razítkem připojí.

7. Z **souhrn topologie** zobrazit, vyberte možnost **Kill** k zastavení topologie.

   > [!NOTE]
   > Topologie Storm pokračovat, spuštění, dokud jsou zastaveny nebo odstranění clusteru.


## <a name="rest-api"></a>REST API

Uživatelské rozhraní Storm je postavený na rozhraní REST API, takže můžete provádět podobné správy a monitorování funkce pomocí rozhraní REST API. Rozhraní REST API můžete použít k vytvoření vlastních nástrojů pro správu a monitorování topologie Storm.

Další informace najdete v tématu [Storm uživatelského rozhraní REST API](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Tyto informace je specifická pro Apache Storm v HDInsight pomocí rozhraní REST API.

### <a name="base-uri"></a>Základní identifikátor URI

Základní identifikátor URI pro rozhraní API REST v clusterech HDInsight **https://&lt;clustername >.azurehdinsight.net/stormui/api/v1/**, kde **clustername** je název vaší cluster Storm v HDInsight.

### <a name="authentication"></a>Authentication

Požadavky na rozhraní REST API musí používat **základní ověřování**, takže můžete použít název správce clusteru HDInsight a heslo.

> [!NOTE]
> Protože základní ověřování je odeslána pomocí nešifrovaného textu, měli byste **vždy** používat protokol HTTPS pro zabezpečenou komunikaci s clusterem.

### <a name="return-values"></a>Návratové hodnoty

Informace, které se vrátí z rozhraní API REST může být pouze z použitelné v rámci clusteru nebo virtuální počítače ve stejné virtuální síti Azure jako cluster. Například plně kvalifikovaný název domény (FQDN) vrátil pro Zookeeper servery nejsou byly přístupné z Internetu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili jak nasadit a monitorovat topologie pomocí řídicího panelu Storm, přečtěte si, jak:

* [Vývoj topologie C# pomocí nástrojů HDInsight pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

* [Vyvíjet topologie založené na jazyce Java pomocí nástroje Maven](apache-storm-develop-java-topology.md)

Seznam Další příklad topologií najdete v tématu [příklad topologií pro Storm v HDInsight](apache-storm-example-topology.md).

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
