---
title: "Začínáme používat Hadoop a Hive v Azure HDInsight | Dokumentace Microsoftu"
description: "Naučte se vytvářet clustery HDInsight a dotazy na data pomocí Hive."
keywords: "začínáme používat hadoop, hadoop linux, hadoop rychlý start, hive začínáme, hive rychlý start"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 11c0a583ab671b27a5c5a65c4bb36032a8582466
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Kurz Hadoopu: Začínáme používat Hadoop v HDInsight

Naučte se vytvářet clustery [Hadoop](http://hadoop.apache.org/) v HDInsight a spouštět úlohy Hive v HDInsight. [Hive Apache](https://hive.apache.org/) je nejoblíbenější součástí ekosystému Hadoop. Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných součásti v HDInsight naleznete v tématu [Co je nového ve verzích clusterů Hadoop poskytovaných v HDInsight?](../hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Požadavky
Než začnete tento kurz, musíte mít:

* **Předplatné Azure**: pro vytvoření bezplatného zkušebního účet na jeden měsíc otevřete web [azure.microsoft.com/free](https://azure.microsoft.com/free).

## <a name="create-cluster"></a>Vytvoření clusteru

Většina úloh Hadoop jsou dávkové úlohy. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V této části vytvoříte cluster Hadoop ve službě HDInsight pomocí [šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-template-deploy.md). Zkušenosti s šablonou Resource Manageru nejsou pro postup dle tohoto kurzu vyžadovány. Další metody vytváření clusterů a principy vlastnosti používaných v tomto kurzu, naleznete v části [Vytváření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

Šablona Resource Manageru použitá v tomto kurzu je umístěná v [GitHubu](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Klikněte na následující obrázek pro přihlášení do Azure a otevřete šablonu Resource Manageru na webu Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Zadejte nebo vyberte tyto hodnoty:

    * **Předplatné**: Vyberte své předplatné Azure.
    * **Skupina prostředků**: Vytvořte skupinu prostředků, nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. 
    * **Umístění**: Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. 
    * **Typ clusteru**: Pro účely tohoto kurzu vyberte **hadoop**.
    * **Název clusteru:** Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může mít až 59 znaků a může obsahovat písmena, číslice a pomlčky. První a poslední znak ale nemůže být pomlčka.
    * **Přihlašovací jméno a heslo clusteru**: výchozí přihlašovací jméno je **admin**. Heslo musí mít minimálně 10 znaků a musí obsahovat alespoň jedno číslo, jedno velké písmeno, jedno malé písmeno a jeden jiný než alfanumerický znak (kromě znaků ' " ` \). 
    * **Uživatelské jméno a heslo SSH**: výchozí uživatelské jméno **sshuser**.  Uživatelské jméno SSH můžete změnit.  Pro heslo uživatele SSH platí stejné požadavky jako pro přihlašovací heslo clusteru.   
   
    ![Šablona Resource Manageru pro zahájení práce s HDInsight Linux na portálu](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Nasazení clusteru Hadoop ve službě HDInsight pomocí portálu Azure Portal a šablony správce skupin prostředků").
     
    Některé vlastnosti jsou v šabloně pevně kódované.  Takové hodnoty můžete konfigurovat v šabloně.
    
    Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být umístěny společně a nacházet se ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště. 
        
    Podrobnější vysvětlení těchto vlastností naleznete v tématu [Vytváření clusterů Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a **Připnout na řídicí panel** a pak klikněte na **Koupit**. Na řídicím panelu portálu by se měla zobrazit nová dlaždice s názvem **Nasazení šablony**. Vytvoření clusteru trvá přibližně 20 minut. 

4. Jakmile je cluster vytvořen, popis dlaždice se změní na zadaný název skupiny prostředků. Kliknutím na dlaždici zobrazíte cluster i výchozí úložiště pro uvedený cluster.
   
    ![Počáteční skupina prostředků HDInsight Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Skupina prostředků clusteru Azure HDInsight")

4. Cluster otevřete tak, že kliknete na jeho název.

   ![Počáteční nastavení clusteru HDInsight Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Vlastnosti clusteru HDInsight")


## <a name="run-hive-queries"></a>Spuštění dotazů Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější součástí používanou v HDInsight. Existuje mnoho způsobů spouštění úloh Hive v HDInsight. V tomto kurzu použijete zobrazení Ambari Hive z portálu. Další metody pro odesílání úloh Hive naleznete v části [Použití Hive v HDInsight](hdinsight-use-hive.md).

1. Pokud chcete otevřít Ambari, klikněte na **Řídicí panel clusteru** uvedený na předchozím snímku obrazovky a pak klikněte na **Řídicí panel clusteru HDInsight**.  Můžete také přejít na adresu **https://&lt;název_clusteru>.azurehdinsight.net**, kde &lt;název_clusteru> je název clusteru vytvořeného v předchozí části.

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno **admin**.

3. Otevřete **Zobrazení Hive**, jak je znázorněno na následujícím snímku obrazovky:
   
    ![Výběr zobrazení Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Nabídka zobrazení Hive služby HDInsight")

4. Na kartě **DOTAZ** vložte následující příkazy HiveQL do pracovního listu:
   
        SHOW TABLES;

    ![Zobrazení Hive služby HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor dotazů zobrazení Hive služby HDInsight")
   
   > [!NOTE]
   > Hive vyžaduje středník.       
   > 
   > 

5. Klikněte na tlačítko **Spustit**. Karta **VÝSLEDKY** se zobrazí pod kartou **DOTAZ** a zobrazí informace o úloze. 
   
    Po dokončení dotazu se na kartě **DOTAZ** zobrazí výsledky operace. Zobrazí jedna tabulka s názvem **hivesampletable**. Tato vzorová tabulka Hive obsahuje všechny clustery HDInsight.
   
    ![Zobrazení Hive služby HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor dotazů zobrazení Hive služby HDInsight")

6. Opakujte kroky 4 a 5 a spusťte následující dotaz:
   
        SELECT * FROM hivesampletable;
   
7. Výsledky dotazu můžete také uložit. Klikněte na tlačítko nabídky na pravé straně a určete, zda chcete stáhnout výsledky jako soubor CSV, nebo je uložit do účtu úložiště přidruženého clusteru.

    ![Uložení výsledku dotazu Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Uložení výsledku dotazu Hive")



Po dokončení úlohy Hive můžete [Exportovat výsledky do databáze Azure SQL nebo databáze systému SQL Server](apache-hadoop-use-sqoop-mac-linux.md), můžete také [zobrazit výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o používání Hive v HDInsight naleznete v části [Použití Hive a HiveQL s Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Vyčistěte kurz
Po dokončení kurzu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. 

> [!NOTE]
> Pomocí [Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md) můžete s HDInsight vytvářet clustery na vyžádání a nakonfigurovat nastavení TimeToLive na automatické mazání clusterů. 
> 
> 

**Postup odstranění clusteru a/nebo výchozího účtu úložiště**

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit pouze cluster, ale zachovat výchozí účet úložiště, můžete kliknout na **Odstranit**:

    ![Odstranění clusteru HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Odstranění clusteru HDInsight")
1. Na pravé straně klikněte na název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.
2. Pokud chcete odstranit skupinu prostředků, která obsahuje cluster a výchozí účet úložiště, klikněte na **Odstranit skupinu prostředků**. Můžete také kliknout na název clusteru na dlaždici **Prostředky** a pak kliknout na **Odstranit**. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Resource Manageru, a jak provádět základní dotazy Hive.

Další informace o analýze dat pomocí HDInsight naleznete v následujících článcích:

* Další informace o používání Hivu se službou HDInsight, včetně postupu provádění dotazů Hivu ze sady Visual Studio, najdete v tématu [Použití Hivu se službou HDInsight](hdinsight-use-hive.md).
* Další informace o jazyce Pig používaném k transformaci dat najdete v tématu [Použití Pigu se službou HDInsight](hdinsight-use-pig.md).
* Další informace o MapReduce, způsobu psaní programů, které zpracovávají data v Hadoopu, najdete v tématu [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md).
* Další informace o použití nástrojů HDInsight pro Visual Studio k analýze dat na HDInsight naleznete v části [Začněte používat nástroje Visual Studio Hadoop pro HDInsight](apache-hadoop-visual-studio-tools-get-started.md).

Pokud chcete začít pracovat s vlastními daty a potřebujete další informace o ukládání dat službou HDInsight nebo o tom, jak data do této služby nahrát, přečtěte si následující články:

* Informace o tom, jak HDInsight používá Azure Storage, najdete v tématu [Používání Azure Storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Informace o tom, jak nahrát data do služby HDInsight, najdete v tématu [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md).

Pokud potřebujete další informace o vytváření a správě clusteru HDInsight, přečtěte si následující články:

* Další informace o správě clusteru HDInsight se systémem Linux naleznete v části [Správa clusterů HDInsight pomocí Ambari](../hdinsight-hadoop-manage-ambari.md).
* Další informace o možnostech, které můžete vybrat při vytváření clusteru služby HDInsight, naleznete v tématu [Vytváření HDInsight na Linuxu pomocí vlastních možností](../hdinsight-hadoop-provision-linux-clusters.md).
* Je-li obeznámeni s Linux a Hadoop, ale chcete znát podrobnosti o Hadoop na HDInsight, prostudujte si část [Práce s HDInsight v systému Linux](../hdinsight-hadoop-linux-information.md). Tento článek obsahuje informace o:
  
  * Adresách URL služeb hostovaných v clusteru, například Ambari a WebHCat
  * Umístění souborů Hadoop a příkladech v místním systému souborů
  * Používání Azure Storage (WASB) namísto HDFS jako výchozího datového úložiště

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


