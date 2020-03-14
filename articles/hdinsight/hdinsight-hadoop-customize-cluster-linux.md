---
title: Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů
description: Pomocí akcí skriptů můžete přidat vlastní součásti do clusterů HDInsight. Akce skriptů jsou skripty bash, které se dají použít k přizpůsobení konfigurace clusteru nebo přidání dalších služeb a nástrojů jako odstín, Solr nebo R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272497"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů

Azure HDInsight poskytuje metodu konfigurace nazvanou **akce skriptů** , které vyvolávají vlastní skripty pro přizpůsobení clusteru. Tyto skripty slouží k instalaci dalších komponent a změně nastavení konfigurace. Akce skriptu lze použít během nebo po vytvoření clusteru.

Akce skriptu je také možné publikovat do Azure Marketplace jako aplikace HDInsight. Další informace o aplikacích HDInsight najdete v tématu [publikování aplikace HDInsight v Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Oprávnění

U clusteru HDInsight připojeného k doméně jsou k dispozici dvě oprávnění Apache Ambari, která se vyžadují při použití akcí skriptu s clusterem:

* **AMBARI. Spusťte příkaz\_vlastní\_** . Role správce Ambari má ve výchozím nastavení toto oprávnění.
* **Cluster. Spusťte příkaz\_vlastní\_** . Ve výchozím nastavení má Správce clusteru HDInsight i správce Ambari toto oprávnění.

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

      * ADLS Gen1: instanční objekt služby HDInsight používá pro přístup k Data Lake Storage musí mít ke skriptu oprávnění ke čtení. Formát identifikátoru URI pro skripty uložené v Data Lake Storage Gen1 je `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Objekt BLOB v účtu Azure Storage, který je buď primárním nebo dalším účtem úložiště pro cluster HDInsight. HDInsight získá přístup k oběma těmto typům účtů úložiště během vytváření clusteru.

        > [!IMPORTANT]  
        > Neotáčejte klíč úložiště na tomto účtu Azure Storage, protože to způsobí selhání dalších akcí skriptu se skripty, které jsou tam uložené.

      * Veřejná služba sdílení souborů přístupná prostřednictvím cest http://. Příklady jsou Azure Blob, GitHub, OneDrive. Příklady identifikátorů URI najdete v tématu [Příklady skriptů akcí](#example-script-action-scripts)skriptů.

     * Pro clustery s protokolem ESP se podporují wasb://nebo wasbs://nebo http [s]://identifikátory URI.

* Dá se omezit na spouštění jenom na určitých typech uzlů. Příklady jsou hlavní uzly nebo pracovní uzly.

* Může být trvalá nebo ad hoc.

    Trvalé akce skriptu musí mít jedinečný název. Trvalé skripty se používají k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalý skript může také při operacích škálování použít změny v jiném typu uzlu. Příkladem je hlavní uzel.

    Ad hoc skripty nejsou trvalé. Akce skriptu použité při vytváření clusteru se automaticky uchovávají. Nejsou aplikovány na pracovní uzly přidané do clusteru po spuštění skriptu. Potom můžete přenést skript ad hoc na trvalý skript nebo snížit úroveň trvalého skriptu na skript ad hoc. Skripty, které selžou, nejsou trvalé, i když výslovně označíte, že by měly být.

* Může přijmout parametry, které skript používá během provádění.

* Spusťte s oprávněními na úrovni root na uzlech clusteru.

* Dá se použít prostřednictvím Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo sady HDInsight .NET SDK.

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

Můžete provádět operace, jako je zastavení a spuštění služeb, včetně služeb souvisejících s Apache Hadoop. Pokud zastavíte služby, ujistěte se, že je před dokončením skriptu spuštěná služba Ambari a další služby související se systémem Hadoop. Tyto služby se vyžadují k úspěšnému určení stavu a stavu clusteru, když se vytváří.

Během vytváření clusteru můžete použít mnoho akcí skriptu najednou. Tyto skripty jsou vyvolány v pořadí, v jakém byly zadány.

> [!IMPORTANT]  
> Akce skriptu musí být dokončeny během 60 minut nebo vypršel časový limit. Při zřizování clusteru se skript spouští souběžně s jinými procesy nastavení a konfigurace. Soutěž na prostředky, jako je čas procesoru nebo šířka pásma sítě, může způsobit, že se dokončení skriptu bude trvat déle, než ve vývojovém prostředí.
>
> Aby se minimalizoval čas potřebný ke spuštění skriptu, vyhněte se úlohám, jako je stahování a kompilování aplikací ze zdroje. Předem zkompilujte aplikace a uložte binární soubor do Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Akce skriptu na spuštěném clusteru

Selhání skriptu spuštěného na již běžícím clusteru automaticky nezpůsobí změnu clusteru na stav selhání. Po dokončení skriptu se cluster musí vrátit do běžícího stavu. I v případě, že cluster má spuštěný stav, může skript obsahovat poškozené věci. Skript může například odstraňovat soubory, které cluster potřebuje.

Akce skriptů se spouští s oprávněními root. Ujistěte se, že rozumíte tomu, co skript dělá předtím, než ho použijete pro váš cluster.

Když použijete skript na cluster, stav clusteru se změní ze **spuštěno** na **přijato**. Pak se změní na **konfiguraci HDInsight** a nakonec zpátky na **spuštěno** pro úspěšné skripty. Stav skriptu je zaznamenán v historii akcí skriptu. Tyto informace vám sdělí, jestli byl skript úspěšný nebo neúspěšný. Například rutina prostředí PowerShell pro `Get-AzHDInsightScriptActionHistory` zobrazuje stav skriptu. Vrátí informace podobné následujícímu textu:

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
* Azure CLI
* Sada HDInsight .NET SDK

HDInsight poskytuje skripty pro instalaci následujících komponent do clusterů HDInsight:

| Název | Skript |
| --- | --- |
| Přidat účet Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Viz [Přidání dalších účtů úložiště do služby HDInsight](hdinsight-hadoop-add-storage.md). |
| Nainstalovat odstín |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Viz [instalace a použití odstínu v clusterech HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Předběžné načtení knihoven podregistru |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Viz [Přidání vlastních knihoven Apache Hive při vytváření clusteru HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Akce skriptu při vytváření clusteru

V této části jsou vysvětleny různé způsoby, jak můžete při vytváření clusteru HDInsight použít akce se skripty.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Použití akce skriptu během vytváření clusteru z Azure Portal

1. Začněte vytvářet cluster, jak je popsáno v tématu [vytváření clusterů se systémem Linux ve službě HDInsight pomocí Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md). Na kartě **Konfigurace + ceny** vyberte **+ přidat akci skriptu**.

    ![Akce skriptu Azure Portal clusteru](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. K výběru předpřipraveného skriptu použijte položku __Vybrat skript__ . Chcete-li použít vlastní skript, vyberte možnost __vlastní__. Pak zadejte __název__ a __identifikátor URI pro skript bash__ pro váš skript.

    ![Přidání skriptu do formuláře pro výběr skriptu](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Vybrat skript | Chcete-li použít vlastní skript, vyberte možnost __vlastní__. V opačném případě vyberte jeden ze zadaných skriptů. |
    | Název |Zadejte název akce skriptu. |
    | Identifikátor URI skriptu bash |Zadejte identifikátor URI skriptu. |
    | Vedoucí/pracovní proces/ZooKeeper |Zadejte uzly, na kterých je spuštěn skript: **head**, **Worker**nebo **Zookeeper**. |
    | Parametry |Zadejte parametry, pokud to skript vyžaduje. |

    Použijte položku __zachovat tuto položku akce skriptu__ a ujistěte se, že je skript použit během operace škálování.

1. Vyberte __vytvořit__ a skript se uloží. Potom můžete k přidání dalšího skriptu použít možnost __+ Odeslat novou__ .

    ![Vícenásobné akce skriptu HDInsight](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Až skončíte s přidáváním skriptů, vrátíte se na kartu **Konfigurace + ceny** .

1. Dokončete zbývající kroky vytváření clusteru jako obvykle.

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

* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Použití akce skriptu během vytváření clusteru z Azure PowerShell

V této části pomocí rutiny [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) vyvoláte skripty pro přizpůsobení clusteru. Než začnete, ujistěte se, že jste nainstalovali a nakonfigurovali Azure PowerShell. Chcete-li použít tyto příkazy PowerShellu, potřebujete [AZ Module](https://docs.microsoft.com/powershell/azure/overview).

Následující skript ukazuje, jak použít akci skriptu při vytváření clusteru pomocí prostředí PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Může to trvat několik minut, než se cluster vytvoří.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Použití akce skriptu během vytváření clusteru ze sady HDInsight .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny, které usnadňují práci s HDInsight z aplikace .NET. Ukázku kódu naleznete v tématu [akce skriptu](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Akce skriptu na běžícím clusteru

V této části se dozvíte, jak aplikovat akce skriptů na běžící cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Použití akce skriptu u běžícího clusteru z Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a Najděte svůj cluster.

1. Ve výchozím zobrazení vyberte v části **Nastavení**možnost **akce skriptu**.

1. V horní části stránky **akce skriptu** vyberte **+ Odeslat novou**.

    ![Přidání skriptu do běžícího clusteru](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. K výběru předpřipraveného skriptu použijte položku __Vybrat skript__ . Chcete-li použít vlastní skript, vyberte možnost __vlastní__. Pak zadejte __název__ a __identifikátor URI pro skript bash__ pro váš skript.

    ![Přidání skriptu do formuláře pro výběr skriptu](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Vybrat skript | Chcete-li použít vlastní skript, vyberte možnost __vlastní__. V opačném případě vyberte poskytnutý skript. |
    | Název |Zadejte název akce skriptu. |
    | Identifikátor URI skriptu bash |Zadejte identifikátor URI skriptu. |
    | Vedoucí/pracovní proces/Zookeeper |Zadejte uzly, na kterých je spuštěn skript: **head**, **Worker**nebo **Zookeeper**. |
    | Parametry |Zadejte parametry, pokud to skript vyžaduje. |

    Pomocí položky __zachovat tuto položku akce skriptu__ zajistěte, aby byl skript použit během operací škálování.

1. Nakonec vyberte tlačítko **vytvořit** , pokud chcete skript použít pro cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Použití akce skriptu pro spuštěný cluster z Azure PowerShell

Chcete-li použít tyto příkazy PowerShellu, potřebujete [AZ Module](https://docs.microsoft.com/powershell/azure/overview). Následující příklad ukazuje, jak použít akci skriptu na běžící cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po dokončení operace obdržíte informace podobné následujícímu textu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Použití akce skriptu u běžícího clusteru z Azure CLI

Než začnete, ujistěte se, že jste nainstalovali a nakonfigurovali rozhraní příkazového řádku Azure CLI. Ujistěte se, že máte nejnovější verzi. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Ověření u vašeho předplatného Azure:

    ```azurecli
    az login
    ```

1. Použít akci skriptu na běžící cluster:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Platné role jsou `headnode`, `workernode`, `zookeepernode``edgenode`. Pokud má být skript použit pro několik typů uzlů, oddělte role mezerou. například `--roles headnode workernode`.

    Pokud chcete skript zachovat, přidejte `--persist-on-success`. Skript můžete také zachovat později pomocí `az hdinsight script-action promote`.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Použití akce skriptu u běžícího clusteru pomocí REST API

Viz téma [REST API clusteru ve službě Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Použití akce skriptu u běžícího clusteru ze sady HDInsight .NET SDK

Příklad použití sady .NET SDK pro použití skriptů v clusteru najdete v tématu [použití akce skriptu proti běžícímu clusteru HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)se systémem Linux.

## <a name="view-history-and-promote-and-demote-script-actions"></a>Zobrazení historie a propagace a snížení úrovně akcí skriptů

### <a name="the-azure-portal"></a>Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a Najděte svůj cluster.

1. Ve výchozím zobrazení vyberte v části **Nastavení**možnost **akce skriptu**.

1. Historie skriptů pro tento cluster se zobrazí v oddílu akce skriptu. Tyto informace obsahují seznam trvalých skriptů. Na následujícím snímku obrazovky vidíte, že skript Solr byl spuštěn v tomto clusteru. Snímek obrazovky nezobrazuje žádné trvalé skripty.

    ![Historie odeslání akcí skriptu na portálu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Výběrem skriptu z historie zobrazíte oddíl **vlastnosti** pro tento skript. V horní části obrazovky můžete skript znovu spustit nebo ho povýšit.

    ![Akce skriptu – vlastnosti povýšení](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Můžete také vybrat tři tečky, **...** vpravo od položek v oddílu akce skriptu k provedení akcí.

    ![Trvalé akce při odstraňování skriptů](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| rutiny | Funkce |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Načte informace o trvalých akcích skriptu. Tato rutina nevrátí zpět akce prováděné skriptem, odebere pouze trvalý příznak.|
| `Get-AzHDInsightScriptActionHistory` |Načtěte historii akcí skriptu použitých pro cluster nebo podrobnosti konkrétního skriptu. |
| `Set-AzHDInsightPersistedScriptAction` |Povýší akci skriptu ad hoc na trvalou akci skriptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Snížit úroveň trvalé akce skriptu na akci ad hoc. |

Následující příklad skriptu ukazuje použití rutin pro povýšení a pak snížení úrovně skriptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Příkaz | Popis |
| --- | --- |
| [AZ HDInsight Script-Action DELETE](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Odstraní zadanou akci trvalého skriptu clusteru. Tento příkaz nevrátí zpět akce prováděné skriptem, odebere pouze trvalý příznak.|
|[AZ HDInsight Script-Action Execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Spustí akce skriptu v zadaném clusteru HDInsight.|
| [AZ HDInsight Script-Action list](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Zobrazí seznam všech trvalých akcí skriptu pro zadaný cluster. |
|[AZ HDInsight Script-Action list-History](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Vypíše historii spuštění všech skriptů pro zadaný cluster.|
|[AZ HDInsight Script-Action Promote](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Propaguje zadané spuštění skriptu ad-hoc na trvalý skript.|
|[AZ HDInsight Script-Action Show-Execution-details](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Získá podrobnosti spuštění skriptu pro dané ID spuštění skriptu.|

### <a name="hdinsight-net-sdk"></a>Sada HDInsight .NET SDK

Příklad použití sady .NET SDK k načtení historie skriptu z clusteru, zvýšení úrovně nebo snížení úrovně skriptů najdete v tématu [použití akce skriptu proti běžícímu clusteru HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)se systémem Linux.

> [!NOTE]  
> Tento příklad také ukazuje, jak nainstalovat aplikaci HDInsight pomocí sady .NET SDK.

## <a name="support-for-open-source-software"></a>Podpora pro open source software

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

## <a name="troubleshooting"></a>Odstraňování potíží

K zobrazení informací protokolovaných akcemi skriptů můžete použít webové uživatelské rozhraní Ambari. Pokud se skript během vytváření clusteru nezdařil, jsou protokoly dostupné taky ve výchozím účtu úložiště přidruženém ke clusteru. V této části najdete informace o tom, jak protokoly načíst pomocí obou těchto možností.

### <a name="the-apache-ambari-web-ui"></a>Webové uživatelské rozhraní Apache Ambari

1. Ve webovém prohlížeči přejděte na `https://CLUSTERNAME.azurehdinsight.net`, kde `CLUSTERNAME` je název vašeho clusteru.

1. Na panelu v horní části stránky vyberte položku **OPS** . V seznamu se zobrazí aktuální a předchozí operace prováděné na clusteru prostřednictvím Ambari.

    ![Panel webového uživatelského rozhraní Ambari s vybranou operací Operations](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. Vyhledá položky, které se **spouštějí\_customscriptaction** ve sloupci **Operations** . Tyto položky jsou vytvořeny při spuštění akcí skriptu.

    ![Operace s akcemi skriptu Apache Ambari](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Chcete-li zobrazit výstup **stdout** a **stderr** , vyberte položku **run\customscriptaction** a přejděte k podrobnostem prostřednictvím odkazů. Tento výstup se generuje při spuštění skriptu a může mít užitečné informace.

### <a name="access-logs-from-the-default-storage-account"></a>Přístup k protokolům z výchozího účtu úložiště

Pokud se vytvoření clusteru nepovede kvůli chybě skriptu, protokoly se uchovávají v účtu úložiště clusteru.

* Protokoly úložiště jsou k dispozici na adrese `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Protokoly akcí skriptů](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    V rámci tohoto adresáře jsou protokoly uspořádány samostatně pro **hlavnímu uzlu**, **pracovní uzel**a **Zookeeper uzel**. Podívejte se na následující příklady:

    * **Hlavnímu uzlu**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Pracovní uzel**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper uzel**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Všechny **stdout** a **stderr** odpovídajícího hostitele se nahrají do účtu úložiště. Pro každou akci skriptu existuje jeden **\*. txt** a **errors-\*. txt** . Soubor **Output-*. txt** obsahuje informace o identifikátoru URI skriptu, který byl spuštěn na hostiteli. Následující text je příkladem těchto informací:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Je možné opakovaně vytvořit cluster akcí skriptu se stejným názvem. V takovém případě můžete rozlišovat příslušné protokoly na základě názvu složky **data** . Například struktura složek pro cluster **mycluster**vytvořená v různých datech vypadá podobně jako v následujících záznamech protokolu:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

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

__Řešení__. Tuto chybu můžete vyřešit tak, že se ručně připojíte k jednotlivým uzlům clusteru pomocí `ssh`. Spusťte následující příkaz, který znovu nainstaluje správnou verzi klienta úložiště:

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

## <a name="next-steps"></a>Další kroky

* [Vývoj skriptů akcí skriptu pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Přidání dalšího úložiště do clusteru HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fáze při vytváření clusteru"
