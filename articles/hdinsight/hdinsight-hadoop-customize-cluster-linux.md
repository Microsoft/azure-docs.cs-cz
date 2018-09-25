---
title: Přizpůsobení clusterů HDInsight pomocí skriptových akcí – Azure
description: Přidáte vlastní komponenty pro clustery založené na Linuxu HDInsight pomocí skriptových akcí. Akce skriptů jsou skripty Bash, které slouží k přizpůsobení konfigurace clusteru nebo přidat další služby a nástroje, jako jsou Hue, Solr nebo R.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: jasonh
ms.openlocfilehash: e9662a0a0def91fa2cace1eb47dceffed5ff1ae6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978025"
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů

HDInsight poskytuje konfigurace metodu nazvanou **akcí skriptů** , který vyvolá vlastní skripty pro přizpůsobení clusteru. Tyto skripty se používají k instalaci dalších komponent a změnit nastavení konfigurace. Akce se skripty můžete použít během nebo po vytvoření clusteru.

> [!IMPORTANT]
> Možnost používat akcí skriptů v clusteru již běžícímu dostupná jenom pro clustery HDInsight založené na Linuxu.
>
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Akce se skripty můžete také publikovat na webu Azure Marketplace jako aplikace HDInsight. Další informace o aplikace HDInsight, naleznete v tématu [aplikací HDInsight publikovat na webu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Oprávnění

Pokud používáte cluster HDInsight připojený k doméně, jsou k dispozici dvě oprávnění Ambari, které jsou potřeba při pomocí akcí skriptů v clusteru:

* **AMBARI. Spustit\_vlastní\_příkaz**: role správce Ambari má toto oprávnění ve výchozím nastavení.
* **CLUSTER. Spustit\_vlastní\_příkaz**: oba HDInsight Správce clusteru a správce Ambari mají toto oprávnění ve výchozím nastavení.

Další informace o práci s oprávněními s HDInsight připojených k doméně najdete v tématu [Správa clusterů HDInsight připojených k doméně](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Řízení přístupu

Pokud nejste správcem/vlastníkem vašeho předplatného Azure, váš účet musí mít alespoň **Přispěvatel** přístup do skupiny prostředků, která obsahuje HDInsight cluster.

Kromě toho při vytváření clusteru služby HDInsight, někdo s nejméně **Přispěvatel** přístup k předplatnému Azure musel již dříve zaregistrovat poskytovatele pro HDInsight. Registrace zprostředkovatele se provede, když uživatel s přístupem Přispěvatel poprvé vytvoří prostředek v rámci příslušného předplatného. Stejného výsledku lze i bez vytvoření prostředku dosáhnout [registrací poskytovatele prostřednictvím REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Další informace o práci se správou přístupu najdete v následujících dokumentech:

* [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
* [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understanding-script-actions"></a>Vysvětlení akcí skriptů

Akce skriptu je Bash skript, který používá na uzlech v clusteru HDInsight. Níže jsou uvedeny vlastnosti a funkce akce skriptu.

* Musí být uložené na identifikátor URI, který je přístupný z clusteru HDInsight. Toto jsou možné úložišť:

    * **Azure Data Lake Store** účet, který je přístupný pro HDInsight cluster. Informace o používání Azure Data Lake Store se službou HDInsight najdete v tématu [rychlý start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

        Při použití skriptů uložených v Data Lake Store, formát identifikátoru URI je `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Instanční objekt služby, kterou používá HDInsight pro přístup k Data Lake Store musí mít oprávnění ke čtení pro skript.

    * Objekt blob do **účtu služby Azure Storage** , který je buď primární nebo další účet úložiště pro HDInsight cluster. HDInsight je udělen přístup k oběma typy účtů úložiště při vytváření clusteru.

    * Soubor veřejného sdílení služby, jako je Azure Blob, Githubu, OneDrive, Dropbox atd.

        Například identifikátory URI, najdete v článku [příklady skriptů pro skript akce](#example-script-action-scripts) oddílu.

        > [!WARNING]
        > HDInsight podporuje pouze __pro obecné účely__ účtů služby Azure Storage. Nepodporuje současné době __úložiště objektů Blob__ typ účtu.

* Je možné omezit na **spouštět pouze určité typy uzlů**, příklad hlavním uzlům nebo pracovních uzlů.

* Může být **trvalé** nebo **ad hoc**.

    **Trvalé** skripty se používají k přizpůsobení nové pracovních uzlů do clusteru prostřednictvím operace škálování. Trvalá akce se skripty může také použít změny na jiný typ uzlu, jako je například hlavního uzlu, když dojde k operace škálování.

  > [!IMPORTANT]
  > Trvalé akce se skripty musí mít jedinečný název.

    **Ad hoc** skriptů nejsou trvalé. Použijí se pracovních uzlů do clusteru přidat po má skript spustili. Následně můžete zvýšit úroveň skriptu ad hoc trvalá akce se skripty nebo snížení úrovně trvalá akce se skripty pro ad hoc skriptů.

  > [!IMPORTANT]
  > Automaticky jsou trvalé akce se skripty používají při vytváření clusteru.
  >
  > Skripty, které nejsou selhání jako trvalý, i v případě, že konkrétně určujete, že by měl být.

* Můžete přijmout **parametry** skriptu, které jsou používány během provádění.

* Spustit s **kořenové úrovně oprávnění** na uzlech clusteru.

* Umožňuje použít **webu Azure portal**, **prostředí Azure PowerShell**, **rozhraní příkazového řádku Azure Classic**, nebo **sady HDInsight .NET SDK**

Cluster uchovává historii všechny skripty, které mají byly spuštěny. Historie je užitečné, když potřebujete vyhledat ID skript pro povýšení nebo degradace operace.

> [!IMPORTANT]
> Neexistuje žádný automatický způsob vrátit zpět změny provedené akce skriptu. Buď ručně, změny nebo zadejte skript, který je vrátí.

### <a name="script-action-in-the-cluster-creation-process"></a>Akce skriptu v procesu vytváření clusteru

Akce se skripty používají při vytváření clusteru se mírně liší od skriptu, který akcí spuštěných v existujícím clusteru:

* Skript je **automaticky trvalý**.

* A **selhání** ve skriptu, může způsobit selhání procesu vytváření clusteru.

Následující diagram znázorňuje při spuštění akce skriptu během procesu vytváření:

![Přizpůsobení clusteru HDInsight a fáze při vytváření clusteru][img-hdi-cluster-states]

Skript se spustí, když konfigurujete HDInsight. Skript spouští paralelně na všechny zadané uzly v clusteru a spustí s kořenovými oprávněními na uzlech.

> [!NOTE]
> Můžete provádět operace, jako je zastavení a spuštění služeb, včetně služby související s Hadoop. Chcete-li zrušit služby musíte zajistit, že služba Ambari a další služby související s Hadoop spuštěné před skript dokončí. Tyto služby jsou nezbytné pro úspěšně zjistit stav a stav clusteru při jeho vytváření.


Při vytváření clusteru můžete použít různé akce skriptu najednou. Tyto skripty jsou vyvolány v pořadí, ve kterém byly zadány.

> [!IMPORTANT]
> Akce se skripty musí dokončit během 60 minut nebo vypršení časového limitu. Při zřizování clusteru, bude skript spuštěn současně jiných nastavení a konfigurace procesů. Soutěže pro prostředky, jako jsou procesor čas nebo síťové šířky pásma může způsobit skriptu trvá déle dokončit, než ve vašem vývojovém prostředí.
>
> Chcete-li minimalizovat čas potřebný ke spuštění skriptu, vyhnete se úlohám, jako je stahování a kompilace aplikace ze zdroje. Před kompilací aplikace a uložení binárního souboru ve službě Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Akce skriptu na spuštěný cluster

Chyba ve skriptu spustili na už běží clusteru nezpůsobí automaticky clusteru změnit do chybového stavu. Po dokončení skriptu clusteru by měl vrátit do stavu "spuštěno".

> [!IMPORTANT]
> I v případě, že má cluster stavu "spuštěno", může mít skript, který selhal nefunkční věci. Skript může například odstranit soubory potřebné pro cluster.
>
> Akce skriptů, spusťte s kořenovými oprávněními. Ujistěte se, že rozumíte skript provede před každým jejím použitím clusteru.

Po použití skriptu na clusteru, změní stav clusteru z **systémem** k **přijato**, pak **konfigurace HDInsight**a nakonec zpět do  **Spuštění** úspěšné skriptů. Stav skriptu je zaznamenána v historii akcí skriptu a tyto informace můžete použít k určení, zda skript úspěšné nebo neúspěšné. Například `Get-AzureRmHDInsightScriptActionHistory` rutiny Powershellu lze použít k zobrazení stavu skriptu. Vrátí informace podobné následujícímu textu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]
> Pokud jste změnili heslo uživatele (správce) clusteru po vytvoření clusteru, skript, který akcí spuštěných na tomto clusteru může selhat. Pokud máte jakékoli trvalé akce se skripty této cílové uzly pracovního procesu, tyto skripty mohou selhat při škálování clusteru.

## <a name="example-script-action-scripts"></a>Příklady skriptů pro skript akce

Skripty akce skriptu umožňuje použít následující nástroje:

* portál Azure
* Azure PowerShell
* Klasické rozhraní příkazového řádku Azure
* Sada HDInsight .NET SDK

HDInsight poskytuje skriptů v clusterech HDInsight nainstalovat následující komponenty:

| Název | Skript |
| --- | --- |
| **Přidání účtu služby Azure Storage** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Zobrazit [přidat další úložiště pro HDInsight cluster](hdinsight-hadoop-add-storage.md). |
| **Instalace aplikace Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Zobrazit [instalace a použití Hue na HDInsight clusterů](hdinsight-hadoop-hue-linux.md). |
| **Nainstalovat Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Zobrazit [instalace a použití Presta na HDInsight clusterů](hdinsight-hadoop-install-presto.md). |
| **Nainstalovat Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Zobrazit [instalace a použití Solru na HDInsight clusterů](hdinsight-hadoop-solr-install-linux.md). |
| **Nainstalovat Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Zobrazit [instalace a použití Giraphu na HDInsight clusterů](hdinsight-hadoop-giraph-install-linux.md). |
| **Přednačíst knihovny Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Zobrazit [knihovny přidat Hive v clusterech HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| **Instalace nebo aktualizace Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Zobrazit [instalace nebo aktualizace Mono na HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Použití akce skriptu při vytváření clusteru

Tato část obsahuje příklady o různých způsobech skriptových akcí můžete použít při vytváření clusteru HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Použití akce skriptu při vytváření clusteru z webu Azure portal

1. Začněte s vytvářením clusteru, jak je popsáno v [vytváření clusterů Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Při vytváření clusteru, můžete přejít na __souhrn clusteru__ stránky. Z __souhrn clusteru__ stránky, vyberte __upravit__ odkaz __upřesňující nastavení__.

    ![Odkaz Upřesnit nastavení](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Z __upřesňující nastavení__ vyberte __akcí skriptů__. Z __akcí skriptů__ vyberte __+ nové odeslání__

    ![Novou akci skriptu odešlete](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Použití __vyberte skript__ položku a vyberte předem vytvořené skript. Chcete-li použít vlastní skript, vyberte __vlastní__ a pak zadejte __název__ a __URI skriptu Bash__ vašeho skriptu.

    ![Přidat skript ve formuláři vyberte skript](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Vyberte skript | Chcete-li použít vlastní skript, vyberte __vlastní__. V opačném případě vyberte jednu z poskytnutých skriptů. |
    | Název |Zadejte název akce skriptu. |
    | URI skriptu Bash |Zadejte identifikátor URI skriptu. |
    | HEAD/Worker/Zookeeper |Zadat uzly (**Head**, **pracovního procesu**, nebo **ZooKeeper**) podle kterého se spouští skript. |
    | Parametry |Zadejte parametry, pokud je to nutné skript. |

    Použití __zachovat tuto akci se skripty__ položku a ujistěte se, že skript používá během operace škálování.

5. Vyberte __vytvořit__ skript uložte. Pak můžete použít __+ odeslat novou__ přidáte další skript.

    ![Více akcí skriptů](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Po dokončení přidávání skriptů, použijte __vyberte__ tlačítko a pak __Další__ se vrátit na __souhrn clusteru__ oddílu.

3. Chcete-li vytvořit cluster, vyberte __vytvořit__ z __souhrn clusteru__ výběru.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Použití akce skriptu ze šablon Azure Resource Manageru

Akce skriptů je možné s šablonami Azure Resource Manageru. Příklad najdete v tématu [ https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/ ](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

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

Informace o tom, jak nasadit šablonu najdete v následujících dokumentech:

* [Nasazení prostředků pomocí šablon a Azure Powershellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Nasazení prostředků pomocí šablon a rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Použití akce skriptu při vytváření clusteru pomocí Azure Powershellu

V této části použijete [přidat AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction) rutiny vyvolání skriptů přizpůsobení clusteru. Než budete pokračovat, ujistěte se, že jste nainstalovali a nakonfigurovali Azure PowerShell. Informace o konfiguraci pracovní stanice ke spouštění rutin prostředí HDInsight PowerShell najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

Tento skript ukazuje, jak použít akci skriptu při vytváření clusteru pomocí prostředí PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Může trvat několik minut, než se cluster vytvoří.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Použití akce skriptu při vytváření clusteru ze sady HDInsight .NET SDK

Sady HDInsight .NET SDK obsahuje klientské knihovny, které usnadňuje práci s HDInsight z aplikace .NET. Ukázku kódu naleznete v tématu [clusterů v HDInsight pomocí sady .NET SDK se systémem Linux vytvořit](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Použít akci skriptu spuštěného clusteru

V této části se dozvíte, jak použít skript akce pro spuštěný cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Použít akci skriptu pro spuštěný cluster na webu Azure Portal

1. Z [webu Azure portal](https://portal.azure.com), vyberte svůj cluster HDInsight.

2. Přehled clusteru HDInsight, vyberte **akcí skriptů** dlaždici.

    ![Dlaždice akce skriptu](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Můžete také vybrat **všechna nastavení** a pak vyberte **akcí skriptů** v části nastavení.

3. V horní části Akce skriptu, vyberte **odeslat novou**.

    ![Přidat skript do spuštěného clusteru](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Použití __vyberte skript__ položku a vyberte předem vytvořené skript. Chcete-li použít vlastní skript, vyberte __vlastní__ a pak zadejte __název__ a __URI skriptu Bash__ vašeho skriptu.

    ![Přidat skript ve formuláři vyberte skript](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Vyberte skript | Chcete-li použít vlastní skript, vyberte __vlastní__. V opačném případě vyberte dodávaného skriptu. |
    | Název |Zadejte název akce skriptu. |
    | URI skriptu Bash |Zadejte identifikátor URI skriptu. |
    | HEAD/Worker/Zookeeper |Zadat uzly (**Head**, **pracovního procesu**, nebo **ZooKeeper**) podle kterého se spouští skript. |
    | Parametry |Zadejte parametry, pokud je to nutné skript. |

    Použití __zachovat tuto akci se skripty__ položku a ujistěte se, že skript je použit během operace škálování.

5. Nakonec použijte **vytvořit** tlačítko použít skript do clusteru.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Použít akci skriptu pro spuštěný cluster z prostředí Azure PowerShell

Než budete pokračovat, ujistěte se, že jste nainstalovali a nakonfigurovali Azure PowerShell. Informace o konfiguraci pracovní stanice ke spouštění rutin prostředí HDInsight PowerShell najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

Následující příklad ukazuje, jak použít akci skriptu spuštěného clusteru:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po dokončení této operace zobrazí informace podobné následujícímu textu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Použít akci skriptu pro spuštěný cluster z příkazového řádku Azure

Než budete pokračovat, ujistěte se, že máte nainstalované a nakonfigurované rozhraní Azure CLI. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure Classic](../cli-install-nodejs.md).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Pokud chcete přepnout do režimu Azure Resource Manageru, použijte následující příkaz na příkazovém řádku:

    ```bash
    azure config mode arm
    ```

2. Použijte následující postup k ověření ke svému předplatnému Azure.

    ```bash
    azure login
    ```

3. Chcete-li použít akci skriptu spuštěný cluster pomocí následujícího příkazu

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Pokud nezadáte parametry pro tento příkaz, zobrazí se výzva pro ně. Pokud skript zadáte s `-u` přijímá parametry, můžete je zadat pomocí `-p` parametru.

    Platný uzel typy jsou `headnode`, `workernode`, a `zookeeper`. Pokud skript by měl použít pro více typy uzlů, určit typy, které jsou oddělené středník (;). Například, `-n headnode;workernode`.

    Chcete-li zachovat tento skript, přidejte `--persistOnSuccess`. Je možné také zachovat tento skript později pomocí `azure hdinsight script-action persisted set`.

    Po dokončení úlohy byste obdržet výstup podobný následujícímu textu:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Použít akci skriptu spuštěného clusteru pomocí rozhraní REST API

Zobrazit [spustit skript akce na spuštěný cluster](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Použití akce skriptu do spuštěného clusteru ze sady HDInsight .NET SDK

Příklad použití skriptů do clusteru pomocí sady .NET SDK najdete v tématu [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Zobrazení historie, podporu a snížení úrovně skriptových akcí

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

1. Z [webu Azure portal](https://portal.azure.com), vyberte svůj cluster HDInsight.

2. Přehled clusteru HDInsight, vyberte **akcí skriptů** dlaždici.

    ![Dlaždice akce skriptu](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Můžete také vybrat **všechna nastavení** a pak vyberte **akcí skriptů** v části nastavení.

4. Historie skripty pro tento cluster se zobrazí v části Akce skriptu. Tyto informace zahrnují seznamu trvalých skriptů. Na snímku obrazovky níže uvidíte, že Solr se skript spustili na tomto clusteru. Snímek obrazovky se nezobrazují žádné trvalých skriptů.

    ![Oddíl akce skriptu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Výběr skriptu z historie zobrazí v části Vlastnosti tohoto skriptu. V horní části obrazovky můžete skript spustit znovu nebo zvyšte jeho úroveň.

    ![Vlastnosti akce skriptu](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Můžete také použít **...**  napravo od položky v části Akce skriptu k provádění akcí.

    ![Akcí skriptů... využití](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

| Použijte tento příkaz... | Chcete... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Načíst informace o trvalé akce se skripty |
| Get-AzureRmHDInsightScriptActionHistory |Načíst historii akcí skriptu u clusteru nebo podrobnosti pro konkrétní skript |
| Set-AzureRmHDInsightPersistedScriptAction |Podporuje ad hoc skriptů akci, která trvalá akce skriptu |
| Remove-AzureRmHDInsightPersistedScriptAction |Sníží úroveň trvalá akce skriptu do ad hoc akce |

> [!IMPORTANT]
> Pomocí `Remove-AzureRmHDInsightPersistedScriptAction` nevrátí zpět akce prováděné pomocí skriptu. Tato rutina odebere jenom příznak trvalý.

Následující ukázkový skript ukazuje pomocí rutin pro podporu a pak snížit úroveň skriptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-classic-cli"></a>Použití Azure Classic CLI

| Použijte tento příkaz... | Chcete... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Načtení seznamu akcí trvalého skriptu |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Načíst informace o konkrétní trvalá akce skriptu |
| `azure hdinsight script-action history list <clustername>` |Načíst historii akcí skriptu u clusteru |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Načíst informace o konkrétní skriptových akcí |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Podporuje ad hoc skriptů akci, která trvalá akce skriptu |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Sníží úroveň trvalá akce skriptu do ad hoc akce |

> [!IMPORTANT]
> Pomocí `azure hdinsight script-action persisted delete` nevrátí zpět akce prováděné pomocí skriptu. Tato rutina odebere jenom příznak trvalý.

### <a name="using-the-hdinsight-net-sdk"></a>Pomocí sady HDInsight .NET SDK

Příklad použití sady .NET SDK k načtení historie skriptu z clusteru, zvýšení úrovně nebo snížení úrovně skriptů, najdete v článku [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Tento příklad také ukazuje, jak nainstalovat aplikaci HDInsight pomocí sady .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora pro open source softwaru používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight využívá ekosystém open source technologií formátovaných kolem Hadoop. Microsoft Azure poskytuje obecné úroveň podpory pro open source technologie. Další informace najdete v tématu **rozsah podpory** část [nejčastější dotazy k podpoře Azure web](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované komponenty.

Existují dva druhy opensourcové komponenty, které jsou k dispozici ve službě HDInsight:

* **Integrované komponenty** – tyto součásti jsou předem nainstalované na clusterech HDInsight a poskytuje základní funkce clusteru. Například správce prostředků YARN, Hive dotazovacího jazyka (HiveQL) a knihovny Mahout patří do této kategorie. Úplný seznam součástí clusteru je k dispozici v [co je nového ve verzích clusterů Hadoop poskytovaných službou HDInsight](hdinsight-component-versioning.md).
* **Vlastní komponenty** -, jako uživatel clusteru, můžete nainstalovat nebo použít ve vašich úloh žádné součásti k dispozici v komunitě nebo vytvořené vámi.

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované. Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
>
> Vlastní komponenty získat obchodně přiměřenou podporu můžete-li dále řešit tento problém. Podpory Microsoftu může být schopni vyřešit problém nebo že vás může požádat o zapojení dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje například mnoho komunitním webům, které lze použít jako: [fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také projektů Apache mít projektovým webům na [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).

Služba HDInsight poskytuje několik způsobů, jak používat vlastní komponenty. Stejnou úroveň podpory platí bez ohledu na to, jak je komponenta použít nebo nainstalované v clusteru. Následující seznam popisuje nejběžnější způsoby, že vlastní komponenty je možné použít v clusterech HDInsight:

1. Odeslání úlohy - Hadoop nebo jiných typů úloh, které spouštění nebo použít vlastní komponenty můžete odeslat do clusteru.

2. Přizpůsobení clusteru – při vytváření clusteru, můžete zadat další nastavení a vlastních součástech, které jsou nainstalovány na uzlech clusteru.

3. Ukázky – pro oblíbené vlastní komponenty, Microsoft a ostatní může poskytnout ukázky použití těchto komponent v clusterech HDInsight. Tyto ukázky jsou k dispozici bez podpory.

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li zobrazit informacím protokolovaným v skriptových akcí můžete použít webové uživatelské rozhraní Ambari. Pokud skript selže při vytváření clusteru, protokoly jsou dostupné v přidružené ke clusteru výchozí účet úložiště. Tato část obsahuje informace o tom, jak načíst protokoly pomocí obou těchto možností.

### <a name="using-the-ambari-web-ui"></a>Pomocí webového uživatelského rozhraní Ambari

1. V prohlížeči přejděte na adresu https://CLUSTERNAME.azurehdinsight.net. CLUSTERNAME nahraďte názvem vašeho clusteru HDInsight.

    Po zobrazení výzvy zadejte název účtu správce (správce) a heslo pro cluster. Bude pravděpodobně nutné znovu zadat přihlašovací údaje správce do webového formuláře.

2. Na panelu v horní části stránky vyberte **ops** položka. Zobrazí se seznam současných a starších operací provedených v clusteru prostřednictvím Ambari.

    ![Panel uživatelského rozhraní Ambari web s operacemi vybrané](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Najděte položky, které mají **spustit\_customscriptaction** v **operace** sloupce. Tyto položky se vytvoří při spuštění skriptových akcí.

    ![Snímek obrazovky s operací](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Chcete-li zobrazit výstup STDOUT a STDERR, vyberte položku run\customscriptaction a přejít na nižší odkazy. Tento výstup se vygeneruje, když bude skript spuštěn a můžou obsahovat užitečné informace.

### <a name="access-logs-from-the-default-storage-account"></a>Přístup k protokolům z výchozí účet úložiště

Pokud se vytvoření clusteru se nepovedlo kvůli chybě skriptu, protokoly se ukládají do účtu úložiště clusteru.

* Protokoly úložiště najdete na adrese `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Snímek obrazovky s operací](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    V tomto adresáři protokoly uspořádané odděleně pro hlavní uzel, workernode a uzly zookeeper. Tady je několik příkladů:

    * **Hlavní uzel** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Pracovní uzel** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Uzel zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Všechny stdout a stderr hostitele se nahraje do účtu úložiště. Existuje jedna **výstupní -\*.txt** a **chyby -\*.txt** pro každou akci se skripty. *.Txt výstupní soubor obsahuje informace o identifikátoru URI skriptu, který je teď spustit na hostiteli. Následující text je příkladem těchto informací:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Je možné, opakovaně vytvořit cluster akce skriptu se stejným názvem. V takovém případě lze rozlišit relevantní protokoly podle názvu složky datum. Například strukturu složek pro vytvořil se cluster (clusteru) na různá data zobrazí podobná následující položky protokolu:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Pokud vytvoříte cluster akce skriptu se stejným názvem v jednom dni, můžete k identifikaci příslušných protokolových souborů jedinečnou předponu.

* Pokud vytvoříte cluster v 12:00 AM (půlnoc), je možné, že soubory protokolu span trvajícího dva dny. V takovém případě se zobrazí dvě složky jiné datum pro stejného clusteru.

* Nahrávají se soubory protokolu na výchozí kontejner může trvat až 5 minut, zejména u velkých clusterech. Ano Pokud chcete přístup k protokolům, byste neměli odstraňovat okamžitě clusteru selhání akce skriptu.

### <a name="ambari-watchdog"></a>Ambari sledovacího zařízení

> [!WARNING]
> Neměňte heslo pro Ambari sledovacích (hdinsightwatchdog) ve vašem clusteru HDInsight se systémem Linux. Změna hesla pro tento účet dělí možnost spouštět nové akce skriptu na clusteru HDInsight.

### <a name="cant-import-name-blobservice"></a>Nelze importovat název BlobService

__Příznaky__: selhání akce skriptu. Při operaci zobrazení Ambari, zobrazí se text podobný následující chybě:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Příčina__: k této chybě dochází, pokud upgradujete klienta služby Azure Storage pro Python, která je součástí clusteru HDInsight. HDInsight očekává, že klient služby Azure Storage 0.20.0.

__Rozlišení__: Chcete-li vyřešit tuto chybu, ručně připojit do každého uzlu clusteru pomocí `ssh` a znovu nainstalovat verzi klienta správné úložiště pomocí následujícího příkazu:

```bash
sudo pip install azure-storage==0.20.0
```

Informace o připojení ke clusteru pomocí SSH najdete v tématu [použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Historie nezobrazí skripty používané při vytváření clusteru

Pokud váš cluster byl vytvořen ještě před 15. března 2016, nemusíte vidět položku v historii akcí skriptu. Změna velikosti clusteru způsobí, že skripty se zobrazí v historii akcí skriptu.

Existují dvě výjimky:

* Pokud váš cluster byl vytvořen před 1. září 2015. Toto datum je, když byly zavedeny skriptových akcí. Libovolný cluster vytvořen před tímto datem nelze použít akce se skripty pro vytvoření clusteru.

* Pokud jste použili při vytváření clusteru více akcí skriptů a používá stejný název pro několik skriptů, nebo stejný název, stejný identifikátor URI, ale jiné parametry pro několik skriptů. V těchto případech se zobrazí následující chybová zpráva:

    Žádný nový skript akce může být spuštěno na tento cluster z důvodu konfliktních názvů skript ve stávající skripty. Vytvořit názvy ve skriptu na clusteru musí být všechny jedinečné. Stávající skripty se spustil na změny velikosti.

## <a name="next-steps"></a>Další postup

* [Vývoj akcí skriptů skript pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalace a použití Solru na clusterech HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalace a použití Giraphu na clusterech HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Přidat další úložiště clusteru služby HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fáze při vytváření clusteru"
