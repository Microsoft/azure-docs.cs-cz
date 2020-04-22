---
title: Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů
description: Přidejte vlastní součásti do clusterů HDInsight pomocí akcí skriptu. Akce skriptu jsou skripty Bash, které lze použít k přizpůsobení konfigurace clusteru. Nebo přidejte další služby a nástroje, jako je Hue, Solr nebo R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 434d4adb763fd0e0a29c065ce051bfd4fa461180
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770737"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů

Azure HDInsight poskytuje metodu konfigurace s názvem **akce skriptu,** které vyvolávají vlastní skripty pro přizpůsobení clusteru. Tyto skripty se používají k instalaci dalších součástí a změně nastavení konfigurace. Akce skriptu lze použít během nebo po vytvoření clusteru.

Akce skriptu se dají taky publikovat na Azure Marketplace jako hdinsightovou aplikaci. Další informace o aplikacích HDInsight najdete [v tématu Publikování aplikace HDInsight na Webu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Oprávnění

Pro cluster HDInsight přilehlý k doméně existují dvě oprávnění Apache Ambari, která jsou vyžadována při použití akcí skriptů s clusterem:

* **AMBARI. SPUSŤTE\_VLASTNÍ\_PŘÍKAZ**. Ve výchozím nastavení má toto oprávnění role Správce Ambari.
* **clusteru. SPUSŤTE\_VLASTNÍ\_PŘÍKAZ**. Správce clusteru HDInsight i správce Ambari mají toto oprávnění ve výchozím nastavení.

Další informace o práci s oprávněními s hdinsightem přilehlým k doméně najdete v [tématu Správa clusterů HDInsight pomocí balíčku Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Řízení přístupu

Pokud nejste správcenebo vlastník vašeho předplatného Azure, váš účet musí mít alespoň přístup přispěvatele ke skupině prostředků, která obsahuje cluster HDInsight.

Někdo s přístupem alespoň přispěvatele k předplatnému Azure musí mít dříve registrovaného poskytovatele. K registraci zprostředkovatele dojde, když uživatel s přístupem přispěvatele k předplatnému vytvoří prostředek. Pro bez vytvoření prostředku, viz [registrace zprostředkovatele pomocí REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Získejte další informace o práci se správou přístupu:

* [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
* [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Principy akcí skriptu

Akce skriptu je bash skript, který běží na uzlech v clusteru HDInsight. Charakteristiky a vlastnosti skriptových akcí jsou následující:

* Musí být uloženna na identifikátoru URI, který je přístupný z clusteru HDInsight. Možná umístění úložišť jsou následující:

    * Pro běžné clustery:

      * ADLS Gen1: Instanční objekt služby HDInsight používá pro přístup k úložišti datových jezer musí mít přístup pro čtení ke skriptu. Formát IDENTIFIKÁTORU URI pro skripty uložené `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`v gen1 úložiště datového jezera je .

      * Objekt blob v účtu Azure Storage, který je primární nebo další účet úložiště pro cluster HDInsight. HDInsight je udělen přístup k oběma těmto typům účtů úložiště během vytváření clusteru.

        > [!IMPORTANT]  
        > Neotáčejte klíč úložiště na tomto účtu úložiště Azure, protože to způsobí, že následné akce skriptu se skripty, které jsou tam uloženy, se nezdaří.

      * Veřejná služba pro sdílení souborů přístupná prostřednictvím http:// cest. Příklady jsou Azure Blob, GitHub, OneDrive. Například identifikátory URI naleznete v [tématu Příklad skriptů akce skriptu](#example-script-action-scripts).

     * Pro clustery s ESP jsou podporovány wasb:// nebo wasbs:// nebo http[s]:// IDENTIFIKÁTORY URI.

* Může být omezena na spuštění pouze na určitých typech uzlů. Příklady jsou hlavní uzly nebo pracovní uzly.

* Může být trvalé `ad hoc`nebo .

    Trvalé akce skriptu musí mít jedinečný název. Trvalé skripty se používají k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalý skript může také použít změny pro jiný typ uzlu při změně měřítka operace. Příkladem je hlavní uzel.

    `Ad hoc`skripty nejsou trvalé. Akce skriptu používané při vytváření clusteru jsou automaticky zachovány. Nejsou použity na pracovní uzly přidané do clusteru po spuštění skriptu. Potom můžete povýšit `ad hoc` skript na trvalý skript nebo snížit `ad hoc` úroveň trvalého skriptu na skript. Skripty, které se nezdaří, nejsou trvalé, i když konkrétně označíte, že by měly být.

* Může přijímat parametry, které jsou používány skriptem během provádění.

* Spustit s oprávněními na úrovni kořenové úrovně v uzlech clusteru.

* Dá se použít na webu Azure Portal, Azure PowerShell, Azure CLI nebo HDInsight .NET SDK.

Cluster uchovává historii všech skriptů, které byly spuštěny. Historie pomáhá, když potřebujete najít ID skriptu pro operace povýšení nebo snížení úrovně.

> [!IMPORTANT]  
> Neexistuje žádný automatický způsob, jak vrátit zpět změny provedené akcí skriptu. Změny můžete buď ručně obrátit, nebo zadat skript, který je obrátí.

### <a name="script-action-in-the-cluster-creation-process"></a>Akce skriptu v procesu vytváření clusteru

Akce skriptu používané při vytváření clusteru se mírně liší od akcí skriptů spuštěných v existujícím clusteru:

* Skript je automaticky trvalý.

* Selhání skriptu může způsobit selhání procesu vytváření clusteru.

Následující diagram znázorňuje spuštění akce skriptu během procesu vytváření:

![Přizpůsobení a fáze clusteru HDInsight během vytváření clusteru][img-hdi-cluster-states]

Skript se spustí při konfiguraci hdinsightu. Skript běží paralelně na všech určených uzlech v clusteru. Běží s oprávněními root na uzlech.

Můžete provést operace, jako je zastavení a spuštění služeb, včetně služeb souvisejících s Apache Hadoop. Pokud zastavíte služby, ujistěte se, že ambari a další služby související s Hadoopem jsou spuštěny před dokončením skriptu. Tyto požadované služby určují stav a stav clusteru při jeho vytváření.

Během vytváření clusteru můžete použít mnoho akcí skriptů najednou. Tyto skripty jsou vyvolány v pořadí, ve kterém byly zadány.

> [!IMPORTANT]  
> Akce skriptu musí být dokončeny do 60 minut, jinak časový mzda. Během zřizování clusteru se skript spouští souběžně s jinými procesy instalace a konfigurace. Konkurence pro prostředky, jako je například čas procesoru nebo šířka pásma sítě může způsobit skript trvat déle dokončit než ve vývojovém prostředí.
>
> Chcete-li minimalizovat čas potřebný ke spuštění skriptu, vyhněte se úlohám, jako je stahování a kompilace aplikací ze zdroje. Předkompilujte aplikace a uložte binární soubor do Služby Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Akce skriptu ve spuštěném clusteru

Selhání skriptu v již spuštěném clusteru automaticky nezpůsobí, že se cluster změní na neúspěšný stav. Po dokončení skriptu by se cluster měl vrátit do spuštěného stavu. I v případě, že cluster má spuštěný stav, může mít poškozený skript poškozené věci. Skript může například odstranit soubory potřebné pro cluster.

Akce skriptů jsou spuštěny s oprávněními root. Ujistěte se, že rozumíte tomu, co skript dělá, než ho použijete ve svém clusteru.

Při použití skriptu v clusteru se stav clusteru změní ze **spuštění** na **přijato**. Pak se změní na **konfiguraci HDInsight** a nakonec zpět na **Spuštěno** pro úspěšné skripty. Stav skriptu je zaznamenán do historie akcí skriptu. Tyto informace sdělují, zda byl skript úspěšný nebo neúspěšný. Například rutina `Get-AzHDInsightScriptActionHistory` prostředí PowerShell zobrazuje stav skriptu. Vrátí informace podobné následujícímu textu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Pokud po vytvoření clusteru změníte uživatele clusteru, správce a heslo, mohou akce skriptu spuštěné proti tomuto clusteru selhat. Pokud máte všechny trvalé akce skriptu, které cílí na pracovní uzly, tyto skripty může selhat při škálování clusteru.

## <a name="example-script-action-scripts"></a>Ukázkové skripty akce skriptu

Skripty akce lze použít prostřednictvím následujících nástrojů:

* portál Azure
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight poskytuje skripty pro instalaci následujících součástí do clusterů HDInsight:

| Název | Skript |
| --- | --- |
| Přidání účtu Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Viz [Přidání dalších účtů úložiště do HDInsightu](hdinsight-hadoop-add-storage.md). |
| Instalace odstínu |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Viz [Instalace a používání hue na clusterech HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Přednačtení knihovny Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Viz [Přidání vlastních knihoven Apache Hive při vytváření clusteru HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Akce skriptu při vytváření clusteru

Tato část vysvětluje různé způsoby použití akcí skriptu při vytváření clusteru HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Použití akce skriptu při vytváření clusteru z portálu Azure

1. Začněte vytvářet cluster, jak je popsáno v [části Vytvoření linuxových clusterů v HDInsightu pomocí portálu Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Na kartě **Konfigurace + ceny** vyberte + Přidat akci **skriptu**.

    ![Akce skriptu clusteru portálu Azure](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Pomocí položky __Vybrat skript__ vyberte předem vyrobený skript. Chcete-li použít vlastní skript, vyberte __možnost Vlastní__. Potom zadejte __název__ a __Bash skript URI__ pro váš skript.

    ![Přidání skriptu do formuláře vybraného skriptu](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Výběr skriptu | Chcete-li použít vlastní skript, vyberte __možnost Vlastní__. V opačném případě vyberte jeden z poskytnutých skriptů. |
    | Název |Zadejte název akce skriptu. |
    | Bash skript URI |Zadejte identifikátor URI skriptu. |
    | Vedoucí/Pracovník/ZooKeeper |Určete uzly, na kterých je skript spuštěn: **Vedoucí**, **Pracovník**nebo **ZooKeeper**. |
    | Parametry |Zadejte parametry, pokud to skript vyžaduje. |

    Pomocí položky __zachovat tuto položku akce skriptu__ se ujistěte, že skript je použit během operací škálování.

1. Vyberte __Vytvořit,__ chcete-li skript uložit. Pak můžete použít __+ Odeslat nový__ přidat další skript.

    ![Vícenásobné akce skriptu HDInsight](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Po dokončení přidávání skriptů se vrátíte na kartu **Konfigurace + ceny.**

1. Proveďte zbývající kroky vytváření clusteru obvyklým způsobem.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Použití akce skriptu ze šablon Azure Resource Manageru

Akce skriptu lze použít se šablonami Azure Resource Manager. Například viz [Vytvoření clusteru HDInsight Linux a spuštění akce skriptu](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

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

Další informace o nasazení šablony:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Použití akce skriptu při vytváření clusteru z Azure PowerShellu

V této části použijete rutinu [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) k vyvolání skriptů pro přizpůsobení clusteru. Než začnete, ujistěte se, že nainstalujete a nakonfigurujete Azure PowerShell. Chcete-li použít tyto příkazy prostředí PowerShell, potřebujete [modul AZ](https://docs.microsoft.com/powershell/azure/overview).

Následující skript ukazuje, jak použít akci skriptu při vytváření clusteru pomocí prostředí PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Vytvoření clusteru může trvat několik minut.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Použití akce skriptu při vytváření clusteru ze sady HDInsight .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny, které usnadňují práci s HDInsight z aplikace .NET. Ukázka kódu naleznete v tématu [Akce skriptu](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Akce skriptu pro spuštěný cluster

Tato část vysvětluje, jak použít akce skriptu pro spuštěný cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Použití akce skriptu na spuštěný cluster z portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a vyhledejte svůj cluster.

1. Ve výchozím zobrazení vyberte v části **Nastavení** **položku Akce skriptu**.

1. V horní části stránky **Akce skriptu** vyberte **+ Odeslat nový**.

    ![Přidání skriptu do spuštěného clusteru](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Pomocí položky __Vybrat skript__ vyberte předem vyrobený skript. Chcete-li použít vlastní skript, vyberte __možnost Vlastní__. Potom zadejte __název__ a __Bash skript URI__ pro váš skript.

    ![Přidání skriptu do formuláře vybraného skriptu](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Výběr skriptu | Chcete-li použít vlastní skript, vyberte __vlastní__. V opačném případě vyberte zadaný skript. |
    | Název |Zadejte název akce skriptu. |
    | Bash skript URI |Zadejte identifikátor URI skriptu. |
    | Vedoucí/Pracovník/Zookeeper |Určete uzly, na kterých je skript spuštěn: **Vedoucí**, **Pracovník**nebo **ZooKeeper**. |
    | Parametry |Zadejte parametry, pokud to skript vyžaduje. |

    Pomocí položky zachovat tuto akci __skriptu__ zkontrolujte, zda je skript použit během operací škálování.

1. Nakonec vyberte tlačítko **Vytvořit,** chcete-li použít skript pro cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Použití akce skriptu na spuštěný cluster z Azure PowerShellu

Chcete-li použít tyto příkazy prostředí PowerShell, potřebujete [modul AZ](https://docs.microsoft.com/powershell/azure/overview). Následující příklad ukazuje, jak použít akci skriptu na spuštěný cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po dokončení operace obdržíte informace podobné následujícímu textu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Použití akce skriptu na spuštěný cluster z azure cli

Než začnete, ujistěte se, že nainstalujete a nakonfigurujete rozhraní příkazového příkazu Azure. Ujistěte se, že máte nejnovější verzi. Další informace najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Ověření předplatného Azure:

    ```azurecli
    az login
    ```

1. Použití akce skriptu na spuštěný cluster:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Platné role `headnode` `workernode`jsou `zookeepernode` `edgenode`, , , . Pokud by měl být skript použit na několik typů uzlů, oddělte role mezerou. Například, `--roles headnode workernode`.

    Chcete-li skript `--persist-on-success`zachovat, přidejte . Skript můžete také zachovat později pomocí `az hdinsight script-action promote`aplikace .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Použití akce skriptu na spuštěný cluster pomocí rozhraní REST API

Viz [Rozhraní API REST clusteru ve službě Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Použití akce skriptu na spuštěný cluster ze sady HDInsight .NET SDK

Příklad použití sady .NET SDK k aplikování skriptů do clusteru najdete v tématu [Použití akce skriptu proti spuštěnému clusteru HDInsight založenému na Linuxu](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Zobrazení historie a povýšení a snížení úrovně akcí skriptu

### <a name="the-azure-portal"></a>Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a vyhledejte svůj cluster.

1. Ve výchozím zobrazení vyberte v části **Nastavení** **položku Akce skriptu**.

1. Historie skriptů pro tento cluster se zobrazí v části akce skriptu. Tyto informace zahrnují seznam trvalých skriptů. Následující snímek obrazovky ukazuje, že skript Solr byl spuštěn v tomto clusteru. Snímek obrazovky nezobrazuje žádné trvalé skripty.

    ![Historie odeslání akcí skriptu portálu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Vyberte skript z historie, chcete-li zobrazit oddíl **Vlastnosti** pro tento skript. V horní části obrazovky můžete skript znovu spustit nebo jej propagovat.

    ![Vlastnosti akcí skriptu podporují](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Můžete také vybrat tři tečky, **...**, napravo od položek v sekci akce skriptu k akcím.

    ![Odstranění trvalých akcí skriptu](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Rutina | Funkce |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Načíst informace o trvalých akcích skriptu. Tato rutina neodvolá zpět akce provedené skriptem, pouze odebere trvalý příznak.|
| `Get-AzHDInsightScriptActionHistory` |Načtěte historii akcí skriptů použitých v clusteru nebo podrobnosti pro konkrétní skript. |
| `Set-AzHDInsightPersistedScriptAction` |Povyšte `ad hoc` akci skriptu na trvalou akci skriptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Snížit úroveň akce trvalého `ad hoc` skriptu na akci. |

Následující ukázkový skript ukazuje použití rutin k povýšení a následnému snížení úrovně skriptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Příkaz | Popis |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Odstraní zadanou akci trvalého skriptu clusteru. Tento příkaz neodvolá akce provedené skriptem, pouze odebere trvalý příznak.|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Spusťte akce skriptu v zadaném clusteru HDInsight.|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Zobrazí seznam všech akcí trvalého skriptu pro zadaný cluster. |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Zobrazí seznam historie spuštění všech skriptů pro zadaný cluster.|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Převede zadané spuštění skriptu ad-hoc na trvalý skript.|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Získá podrobnosti spuštění skriptu pro dané ID spuštění skriptu.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Příklad použití sady .NET SDK k načtení historie skriptů z clusteru, povýšení nebo snížení úrovně skriptů naleznete v tématu [Použití akce skriptu proti spuštěnému clusteru HDInsight založenému na Linuxu](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Tento příklad také ukazuje, jak nainstalovat aplikaci HDInsight pomocí sady .NET SDK.

## <a name="next-steps"></a>Další kroky

* [Vývoj skriptovacích akčních skriptů pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Přidání dalšího úložiště do clusteru HDInsight](hdinsight-hadoop-add-storage.md)
* [Poradce při potížích s akcemi skriptu](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fáze při vytváření clusteru"
