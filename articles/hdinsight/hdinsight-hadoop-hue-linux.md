---
title: HUE s Hadoop v clusterech se systémem HDInsight Linux - Azure | Microsoft Docs
description: Postup instalace aplikace Hue clustery prostředí HDInsight a směrovat požadavky na Hue pomocí tunelové propojení. Použijte Hue procházet úložiště a spuštění Hive nebo Pig.
keywords: HUE hadoop
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 9e57fcca-e26c-479d-a745-7b80a9290447
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 829f309fa66bc548b9f74cca8112c244cc88775e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403130"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Na nainstalovat a používat Hue clusterů systému HDInsight Hadoop

Postup instalace aplikace Hue clustery prostředí HDInsight a směrovat požadavky na Hue pomocí tunelové propojení.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>Co je Hue?
HUE je sada webových aplikací používaných pro interakci s clusterem Hadoop. Můžete použít Hue procházet úložiště přidružený k clusteru Hadoop (WASB, v případě clusterů HDInsight), spouštět úlohy Hive a Pig skripty a tak dále. Následující součásti jsou k dispozici s instalací Hue na clusteru HDInsight Hadoop.

* Editor Hive včelí vosk
* Pig
* Správce Metaúložiště.
* Oozie
* FileBrowser (který komunikuje se výchozí kontejner WASB)
* Prohlížeč úloh

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomůže k izolování a vyřešení problémů týkajících se těchto součástí.
>
> Vlastní komponenty získat vyvineme podporu k pomoci při další řešení problému. To může způsobit řešení problému nebo s žádostí o zapojení dostupné kanály pro technologie s otevřeným zdrojem, kterých se nachází hluboké znalosti pro tuto technologii. Například existuje mnoho komunity webů, které lze použít jako: [fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také Apache projekty mají na projektu serverů [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalace aplikace Hue pomocí akcí skriptů

Skript tak, aby instalace aplikace Hue na clusteru HDInsight se systémem Linux je k dispozici na https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Postup instalace aplikace Hue v clusterech s Azure Storage objekty BLOB (WASB) nebo Azure Data Lake Store jako výchozí úložiště můžete použít tento skript.

Tato část obsahuje informace o tom, jak pomocí skriptu při zřizování clusteru pomocí portálu Azure.

> [!NOTE]
> Azure PowerShell, rozhraní příkazového řádku Azure, sady .NET SDK HDInsight nebo šablon Azure Resource Manageru lze také použít skript akce. Můžete taky použít skript akce již clustery spuštěná. Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Spuštění zřizování clusteru pomocí kroků v [zřizování clusterů HDInsight v Linuxu](hdinsight-hadoop-provision-linux-clusters.md), ale se nedokončí zřizování.

   > [!NOTE]
   > Postup instalace aplikace Hue v clusterech HDInsight, headnode doporučená velikost je alespoň A4 (8 jader, 14 GB paměti).
   >
   >
2. Na **volitelné konfiguraci** vyberte **akcí skriptů**a zadejte informace, jak je uvedeno níže:

    ![Zadejte parametry akce skriptu pro Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "poskytnout skripty parametry akcí pro Hue")

   * **NÁZEV**: Zadejte popisný název akce skriptu.
   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: zaškrtnete tuto možnost,
   * **PRACOVNÍ**: nechat prázdné.
   * **ZOOKEEPER**: nechat prázdné.
   * **Parametry**: nechat prázdné.
3. V dolní části **akcí skriptů**, použijte **vyberte** tlačítko Uložit konfiguraci. Nakonec použijte **vyberte** tlačítko v dolní části **volitelné konfiguraci** okno a uložte informace o volitelné konfiguraci.
4. Pokračovat zřizování clusteru, jak je popsáno v [zřizování clusterů HDInsight v Linuxu](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Použití Hue s clustery HDInsight

Tunelové připojení SSH je jedinou možností k přístupu ke Hue na clusteru, jakmile je spuštěna. Tunelové propojení pomocí protokolu SSH umožňuje přenos přejdete přímo na headnode clusteru, kde je spuštěna Hue. Po dokončení zřizování clusteru pomocí následujících kroků můžete použít Hue v clusteru HDInsight Linux.

> [!NOTE]
> Doporučujeme používat webový prohlížeč Firefox podle pokynů níže.
>
>

1. Pomocí informací v [používání tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Ambari, ResourceManager, JobHistory, NameNode, Oozie a jiným webovým uživatelským rozhraním je](hdinsight-linux-ambari-ssh-tunnel.md) k vytvoření tunelu SSH z vašeho systému klienta ke clusteru HDInsight a pak proveďte konfiguraci webového prohlížeči používat tunelového propojení SSH jako proxy server.

2. Po vytvoření tunelu SSH a nakonfigurovaný prohlížeč tak, aby provoz proxy jeho prostřednictvím, musíte vyhledat název hostitele primární hlavního uzlu. Můžete to provést prostřednictvím připojení ke clusteru pomocí protokolu SSH na port 22. Například `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` kde **uživatelské jméno** se svým uživatelským jménem SSH a **CLUSTERNAME** je název clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po připojení, použijte následující příkaz k získání plně kvalifikovaný název domény primární headnode:

        hostname -f

    Název pro tento postup vrátí podobný následujícímu:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Toto je název hostitele primární headnode, kde se nachází na webu Hue.
4. Pomocí prohlížeče otevření portál Hue v http://HOSTNAME:8888. Název hostitele nahraďte název, který jste získali v předchozím kroku.

   > [!NOTE]
   > Pokud se přihlašujete poprvé, vyzve k vytvoření účtu k přihlášení na portál Hue. Přihlašovací údaje, který zde určíte bude omezena na portál a nesouvisí se správce nebo přihlašovací údaje uživatele SSH, které jste zadali při zřizování clusteru.
   >
   >

    ![Přihlášení na portál Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "zadejte přihlašovací údaje pro portál Hue")

### <a name="run-a-hive-query"></a>Spuštění dotazu Hive
1. Z portálu Hue, klikněte na tlačítko **dotazu editory**a potom klikněte na **Hive** otevřete Hive editor.

    ![Použijte Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "používání Hive")
2. Na **pomůže** v části **databáze**, měli byste vidět **hivesampletable**. Toto je ukázkový tabulku, která je součástí všech clusterů systému Hadoop v HDInsight. V pravém podokně zadejte ukázkový dotaz a zobrazí se výstup na **výsledky** klikněte v podokně pod, jak je znázorněno na snímku obrazovky.

    ![Spuštění dotazu Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "spuštění Hive dotaz")

    Můžete také **grafu** karty zobrazíte vizuální reprezentace výsledku.

### <a name="browse-the-cluster-storage"></a>Procházet úložiště clusteru
1. Z portálu Hue, klikněte na tlačítko **prohlížeč souborů** v pravém horním rohu panelu nabídek.
2. Ve výchozím nastavení otevře prohlížeč souborů na **/uživatel/myuser** adresáře. Klikněte na tlačítko lomítkem bezprostředně před adresáře uživatelů v cestě k přejít na kořenovém kontejneru úložiště Azure přidruženého k clusteru.

    ![Prohlížeč souborů použijte](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "použijte prohlížeč souborů")
3. Klikněte pravým tlačítkem myši na soubor nebo složku, kterou chcete zobrazit dostupné operace. Použití **nahrát** tlačítko v pravém rohu k nahrání souborů do aktuální adresář. Použití **nový** tlačítko pro vytvoření nových souborů nebo adresářů.

> [!NOTE]
> Prohlížeč souborů Hue lze zobrazit pouze obsah výchozí kontejner, který je přidružen ke clusteru HDInsight. Žádné další úložiště účtů nebo kontejnery, které vám mohou být přidruženy s clusterem nebudou přístupné pomocí prohlížeče souboru. Však další kontejnery, které jsou přidruženy ke clusteru bude vždy dostupné pro úlohy Hive. Například pokud zadáte příkaz `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` v editoru Hive můžete zobrazit obsah také další kontejnery. V tomto příkazu **newcontainer** není výchozí kontejner spojené s clusterem.
>
>

## <a name="important-considerations"></a>Důležité informace
1. Skript použitý k instalaci Hue ho nainstaluje jenom na primární headnode clusteru.

2. Během instalace jsou pro aktualizaci konfigurace restartovat více služby Hadoop (HDFS, YARN, MR2, Oozie). Po dokončení instalace Hue skript může trvat nějakou dobu pro jiné služby Hadoop, aby bylo možné spustit. Může to mít vliv na Hue výkonu původně. Po spuštění všech služeb, bude Hue plně funkční.
3. HUE nerozumí úlohách Tez, což je aktuální výchozí nastavení pro Hive. Pokud chcete použít jako spouštěcí modul Hive MapReduce, aktualizujte skript tak, aby ve vašem skriptu použijte následující příkaz:

        set hive.execution.engine=mr;

4. U clusterů systému Linux můžete mít scénář, kde vaše služby spuštěné na primární headnode při Resource Manager může běžet na sekundární. Takové situaci může způsobit chyby (viz následující obrázek) při použití Hue k zobrazení podrobností úloh SPUŠTĚNA v clusteru. Po dokončení úlohy však můžete zobrazit podrobnosti úlohy.

   ![Chyba portál Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "chyba portál Hue")

   To je kvůli známému problému. Jako alternativní řešení upravte, aby byl aktivní Resource Manager je spuštěna také na primární headnode Ambari.
5. HUE rozumí WebHDFS i clusterů HDInsight pomocí Azure Storage pomocí `wasb://`. Vlastní skript použít s akce skriptu tedy nainstaluje WebWasb, který je kompatibilní s webhdfs, které služba pro rozhovoru s WASB. Ano, i když portál Hue uvádí HDFS místech (například když přesunutím ukazatele myši nad **prohlížeč souborů**), by měl být interpretován jako WASB.

## <a name="next-steps"></a>Další postup
* [Nainstalujte Giraph clustery HDInsight](hdinsight-hadoop-giraph-install-linux.md). Přizpůsobení clusteru použijte k instalaci Giraph clusterů systému HDInsight Hadoop. Giraph umožňuje provádět zpracování grafu pomocí Hadoop a dá použít s Azure HDInsight.
* [Nainstalujte Solr clustery HDInsight](hdinsight-hadoop-solr-install-linux.md). Přizpůsobení clusteru použijte k instalaci Solr clusterů systému HDInsight Hadoop. Solr umožňuje provádět operace výkonné hledání na uložená data.
* [Nainstalujte jazyk R v clusterech HDInsight](hdinsight-hadoop-r-scripts-linux.md). Přizpůsobení clusteru použijte k instalaci R clusterů systému HDInsight Hadoop. R je open-source jazyk a prostředí pro statistické výpočty. Poskytuje stovky statistické funkce předdefinované a vlastní programovací jazyk, který kombinuje aspektů funkčnosti a objektově orientované programování. Také poskytuje rozsáhlé možnosti grafického rozhraní.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
