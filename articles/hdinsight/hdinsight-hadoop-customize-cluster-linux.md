---
title: Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů
description: Pomocí akcí skriptů můžete přidat vlastní součásti do clusterů HDInsight. Akce skriptů jsou skripty bash, které se dají použít k přizpůsobení konfigurace clusteru. Nebo přidejte další služby a nástroje, jako je odstín, Solr nebo R.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, contperf-fy21q2
ms.date: 09/02/2020
ms.openlocfilehash: 46be3349490f04660d4fc8b69e4cdc295d8ecc1c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945804"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů

Azure HDInsight poskytuje metodu konfigurace nazvanou **akce skriptů** , které vyvolávají vlastní skripty pro přizpůsobení clusteru. Tyto skripty slouží k instalaci dalších komponent a změně nastavení konfigurace. Akce skriptu lze použít během nebo po vytvoření clusteru.

Akce skriptu je také možné publikovat do Azure Marketplace jako aplikace HDInsight. Další informace o aplikacích HDInsight najdete v tématu [publikování aplikace HDInsight v Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="understand-script-actions"></a>Porozumění akcím skriptů

Akce skriptu je skript bash, který běží na uzlech v clusteru HDInsight. Následující vlastnosti a funkce skriptových akcí jsou tyto:

- Musí být uložené na identifikátoru URI, který je přístupný z clusteru HDInsight. Níže jsou možná umístění úložiště:

    - Pro běžné clustery (bez protokolu ESP):
      - Data Lake Storage Gen1/Gen2: instanční objekt služby HDInsight používá pro přístup k Data Lake Storage musí mít ke skriptu oprávnění ke čtení. Formát identifikátoru URI pro skripty uložené v Data Lake Storage Gen1 je `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . Formát identifikátoru URI pro skripty Data Lake Storage Gen2 je `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`
      - Objekt BLOB v účtu Azure Storage, který je buď primárním nebo dalším účtem úložiště pro cluster HDInsight. HDInsight získá přístup k oběma těmto typům účtů úložiště během vytváření clusteru.

        > [!IMPORTANT]  
        > Neotáčejte klíč úložiště na tomto účtu Azure Storage, protože to způsobí selhání dalších akcí skriptu se skripty, které jsou tam uložené.

      - Veřejná služba pro sdílení souborů, která je přístupná prostřednictvím `http://` cest. Příklady jsou Azure Blob, GitHub nebo OneDrive. Příklady identifikátorů URI najdete v tématu [Příklady skriptů akcí](#example-script-action-scripts)skriptů.
    - U clusterů s protokolem `wasb://` ESP `wasbs://` `http[s]://` jsou podporovány identifikátory URI nebo nebo.

- Dá se omezit na spouštění jenom na určitých typech uzlů. Příklady jsou hlavní uzly nebo pracovní uzly.
- Může být trvalá nebo *ad hoc*.

    - Trvalé akce skriptu musí mít jedinečný název. Trvalé skripty se používají k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalý skript může také při operacích škálování použít změny v jiném typu uzlu. Příkladem je hlavní uzel.
    - *Ad hoc* skripty nejsou trvalé. Akce skriptu použité při vytváření clusteru se automaticky uchovávají. Nejsou aplikovány na pracovní uzly přidané do clusteru po spuštění skriptu. Potom můžete přenést skript *ad hoc* na trvalý skript nebo snížit úroveň trvalého skriptu na skript *ad hoc* . Skripty, které selžou, nejsou trvalé, i když výslovně označíte, že by měly být.

- Může přijmout parametry, které skript používá během provádění.
- Spusťte s oprávněními na úrovni root na uzlech clusteru.
- Dá se použít prostřednictvím Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo sady HDInsight .NET SDK.
- Akce skriptů, které odstraňují nebo upravují soubory služby na virtuálním počítači, můžou ovlivnit stav a dostupnost služby.

Cluster uchovává historii všech skriptů, které byly spuštěny. Historie pomáhá v případě, že potřebujete najít ID skriptu pro operace zvýšení nebo snížení úrovně.

> [!IMPORTANT]  
> Neexistuje žádný automatický způsob, jak vrátit zpět změny provedené akcí skriptu. Buď ručně převratte změny, nebo zadejte skript, který je obrátí.

## <a name="permissions"></a>Oprávnění

U clusteru HDInsight připojeného k doméně jsou k dispozici dvě oprávnění Apache Ambari, která se vyžadují při použití akcí skriptu s clusterem:

* **AMBARI. Spusťte \_ vlastní \_ příkaz**. Role správce Ambari má ve výchozím nastavení toto oprávnění.
* **Cluster. Spusťte \_ vlastní \_ příkaz**. Ve výchozím nastavení má Správce clusteru HDInsight i správce Ambari toto oprávnění.

Další informace o práci s oprávněními s HDInsight připojenými k doméně najdete v tématu [Správa clusterů HDInsight pomocí balíček zabezpečení podniku](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Řízení přístupu

Pokud nejste správcem nebo vlastníkem předplatného Azure, váš účet musí mít alespoň `Contributor` přístup ke skupině prostředků, která obsahuje cluster HDInsight.

Někdo, který má minimálně přístup Přispěvatel k předplatnému Azure, musí předtím zaregistrovat poskytovatele. K registraci poskytovatele dojde, když uživatel s přístupem Přispěvatel k předplatnému vytvoří prostředek. V případě bez vytvoření prostředku si přečtěte téma [registrace poskytovatele pomocí REST](/rest/api/resources/providers#Providers_Register).

Získejte další informace o práci se správou přístupu:

- [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
- [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Metody použití akcí skriptů

Máte možnost konfigurovat akci skriptu, která se spustí při prvním vytvoření clusteru, nebo jeho spuštění v existujícím clusteru.

### <a name="script-action-in-the-cluster-creation-process"></a>Akce skriptu v procesu vytváření clusteru

Akce skriptu používané při vytváření clusteru se mírně liší od akcí skriptů spuštěných v existujícím clusteru:

- Skript se automaticky uchová.
- Selhání ve skriptu může způsobit selhání procesu vytváření clusteru.

Následující diagram ilustruje, kdy se akce skriptu spustí během procesu vytváření:

![Přizpůsobení a fáze clusteru HDInsight během vytváření clusteru][img-hdi-cluster-states]

Skript se spustí při konfiguraci HDInsight. Skript běží paralelně na všech zadaných uzlech v clusteru. Spouští se s oprávněními root na uzlech.

Můžete provádět operace, jako je zastavení a spuštění služeb, včetně služeb souvisejících s Apache Hadoop. Pokud zastavíte služby, ujistěte se, že Ambari a další služby související s Hadoop jsou spuštěné před dokončením skriptu. Tyto požadované služby určují stav a stav clusteru při jeho vytváření.

Během vytváření clusteru můžete použít mnoho akcí skriptu najednou. Tyto skripty jsou vyvolány v pořadí, v jakém byly zadány.

> [!IMPORTANT]  
> Akce skriptu musí být dokončeny během 60 minut nebo vypršel časový limit. Při zřizování clusteru se skript spouští souběžně s jinými procesy nastavení a konfigurace. Soutěž na prostředky, jako je čas procesoru nebo šířka pásma sítě, může způsobit, že se dokončení skriptu bude trvat déle, než ve vývojovém prostředí.
>
> Aby se minimalizoval čas potřebný ke spuštění skriptu, vyhněte se úlohám, jako je stahování a kompilování aplikací ze zdroje. Předem zkompilujte aplikace a uložte binární soubor do Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Akce skriptu na spuštěném clusteru

Selhání skriptu v případě spuštěného clusteru automaticky nezpůsobí změnu clusteru na stav selhání. Po dokončení skriptu se cluster musí vrátit do běžícího stavu. I v případě, že cluster má spuštěný stav, může skript obsahovat poškozené věci. Skript může například odstraňovat soubory, které cluster potřebuje.

Akce skriptů se spouští s oprávněními root. Ujistěte se, že rozumíte tomu, co skript dělá předtím, než ho použijete pro váš cluster.

Když použijete skript na cluster, stav clusteru se změní ze **spuštěno** na **přijato**. Pak se změní na **konfiguraci HDInsight** a nakonec zpátky na **spuštěno** pro úspěšné skripty. Stav skriptu je zaznamenán v historii akcí skriptu. Tyto informace vám sdělí, jestli byl skript úspěšný nebo neúspěšný. Například `Get-AzHDInsightScriptActionHistory` rutina PowerShellu zobrazuje stav skriptu. Vrátí informace podobné následujícímu textu:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Pokud po vytvoření clusteru změníte uživatele clusteru, správce, heslo, akce skriptu spuštěné proti tomuto clusteru můžou selhat. Pokud máte trvalé akce skriptů, které cílí na pracovní uzly, můžou se tyto skripty při škálování clusteru nezdařily.

## <a name="example-script-action-scripts"></a>Ukázkové skripty akcí skriptů

Skripty akcí skriptů lze použít prostřednictvím následujících nástrojů:

* portál Azure
* Azure PowerShell
* Azure CLI
* Sada HDInsight .NET SDK

HDInsight poskytuje skripty pro instalaci následujících komponent do clusterů HDInsight:

| Name | Skript |
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
    | Name |Zadejte název akce skriptu. |
    | Identifikátor URI skriptu bash |Zadejte identifikátor URI skriptu. |
    | Vedoucí/pracovní proces/ZooKeeper |Zadejte uzly, na kterých je spuštěn skript: **head**, **Worker** nebo **Zookeeper**. |
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

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md)
- [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Použití akce skriptu během vytváření clusteru z Azure PowerShell

V této části pomocí rutiny [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) vyvoláte skripty pro přizpůsobení clusteru. Než začnete, ujistěte se, že jste nainstalovali a nakonfigurovali Azure PowerShell. Chcete-li použít tyto příkazy PowerShellu, potřebujete [AZ Module](/powershell/azure/).

Následující skript ukazuje, jak použít akci skriptu při vytváření clusteru pomocí prostředí PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Může to trvat několik minut, než se cluster vytvoří.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Použití akce skriptu během vytváření clusteru ze sady HDInsight .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny, které usnadňují práci s HDInsight z aplikace .NET. Ukázku kódu naleznete v tématu [akce skriptu](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Akce skriptu na běžícím clusteru

V této části se dozvíte, jak aplikovat akce skriptů na běžícím clusteru.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Použití akce skriptu u běžícího clusteru z Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a Najděte svůj cluster.

1. Ve výchozím zobrazení vyberte v části **Nastavení** možnost **akce skriptu**.

1. V horní části stránky **akce skriptu** vyberte **+ Odeslat novou**.

    ![Přidání skriptu do běžícího clusteru](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. K výběru předpřipraveného skriptu použijte položku __Vybrat skript__ . Chcete-li použít vlastní skript, vyberte možnost __vlastní__. Pak zadejte __název__ a __identifikátor URI pro skript bash__ pro váš skript.

    ![Přidání skriptu do formuláře pro výběr skriptu](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Následující tabulka popisuje prvky ve formuláři:

    | Vlastnost | Hodnota |
    | --- | --- |
    | Vybrat skript | Chcete-li použít vlastní skript, vyberte možnost __vlastní__. V opačném případě vyberte poskytnutý skript. |
    | Name |Zadejte název akce skriptu. |
    | Identifikátor URI skriptu bash |Zadejte identifikátor URI skriptu. |
    | Vedoucí/pracovní proces/Zookeeper |Zadejte uzly, na kterých je spuštěn skript: **head**, **Worker** nebo **Zookeeper**. |
    | Parametry |Zadejte parametry, pokud to skript vyžaduje. |

    Pomocí položky __zachovat tuto položku akce skriptu__ zajistěte, aby byl skript použit během operací škálování.

1. Nakonec vyberte tlačítko **vytvořit** , pokud chcete skript použít pro cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Použití akce skriptu pro spuštěný cluster z Azure PowerShell

Chcete-li použít tyto příkazy PowerShellu, potřebujete [AZ Module](/powershell/azure/). Následující příklad ukazuje, jak použít akci skriptu na běžící cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po dokončení operace obdržíte informace podobné následujícímu textu:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Použití akce skriptu u běžícího clusteru z Azure CLI

Než začnete, ujistěte se, že jste nainstalovali a nakonfigurovali rozhraní příkazového řádku Azure CLI. Ujistěte se, že máte nejnovější verzi. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

1. Ověření u vašeho předplatného Azure:

    ```azurecli
    az login
    ```

1. Použít akci skriptu na běžící cluster:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Platné role jsou `headnode` , `workernode` , `zookeepernode` , `edgenode` . Pokud má být skript použit pro několik typů uzlů, oddělte role mezerou. Například `--roles headnode workernode`.

    Pro zachování skriptu přidejte `--persist-on-success` . Skript můžete také zachovat později pomocí `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Použití akce skriptu u běžícího clusteru pomocí REST API

Viz téma [REST API clusteru ve službě Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Použití akce skriptu u běžícího clusteru ze sady HDInsight .NET SDK

Příklad použití sady .NET SDK pro použití skriptů v clusteru najdete v tématu [použití akce skriptu proti běžícímu clusteru HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)se systémem Linux.

## <a name="view-history-and-promote-and-demote-script-actions"></a>Zobrazení historie a propagace a snížení úrovně akcí skriptů

### <a name="the-azure-portal"></a>Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a Najděte svůj cluster.

1. Ve výchozím zobrazení vyberte v části **Nastavení** možnost **akce skriptu**.

1. Historie skriptů pro tento cluster se zobrazí v oddílu akce skriptu. Tyto informace obsahují seznam trvalých skriptů. Na následujícím snímku obrazovky vidíte, že skript Solr byl spuštěn v tomto clusteru. Snímek obrazovky nezobrazuje žádné trvalé skripty.

    ![Historie odeslání akcí skriptu na portálu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Výběrem skriptu z historie zobrazíte oddíl **vlastnosti** pro tento skript. V horní části obrazovky můžete skript znovu spustit nebo ho povýšit.

    ![Akce skriptu – vlastnosti povýšení](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Můžete také vybrat tři tečky, **...** vpravo od položek v oddílu akce skriptu k provedení akcí.

    ![Trvalé akce při odstraňování skriptů](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| rutiny | Funkce |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Načte informace o trvalých akcích skriptu. Tato rutina nevrátí zpět akce provedené skriptem, odebere pouze trvalý příznak.|
| `Get-AzHDInsightScriptActionHistory` |Načtěte historii akcí skriptu použitých pro cluster nebo podrobnosti konkrétního skriptu. |
| `Set-AzHDInsightPersistedScriptAction` |Povýší `ad hoc` akci skriptu na trvalou akci skriptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Snížit úroveň trvalé akce skriptu na `ad hoc` akci. |

Následující příklad skriptu ukazuje použití rutin pro povýšení a pak snížení úrovně skriptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Příkaz | Popis |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |Odstraní zadanou akci trvalého skriptu clusteru. Tento příkaz nevrátí zpět akce provedené skriptem, odebere pouze trvalý příznak.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|Spustí akce skriptu v zadaném clusteru HDInsight.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |Zobrazí seznam všech trvalých akcí skriptu pro zadaný cluster. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|Vypíše historii spuštění všech skriptů pro zadaný cluster.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|Propaguje zadané spuštění skriptu ad hoc na trvalý skript.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|Získá podrobnosti spuštění skriptu pro dané ID spuštění skriptu.|

### <a name="hdinsight-net-sdk"></a>Sada HDInsight .NET SDK

Příklad použití sady .NET SDK k načtení historie skriptu z clusteru, zvýšení úrovně nebo snížení úrovně skriptů najdete v tématu [ použití akce skriptu proti běžícímu clusteru HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)se systémem Linux.

> [!NOTE]  
> Tento příklad také ukazuje, jak nainstalovat aplikaci HDInsight pomocí sady .NET SDK.

## <a name="next-steps"></a>Další kroky

* [Vývoj skriptů akcí skriptu pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Přidání dalšího úložiště do clusteru HDInsight](hdinsight-hadoop-add-storage.md)
* [Řešení potíží s akcemi skriptů](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fáze při vytváření clusteru"
