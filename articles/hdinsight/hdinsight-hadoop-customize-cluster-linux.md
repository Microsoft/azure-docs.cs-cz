---
title: Přizpůsobení clusterů HDInsight pomocí skriptových akcí Azure
description: Přidáte vlastní komponenty na clusterech HDInsight založených na Linuxu pomocí skriptových akcí. Akce skriptů jsou skripty Bash, které slouží k přizpůsobení konfigurace clusteru nebo přidat další služby a nástroje, jako jsou Hue, Solr nebo R.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 20894a74bf0cc3911272385721da74871df95ade
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913214"
---
# <a name="customize-linux-based-hdinsight-clusters-by-using-script-actions"></a>Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu

Azure HDInsight poskytuje konfigurace metodu nazvanou **akcí skriptů** , který vyvolá vlastní skripty pro přizpůsobení clusteru. Tyto skripty se používají k instalaci dalších komponent a změnit nastavení konfigurace. Akce se skripty můžete použít během nebo po vytvoření clusteru.

> [!IMPORTANT]  
> Možnost používat akcí skriptů v clusteru již běžícímu dostupná jenom pro clustery HDInsight založené na Linuxu.
>
> Linux je pouze operační systém používaný v HDInsight verze 3.4 a vyšší. Další informace najdete v tématu [HDInsight Windows vyřazení](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Akce se skripty můžete také publikovat na webu Azure Marketplace jako aplikace HDInsight. Další informace o aplikace HDInsight, naleznete v tématu [publikovat aplikace HDInsight na Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Oprávnění

Pro cluster HDInsight připojený k doméně existují dvě Apache Ambari oprávnění, které jsou požadovány při použití akcí skriptů v clusteru:

* **AMBARI. SPUSTIT\_VLASTNÍ\_PŘÍKAZ**. Role správce Ambari má toto oprávnění ve výchozím nastavení.
* **CLUSTER. SPUSTIT\_VLASTNÍ\_PŘÍKAZ**. Správce clusteru HDInsight i správce Ambari mají toto oprávnění ve výchozím nastavení.

Další informace o práci s oprávněními s HDInsight připojených k doméně najdete v tématu [spravovat HDInsight clustery s balíčkem Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Řízení přístupu

Pokud si nejste správce ani vlastník předplatného Azure, váš účet musí mít alespoň přístup přispěvatele do skupiny prostředků, která obsahuje HDInsight cluster.

Pokud alespoň vytvoříte cluster HDInsight, osoba s přístupem Přispěvatel pro příslušné předplatné Azure musel již dříve zaregistrovat poskytovatele pro HDInsight. Registrace zprostředkovatele se provede, když uživatel s přístupem Přispěvatel poprvé vytvoří prostředek v rámci příslušného předplatného. Můžete také provést bez vytvoření prostředku, pokud jste [registraci zprostředkovatele pomocí REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Získejte další informace o práci se správou přístupu:

* [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
* [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Vysvětlení akcí skriptů

Akce skriptu je Bash skript, který používá na uzlech v clusteru HDInsight. Vlastnosti a funkce akce skriptu jsou následující:

* Musí být uložené na identifikátor URI, který je přístupný z clusteru HDInsight. Toto jsou možné úložišť:

    * Účet úložiště Azure Data Lake, který je přístupný pro HDInsight cluster. Informace o používání služby Azure Data Lake Storage s HDInsight naleznete v tématu [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

        Formát identifikátoru URI pro skripty uložené ve službě Data Lake Storage je `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]  
        > Instanční objekt služby, kterou používá HDInsight pro přístup k Data Lake Storage musí mít oprávnění ke čtení pro skript.

    * Objekt blob v účtu služby Azure Storage, který je buď primární a dodatečné úložiště účtu pro HDInsight cluster. HDInsight je udělen přístup k oběma typy účtů úložiště při vytváření clusteru.

    * Veřejné služby Sdílení souborů. Příklady jsou objektů Blob v Azure, Githubu, OneDrive nebo Dropbox.

        Například identifikátory URI, najdete v článku [příklady skriptů pro skript akce](#example-script-action-scripts).

        > [!WARNING]  
        > HDInsight podporuje pouze objektů Blob v účtech Azure Storage s úrovní výkonu. 

* Může být omezen na spouštět pouze určité typy uzlů. Příklady jsou hlavní uzly nebo pracovních uzlů.

* Může být trvalé nebo ad hoc.

    Trvalých skriptů se používají k přizpůsobení nové pracovních uzlů do clusteru prostřednictvím operace škálování. Trvalá akce se skripty může také použít změny na jiný typ uzlu při výskytu operace škálování. Příkladem je hlavní uzel.

  > [!IMPORTANT]  
  > Trvalé akce se skripty musí mít jedinečný název.

    Ad hoc skriptů nejsou trvalé. Použijí se pracovních uzlů do clusteru přidat po spuštění skriptu. Potom můžete zvýšit úroveň skriptu ad hoc trvalá akce se skripty nebo snížení úrovně trvalá akce se skripty pro ad hoc skriptů.

  > [!IMPORTANT]  
  > Automaticky jsou trvalé akce se skripty používají při vytváření clusteru.
  >
  > Skripty, které nesplní se jako trvalý, i v případě, že konkrétně určujete, že by měl být.

* Může přijímat parametry, které se používají ve skriptu při spuštění.

* Spusťte s oprávněními na úrovni kořenového adresáře na uzlech clusteru.

* Je možné prostřednictvím webu Azure portal, prostředí Azure PowerShell, Azure classic CLI nebo sady HDInsight .NET SDK.

Cluster uchovává historii všechny skripty, které byly spuštěny. Historie je výhodné, pokud potřebujete vyhledat ID skript pro povýšení nebo degradace operace.

> [!IMPORTANT]  
> Neexistuje žádný automatický způsob vrátit zpět změny provedené akce skriptu. Buď ručně, změny nebo zadejte skript, který je vrátí.

### <a name="script-action-in-the-cluster-creation-process"></a>Akce skriptu v procesu vytváření clusteru

Akce skriptů používá při vytváření clusteru jsou mírně lišit od spuštění v existujícím clusteru akcí skriptů:

* Skript se automaticky ukládají.

* Chyba ve skriptu může způsobit selhání procesu vytváření clusteru.

Následující diagram znázorňuje spuštění během procesu vytváření akcí skriptů:

![Přizpůsobení clusteru HDInsight a fáze při vytváření clusteru][img-hdi-cluster-states]

Skript se spustí, když konfigurujete HDInsight. Skript se spustí paralelně na všechny zadané uzly v clusteru. Spuštění s kořenovými oprávněními na uzlech.

> [!NOTE]  
> Můžete provádět operace, jako je zastavení a spuštění služeb, včetně služby související s Apache Hadoop. Chcete-li zrušit služby, ujistěte se, že služba Ambari a další související s Hadoop služby jsou spuštěny před dokončením skript. Tyto služby jsou nezbytné pro úspěšně zjistit stav a stav clusteru při jeho vytváření.


Při vytváření clusteru můžete použít mnoho akce skriptu najednou. Tyto skripty jsou vyvolány v pořadí, ve kterém byly zadány.

> [!IMPORTANT]  
> Akce se skripty musí dokončit během 60 minut, nebo vypršení časového limitu. Při zřizování clusteru, bude skript spuštěn současně jiných nastavení a konfigurace procesů. Konkurenční žádosti o prostředky, jako jsou procesor čas nebo síťové šířky pásma může způsobit skriptu trvá déle dokončit, než ve vašem vývojovém prostředí.
>
> Chcete-li minimalizovat čas potřebný ke spuštění skriptu, vyhněte se úkoly, jako je stahování a kompilace aplikace ze zdroje. Předkompilovat aplikace a uložení binárního souboru ve službě Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Akce skriptu na spuštěný cluster

Chyba ve skriptu, spusťte na již spuštěný cluster nezpůsobí automaticky clusteru změnit do chybového stavu. Po dokončení skriptu clusteru by měl vrátit do funkčního stavu.

> [!IMPORTANT]  
> I v případě, že cluster má spuštěném stavu, skript, který selhal nefunkční věci. Skript může například odstranit soubory potřebné pro cluster.
>
> Akce skriptů, spusťte s kořenovými oprávněními. Ujistěte se, že chápete, co dělá skript před použitím ke svému clusteru.

Při použití skriptu na clusteru se stav clusteru změní z **systémem** k **přijato**. Pak se změní na **konfigurace HDInsight** a nakonec zpět do **systémem** úspěšné skriptů. Stav skriptu je zaznamenána v historii akcí skriptu. Tyto informace zjistíte, zda skript úspěšné nebo neúspěšné. Například `Get-AzureRmHDInsightScriptActionHistory` rutiny Powershellu se zobrazuje stav skriptu. Vrátí informace podobné následujícímu textu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Pokud po vytvoření clusteru změnit uživatele clusteru, admin, heslo, akce se skripty spustit tento cluster může selhat. Pokud máte jakékoli trvalé akce se skripty této cílové uzly pracovního procesu, tyto skripty může selhat při škálování clusteru.

## <a name="example-script-action-scripts"></a>Příklady skriptů pro skript akce

Skripty akce skriptu umožňuje použít následující nástroje:

* Azure Portal
* Azure PowerShell
* Azure classic CLI
* Sada HDInsight .NET SDK

HDInsight poskytuje skriptů v clusterech HDInsight nainstalovat následující komponenty:

| Název | Skript |
| --- | --- |
| Přidat účet Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zobrazit [přidání dalších účtů úložiště pro HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalace rozhraní Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zobrazit [instalace a použití rozhraní Hue v HDInsight Hadoop clusterů](hdinsight-hadoop-hue-linux.md). |
| Nainstalovat Presto |`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`. Zobrazit [instalace a použití Presta na technologii hadoop HDInsight clustery](hdinsight-hadoop-install-presto.md). |
| Nainstalovat Solr |`https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh`. Zobrazit [instalace a použití Apache Solr na HDInsight Hadoop clusterů](hdinsight-hadoop-solr-install-linux.md). |
| Nainstalovat Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Zobrazit [nainstalovat Giraph Apache na platformě HDInsight Hadoop clusterů](hdinsight-hadoop-giraph-install-linux.md). |
| Předběžné načtení knihoven Hivu |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zobrazit [přidat vlastní knihovny Apache Hive při vytváření clusteru HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| Instalace nebo aktualizace Mono | `https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash`. Zobrazit [instalace nebo aktualizace Mono na HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Použití akce skriptu při vytváření clusteru

Tato část popisuje různé způsoby, jak skriptových akcí můžete použít při vytváření clusteru HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Použití akce skriptu při vytváření clusteru z webu Azure portal

1. Start k vytvoření clusteru, jak je popsáno v [nastavení clusterů v HDInsight se Apache Hadoop, Apache Spark, Apache Kafka a další](hdinsight-hadoop-provision-linux-clusters.md). Při vytváření clusteru, můžete přejít na __souhrn clusteru__ stránky. Z __souhrn clusteru__ stránky, vyberte __upravit__ odkaz __upřesňující nastavení__.

    ![Odkaz Upřesnit nastavení](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Z __upřesňující nastavení__ vyberte __akcí skriptů__. Z __akcí skriptů__ vyberte __+ odeslat novou__.

    ![Novou akci skriptu odešlete](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Použití __vyberte skript__ položku a vyberte předem připravené skript. Chcete-li použít vlastní skript, vyberte __vlastní__. Zadejte __název__ a __URI skriptu Bash__ vašeho skriptu.

    ![Přidat skript ve formuláři vyberte skript](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Vyberte skript | Chcete-li použít vlastní skript, vyberte __vlastní__. V opačném případě vyberte jednu z poskytnutých skriptů. |
    | Název |Zadejte název akce skriptu. |
    | URI skriptu Bash |Zadejte identifikátor URI skriptu. |
    | HEAD/Worker/Zookeeper |Zadejte uzly, na kterých je skript spuštěn: **Hlavní**, **pracovního procesu**, nebo **ZooKeeper**. |
    | Parametry |Zadejte parametry, pokud je to nutné skript. |

    Použití __zachovat tuto akci se skripty__ položku a ujistěte se, že skript je použit během operace škálování.

5. Vyberte __vytvořit__ skript uložte. Můžete použít __+ odeslat novou__ přidáte další skript.

    ![Více akcí skriptů](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Po dokončení přidávání skriptů, vyberte __vyberte__ tlačítko a pak __Další__ se vrátit na __souhrn clusteru__ oddílu.

3. Chcete-li vytvořit cluster, vyberte __vytvořit__ z __souhrn clusteru__ výběru.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Použití akce skriptu ze šablon Azure Resource Manageru

Akce skriptů je možné s šablonami Azure Resource Manageru. Příklad najdete v tématu [vytvoření clusteru HDInsight Linux a spusťte skript akce](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

V tomto příkladu se přidá akci skriptu pomocí následujícího kódu:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Získejte další informace o tom, jak nasadit šablonu:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Použití akce skriptu při vytváření clusteru pomocí Azure Powershellu

V této části použijete [přidat AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction) rutiny vyvolání skriptů přizpůsobení clusteru. Než začnete, ujistěte se, že instalace a konfigurace Azure Powershellu. Informace o konfiguraci pracovní stanice ke spouštění rutin prostředí HDInsight PowerShell najdete v tématu [Přehled prostředí Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azps-1.1.0#run-or-install).

Tento skript ukazuje, jak použít akci skriptu při vytváření clusteru pomocí prostředí PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Může trvat několik minut, než se cluster vytvoří.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Použití akce skriptu při vytváření clusteru ze sady HDInsight .NET SDK

Sady HDInsight .NET SDK obsahuje klientské knihovny, které usnadňují práci s HDInsight z aplikace .NET. Ukázku kódu naleznete v tématu [vytvoření linuxových clusterech v HDInsight pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Použít akci skriptu spuštěného clusteru

Tato část vysvětluje, jak použít skript akce pro spuštěný cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Použít akci skriptu pro spuštěný cluster na webu Azure Portal

Přejděte [webu Azure portal](https://portal.azure.com):

1. V nabídce vlevo vyberte **všechny služby**.

1. V části **ANALYTICS**vyberte **clustery HDInsight**.

1. Vyberte ze seznamu, které se otevře zobrazení výchozí svůj cluster.

1. Výchozí zobrazení v části **nastavení**vyberte **akcí skriptů**.

1. V horní části **akcí skriptů** stránce **+ odeslat novou**.

    ![Přidat skript do spuštěného clusteru](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Použití __vyberte skript__ položku a vyberte předem připravené skript. Chcete-li použít vlastní skript, vyberte __vlastní__. Zadejte __název__ a __URI skriptu Bash__ vašeho skriptu.

    ![Přidat skript ve formuláři vyberte skript](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Vyberte skript | Chcete-li použít vlastní skript, vyberte __vlastní__. V opačném případě vyberte dodávaného skriptu. |
    | Název |Zadejte název akce skriptu. |
    | URI skriptu Bash |Zadejte identifikátor URI skriptu. |
    | HEAD/Worker/Zookeeper |Zadejte uzly, na kterých je skript spuštěn: **Hlavní**, **pracovního procesu**, nebo **ZooKeeper**. |
    | Parametry |Zadejte parametry, pokud je to nutné skript. |

    Použití __zachovat tuto akci se skripty__ položku a ujistěte se, že skript je použit během operace škálování.

5. Nakonec vyberte **vytvořit** tlačítko použít skript do clusteru.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Použít akci skriptu pro spuštěný cluster z prostředí Azure PowerShell

Než začnete, ujistěte se, že instalace a konfigurace Azure Powershellu. Informace o konfiguraci pracovní stanice ke spouštění rutin prostředí HDInsight PowerShell najdete v tématu [Přehled prostředí Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azps-1.1.0#run-or-install).

Následující příklad ukazuje, jak použít akci skriptu spuštěného clusteru:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po dokončení operace se zobrazí informace podobné následujícímu textu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Použít akci skriptu pro spuštěný cluster z příkazového řádku Azure

Než začnete, ujistěte se, že instalace a konfigurace rozhraní příkazového řádku Azure. Další informace najdete v tématu [instalace Azure classic CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Přepnout do režimu Azure Resource Manageru:

    ```bash
    azure config mode arm
    ```

2. Ověření ke svému předplatnému Azure:

    ```bash
    azure login
    ```

3. Použití akce skriptu do spuštěného clusteru:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Vynecháte-li parametry tohoto příkazu se zobrazí výzva k jejich. Pokud skript zadáte s `-u` přijímá parametry, je možné zadat pomocí `-p` parametru.

    Platný uzel typy jsou `headnode`, `workernode`, a `zookeeper`. Pokud skript by měl použít pro několik typů uzlů, určit typy oddělené středníkem `;`. Například, `-n headnode;workernode`.

    Chcete-li zachovat tento skript, přidejte `--persistOnSuccess`. Je možné také zachovat tento skript později pomocí `azure hdinsight script-action persisted set`.

    Jakmile se úloha dokončí, získáte výstup podobný následujícímu textu:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Použití akce skriptu do spuštěného clusteru pomocí rozhraní REST API

Zobrazit [clusteru rozhraní REST API v Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Použití akce skriptu do spuštěného clusteru ze sady HDInsight .NET SDK

Příklad použití skriptů do clusteru pomocí sady .NET SDK najdete v tématu [použít akci skriptu na clusteru HDInsight pracovat založených na Linuxu](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Zobrazení historie a propagace a snížení úrovně skriptových akcí

### <a name="the-azure-portal"></a>Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **všechny služby**.

1. V části **ANALYTICS**vyberte **clustery HDInsight**.

1. Vyberte ze seznamu, které se otevře zobrazení výchozí svůj cluster.

1. Výchozí zobrazení v části **nastavení**vyberte **akcí skriptů**.

4. Historie skripty pro tento cluster se zobrazí v části Akce skriptu. Tyto informace zahrnují seznamu trvalých skriptů. Následující snímek obrazovky ukazuje, že spuštění skriptu Solr na tomto clusteru. Na snímku obrazovky nezobrazí žádné trvalých skriptů.

    ![Akce skriptů](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Vyberte skript z historie pro zobrazení **vlastnosti** oddíl pro tento skript. V horní části obrazovky můžete skript spustit znovu nebo zvyšte jeho úroveň.

    ![Akce skriptů, vlastnosti](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Můžete také vybrat se třemi tečkami **...** , napravo od položky v části Akce skriptu k provádění akcí.

    ![Akce skriptů, tlačítko se třemi tečkami](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funkce |
| --- | --- |
| `Get-AzureRmHDInsightPersistedScriptAction` |Načíst informace o trvalé akce se skripty. |
| `Get-AzureRmHDInsightScriptActionHistory` |Načtěte historii akcí skriptu u clusteru nebo podrobnosti pro konkrétní skript. |
| `Set-AzureRmHDInsightPersistedScriptAction` |Umožňuje zvýšit úroveň akci ad hoc skriptů s akcí trvalého skriptu. |
| `Remove-AzureRmHDInsightPersistedScriptAction` |Snížení úrovně trvalá akce skriptu do ad hoc akce. |

> [!IMPORTANT]  
> `Remove-AzureRmHDInsightPersistedScriptAction` nelze vrátit zpět akce prováděné pomocí skriptu. Tato rutina odebere jenom příznak trvalý.

Následující ukázkový skript ukazuje použití rutiny k propagaci a pak snížit úroveň skriptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Azure classic CLI

| cmdlet | Funkce |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Načtení seznamu akcí trvalého skriptu. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Načíst informace o konkrétní trvalý skriptových akcí. |
| `azure hdinsight script-action history list <clustername>` |Načtěte historii akcí skriptu u clusteru. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Načíst informace o konkrétní skriptových akcí. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Umožňuje zvýšit úroveň akci ad hoc skriptů s akcí trvalého skriptu. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Snížení úrovně trvalá akce skriptu do ad hoc akce. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` nelze vrátit zpět akce prováděné pomocí skriptu. Tato rutina odebere jenom příznak trvalý.

### <a name="the-hdinsight-net-sdk"></a>Sady HDInsight .NET SDK

Příklad použití sady .NET SDK k načtení historie skriptu z clusteru, zvýšení úrovně nebo snížení úrovně skriptů, najdete v článku [ použít akci skriptu na clusteru HDInsight pracovat založených na Linuxu](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Tento příklad také ukazuje, jak nainstalovat aplikaci HDInsight pomocí sady .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora pro open source softwaru používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight využívá ekosystém open source technologií formátovaných kolem Apache Hadoop. Microsoft Azure poskytuje obecné úroveň podpory pro open source technologie. Další informace najdete v tématu **rozsah podpory** část [nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované komponenty.

Dva druhy opensourcové komponenty jsou k dispozici ve službě HDInsight:

* **Integrované komponenty**. Tyto součásti jsou předinstalovány v clusterech HDInsight a poskytuje základní funkce clusteru. Do této kategorie patří následující součásti:

    * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
    * Jazyk dotazu Hive [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
    * [Apache Mahout](https://mahout.apache.org/). 
    
    Úplný seznam součástí clusteru je k dispozici v [co je Apache Hadoop komponenty a verze, které jsou k dispozici s HDInsight?](hdinsight-component-versioning.md)

* **Vlastní komponenty**. Jako uživatel clusteru můžete nainstalovat nebo použít ve vašich úloh žádné součásti k dispozici v komunitě nebo vytvořené vámi.

> [!WARNING]  
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované. Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
>
> Vlastní komponenty získat obchodně přiměřenou podporu můžete dále řešit tento problém. Chcete-li vyřešit tento problém možná půjde Microsoft Support. Nebo mohou kontaktovat dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii požádat. Dá se řada komunitních webů. Mezi příklady patří [fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) a [Stack Overflow](https://stackoverflow.com). 
>
> Projektů Apache také mít projektovým webům na [webu Apache](https://apache.org). Příkladem je [Hadoop](https://hadoop.apache.org/).

Služba HDInsight poskytuje několik způsobů, jak používat vlastní komponenty. Stejnou úroveň podpory platí bez ohledu na to, jak je komponenta použít nebo nainstalované v clusteru. Následující seznam popisuje nejběžnější způsobem, jsou v clusterech HDInsight používá vlastní komponenty:

1. **Odeslání úlohy**. Hadoop nebo jiných typů úloh, které spouštění nebo použít vlastní komponenty můžete odeslat do clusteru.

2. **Přizpůsobení clusteru**. Při vytváření clusteru můžete zadat další nastavení a vlastních součástech, které jsou nainstalovány na uzlech clusteru.

3. **Ukázky**. Oblíbené vlastní komponenty Microsoftu a dalších může poskytnout ukázky použití těchto komponent v clusterech HDInsight. Tyto ukázky jsou k dispozici bez podpory.

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li zobrazit informacím protokolovaným v skriptových akcí můžete použít webové uživatelské rozhraní Ambari. Pokud skript selže při vytváření clusteru, protokoly jsou dostupné v přidružené ke clusteru výchozí účet úložiště. Tato část obsahuje informace o tom, jak tyto protokoly načíst pomocí obou těchto možností.

### <a name="the-apache-ambari-web-ui"></a>Webové uživatelské rozhraní Apache Ambari

1. V prohlížeči přejděte na https://CLUSTERNAME.azurehdinsight.net. Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.

    Po zobrazení výzvy zadejte název účtu správce **správce**a heslo pro cluster. Budete muset znovu zadat přihlašovací údaje správce do webového formuláře.

2. Na panelu v horní části stránky vyberte **ops** položka. Seznam zobrazí aktuální a předchozí operace v clusteru prostřednictvím Ambari.

    ![Panel uživatelského rozhraní Ambari web s operacemi vybrané](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Najděte položky, které mají **spustit\_customscriptaction** v **operace** sloupce. Tyto položky se vytvoří při spuštění skriptových akcí.

    ![Snímek obrazovky s operací](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Chcete-li zobrazit **STDOUT** a **STDERR** výstup, vyberte **run\customscriptaction** položku a také procházet prostřednictvím odkazů. Tento výstup se vygeneruje po spuštění skriptu a může být užitečné informace.

### <a name="access-logs-from-the-default-storage-account"></a>Přístup k protokolům z výchozí účet úložiště

Pokud se vytvoření clusteru se nepovedlo kvůli chybě skriptu, protokoly se ukládají do účtu úložiště clusteru.

* Protokoly úložiště najdete na adrese `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Snímek obrazovky s operací](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    V tomto adresáři, jsou zvlášť uspořádány v protokolech pro **hlavního uzlu**, **pracovní uzel**, a **uzlu zookeeper**. Podívejte se na následující příklady:

    * **Hlavní uzel**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Pracovní uzel**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Uzel zookeeper**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Všechny **stdout** a **stderr** odpovídající hostitele se nahraje do účtu úložiště. Existuje jedna **výstupní -\*.txt** a **chyby -\*.txt** pro každou akci se skripty. **Výstup *.txt** soubor obsahuje informace o identifikátoru URI skriptu, který byl spuštěn na hostiteli. Následující text je příkladem těchto informací:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Je možné, opakovaně vytvořit cluster akce skriptu se stejným názvem. V takovém případě lze rozlišit relevantní protokoly na základě **datum** název složky. Například strukturu složek pro cluster, **mycluster**, které byly vytvořeny na různá data se zobrazí podobná následující položky protokolu:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Pokud vytvoříte cluster akce skriptu se stejným názvem v jednom dni, můžete k identifikaci příslušných protokolových souborů jedinečnou předponu.

* Pokud vytvoříte cluster v 12:00 AM, midnight, je možné, že soubory protokolu span trvajícího dva dny. V takovém případě se zobrazí dvě složky jiné datum pro stejného clusteru.

* Nahrávají se soubory protokolu na výchozí kontejner může trvat až pět minut, zejména u velkých clusterech. Takže pokud chcete přístup k protokolům, byste neměli odstraňovat okamžitě clusteru selhání akce skriptu.

### <a name="ambari-watchdog"></a>Ambari sledovacího zařízení

> [!WARNING]  
> Neměňte heslo Ambari sledovacího zařízení hdinsightwatchdog ve vašem clusteru HDInsight se systémem Linux. Změna hesla pro tento účet dělí možnost spouštět nové akce skriptu na clusteru HDInsight.

### <a name="cant-import-name-blobservice"></a>Nelze importovat název BlobService

__Příznaky__. Skript akce se nezdaří. Při operaci zobrazení Ambari, zobrazí se text podobný následující chybě:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Příčina__. K této chybě dochází, pokud upgradujete klienta služby Azure Storage pro Python, který je součástí clusteru HDInsight. HDInsight očekává, že klient služby Azure Storage 0.20.0.

__Rozlišení__. Chcete-li vyřešit tuto chybu, ručně připojit k jednotlivým uzlům clusteru s použitím `ssh`. Spusťte následující příkaz k přeinstalování úložiště správná verze klienta:

```bash
sudo pip install azure-storage==0.20.0
```

Informace o připojení ke clusteru pomocí SSH najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historie nezobrazí skripty použité při vytváření clusteru

Pokud váš cluster byl vytvořen ještě před 15. března 2016, nemusíte to vidět položku v historii akcí skriptu. Změna velikosti clusteru způsobí, že skripty se zobrazí v historii akcí skriptu.

Existují dvě výjimky:

* Váš cluster byl vytvořen ještě před 1. září 2015. Toto datum je, když byly zavedeny skriptových akcí. Libovolný cluster vytvořen před tímto datem nelze použít akce se skripty pro vytvoření clusteru.

* Více akcí skriptů jste použili při vytváření clusteru. Nebo můžete použít stejný název pro několik skriptů nebo se stejným názvem, stejný identifikátor URI, ale jiné parametry pro několik skriptů. V těchto případech se zobrazí následující chyba:

    Žádné nové akce se skripty můžete spustit na tomto clusteru z důvodu konfliktní názvy ve skriptu v stávající skripty. Skript názvy zadané při vytváření clusteru musí být všechny jedinečné. Stávající skripty se spouštějí na změny velikosti.

## <a name="next-steps"></a>Další postup

* [Vývoj akcí skriptů skript pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalace a použití Apache Solr na clusterech HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalace a použití Giraphu Apache v clusterech HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Přidat další úložiště clusteru služby HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fáze při vytváření clusteru"
