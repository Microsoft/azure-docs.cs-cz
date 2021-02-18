---
title: Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal
description: Naučte se vytvářet a spravovat clustery Azure HDInsight pomocí Azure Portal.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: e21361b6d491f53f41754831d2bdf896ef2719db
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091426"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Pomocí [Azure Portal](https://portal.azure.com)můžete spravovat clustery [Apache Hadoop](https://hadoop.apache.org/) ve službě Azure HDInsight. Pro informace o správě clusterů Hadoop ve službě HDInsight pomocí jiných nástrojů použijte selektor karet výše.

## <a name="prerequisites"></a>Požadavky

Existující cluster Apache Hadoop v HDInsight.  Další informace najdete v tématu [Vytvoření clusterů se systémem Linux v HDInsight pomocí Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Začínáme

Přihlaste se k webu [https://portal.azure.com](https://portal.azure.com).

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> Výpis a zobrazení clusterů

Na stránce **clustery HDInsight** se zobrazí seznam stávajících clusterů.  Z portálu:
1. V nabídce vlevo vyberte **všechny služby** .
2. V části **Analýza** vyberte **clustery HDInsight** .

## <a name="cluster-home-page"></a><a name="homePage"></a> Domovská stránka clusteru

Vyberte název vašeho clusteru ze stránky [**clustery HDInsight**](#showClusters) .  Tím otevřete zobrazení **přehledu** , které bude vypadat podobně jako na následujícím obrázku:

![Základy clusteru HDInsight Azure Portal](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Horní nabídka:**  

| Položka| Popis |
|---|---|
|Přesunout|Přesune cluster do jiné skupiny prostředků nebo do jiného předplatného.|
|Odstranit|Odstraní cluster. |
|Aktualizovat|Aktualizuje zobrazení.|

**Levá nabídka:**  

  - **Levá horní nabídka**

    | Položka| Popis |
    |---|---|
    |Přehled|Poskytuje obecné informace o clusteru.|
    |Protokol aktivit|Zobrazení a dotazování protokolů aktivit.|
    |Řízení přístupu (IAM)|Použijte přiřazení rolí.  Informace najdete v tématu [přiřazení rolí Azure ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).|
    |Značky|Umožňuje nastavit páry klíč/hodnota pro definování vlastní taxonomie vašich cloudových služeb. Můžete například vytvořit klíč s názvem **Project** a potom použít společnou hodnotu pro všechny služby přidružené ke konkrétnímu projektu.|
    |Diagnostikovat a řešit problémy|Zobrazit informace o řešení potíží.|
    |Rychlé zprovoznění|Zobrazuje informace, které vám pomůžou začít používat HDInsight.|
    |nástroje|Informace o nápovědě k nástrojům souvisejícím s HDInsight|

  - **Nabídka Nastavení**  

    | Položka| Popis |
    |---|---|
    |Velikost clusteru|Kontrolovat, zvyšovat a snižovat počet pracovních uzlů clusteru. Viz [škálování clusterů](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Omezení kvót|Zobrazí použitá a dostupná jádra pro vaše předplatné.|
    |SSH + přihlášení k clusteru|Zobrazuje pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Nakonfigurujte přístup Data Lake Storage Gen1.  Viz [rychlý Start: nastavení clusterů v HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).|
    |Účty úložiště|Zobrazení účtů úložiště a klíčů. Účty úložiště se konfigurují během procesu vytváření clusteru.|
    |Aplikace|Přidat nebo odebrat aplikace HDInsight.  Viz [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Akce skriptů|Spusťte skripty bash v clusteru. Viz [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).|
    |Externí metaúložiště|Zobrazte [Apache Hive](https://hive.apache.org/) a [Apache Oozie](https://oozie.apache.org/) metaúložiště. Metaúložiště se dá nakonfigurovat jenom během procesu vytváření clusteru.|
    |Partner HDInsight|Přidat nebo odebrat aktuálního partnera HDInsight.|
    |Vlastnosti|Zobrazení [vlastností clusteru](#properties).|
    |Zámky|Přidejte zámek, abyste zabránili úpravám nebo odstranění clusteru.|
    |Export šablony|Zobrazte a exportujte šablonu Azure Resource Manager pro cluster. V současné době můžete exportovat jenom závislý účet úložiště Azure. Další informace najdete [v tématu Vytvoření clusterů Apache Hadoop se systémem Linux v HDInsight pomocí šablon Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Nabídka monitorování**

    | Položka| Popis |
    |---|---|
    |Výstrahy|Spravujte výstrahy a akce.|
    |Metriky|Monitorujte metriky clusteru v protokolech Azure Monitor.|
    |Nastavení diagnostiky|Nastavení, kde se mají ukládat metriky diagnostiky.|
    |Azure Monitor|Monitorujte svůj cluster v Azure Monitor.|

  - **Nabídka podpora a řešení potíží**

    | Položka| Popis |
    |---|---|
    |Stav prostředků|Podívejte se na téma [Přehled Azure Resource Health](../service-health/resource-health-overview.md).|
    |Nová žádost o podporu|Umožňuje vytvořit lístek podpory s podporou Microsoftu.|

## <a name="cluster-properties"></a><a name="properties"></a> Vlastnosti clusteru

Na [domovské stránce clusteru](#homePage)v části **Nastavení** vyberte **vlastnosti**.

|Položka | Popis |
|---|---|
|NÁZEV hostitele|Název clusteru|
|ADRESA URL CLUSTERU|Adresa URL webového rozhraní Ambari|
|Privátní koncový bod|Privátní koncový bod pro cluster.|
|Secure Shell (SSH)|Uživatelské jméno a název hostitele, který se má použít při přístupu ke clusteru přes SSH.|
|STAV|Jedna z těchto funkcí: přerušeno, přijato, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, provozní, spuštěno, chyba, odstranění, odstranění, vypršení časového limitu, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued nebo ClusterCustomization.|
|REGION|Umístění Azure. Seznam podporovaných umístění Azure najdete v rozevíracím seznamu **oblast** na stránce [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATUM VYTVOŘENÍ|Datum, kdy byl cluster nasazen.|
|OPERAČNÍ SYSTÉM|Buď **Windows** , nebo **Linux**.|
|TYP|Hadoop, HBA, vyplavení, Spark.|
|Verze|Viz [verze HDInsight](hdinsight-component-versioning.md).|
|Minimální verze protokolu TLS|Verze TLS.|
|PŘEDPLATNÉ|Název předplatného|
|VÝCHOZÍ ZDROJ DAT|Výchozí systém souborů clusteru.|
|Velikosti uzlů pracovních procesů|Vybraná velikost virtuálního počítače pracovních uzlů.|
|Head node size|Vybraná velikost virtuálního počítače pro hlavní uzly.|
|Virtuální síť|Název Virtual Network, který cluster nasazen, pokud byl v době nasazení vybrán.|

## <a name="move-clusters"></a>Přesunout clustery

Cluster HDInsight můžete přesunout do jiné skupiny prostředků Azure nebo jiného předplatného.

Z [domovské stránky clusteru](#homePage):

1. V horní nabídce vyberte **přesunout** .
2. Vyberte **přesunout do jiné skupiny prostředků** nebo **Přejít na jiné předplatné**.
3. Postupujte podle pokynů na nové stránce.

## <a name="delete-clusters"></a>Odstranění clusterů

Odstranění clusteru neodstraní výchozí účet úložiště ani žádné propojené účty úložiště. Cluster můžete znovu vytvořit pomocí stejných účtů úložiště a stejného metaúložiště. Při opětovném vytvoření clusteru doporučujeme použít nový výchozí kontejner objektů BLOB.

Z [domovské stránky clusteru](#homePage):

1. V horní nabídce vyberte **Odstranit** .
2. Postupujte podle pokynů na nové stránce.

Viz také [pozastavit/vypnout clustery](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Po vytvoření clusteru můžete přidat další účty Azure Storage a účty Azure Data Lake Storage. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Škálování clusterů

Funkce škálování clusteru umožňuje změnit počet pracovních uzlů používaných clusterem Azure HDInsight, aniž by bylo nutné cluster znovu vytvořit.

Úplné informace najdete v tématu věnovaném [škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md) .

## <a name="pauseshut-down-clusters"></a>Pozastavit/vypnout clustery

Většina úloh systému Hadoop je dávkových úloh, které jsou spouštěny pouze občas. Pro většinu clusterů Hadoop je k dispozici velké časové období, po které se cluster nepoužívá ke zpracování. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá.
Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Proces můžete programovat mnoha způsoby:

- Uživatel Azure Data Factory. Další informace najdete v tématu Vytvoření propojených služeb HDInsight na vyžádání pro [systémy Linux Apache Hadoop v HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) .
- Použijte Azure PowerShell.  Viz [Analýza dat o zpoždění letu](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Použijete Azure CLI Přečtěte si téma [Správa clusterů Azure HDInsight pomocí Azure CLI](hdinsight-administer-use-command-line.md).
- Použijte sadu HDInsight .NET SDK. Viz [odeslání Apache Hadoop úloh](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Informace o cenách najdete v tématu [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Postup odstranění clusteru z portálu najdete v tématu [odstranění clusterů](#delete-clusters) .

## <a name="upgrade-clusters"></a>Upgradovat clustery

Viz [upgrade clusteru HDInsight na novější verzi](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Otevření webového uživatelského rozhraní Apache Ambari

Ambari poskytuje intuitivní a snadno použitelné webové uživatelské rozhraní správy Hadoop, které zajišťuje rozhraní API pro RESTful. Ambari umožňuje správcům systémů spravovat a monitorovat clustery Hadoop.

Z [domovské stránky clusteru](#homePage):

1. Vyberte **řídicí panely clusteru**.

    ![Nabídka clusteru Apache Hadoop HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Na nové stránce vyberte **Ambari domů** .
1. Zadejte uživatelské jméno a heslo clusteru.  Výchozí uživatelské jméno clusteru je _admin_.

Další informace najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Změna hesel

Cluster An HDInsight může mít dva uživatelské účty. Během procesu vytváření se vytvoří uživatelský účet clusteru HDInsight (uživatelský účet HTTP) a uživatelský účet SSH. Portál můžete použít ke změně hesla uživatelského účtu clusteru a akcí skriptů ke změně uživatelského účtu SSH.

### <a name="change-the-cluster-user-password"></a>Změna hesla uživatele clusteru

> [!NOTE]  
> Změna hesla uživatele clusteru (správce) může způsobit selhání akcí skriptu spuštěných pro tento cluster. Pokud máte trvalé akce skriptů, které cílí na pracovní uzly, můžou se tyto skripty při přidávání uzlů do clusteru přes operace změny velikosti zdařit. Další informace o akcích skriptu najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

Z [domovské stránky clusteru](#homePage):
1. V části **Nastavení** vyberte **ssh + přihlášení clusteru** .
2. Vyberte **resetovat přihlašovací údaje**.
3. Do textových polí zadejte a potvrďte nové heslo.
4. Vyberte **OK**.

Heslo se změní na všech uzlech v clusteru.

### <a name="change-the-ssh-user-password-or-public-key"></a>Změna hesla uživatele SSH nebo veřejného klíče

1. Pomocí textového editoru uložte následující text jako soubor s názvem **changecredentials.sh**.

    > [!IMPORTANT]  
    > Je nutné použít editor, který jako konec řádku používá LF. Pokud editor používá znaky CRLF, skript nefunguje.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Nahrajte soubor do umístění úložiště, ke kterému se dá dostat z HDInsight pomocí adresy HTTP nebo HTTPS. Například veřejné úložiště souborů, jako je OneDrive nebo Azure Blob Storage. Uložte do souboru identifikátor URI (adresa HTTP nebo HTTPS), protože tento identifikátor URI je potřeba v dalším kroku.
3. Na [domovské stránce clusteru](#homePage)vyberte v části **Nastavení** možnost **akce skriptu** .
4. Na stránce **akce skriptu** vyberte **Odeslat novou**.
5. Na stránce **Odeslat akci skriptu** zadejte následující informace:

> [!NOTE]
> Hesla SSH nesmí obsahovat tyto znaky:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Pole | Hodnota |
   | --- | --- |
   | Typ skriptu | Z rozevíracího seznamu vyberte **-vlastní** .|
   | Name |Změna přihlašovacích údajů SSH |
   | Identifikátor URI skriptu bash |Identifikátor URI souboru changecredentials.sh |
   | Typ (typy) uzlů: (Head, work, Nimbus, nadřízený nebo Zookeeper.) |✓ pro všechny typy uzlů v seznamu |
   | Parametry |Zadejte uživatelské jméno SSH a pak nové heslo. Mezi uživatelským jménem a heslem by měla být jedna mezera. |
   | Zachovat tuto akci se skripty... |Nechte toto pole nezaškrtnuté. |

6. Vyberte **vytvořit** pro použití skriptu. Po dokončení skriptu se můžete připojit ke clusteru pomocí protokolu SSH s novými přihlašovacími údaji.

## <a name="find-the-subscription-id"></a>Najít ID předplatného

Každý cluster je vázaný na předplatné Azure.  ID předplatného Azure se zobrazuje na [domovské stránce clusteru](#homePage).

## <a name="find-the-resource-group"></a>Najít skupinu prostředků

V režimu Azure Resource Manager se každý cluster HDInsight vytvoří se skupinou Azure Resource Manager. Skupina Správce prostředků se zobrazuje na [domovské stránce clusteru](#homePage).

## <a name="find-the-storage-accounts"></a>Vyhledání účtů úložiště

Clustery HDInsight používají k ukládání dat účet Azure Storage nebo Azure Data Lake Storage. Každý cluster HDInsight může mít jeden výchozí účet úložiště a řadu propojených účtů úložiště. Pokud chcete zobrazit seznam účtů úložiště, na [domovské stránce clusteru](#homePage) v části **Nastavení** vyberte **účty úložiště**.

## <a name="monitor-jobs"></a>Monitorování úloh

Další informace najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Velikost clusteru

Dlaždice **Velikost clusteru** z [domovské stránky clusteru](#homePage) zobrazuje počet jader přidělených tomuto clusteru a způsob přidělování uzlů v rámci tohoto clusteru.

> [!IMPORTANT]  
> Pokud chcete monitorovat služby poskytované clusterem HDInsight, musíte použít web Ambari nebo Ambari REST API. Další informace o použití Ambari najdete v tématu [Správa clusterů HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md) .

## <a name="connect-to-a-cluster"></a>Připojení ke clusteru

- [Použití Apache Hive se službou HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Použití SSH s HDInsightem](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili se základními funkcemi pro správu. Další informace najdete v těchto článcích:

- [Správa HDInsight pomocí Azure PowerShell](hdinsight-administer-use-powershell.md)
- [Správa HDInsight pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md)
- [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Podrobnosti o používání REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Použití Apache Hive ve službě HDInsight](hadoop/hdinsight-use-hive.md)
- [Použití Apache Sqoop ve službě HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache prasetem v HDInsight](hadoop/python-udf-hdinsight.md)
- [Jakou verzi Apache Hadoop ve službě Azure HDInsight?](hdinsight-component-versioning.md)