---
title: Použití Grafana ve službě Azure HDInsight
description: Přečtěte si, jak získat přístup k řídicímu panelu Grafana s clustery Apache Hadoop ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: cea0e9709afb65caa23d28be093c28498f2b82d0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122980"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Přístup k Grafana ve službě Azure HDInsight

[Grafana](https://grafana.com/) je oblíbený Open Source tvůrce grafů a řídicích panelů. Grafana je funkce bohatá; neumožňuje uživatelům vytvářet přizpůsobitelné řídicí panely a sdílet je, ale také nabízí šablony/skripty řídicí panely, integraci protokolu LDAP, více zdrojů dat a další.

V současné době se v Azure HDInsight Grafana podporuje s typy clusterů HBA a interaktivních dotazů.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

V této části vytvoříte pomocí šablony Azure Resource Manager interaktivní cluster dotazů v HDInsight. Zkušenosti se šablonami Resource Manageru se k postupu podle tohoto článku nevyžadují. 

1. Klikněte na následující tlačítko **Nasadit do Azure**, přihlaste se k Azure a otevřete šablonu Resource Manageru na webu Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

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
    |**Název clusteru**     | Zadejte název clusteru Apache Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může mít až 59 znaků a může obsahovat písmena, číslice a pomlčky. První a poslední znak názvu nemůže být pomlčka. |
    |**Přihlašovací jméno a heslo clusteru**     | Výchozí přihlašovací jméno je **admin** (správce). Heslo musí mít minimálně 10 znaků a musí obsahovat alespoň jedno číslo, jedno velké písmeno, jedno malé písmeno a jeden jiný než alfanumerický znak (kromě znaků ' " ` \). **Nezadávejte** běžné heslo, jako je „Pass@word1“.|
    |**Uživatelské jméno a heslo SSH**     | Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Uživatelské jméno SSH můžete změnit.  Pro heslo uživatele SSH platí stejné požadavky jako pro přihlašovací heslo clusteru.|

    Některé vlastnosti jsou v šabloně pevně kódované.  Takové hodnoty můžete konfigurovat v šabloně. Další vysvětlení těchto vlastností najdete v tématu [Vytvoření clusterů Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a **Připnout na řídicí panel** a pak vyberte **Koupit**. Na řídicím panelu portálu by se měla zobrazit nová dlaždice s názvem **Odesílá se nasazení**. Vytvoření clusteru trvá přibližně 20 minut.

    ![Průběh Template Deployment Azure](./media/hdinsight-grafana/deployment-progress-tile.png "Průběh Template Deployment Azure")

4. Jakmile se cluster vytvoří, popis dlaždice se změní na zadaný název skupiny prostředků. Na dlaždici je uvedený také cluster HDInsight vytvořený v rámci skupiny prostředků.

    ![Počáteční skupina prostředků HDInsight Linux](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Skupina prostředků clusteru Azure HDInsight")

5. Na dlaždici je uvedené také výchozí úložiště přidružené ke clusteru. Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.
    

> [!NOTE]  
> Další metody vytváření clusterů a porozumění vlastnostem používaným v tomto článku najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Přístup k řídicímu panelu Grafana

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **clustery HDInsight**a potom vyberte název clusteru, který jste vytvořili v poslední části.

3. V části **Rychlé odkazy**klikněte na **řídicí panel clusteru**.

    ![Portál pro řídicí panel clusteru HDInsight](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "Řídicí panel clusteru HDInsight na portálu")

4. Na řídicím panelu klikněte na dlaždici **Grafana** . Případně přejděte k `/grafana/` cestě k adrese URL vašeho clusteru. Například, `https://<clustername>.azurehdinsight.net/grafana/`.

5. Zadejte přihlašovací údaje uživatele clusteru Hadoop.

6. Zobrazí se řídicí panel Grafana, který bude vypadat jako v tomto příkladu:

    ![Webový řídicí panel HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Řídicí panel HDInsight Grafana")

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete s článkem hotovi, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. 

> [!NOTE]  
> Pokud *rovnou* pokračujete k dalšímu kurzu, ve kterém se dozvíte, jak spouštět operace ETL s využitím Hadoopu ve službě HDInsight, můžete cluster nechat spuštěný. To proto, že v kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale nebudete hned pokračovat dalším kurzem, musíte teď cluster odstranit.

**Postup odstranění clusteru a/nebo výchozího účtu úložiště**

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Ikona odstranění Azure Portalho clusteru](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Odstranit cluster HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Správce prostředků a jak provádět základní dotazy Apache Hive. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Pokud chcete začít pracovat s vlastními daty a potřebujete další informace o ukládání dat službou HDInsight nebo o tom, jak data do této služby nahrát, přečtěte si následující články:

* Informace o tom, jak HDInsight používá Azure Storage, najdete v tématu [Používání Azure Storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Informace o tom, jak nahrát data do služby HDInsight, najdete v tématu [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md).

Další informace o analýze dat pomocí HDInsight najdete v následujících článcích:

* Další informace o použití podregistru se službou HDInsight, včetně postupu pro provádění dotazů na podregistry ze sady Visual Studio, najdete v tématu [použití Apache Hive se službou HDInsight](../hdinsight-use-hive.md).
* Další informace o prasečím jazyce, který slouží k transformaci dat, najdete v tématu [použití nástroje Apache prasete se službou HDInsight](../hdinsight-use-pig.md).
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
