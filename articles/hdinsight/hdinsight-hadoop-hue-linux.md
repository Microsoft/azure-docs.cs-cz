---
title: Odstín, který se systémem Hadoop v clusterech se systémem HDInsight Linux – Azure
description: Zjistěte, jak instalace aplikace Hue v clusterech HDInsight a směrovat požadavky na odstín, který pomocí tunelové propojení. Použití Hue procházet úložiště a spuštění Hive a Pig.
keywords: HUE hadoop
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 6c1acf0e0b93ca6d3a8eae9f291f37d7bb0aa763
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51009279"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Nainstalovat a používat rozhraní Hue v clusterech HDInsight Hadoop

Zjistěte, jak instalace aplikace Hue v clusterech HDInsight a směrovat požadavky na odstín, který pomocí tunelové propojení.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>Co je Hue?
Odstín, který je sada webových aplikací používaných pro interakci s clusterem Hadoop. Použití Hue procházet úložiště spojené s clusterem Hadoop (v případě clusterů HDInsight WASB), spouštět úlohy Hive a Pig skripty a tak dále. Následující komponenty jsou k dispozici instalace aplikace Hue v clusteru HDInsight Hadoop.

* Beeswax Hive Editor
* Pig
* Správce úložiště metadat
* Oozie
* FileBrowser (která komunikuje s výchozí kontejner WASB)
* Prohlížeč úloh

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
>
> Vlastní komponenty získat obchodně přiměřenou podporu můžete-li dále řešit tento problém. To může vést řeší problém nebo s výzvou k zapojení dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje například mnoho komunitním webům, které lze použít jako: [fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také projektů Apache mít projektovým webům na [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalace aplikace Hue pomocí akcí skriptů

Skript pro instalaci aplikace Hue do clusteru HDInsight se systémem Linux je k dispozici na https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Tento skript můžete použít k instalaci aplikace Hue v clusterech s objekty BLOB Azure Storage (WASB) nebo Azure Data Lake Store jako výchozím úložištěm.

Tato část obsahuje informace o tom, jak pomocí skriptu při zřizování clusteru pomocí webu Azure portal.

> [!NOTE]
> Prostředí Azure PowerShell, rozhraní příkazového řádku Azure Classic, sady HDInsight .NET SDK nebo šablony Azure Resource Manageru je také možné použití akce skriptu. Akce se skripty můžete také použít k už běží clustery. Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Zahajte zřizování clusteru pomocí kroků v [zřídit HDInsight clustery v Linuxu](hdinsight-hadoop-provision-linux-clusters.md), ale nedokončí zřizování.

   > [!NOTE]
   > Postup instalace aplikace Hue v clusterech HDInsight, velikost doporučená hlavního uzlu je nejméně A4 (8 jader, 14 GB paměti).
   >
   >
2. Na **volitelná konfigurace** okně vyberte **akcí skriptů**a zadejte informace, jak je znázorněno níže:

    ![Zadejte parametry akce skriptu pro Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "zadejte parametry akce skriptu pro Hue")

   * **NÁZEV**: Zadejte popisný název akce skriptu.
   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HLAVNÍ**: Zaškrtněte tuto možnost
   * **PRACOVNÍK**: Toto pole nechat prázdné.
   * **ZOOKEEPER**: Toto pole nechat prázdné.
   * **Parametry**: Toto pole nechat prázdné.
3. V dolní části **akcí skriptů**, použijte **vyberte** tlačítko, čímž konfiguraci uložíte. Nakonec použijte **vyberte** tlačítko v dolní části **volitelná konfigurace** okno a uložte informace o konfiguraci volitelné.
4. Pokračovat zřizování clusteru, jak je popsáno v [zřídit HDInsight clustery v Linuxu](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Použití Hue s clustery HDInsight

Tunelové připojení pomocí protokolu SSH je jediný způsob, jak přistupovat k odstín, který v clusteru, jakmile je spuštěna. Tunelovým propojením přes SSH umožní provoz a přejděte rovnou k hlavnímu uzlu clusteru se spuštěným systémem odstín. Po dokončení zřizování clusteru použití Hue v clusteru HDInsight Linux pomocí následujících kroků.

> [!NOTE]
> Doporučujeme, abyste pomocí webového prohlížeče Firefox postupovat podle pokynů níže.
>
>

1. Pomocí informací v [používání tunelového propojení SSH pro přístup k webové uživatelské rozhraní Ambari, ResourceManager, JobHistory, NameNode, Oozie a dalším webovým Uživatelským rozhraním](hdinsight-linux-ambari-ssh-tunnel.md) vytvoření tunelu SSH z klientského systému do clusteru HDInsight, a potom nakonfigurujte webu Prohlížeč pro použití tunelu SSH jako proxy server.

2. Po vytvoření tunelu SSH a nakonfigurovaný v prohlížeči skrze ni provoz proxy, musíte najít název hostitele primárnímu hlavnímu uzlu. To můžete provést připojení ke clusteru pomocí SSH na portu 22. Například `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` kde **uživatelské jméno** se svým uživatelským jménem SSH a **CLUSTERNAME** je název vašeho clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Jakmile budete připojeni, použijte následující příkaz získat plně kvalifikovaný název primárnímu hlavnímu uzlu:

        hostname -f

    Vrátí název nějak takto:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Toto je název hostitele primárnímu hlavnímu uzlu, kde se nachází na webu Hue.
4. Použijte postup otevření portál Hue v prohlížeči http://HOSTNAME:8888. Nahraďte názvem hostitele s názvem, který jste získali v předchozím kroku.

   > [!NOTE]
   > Po přihlášení poprvé vyzve k vytvoření účtu k přihlášení na portál Hue. Přihlašovací údaje, které zadáte tady bude omezená na portálu a nesouvisí se správce nebo přihlašovacích údajů uživatele SSH, které jste zadali během zřizování clusteru.
   >
   >

    ![Přihlaste se k portálu Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "zadejte přihlašovací údaje pro portál Hue")

### <a name="run-a-hive-query"></a>Spuštění dotazu Hive
1. Portál Hue, klikněte na **dotazu editory**a potom klikněte na tlačítko **Hive** otevřete Hive editor.

    ![Použití Hivu](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "pomocí Hivu")
2. Na **pomoct** ve skupině **databáze**, byste měli vidět **hivesampletable**. Toto je ukázka tabulky, který je součástí všech clustery Hadoop v HDInsight. V pravém podokně zadejte ukázkový dotaz a zobrazit výstup na **výsledky** kartě v podokně pod, jak je znázorněno na snímku obrazovky.

    ![Spuštění dotazu Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "spuštění Hive dotaz")

    Můžete také použít **grafu** kartu pro zobrazení vizuální znázornění výsledek.

### <a name="browse-the-cluster-storage"></a>Procházet úložiště clusteru
1. Z portálu Hue, klikněte na tlačítko **prohlížeč souborů** v pravém horním rohu řádku nabídek.
2. Ve výchozím nastavení se otevře prohlížeč souborů na **/uživatel/myuser** adresáře. Klikněte na dopředné lomítko bezprostředně před adresáři uživatele cestu ke kořenové složce kontejneru úložiště Azure, které jsou přidružené ke clusteru.

    ![Použití souboru prohlížeče](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "použití soubor prohlížeče")
3. Klikněte pravým tlačítkem na soubor nebo složku, kterou chcete zobrazit dostupné operace. Použití **nahrát** tlačítko v pravém rohu k nahrání souborů do aktuálního adresáře. Použití **nový** pro vytvoření nové soubory nebo adresáře.

> [!NOTE]
> Prohlížeč souborů odstín, který lze zobrazit pouze obsah výchozí kontejner přidružené ke clusteru HDInsight. Žádné další účty/kontejnery úložiště, které vám mohou být přidruženy s clusterem nebudou přístupné pomocí prohlížeč souborů. Však další kontejnery přidružené ke clusteru bude vždycky dostupný pro úlohy Hive. Pokud zadáte tento příkaz například `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` v editoru Hive můžete zobrazit obsah i další kontejnery. V tomto příkazu **newcontainer** není výchozí kontejner související s clusterem.
>
>

## <a name="important-considerations"></a>Důležité informace
1. Skript pro instalaci aplikace Hue ho nainstaluje jenom na primární hlavní uzel clusteru.

2. Během instalace restartují se více služby Hadoop (HDFS, YARN, MR2, Oozie) pro aktualizaci konfigurace. Po dokončení skriptu instalace aplikace Hue, může trvat nějakou dobu jinými službami Hadoop a spouštění. To může ovlivnit výkon vaší aplikace Hue původně. Po spuštění všech služeb, odstín, který bude plně funkční.
3. Odstín, který nerozumí úlohách Tez, což je výchozí pro Hive. Pokud chcete použít jako Hive prováděcí modul MapReduce, aktualizujte skript, který chcete ve skriptu použijte následující příkaz:

        set hive.execution.engine=mr;

4. Linuxové clustery máte scénáře, kde služby běží na primárnímu hlavnímu uzlu a správce prostředků mohl být spuštěn na sekundárním. Takový scénář může vést k chybám (viz dole) při použití odstín, který Pokud chcete zobrazit podrobnosti o spouštění úloh na clusteru. Po dokončení úlohy, ale můžete zobrazit podrobnosti o úloze.

   ![Chyba portál Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "chyba portál Hue")

   Příčinou je známý problém. Jako alternativní řešení upravte Ambari, tak, aby active Resource Manager je spuštěna také na primárnímu hlavnímu uzlu.
5. HUE rozumí WebHDFS, zatímco clustery HDInsight se používají služby Azure Storage pomocí `wasb://`. Vlastní skript použít pomocí akce skriptu tedy nainstaluje WebWasb, což je služba kompatibilních s WebHDFS rozhovor WASB. Ano, i když portál Hue říká HDFS místech (podobně jako když jste najeďte myší **prohlížeč souborů**), by měl být interpretován jako WASB.

## <a name="next-steps"></a>Další postup
* [Clustery HDInsight nainstalovat Giraph](hdinsight-hadoop-giraph-install-linux.md). Clustery HDInsight Hadoop nainstalovat Giraph pomocí přizpůsobení clusteru. Giraph umožňuje provádět zpracování grafů pomocí Hadoopu a jde použít s Azure HDInsight.
* [Nainstalovat Solr na clusterech HDInsight](hdinsight-hadoop-solr-install-linux.md). Clusterů systému HDInsight Hadoop nainstalovat Solr pomocí přizpůsobení clusteru. Solr umožňuje provádět operace výkonné hledání na uložená data.
* [Nainstalovat jazyk R na clusterech HDInsight](hdinsight-hadoop-r-scripts-linux.md). Přizpůsobení clusteru použijte k instalaci R na clusterech HDInsight Hadoop. R je open-source jazyk a prostředí pro statistické výpočty. Nabízí stovky integrovaných statistické funkce a vlastní programovací jazyk, který kombinuje aspektů funkční a objektově orientované programování. Také nabízí rozsáhlé možnosti pro grafický.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
