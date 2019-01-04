---
title: Použití Grafana v Azure HDInsight
description: Zjistěte, jak získat přístup k Grafana v Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 7f33742fc5e765aa5ab0c66d13e844b3be2bab9e
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599872"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Grafana přístupu v Azure HDInsight


[Grafana](https://grafana.com/) je Oblíbené, open source grafů a řídicích panelů Tvůrce. Grafana je funkce bohaté; nejen nemá umožníte uživatelům vytvářet přizpůsobitelný a ke sdílení řídicích panelů, nabízí také bez vizuálního vzhledu/skripty řídicí panely, integrace protokolu LDAP, více zdrojů dat a další.

V současné době v Azure HDInsight, Grafana podporuje typy clusterů Hbase a Interactive Query.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvořit cluster Apache Hadoop

V této části vytvoříte cluster Interactive Query v HDInsight pomocí šablony Azure Resource Manageru. Zkušenosti se šablonami Resource Manageru se k postupu podle tohoto článku nevyžadují. 

1. Klikněte na následující tlačítko **Nasadit do Azure**, přihlaste se k Azure a otevřete šablonu Resource Manageru na webu Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Zadejte nebo vyberte hodnoty navržené na následujícím snímku obrazovky:

    > [!NOTE]  
    > Zadané hodnoty musí být jedinečné a měly by splňovat pokyny pro pojmenování. Šablona neprovádí ověřovací kontroly. Pokud se zadané hodnoty již používají nebo pokud nesplňují příslušné pokyny, po odeslání šablony se zobrazí chyba.       
    > 
    >
    
    ![Šablona Resource Manageru pro zahájení práce s HDInsight Linux na portálu](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Nasazení clusteru Hadoop ve službě HDInsight pomocí webu Azure Portal a šablony správce skupin prostředků")

    Zadejte nebo vyberte tyto hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Předplatné**     |  Vyberte své předplatné Azure. |
    |**Skupina prostředků**     | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |**Umístění**     | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |
    |**Typ clusteru**     | Vyberte **hadoop**. |
    |**Název clusteru**     | Zadejte název pro cluster Apache Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může mít až 59 znaků a může obsahovat písmena, číslice a pomlčky. První a poslední znak názvu nemůže být pomlčka. |
    |**Přihlašovací jméno a heslo clusteru**     | Výchozí přihlašovací jméno je **admin** (správce). Heslo musí mít minimálně 10 znaků a musí obsahovat alespoň jedno číslo, jedno velké písmeno, jedno malé písmeno a jeden jiný než alfanumerický znak (kromě znaků ' " ` \). **Nezadávejte** běžné heslo, jako je „Pass@word1“.|
    |**Uživatelské jméno a heslo SSH**     | Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Uživatelské jméno SSH můžete změnit.  Pro heslo uživatele SSH platí stejné požadavky jako pro přihlašovací heslo clusteru.|
       
    Některé vlastnosti jsou v šabloně pevně kódované.  Takové hodnoty můžete konfigurovat v šabloně. Další vysvětlení těchto vlastností najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a **Připnout na řídicí panel** a pak vyberte **Koupit**. Na řídicím panelu portálu by se měla zobrazit nová dlaždice s názvem **Odesílá se nasazení**. Vytvoření clusteru trvá přibližně 20 minut.

    ![Průběh nasazení šablony](./media/hdinsight-grafana/deployment-progress-tile.png "Průběh nasazení šablony Azure")

4. Jakmile se cluster vytvoří, popis dlaždice se změní na zadaný název skupiny prostředků. Na dlaždici je uvedený také cluster HDInsight vytvořený v rámci skupiny prostředků. 
   
    ![Počáteční skupina prostředků HDInsight Linux](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Skupina prostředků clusteru Azure HDInsight")
    
5. Na dlaždici je uvedené také výchozí úložiště přidružené ke clusteru. Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. HDInsight cluster a jeho výchozí účet úložiště, musí být umístěny společně ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.
    

> [!NOTE]  
> Další metody vytváření clusterů a principy vlastnosti používaných v tomto kurzu najdete v části [Vytváření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Přístup k řídicím panelem Grafana

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **clustery HDInsight**a pak vyberte název clusteru, který jste vytvořili v předchozí části.

3. V části **rychlé odkazy**, klikněte na tlačítko **řídicí panel clusteru**.

    ![Portál řídicí panel clusteru HDInsight](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "řídicí panel clusteru HDInsight na portálu")

4. Z řídicího panelu, klikněte na tlačítko **Grafana** dlaždici. Alternativně přejděte `/grafana/` cestu adresy URL vašeho clusteru. Například, `https://<clustername>.azurehdinsight.net/grafana/`.

5. Zadejte přihlašovací údaje clusteru Hadoop.

6. Řídicím panelem Grafana se zobrazí a bude vypadat jako v tomto příkladu:

    ![Řídicím panelem HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "řídicím panelem HDInsight Grafana")

   

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete s článkem hotovi, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. 

> [!NOTE]  
> Pokud *rovnou* pokračujete k dalšímu kurzu, ve kterém se dozvíte, jak spouštět operace ETL s využitím Hadoopu ve službě HDInsight, můžete cluster nechat spuštěný. To proto, že v kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale nebudete hned pokračovat dalším kurzem, musíte teď cluster odstranit.

**Postup odstranění clusteru a/nebo výchozího účtu úložiště**

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Odstranění clusteru HDInsight](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Odstranění clusteru HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Resource Manageru a jak provádět základní dotazy Apache Hive. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)

Pokud chcete začít pracovat s vlastními daty a potřebujete další informace o ukládání dat službou HDInsight nebo o tom, jak data do této služby nahrát, přečtěte si následující články:

* Informace o tom, jak HDInsight používá Azure Storage, najdete v tématu [Používání Azure Storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Informace o tom, jak nahrát data do služby HDInsight, najdete v tématu [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md).

Další informace o analýze dat pomocí HDInsight najdete v následujících článcích:

* Další informace o používání Hivu se službou HDInsight, včetně postupu provádění dotazů Hivu ze sady Visual Studio, naleznete v tématu [použití Apache Hivu se službou HDInsight](../hdinsight-use-hive.md).
* Další informace o jazyce používaném k transformaci dat, Pig najdete [použití Apache Pig s HDInsight](../hdinsight-use-pig.md).
* Další informace o MapReduce, způsobu psaní programů, které zpracovávají data v Hadoopu, najdete v tématu [Použití MapReduce se službou HDInsight](../hdinsight-use-mapreduce.md).
* Další informace o použití nástrojů HDInsight pro Visual Studio k analýze dat na HDInsight najdete v části [Začněte používat nástroje Visual Studio Hadoop pro HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Pokud potřebujete další informace o vytváření a správě clusteru HDInsight, přečtěte si následující články:

* Další informace o správě clusteru HDInsight se systémem Linux najdete v části [Správa clusterů HDInsight pomocí Ambari](../hdinsight-hadoop-manage-ambari.md).
* Další informace o možnostech, které můžete vybrat při vytváření clusteru služby HDInsight, najdete v tématu [Vytváření HDInsight na Linuxu pomocí vlastních možností](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
