---
title: Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure
description: Zjistěte, jak vytvářet a spravovat clustery Azure HDInsight pomocí portálu Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/12/2020
ms.openlocfilehash: b9d923b3272f9d8b3da39d7cdb771a766eee4eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272731"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Pomocí [portálu Azure](https://portal.azure.com)můžete spravovat clustery [Apache Hadoop](https://hadoop.apache.org/) v Azure HDInsight. Pomocí výše uvedeného voliče karet získáte informace o správě clusterů Hadoop v HDInsight pomocí jiných nástrojů.

## <a name="prerequisites"></a>Požadavky

Existující cluster Apache Hadoop v HDInsight.  Viz [Vytváření linuxových clusterů ve službě HDInsight pomocí portálu Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Začínáme

Přihlaste [https://portal.azure.com](https://portal.azure.com)se do .

## <a name="list-and-show-clusters"></a><a name="showClusters"></a>Seznam a zobrazení clusterů

Na stránce **clusterů HDInsight** budou uvedeny vaše stávající clustery.  Z portálu:
1. V levé nabídce vyberte **Všechny služby.**
2. V části **ANALYTICS** **vyberte clustery HDInsight** .

## <a name="cluster-home-page"></a><a name="homePage"></a>Domovská stránka clusteru

Vyberte název clusteru na stránce [**clusterů HDInsight.**](#showClusters)  Otevře se zobrazení **Přehled,** které vypadá podobně jako na následujícím obrázku:

![Základy clusteru Azure portal HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Horní nabídka:**  

| Položka| Popis |
|---|---|
|Přesunout|Přesune cluster do jiné skupiny prostředků nebo do jiného předplatného.|
|Odstranění|Odstraní cluster. |
|Obnovení|Aktualizuje zobrazení.|

**Levé menu:**  

  - **Nabídka vlevo nahoře**

    | Položka| Popis |
    |---|---|
    |Přehled|Obsahuje obecné informace pro váš cluster.|
    |Protokol aktivit|Zobrazit protokoly aktivit a dotazovat se.|
    |Řízení přístupu (IAM)|Použijte přiřazení rolí.  Viz [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).|
    |Značky|Umožňuje nastavit páry klíč/hodnota pro definování vlastní taxonomie cloudových služeb. Můžete například vytvořit klíč s názvem **projekt**a potom použít společnou hodnotu pro všechny služby přidružené k určitému projektu.|
    |Diagnostikovat a řešit problémy|Zobrazí informace o odstraňování potíží.|
    |Rychlý start|Zobrazuje informace, které vám pomohou začít používat HDInsight.|
    |Nástroje|Informace nápovědy pro nástroje související s HDInsight.|

  - **Nabídka Nastavení**  

    | Položka| Popis |
    |---|---|
    |Velikost clusteru|Zkontrolujte, zvyšte a snižte počet pracovních uzlů clusteru. Viz [Škálování clusterů](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kvótové limity|Zobrazte použitá a dostupná jádra pro vaše předplatné.|
    |SSH + Přihlášení do clusteru|Zobrazuje pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Konfigurace přístupu Data Lake Storage Gen1.  Viz [Úvodní příručka: Nastavení clusterů v HDInsightu](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Účty úložiště|Zobrazení účtů úložiště a klíčů. Účty úložiště jsou konfigurovány během procesu vytváření clusteru.|
    |Aplikace|Přidání nebo odebrání aplikací HDInsight.  Viz [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Akce skriptu|Spusťte skripty Bash v clusteru. Viz [Přizpůsobení clusterů HDInsight založených na Linuxu pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).|
    |Externí metaobchody|Prohlédněte si metaobchody [Apache Hive](https://hive.apache.org/) a [Apache Oozie.](https://oozie.apache.org/) Metaobchody lze konfigurovat pouze během procesu vytváření clusteru.|
    |HdInsight partner|Přidejte nebo odeberte aktuálního partnera HDInsight.|
    |Vlastnosti|Zobrazení [vlastností clusteru](#properties).|
    |Zámky|Přidejte zámek, který zabrání úpravám nebo odstranění clusteru.|
    |Export šablony|Zobrazení a export šablony Azure Resource Manager pro cluster. V současné době můžete exportovat jenom závislý účet úložiště Azure. Viz [Vytváření linuxových clusterů Apache Hadoop ve službě HDInsight pomocí šablon Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Monitorovací nabídka**

    | Položka| Popis |
    |---|---|
    |Výstrahy|Spravujte výstrahy a akce.|
    |Metriky|Sledujte metriky clusteru v protokolech Azure Monitoru.|
    |Nastavení diagnostiky|Nastavení, kam uložit metriky diagnostiky.|
    |Azure Monitor|Sledujte svůj cluster ve službě Azure Monitor.|

  - **Nabídka Podpora + řešení potíží**

    | Položka| Popis |
    |---|---|
    |Stav prostředků|Viz [Přehled stavu prostředků Azure](../service-health/resource-health-overview.md).|
    |Nová žádost o podporu|Umožňuje vytvořit lístek podpory s podporou společnosti Microsoft.|

## <a name="cluster-properties"></a><a name="properties"></a>Vlastnosti clusteru

Na [domovské stránce clusteru](#homePage)vyberte v části **Nastavení** **vlastnosti**.

|Položka | Popis |
|---|---|
|Hostname|Název clusteru.|
|ADRESA URL CLUSTERU|Adresa URL webového rozhraní Ambari.|
|Privátní koncový bod|Soukromý koncový bod pro cluster.|
|Bezpečné skořepiny (SSH)|Uživatelské jméno a název hostitele, které se mají použít při přístupu ke clusteru prostřednictvím SSH.|
|STAV|Jeden z: Přerušeno, Přijato, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Provozní, Běh, Chyba, Odstranění, Odstraněno, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, Změna velikostiQueued nebo ClusterCustomization.|
|REGION|Umístění Azure. Seznam podporovaných umístění Azure najdete v rozevíracím seznamu **Oblast** v [cenách HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATUM VYTVOŘENÍ|Datum nasazení clusteru.|
|OPERAČNÍ SYSTÉM|**Windows** nebo **Linux**.|
|TYP|Hadoop, HBase, Storm, Spark.|
|Version|Viz [verze HDInsight](hdinsight-component-versioning.md).|
|Minimální verze TLS|Verze TLS.|
|PŘEDPLATNÉ|Název předplatného.|
|VÝCHOZÍ ZDROJ DAT|Výchozí systém souborů clusteru.|
|Velikosti pracovních uzlů|Vybraná velikost virtuálního počítače pracovních uzlů.|
|Head node size|Vybraná velikost virtuálního počítače uzly hlavy.|
|Virtuální síť|Název virtuální sítě, kterou je cluster nasazen, pokud byl vybrán v době nasazení.|

## <a name="move-clusters"></a>Přesunutí clusterů

Cluster HDInsight můžete přesunout do jiné skupiny prostředků Azure nebo jiného předplatného.

Z [domovské stránky clusteru](#homePage):

1. V horní nabídce vyberte **Přesunout.**
2. Vyberte **Přesunout do jiné skupiny prostředků** nebo **Přesunout do jiného předplatného**.
3. Postupujte podle pokynů na nové stránce.

## <a name="delete-clusters"></a>Odstranění clusterů

Odstraněním clusteru se neodstraní výchozí účet úložiště ani žádné propojené účty úložiště. Cluster můžete znovu vytvořit pomocí stejných účtů úložiště a stejných metastores. Doporučujeme použít nový výchozí kontejner objektů Blob při opětovném vytvoření clusteru.

Z [domovské stránky clusteru](#homePage):

1. V horní nabídce vyberte **Odstranit.**
2. Postupujte podle pokynů na nové stránce.

Viz také [Pozastavení/vypnutí clusterů](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Po vytvoření clusteru můžete přidat další účty Azure Storage a účty Azure Data Lake Storage. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Škálovat clustery

Funkce škálování clusteru umožňuje změnit počet pracovních uzlů používaných clusterem Azure HDInsight, aniž byste museli cluster znovu vytvářet.

Úplné informace najdete v [tématu Škálování clusterů HDInsight.](./hdinsight-scaling-best-practices.md)

## <a name="pauseshut-down-clusters"></a>Pozastavení/vypnutí clusterů

Většina úloh Hadoop jsou dávkové úlohy, které jsou spuštěny pouze příležitostně. Pro většinu clusterů Hadoop existují velké časové období, které cluster není používán pro zpracování. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu.
Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Proces lze naprogramovat mnoha způsoby:

- Uživatel Azure Data Factory. Na [webu Vytvoření clusterů Apache Hadoop založených na Linuxu na vyžádání ve službě HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) najdete v tématu Vytváření propojených služeb HDInsight na vyžádání.
- Použijte Azure PowerShell.  Viz [Analýza dat zpoždění letu](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Použijete Azure CLI Viz [Správa clusterů Azure HDInsight pomocí rozhraní příkazového příkazového příkazu Kontu Azure](hdinsight-administer-use-command-line.md).
- Použijte hdinsight .NET SDK. Viz [Odeslat pracovní příležitosti Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Informace o cenách naleznete v [tématu HDInsight pricing](https://azure.microsoft.com/pricing/details/hdinsight/). Pokud chcete odstranit cluster z portálu, přečtěte si témat [odstranění clusterů.](#delete-clusters)

## <a name="upgrade-clusters"></a>Upgrade clusterů

Viz [Upgrade clusteru HDInsight na novější verzi](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Otevření webového uživatelského uživatelského uživatelského uživatelského panelu Apache Ambari

Ambari poskytuje intuitivní, snadno použitelné webové uživatelské uživatelské prostředí pro správu Hadoop uprostřed jeho RESTful API. Ambari umožňuje správcům systému spravovat a monitorovat clustery Hadoop.

Z [domovské stránky clusteru](#homePage):

1. Vyberte **řídicí panely clusteru**.

    ![Nabídka clusteru HDInsight Apache Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Na nové stránce vyberte **Ambari domů.**
1. Zadejte uživatelské jméno a heslo clusteru.  Výchozí uživatelské jméno clusteru je _admin_.

Další informace naleznete v [tématu Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Změna hesla

Cluster HDInsight může mít dva uživatelské účty. Uživatelský účet clusteru HDInsight (uživatelský účet HTTP) a uživatelský účet SSH jsou vytvořeny během procesu vytváření. Pomocí portálu můžete změnit heslo uživatelského účtu clusteru a akce skriptu ke změně uživatelského účtu SSH.

### <a name="change-the-cluster-user-password"></a>Změna uživatelského hesla clusteru

> [!NOTE]  
> Změna hesla uživatele (správce) clusteru může způsobit selhání akcí skriptu spuštěných proti tomuto clusteru. Pokud máte všechny trvalé akce skriptu, které cílí na pracovní uzly, tyto skripty může selhat při přidání uzlů do clusteru prostřednictvím operace změny velikosti. Další informace o akcích skriptů naleznete v [tématu Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

Z [domovské stránky clusteru](#homePage):
1. V části **Nastavení**vyberte **možnost SSH + Přihlášení ke clusteru** .
2. Vyberte **Obnovit pověření**.
3. Zadejte a potvrďte nové heslo do textových polí.
4. Vyberte **OK**.

Heslo se změní ve všech uzlech v clusteru.

### <a name="change-the-ssh-user-password"></a>Změna uživatelského hesla SSH

1. Pomocí textového editoru uložte následující text jako soubor s názvem **changepassword.sh**.

    > [!IMPORTANT]  
    > Musíte použít editor, který používá LF jako konec řádku. Pokud editor používá CRLF, skript nefunguje.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Nahrajte soubor do umístění úložiště, ke kterému lze přistupovat z HDInsightpomocí http nebo https adresy. Například veřejné úložiště souborů, jako je OneDrive nebo úložiště objektů blob Azure. Uložte identifikátor URI (adresu HTTP nebo HTTPS) do souboru, protože tento identifikátor URI je potřebný v dalším kroku.
3. Na [domovské stránce clusteru](#homePage)vyberte **možnost Akce skriptu** v části **Nastavení**.
4. Na stránce **Akce skriptu** vyberte **Odeslat nový**.
5. Na stránce **Akce Odeslat skript** zadejte následující informace:

   | Pole | Hodnota |
   | --- | --- |
   | Typ skriptu | V rozevíracím seznamu vyberte **– vlastní.**|
   | Name (Název) |"Změnit ssh heslo" |
   | Bash skript URI |Identifikátor URI do souboru changepassword.sh |
   | Typ (typy uzlů): (vedoucí, pracovník, Nimbus, supervizor nebo zookeeper.) |✓ pro všechny uvedené typy uzlů |
   | Parametry |Zadejte uživatelské jméno SSH a pak nové heslo. Mezi uživatelským jménem a heslem by měla být jedna mezera. |
   | Zachovat tuto akci skriptu ... |Ponechte toto pole nezaškrtnuté. |

6. Vyberte **Vytvořit,** chcete-li použít skript. Po dokončení skriptu se můžete připojit ke clusteru pomocí SSH s novým heslem.

## <a name="find-the-subscription-id"></a>Vyhledání ID předplatného

Každý cluster je vázaný na předplatné Azure.  ID předplatného Azure je viditelné z [domovské stránky clusteru](#homePage).

## <a name="find-the-resource-group"></a>Vyhledání skupiny prostředků

V režimu Azure Resource Manager se každý cluster HDInsight vytvoří pomocí skupiny Azure Resource Manager. Skupina Správce prostředků je viditelná z [domovské stránky clusteru](#homePage).

## <a name="find-the-storage-accounts"></a>Vyhledání účtů úložiště

Clustery HDInsight používají k ukládání dat účet Azure Storage nebo Azure Data Lake Storage. Každý cluster HDInsight může mít jeden výchozí účet úložiště a několik propojených účtů úložiště. Chcete-li uvést účty úložiště, vyberte na [domovské stránce clusteru](#homePage) v části **Nastavení** **položku Účty úložiště**.

## <a name="monitor-jobs"></a>Monitorování úloh

Viz [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Velikost clusteru

Dlaždice **velikosti clusteru** z [domovské stránky clusteru](#homePage) zobrazuje počet jader přidělených tomuto clusteru a způsob jejich přidělení pro uzly v rámci tohoto clusteru.

> [!IMPORTANT]  
> Chcete-li sledovat služby poskytované clusterem HDInsight, musíte použít web Ambari nebo rozhraní API Ambari REST. Další informace o používání Ambari najdete v [tématu Správa clusterů HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Připojení ke clusteru

- [Použití Apache Hive s HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Použití SSH s HDInsightem](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili některé základní administrativní funkce. Další informace naleznete v následujících článcích:

- [Správa HDInsightu pomocí Azure PowerShellu](hdinsight-administer-use-powershell.md)
- [Správa HDInsightu pomocí rozhraní PŘÍKAZOVÉHO SYSTÉMU Azure](hdinsight-administer-use-command-line.md)
- [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Podrobnosti o používání rozhraní Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Použití Apache Hive v HDInsight](hadoop/hdinsight-use-hive.md)
- [Použití Apache Sqoop v HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache Pig v HDInsightu](hadoop/python-udf-hdinsight.md)
- [Jaká verze Apache Hadoop je v Azure HDInsight?](hdinsight-component-versioning.md)
