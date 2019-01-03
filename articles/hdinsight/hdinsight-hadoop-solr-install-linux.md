---
title: Použití akce skriptu k Linuxovým systémem HDInsight – Azure nainstalovat Solr
description: Zjistěte, jak nainstalovat Solr na clusterech Hadoop využívající systém Linux HDInsight pomocí skriptových akcí.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: e52a7cbb5c81f779ac4d12d2f3e5fd1e4ae62852
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714011"
---
# <a name="install-and-use-apache-solr-on-hdinsight-hadoop-clusters"></a>Nainstalovat a používat Apache Solr na clusterech HDInsight Hadoop

Zjistěte, jak nainstalovat Apache Solr Azure HDInsight pomocí skriptových akcí. Solr je platforma pro výkonné hledání a poskytuje funkce vyhledávání na podnikové úrovni s daty spravovanými Hadoopem.

> [!IMPORTANT]  
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]  
> Ukázkový skript v tomto dokumentu instaluje Solr 4.9 s určitou konfiguraci. Pokud chcete konfigurovat Solr cluster pomocí různých kolekcí, horizontální oddíly, schémata, repliky a podobně, je třeba upravit skript a Solr binární soubory.

## <a name="whatis"></a>Co je Solr

[Apache Solr](https://lucene.apache.org/solr/features.html) je platforma pro podnikové vyhledávání, která umožňuje výkonné fulltextové vyhledávání na datech. Hadoop umožňuje ukládání a správu obrovských objemů dat, Apache Solr poskytuje funkce vyhledávání rychle načíst data.

> [!WARNING]   
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporovány společností Microsoft.
>
> Vlastní komponenty, jako je například Solr, přijímat obchodně přiměřenou podporu můžete-li dále řešit tento problém. Podpora společnosti Microsoft nemusí být schopni vyřešit problémy s vlastními komponentami. Budete muset zapojit opensourcové komunity žádostí o pomoc. Existuje například mnoho komunitním webům, které lze použít jako: [Fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Také projektů Apache mít projektovým webům na [ https://apache.org ](https://apache.org), například: [Hadoop](https://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Co dělá skriptu

Tento skript provede tyto změny do clusteru HDInsight:

* Nainstaluje Solr 4.9 do `/usr/hdp/current/solr`
* Vytvoří uživatele, **solrusr**, který se používá ke spouštění služby Solr
* Nastaví **solruser** jako vlastníka `/usr/hdp/current/solr`
* Přidá [Upstart](http://upstart.ubuntu.com/) konfigurace, který se automaticky spustí Solr.

## <a name="install"></a>Nainstalovat Solr pomocí akcí skriptů

Ukázkový skript do clusteru služby HDInsight nainstalovat Solr je k dispozici v následujícím umístění:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Pokud chcete vytvořit cluster, který se má nainstalovat Solr, použijte postup v [clusterů HDInsight vytvořit](hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu. Během procesu vytváření nainstalovat Solr pomocí následujících kroků:

1. Z __souhrn clusteru__ oddílu, select__Advanced settings__, pak __akcí skriptů__. K naplnění formuláře použijte následující informace:

   * **NÁZEV**: Zadejte popisný název akce skriptu.
   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HLAVNÍ**: Zaškrtněte tuto možnost
   * **PRACOVNÍK**: Zaškrtněte tuto možnost
   * **ZOOKEEPER**: Zaškrtněte tuto možnost k instalaci na uzlu Zookeeper
   * **PARAMETRY**: Toto pole nechat prázdné

2. V dolní části **akcí skriptů** části **vyberte** tlačítko, čímž konfiguraci uložíte. Nakonec použijte **Další** se vrátit na __souhrn clusteru__

3. Z __souhrn clusteru__ stránce __vytvořit__ k vytvoření clusteru.

## <a name="usesolr"></a>Použití Solr v HDInsight

> [!IMPORTANT]  
> Kroky v této části ukazují základní funkce Solr. Další informace o použití Solr, najdete v článku [webu Apache Solr](https://lucene.apache.org/solr/).

### <a name="index-data"></a>Indexování dat

Pomocí následujícího postupu můžete přidat do Solr příkladu a pak ji dotazujte:

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    > [!NOTE]  
    > Nahraďte `sshuser` s uživatele SSH pro cluster. Nahraďte `clustername` s názvem clusteru.

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]  
     > Kroky dále v tomto dokumentu používají tunelového propojení SSH pro připojení k webovým Uživatelským rozhraním Solr. Pokud chcete použít tyto kroky, musíte vytvořit tunel SSH a pak nakonfigurujte prohlížeč tak, že ho použít.
     >
     > Další informace najdete v tématu [použití tunelování SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

2. Aby Solr index ukázková data použijte následující příkazy:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    Následující výstup se vrátí do konzoly:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    `post.jar` Přidá nástroj **solr.xml** a **monitor.xml** dokumentů do indexu.
  
3. K dotazování do rozhraní API REST Solr, použijte následující příkaz:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Tento příkaz vyhledá **collection1** pro všechny dokumenty odpovídající **\*:\*** (kódovaný jako \*% 3A\* v řetězci dotazu). Následující dokument JSON je příklad odpovědi:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Na řídicím panelu Solr

Řídicí panel Solr je webové uživatelské rozhraní, které umožňuje pracovat s Solr prostřednictvím webového prohlížeče. Řídicí panel Solr nezveřejňují přímo na Internetu z vašeho clusteru HDInsight. Tunelu SSH můžete použít k přístupu. Další informace o používání tunelu SSH najdete v článku [použití tunelování SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Po vytvoření tunelu SSH pomocí řídicího panelu Solr pomocí následujících kroků:

1. Určení názvu hostitele k primárnímu hlavnímu uzlu:

   1. Pomocí SSH se připojte k hlavnímu uzlu clusteru. Například, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Další informace o používání SSH najdete v tématu [použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Chcete-li získat plně kvalifikovaný název hostitele, použijte následující příkaz:

        ```bash
        hostname -f
        ```

        Tento příkaz vrátí hodnotu podobně jako následující název hostitele:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Hodnota vrácená, uložte, protože se později používá.

2. V prohlížeči se připojte k **http://HOSTNAME:8983/solr/#/**, kde **HOSTNAME** je název, který jste nadefinovali v předchozím kroku.

    Žádost je směrován přes tunel SSH do Solr webového uživatelského rozhraní ve vašem clusteru. Podobně jako na následujícím obrázku se zobrazí na stránce:

    ![Obrázek řídicího panelu Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. V levém podokně, použijte **Core selektor** rozevíracího seznamu vyberte **collection1**. Několik položek by se měla je zobrazit pod **collection1**.

4. V níže uvedené položky **collection1**vyberte **dotazu**. K naplnění vyhledávací stránku, použijte následující hodnoty:

   * V **q** textové pole, zadejte  **\*:**\*. Tento dotaz vrátí všechny dokumenty, která jsou indexována Solr. Pokud chcete vyhledat řetězec specifický v rámci dokumentů, můžete zadat Tento řetězec tady.
   * V **wt** textové pole, vyberte formát výstupu. Výchozí hodnota je **json**.

     Nakonec vyberte **provést dotaz** tlačítko v dolní části vložte vyhledávání.

     ![Přizpůsobení clusteru pomocí skriptových akcí](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     Výstup vrací dva dokumenty, které jste přidali dříve do indexu. Výstup se podobá následující dokument JSON:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Spuštění a zastavení Solr

Ručně zastavit a spustit Solr, použijte následující příkazy:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Zálohování indexovaná data

Použijte následující kroky pro zálohování Solr data do výchozího úložiště pro cluster:

1. Připojte se ke clusteru pomocí SSH a pak následujícím příkazem pro získání názvu hostitele pro hlavní uzel:

    ```bash
    hostname -f
    ```

2. Použijte následující příkaz pro vytvoření snímku indexovaná data. Nahraďte **HOSTNAME** s názvem vrácenou předchozím příkazem:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    Odpověď se podobá následující kód XML:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Přejděte do adresáře `/usr/hdp/current/solr/example/solr`. Je podadresář zde pro každou kolekci. Každý adresář kolekce obsahuje `data` adresáře, který obsahuje snímek pro kolekci.

4. Pokud chcete vytvořit komprimovaný archiv ze složky snímků, použijte následující příkaz:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Nahradit `snapshot.20150806185338855` hodnoty s názvem snímku pro kolekci.

    Tento příkaz vytvoří s názvem Archiv **snapshot.20150806185338855.tgz**, který obsahuje obsah **snapshot.20150806185338855** adresáře.

5. Pak můžete uložit archiv do primárního úložiště clusteru pomocí následujícího příkazu:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Další informace o práci s Apache Solr zálohování a obnovení najdete v tématu [ https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups ](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Další postup

* [Clustery HDInsight nainstalovat Apache Giraph](hdinsight-hadoop-giraph-install-linux.md). Clustery HDInsight Hadoop nainstalovat Giraph pomocí přizpůsobení clusteru. Giraph umožňuje provádět zpracování s použitím Hadoopu grafů a jde použít s Azure HDInsight.

* [Instalace aplikace Hue v clusterech HDInsight](hdinsight-hadoop-hue-linux.md). Přizpůsobení clusteru použijte postup instalace aplikace Hue v clusterech HDInsight Hadoop. Odstín, který je sada webových aplikací používaných pro interakci s clusterem Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
