---
title: Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů
description: Pomocí akcí skriptů můžete přidat vlastní součásti do clusterů HDInsight se systémem Linux. Akce skriptů jsou skripty bash, které se dají použít k přizpůsobení konfigurace clusteru nebo přidání dalších služeb a nástrojů jako odstín, Solr nebo R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: c6f55b40b3ee077b81a3cdd6f3add7a2cad23f95
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70809921"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů

Azure HDInsight poskytuje metodu konfigurace nazvanou **akce skriptu** , která vyvolá vlastní skripty pro přizpůsobení clusteru. Tyto skripty slouží k instalaci dalších komponent a změně nastavení konfigurace. Akce skriptu lze použít během nebo po vytvoření clusteru.

Akce skriptu je také možné publikovat do Azure Marketplace jako aplikace HDInsight. Další informace o aplikacích HDInsight najdete v tématu [publikování aplikace HDInsight v Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Oprávnění

U clusteru HDInsight připojeného k doméně jsou k dispozici dvě oprávnění Apache Ambari, která se vyžadují při použití akcí skriptu s clusterem:

* **AMBARI. SPUSŤTE\_VLASTNÍ\_PŘÍKAZ**. Role správce Ambari má ve výchozím nastavení toto oprávnění.
* **CLUSTER. SPUSŤTE\_VLASTNÍ\_PŘÍKAZ**. Ve výchozím nastavení má Správce clusteru HDInsight i správce Ambari toto oprávnění.

Další informace o práci s oprávněními s HDInsight připojenými k doméně najdete v tématu [Správa clusterů HDInsight pomocí balíček zabezpečení podniku](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Řízení přístupu

Pokud nejste správcem nebo vlastníkem svého předplatného Azure, váš účet musí mít k této skupině prostředků, která obsahuje cluster HDInsight, aspoň přístup přispěvatele.

Pokud vytvoříte cluster HDInsight, musí mít někdo s minimálně přístupem Přispěvatel k předplatnému Azure předtím zaregistrovat poskytovatele pro HDInsight. Registrace zprostředkovatele se provede, když uživatel s přístupem Přispěvatel poprvé vytvoří prostředek v rámci příslušného předplatného. Dá se taky dělat bez vytvoření prostředku, pokud [zaregistrujete poskytovatele pomocí REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Získejte další informace o práci se správou přístupu:

* [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
* [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Porozumění akcím skriptů

Akce skriptu je skript bash, který běží na uzlech v clusteru HDInsight. Následující vlastnosti a funkce skriptových akcí jsou tyto:

* Musí být uložené na identifikátoru URI, který je přístupný z clusteru HDInsight. Níže jsou možná umístění úložiště:
    
    * Pro běžné clustery:
    
      * ADLS Gen1: Služba HDInsight instančního objektu používá pro přístup Data Lake Storage musí mít ke skriptu oprávnění ke čtení. Formát identifikátoru URI pro skripty uložené v Data Lake Storage Gen1 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`je.
      
      * Objekt BLOB v účtu Azure Storage, který je buď primárním nebo dalším účtem úložiště pro cluster HDInsight. HDInsight získá přístup k oběma těmto typům účtů úložiště během vytváření clusteru.

        > [!IMPORTANT]  
        > Neotáčejte klíč úložiště na tomto účtu Azure Storage, protože to způsobí selhání dalších akcí skriptu se skripty, které jsou tam uložené.

      * Veřejná služba sdílení souborů přístupná prostřednictvím cest http://. Příklady jsou Azure Blob, GitHub, OneDrive.

        Příklady identifikátorů URI najdete v tématu [Příklady skriptů akcí](#example-script-action-scripts)skriptů.

     * Pro clustery s protokolem ESP:
         
         * Jsou podporovány identifikátory URI wasb://nebo wasbs://nebo http [s]://.
            
* Dá se omezit na spouštění jenom na určitých typech uzlů. Příklady jsou hlavní uzly nebo pracovní uzly.

* Může být trvalá nebo ad hoc.

    Trvalé skripty se používají k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalý skript může také při operacích škálování použít změny v jiném typu uzlu. Příkladem je hlavní uzel.

  > [!IMPORTANT]  
  > Trvalé akce skriptu musí mít jedinečný název.

    Ad hoc skripty nejsou trvalé. Nejsou aplikovány na pracovní uzly přidané do clusteru po spuštění skriptu. Potom můžete přenést skript ad hoc na trvalý skript nebo snížit úroveň trvalého skriptu na skript ad hoc.

  > [!IMPORTANT]  
  > Akce skriptu použité při vytváření clusteru se automaticky uchovávají.
  >
  > Skripty, které selžou, nejsou trvalé, i když výslovně označíte, že by měly být.

* Může přijmout parametry, které skript používá během provádění.

* Spusťte s oprávněními na úrovni root na uzlech clusteru.

* Dá se použít prostřednictvím Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure Classic nebo sady SDK HDInsight .NET.

Cluster uchovává historii všech skriptů, které byly spuštěny. Historie pomáhá v případě, že potřebujete najít ID skriptu pro operace zvýšení nebo snížení úrovně.

> [!IMPORTANT]  
> Neexistuje žádný automatický způsob, jak vrátit zpět změny provedené akcí skriptu. Buď ručně převratte změny, nebo zadejte skript, který je obrátí.

### <a name="script-action-in-the-cluster-creation-process"></a>Akce skriptu v procesu vytváření clusteru

Akce skriptu používané při vytváření clusteru se mírně liší od akcí skriptů spuštěných v existujícím clusteru:

* Skript se automaticky uchová.

* Selhání ve skriptu může způsobit selhání procesu vytváření clusteru.

Následující diagram ilustruje, kdy se akce skriptu spustí během procesu vytváření:

![Přizpůsobení a fáze clusteru HDInsight během vytváření clusteru][img-hdi-cluster-states]

Skript se spustí při konfiguraci HDInsight. Skript běží paralelně na všech zadaných uzlech v clusteru. Spouští se s oprávněními root na uzlech.

> [!NOTE]  
> Můžete provádět operace, jako je zastavení a spuštění služeb, včetně služeb souvisejících s Apache Hadoop. Pokud zastavíte služby, ujistěte se, že je před dokončením skriptu spuštěná služba Ambari a další služby související se systémem Hadoop. Tyto služby se vyžadují k úspěšnému určení stavu a stavu clusteru, když se vytváří.


Během vytváření clusteru můžete použít mnoho akcí skriptu najednou. Tyto skripty jsou vyvolány v pořadí, v jakém byly zadány.

> [!IMPORTANT]  
> Akce skriptu musí být dokončeny během 60 minut nebo vypršel časový limit. Při zřizování clusteru se skript spouští souběžně s jinými procesy nastavení a konfigurace. Soutěž na prostředky, jako je čas procesoru nebo šířka pásma sítě, může způsobit, že se dokončení skriptu bude trvat déle, než ve vývojovém prostředí.
>
> Aby se minimalizoval čas potřebný ke spuštění skriptu, vyhněte se úlohám, jako je stahování a kompilování aplikací ze zdroje. Předem zkompilujte aplikace a uložte binární soubor do Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Akce skriptu na spuštěném clusteru

Selhání skriptu spuštěného na již běžícím clusteru automaticky nezpůsobí změnu clusteru na stav selhání. Po dokončení skriptu se cluster musí vrátit do běžícího stavu.

> [!IMPORTANT]  
> I v případě, že cluster má spuštěný stav, může skript obsahovat poškozené věci. Skript může například odstraňovat soubory, které cluster potřebuje.
>
> Akce skriptů se spouští s oprávněními root. Ujistěte se, že rozumíte tomu, co skript dělá předtím, než ho použijete pro váš cluster.

Když použijete skript na cluster, stav clusteru se změní ze **spuštěno** na **přijato**. Pak se změní na **konfiguraci HDInsight** a nakonec zpátky na **spuštěno** pro úspěšné skripty. Stav skriptu je zaznamenán v historii akcí skriptu. Tyto informace vám sdělí, jestli byl skript úspěšný nebo neúspěšný. Například rutina `Get-AzHDInsightScriptActionHistory` PowerShellu zobrazuje stav skriptu. Vrátí informace podobné následujícímu textu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Pokud po vytvoření clusteru změníte uživatele clusteru, správce, heslo, akce skriptu spuštěné proti tomuto clusteru můžou selhat. Pokud máte trvalé akce skriptů, které cílí na pracovní uzly, můžou se tyto skripty při škálování clusteru nezdařily.

## <a name="example-script-action-scripts"></a>Ukázkové skripty akcí skriptů

Skripty akcí skriptů lze použít prostřednictvím následujících nástrojů:

* Azure Portal
* Azure PowerShell
* Rozhraní příkazového řádku Azure Classic
* Sada An HDInsight .NET SDK

HDInsight poskytuje skripty pro instalaci následujících komponent do clusterů HDInsight:

| Name | Skript |
| --- | --- |
| Přidat účet Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Viz [Přidání dalších účtů úložiště do služby HDInsight](hdinsight-hadoop-add-storage.md). |
| Nainstalovat Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Viz [instalace a použití odstínu v clusterech HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Nainstalovat Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Viz [instalace Apache Giraph v clusterech HDInsight Hadoop](hdinsight-hadoop-giraph-install-linux.md). |
| Předběžné načtení knihoven podregistru |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Viz [Přidání vlastních knihoven Apache Hive při vytváření clusteru HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Použití akce skriptu během vytváření clusteru

V této části jsou vysvětleny různé způsoby, jak můžete při vytváření clusteru HDInsight použít akce se skripty.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Použití akce skriptu během vytváření clusteru z Azure Portal

1. Začněte vytvářet cluster, jak je popsáno v tématu [Nastavení clusterů v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md). Během vytváření clusteru přijdete na stránku __souhrnu clusteru__ . Na stránce __Souhrn clusteru__ vyberte odkaz __Upravit__ pro __upřesňující nastavení__.

    ![Odkaz Rozšířená nastavení](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. V části __Upřesnit nastavení__ vyberte __akce skriptu__. V části __akce skriptu__ vyberte __+ Odeslat novou__.

    ![Odeslat novou akci skriptu](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. K výběru předpřipraveného skriptu použijte položku __Vybrat skript__ . Chcete-li použít vlastní skript, vyberte možnost __vlastní__. Pak zadejte __název__ a __identifikátor URI pro skript bash__ pro váš skript.

    ![Přidání skriptu do formuláře pro výběr skriptu](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Value |
    | --- | --- |
    | Vyberte skript | Chcete-li použít vlastní skript, vyberte možnost __vlastní__. V opačném případě vyberte jeden ze zadaných skriptů. |
    | Name |Zadejte název akce skriptu. |
    | URI skriptu Bash |Zadejte identifikátor URI skriptu. |
    | Vedoucí/pracovní proces/ZooKeeper |Zadejte uzly, na kterých je spuštěn skript: **HEAD**, **Worker**, nebo **ZooKeeper**. |
    | Parametry |Zadejte parametry, pokud to skript vyžaduje. |

    Použijte položku __zachovat tuto položku akce skriptu__ a ujistěte se, že je skript použit během operace škálování.

5. Vyberte __vytvořit__ a skript se uloží. Potom můžete k přidání dalšího skriptu použít možnost __+ Odeslat novou__ .

    ![Několik akcí skriptů](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Až budete s přidáváním skriptů hotovi, vyberte tlačítko __Vybrat__ a potom klikněte na tlačítko __Další__ a vraťte se do části __Souhrn clusteru__ .

3. Pokud chcete vytvořit cluster, vyberte __vytvořit__ z výběru __souhrnu clusteru__ .

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Použití akce skriptu z Azure Resource Manager šablon

Akce skriptu lze použít s Azure Resource Manager šablon. Příklad najdete v tématu [Vytvoření clusteru HDInsight Linux a spuštění akce skriptu](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

V tomto příkladu je akce skriptu přidána pomocí následujícího kódu:

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

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Použití akce skriptu během vytváření clusteru z Azure PowerShell

V této části pomocí rutiny [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) vyvoláte skripty pro přizpůsobení clusteru. Než začnete, ujistěte se, že jste nainstalovali a nakonfigurovali Azure PowerShell. Chcete-li použít tyto příkazy PowerShellu, potřebujete [AZ Module](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující skript ukazuje, jak použít akci skriptu při vytváření clusteru pomocí prostředí PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Může to trvat několik minut, než se cluster vytvoří.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Použití akce skriptu během vytváření clusteru ze sady HDInsight .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny, které usnadňují práci s HDInsight z aplikace .NET. Ukázku kódu najdete v tématu [vytváření clusterů se systémem Linux v HDInsight pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Použití akce skriptu u běžícího clusteru

V této části se dozvíte, jak aplikovat akce skriptů na běžící cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Použití akce skriptu u běžícího clusteru z Azure Portal

Přejít na [Azure Portal](https://portal.azure.com):

1. V nabídce vlevo vyberte **všechny služby**.

1. V části **Analýza**vyberte **clustery HDInsight**.

1. V seznamu vyberte svůj cluster, ve kterém se otevře výchozí zobrazení.

1. Ve výchozím zobrazení vyberte v části **Nastavení**možnost **akce skriptu**.

1. V horní části stránky **akce skriptu** vyberte **+ Odeslat novou**.

    ![Přidání skriptu do běžícího clusteru](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. K výběru předpřipraveného skriptu použijte položku __Vybrat skript__ . Chcete-li použít vlastní skript, vyberte možnost __vlastní__. Pak zadejte __název__ a __identifikátor URI pro skript bash__ pro váš skript.

    ![Přidání skriptu do formuláře pro výběr skriptu](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Value |
    | --- | --- |
    | Vyberte skript | Chcete-li použít vlastní skript, vyberte možnost __vlastní__. V opačném případě vyberte poskytnutý skript. |
    | Name |Zadejte název akce skriptu. |
    | URI skriptu Bash |Zadejte identifikátor URI skriptu. |
    | Vedoucí/pracovní proces/Zookeeper |Zadejte uzly, na kterých je spuštěn skript: **HEAD**, **Worker**, nebo **ZooKeeper**. |
    | Parametry |Zadejte parametry, pokud to skript vyžaduje. |

    Pomocí položky __zachovat tuto položku akce skriptu__ zajistěte, aby byl skript použit během operací škálování.

5. Nakonec vyberte tlačítko **vytvořit** , pokud chcete skript použít pro cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Použití akce skriptu pro spuštěný cluster z Azure PowerShell

Chcete-li použít tyto příkazy PowerShellu, potřebujete [AZ Module](https://docs.microsoft.com/powershell/azure/overview).

Následující příklad ukazuje, jak použít akci skriptu na běžící cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po dokončení operace obdržíte informace podobné následujícímu textu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Použití akce skriptu u běžícího clusteru z Azure CLI

Než začnete, ujistěte se, že jste nainstalovali a nakonfigurovali rozhraní příkazového řádku Azure CLI. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure Classic](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Přepněte do režimu Azure Resource Manager:

    ```bash
    azure config mode arm
    ```

2. Ověření u vašeho předplatného Azure:

    ```bash
    azure login
    ```

3. Použít akci skriptu na běžící cluster:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Pokud vynecháte parametry pro tento příkaz, budete vyzváni k jejich zadání. Pokud skript, který zadáte `-u` , přijímá parametry, můžete je zadat `-p` pomocí parametru.

    Platné typy uzlů jsou `headnode`, `workernode`a `zookeeper`. Pokud má být skript použit pro několik typů uzlů, určete typy oddělené středníkem `;`. Například, `-n headnode;workernode`.

    Pro zachování skriptu přidejte `--persistOnSuccess`. Skript můžete také zachovat později pomocí `azure hdinsight script-action persisted set`.

    Po dokončení úlohy se zobrazí výstup podobný následujícímu textu:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Použití akce skriptu u běžícího clusteru pomocí REST API

Viz téma [REST API clusteru ve službě Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Použití akce skriptu u běžícího clusteru ze sady HDInsight .NET SDK

Příklad použití sady .NET SDK pro použití skriptů v clusteru najdete v tématu [použití akce skriptu proti běžícímu clusteru HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)se systémem Linux.

## <a name="view-history-and-promote-and-demote-script-actions"></a>Zobrazení historie a propagace a snížení úrovně akcí skriptů

### <a name="the-azure-portal"></a>Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **všechny služby**.

1. V části **Analýza**vyberte **clustery HDInsight**.

1. V seznamu vyberte svůj cluster, ve kterém se otevře výchozí zobrazení.

1. Ve výchozím zobrazení vyberte v části **Nastavení**možnost **akce skriptu**.

4. Historie skriptů pro tento cluster se zobrazí v oddílu akce skriptu. Tyto informace obsahují seznam trvalých skriptů. Na následujícím snímku obrazovky vidíte, že skript Solr byl spuštěn v tomto clusteru. Snímek obrazovky nezobrazuje žádné trvalé skripty.

    ![Akce skriptů](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Výběrem skriptu z historie zobrazíte oddíl **vlastnosti** pro tento skript. V horní části obrazovky můžete skript znovu spustit nebo ho povýšit.

    ![Akce skriptu, vlastnosti](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Můžete také vybrat tři tečky, **...** vpravo od položek v oddílu akce skriptu k provedení akcí.

    ![Akce skriptu, tři tečky](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| rutiny | Funkce |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Načte informace o trvalých akcích skriptu. |
| `Get-AzHDInsightScriptActionHistory` |Načtěte historii akcí skriptu použitých pro cluster nebo podrobnosti konkrétního skriptu. |
| `Set-AzHDInsightPersistedScriptAction` |Povýší akci skriptu ad hoc na trvalou akci skriptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Snížit úroveň trvalé akce skriptu na akci ad hoc. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction`nevrátí zpět akce provedené skriptem. Tato rutina odebere jenom trvalý příznak.

Následující příklad skriptu ukazuje použití rutin pro povýšení a pak snížení úrovně skriptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Rozhraní příkazového řádku Azure Classic

| rutiny | Funkce |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Načte seznam trvalých akcí skriptu. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Načte informace o konkrétní trvalé akci skriptu. |
| `azure hdinsight script-action history list <clustername>` |Načtěte historii akcí skriptu použitých v clusteru. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Načte informace o konkrétní akci skriptu. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Povýší akci skriptu ad hoc na trvalou akci skriptu. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Snížit úroveň trvalé akce skriptu na akci ad hoc. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete`nevrátí zpět akce provedené skriptem. Tato rutina odebere jenom trvalý příznak.

### <a name="the-hdinsight-net-sdk"></a>Sada HDInsight .NET SDK

Příklad použití sady .NET SDK k načtení historie skriptu z clusteru, zvýšení úrovně nebo snížení úrovně skriptů najdete v tématu [použití akce skriptu proti běžícímu clusteru HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)se systémem Linux.

> [!NOTE]  
> Tento příklad také ukazuje, jak nainstalovat aplikaci HDInsight pomocí sady .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora open source softwaru používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight používá ekosystém open source technologií vytvořených v Apache Hadoop. Microsoft Azure poskytuje obecnou úroveň podpory pro open source technologie. Další informace najdete v části věnované **oboru podpory** v tématu [Nejčastější dotazy týkající se podpory Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované součásti.

Ve službě HDInsight jsou k dispozici dva typy open source komponent:

* **Předdefinované komponenty**. Tyto součásti jsou předem nainstalovány v clusterech HDInsight a poskytují základní funkce clusteru. Do této kategorie patří tyto komponenty:

  * [Apache HADOOP nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * Dotazovací jazyk podregistru [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/) 
    
    Úplný seznam součástí clusteru je k dispozici v části [Jaké jsou Apache Hadoop komponenty a verze, které jsou k dispozici v HDInsight?](hdinsight-component-versioning.md)

* **Vlastní součásti**. Jako uživatel clusteru můžete v rámci své úlohy nainstalovat nebo použít všechny součásti dostupné ve komunitě nebo vytvořené vámi.

> [!WARNING]  
> Součásti dodávané s clusterem HDInsight jsou plně podporované. Podpora Microsoftu pomáhá izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. Je možné, že podpora Microsoftu problém vyřešit. Případně vás můžou požádat, abyste provedli dostupné kanály pro technologie Open Source, kde se nachází hlubokou odbornost této technologie. Je možné použít mnoho webů komunity. Příklady jsou [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) a [Stack Overflow](https://stackoverflow.com). 
>
> Projekty Apache také obsahují projektové weby na [webu Apache](https://apache.org). Příkladem je [Hadoop](https://hadoop.apache.org/).

Služba HDInsight nabízí několik způsobů použití vlastních komponent. Stejná úroveň podpory platí bez ohledu na to, jak se součást používá nebo je v clusteru nainstalovaná. Následující seznam popisuje nejběžnější způsoby použití vlastních komponent v clusterech HDInsight:

1. **Odeslání úlohy**. Cluster Hadoop nebo jiné typy úloh, které spouštějí nebo používají vlastní komponenty, lze odeslat do clusteru.

2. **Přizpůsobení clusteru**. Během vytváření clusteru můžete zadat další nastavení a vlastní součásti, které jsou nainstalované na uzlech clusteru.

3. **Ukázky**. V případě oblíbených vlastních komponent mohou společnosti Microsoft a jiné poskytnout ukázky, jak lze tyto komponenty použít v clusterech HDInsight. Tyto ukázky jsou k dispozici bez podpory.

## <a name="troubleshooting"></a>Řešení potíží

K zobrazení informací protokolovaných akcemi skriptů můžete použít webové uživatelské rozhraní Ambari. Pokud se skript během vytváření clusteru nezdařil, jsou protokoly dostupné taky ve výchozím účtu úložiště přidruženém ke clusteru. V této části najdete informace o tom, jak protokoly načíst pomocí obou těchto možností.

### <a name="the-apache-ambari-web-ui"></a>Webové uživatelské rozhraní Apache Ambari

1. V prohlížeči, přejít na https://CLUSTERNAME.azurehdinsight.net. Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.

    Po zobrazení výzvy zadejte název účtu správce, **správce**a heslo pro cluster. Je možné, že bude nutné znovu zadat přihlašovací údaje správce ve webovém formuláři.

2. Na panelu v horní části stránky vyberte položku **OPS** . V seznamu se zobrazí aktuální a předchozí operace prováděné na clusteru prostřednictvím Ambari.

    ![Panel webového uživatelského rozhraní Ambari s vybranou operací Operations](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Vyhledá položky, které mají ve sloupci **Operations** **customscriptaction běžet\_** . Tyto položky jsou vytvořeny při spuštění akcí skriptu.

    ![Snímek obrazovky s operacemi](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Chcete-li zobrazit výstup **stdout** a **stderr** , vyberte položku **run\customscriptaction** a přejděte k podrobnostem prostřednictvím odkazů. Tento výstup se generuje při spuštění skriptu a může mít užitečné informace.

### <a name="access-logs-from-the-default-storage-account"></a>Přístup k protokolům z výchozího účtu úložiště

Pokud se vytvoření clusteru nepovede kvůli chybě skriptu, protokoly se uchovávají v účtu úložiště clusteru.

* Protokoly úložiště jsou k dispozici `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`na adrese.

    ![Protokoly akcí skriptů](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    V rámci tohoto adresáře jsou protokoly uspořádány samostatně pro **hlavnímu uzlu**, **pracovní uzel**a **Zookeeper uzel**. Podívejte se na následující příklady:

    * **Hlavnímu uzlu**:`<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Pracovní uzel**:`<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Uzel Zookeeper**:`<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Všechny **stdout** a **stderr** odpovídajícího hostitele se nahrají do účtu úložiště. Pro každou akci skriptu existuje jeden **výstup\*-. txt** a **Errors\*. txt** . Soubor **Output-*. txt** obsahuje informace o identifikátoru URI skriptu, který byl spuštěn na hostiteli. Následující text je příkladem těchto informací:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Je možné opakovaně vytvořit cluster akcí skriptu se stejným názvem. V takovém případě můžete rozlišovat příslušné protokoly na základě názvu složky **data** . Například struktura složek pro cluster **mycluster**vytvořená v různých datech vypadá podobně jako v následujících záznamech protokolu:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04``\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Pokud vytvoříte cluster akcí skriptu se stejným názvem ve stejném dni, můžete k identifikaci relevantních souborů protokolu použít jedinečnou předponu.

* Pokud vytvoříte cluster poblíž 12:00. půlnoci, je možné, že soubory protokolu jsou rozloženy do dvou dnů. V takovém případě uvidíte dvě různé složky kalendářních dat pro stejný cluster.

* Nahrávání souborů protokolu do výchozího kontejneru může trvat až pět minut, zejména u velkých clusterů. Pokud tedy chcete získat přístup k protokolům, neměli byste okamžitě odstranit cluster, pokud dojde k chybě skriptu.

### <a name="ambari-watchdog"></a>Sledovací zařízení Ambari

> [!WARNING]  
> Neměňte heslo pro sledovací zařízení Ambari, hdinsightwatchdog v clusteru HDInsight se systémem Linux. Změna hesla pro tento účet ukončí schopnost spouštět nové akce skriptu v clusteru HDInsight.

### <a name="cant-import-name-blobservice"></a>Nelze importovat název BlobService

__Příznaky__. Akce skriptu se nezdařila. Při zobrazení operace v Ambari se zobrazí text podobný následující chybě:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Příčina__: K této chybě dojde, když upgradujete klienta Python Azure Storage, který je součástí clusteru HDInsight. HDInsight očekává Azure Storage 0.20.0 klienta.

__Řešení__. Chcete-li tuto chybu vyřešit, připojte se ručně k jednotlivým uzlům clusteru pomocí nástroje `ssh`. Spusťte následující příkaz, který znovu nainstaluje správnou verzi klienta úložiště:

```bash
sudo pip install azure-storage==0.20.0
```

Informace o připojení ke clusteru pomocí SSH najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historie nezobrazuje skripty používané při vytváření clusteru.

Pokud byl cluster vytvořen před 15. března 2016, nemusí se položka zobrazit v historii akcí skriptu. Změna velikosti clusteru způsobí, že se skripty objeví v historii akcí skriptu.

Existují dvě výjimky:

* Cluster byl vytvořen před 1. září 2015. Toto datum je v případě, že byly zavedeny akce skriptu. Žádný cluster vytvořený před tímto datem nedokázal použít akce skriptu pro vytvoření clusteru.

* Během vytváření clusteru jste použili několik akcí skriptů. Nebo jste použili stejný název pro více skriptů nebo stejný název, stejný identifikátor URI, ale různé parametry pro více skriptů. V těchto případech se zobrazí následující chyba:

    V tomto clusteru se nedají spouštět žádné nové akce skriptu kvůli konfliktům názvů skriptů v existujících skriptech. Názvy skriptů zadané při vytváření clusteru musí být jedinečné. Existující skripty jsou spouštěny při změně velikosti.

## <a name="next-steps"></a>Další postup

* [Vývoj skriptů akcí skriptu pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalace a použití Apache Giraph v clusterech HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Přidání dalšího úložiště do clusteru HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fáze při vytváření clusteru"
