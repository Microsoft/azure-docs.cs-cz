---
title: Použití prázdných hraničních uzlů u clusterů Apache Hadoop ve službě Azure HDInsight
description: Postup přidání prázdného hraničního uzlu do clusteru HDInsight. Slouží jako klient a pak se testují nebo hostují aplikace HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 717c3c8052c89825b161f77614d7411c5912d1cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799900"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Použití prázdných hraničních uzlů na Apache Hadoop clusterech v HDInsight

Naučte se, jak přidat prázdný hraniční uzel do clusteru HDInsight. Prázdný hraniční uzel je virtuální počítač se systémem Linux se stejnými klientskými nástroji, který je nainstalovaný a nakonfigurovaný jako hlavních. Ale bez spuštěných služeb [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) . Hraniční uzel můžete použít pro přístup ke clusteru, testování klientských aplikací a hostování klientských aplikací.

Můžete přidat prázdný hraniční uzel do stávajícího clusteru HDInsight do nového clusteru při vytváření clusteru. Přidání prázdného hraničního uzlu se provádí pomocí šablony Azure Resource Manager.  Následující příklad ukazuje, jak se provádí pomocí šablony:

```json
"resources": [
    {
        "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
        "type": "Microsoft.HDInsight/clusters/applications",
        "apiVersion": "2015-03-01-preview",
        "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
        "properties": {
            "marketPlaceIdentifier": "EmptyNode",
            "computeProfile": {
                "roles": [{
                    "name": "edgenode",
                    "targetInstanceCount": 1,
                    "hardwareProfile": {
                        "vmSize": "{}"
                    }
                }]
            },
            "installScriptActions": [{
                "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                "uri": "[parameters('installScriptAction')]",
                "roles": ["edgenode"]
            }],
            "uninstallScriptActions": [],
            "httpsEndpoints": [],
            "applicationType": "CustomApplication"
        }
    }
],
```

Jak je znázorněno v ukázce, můžete volitelně zavolat [akci skriptu](hdinsight-hadoop-customize-cluster-linux.md) pro provedení další konfigurace. Například instalace [odstínu Apache](hdinsight-hadoop-hue-linux.md) do hraničního uzlu. Skript akce skriptu musí být veřejně přístupný na webu.  Pokud je například skript uložen v Azure Storage, použijte buď veřejné kontejnery nebo veřejné objekty blob.

Velikost virtuálního počítače hraničního uzlu musí splňovat požadavky na velikost virtuálního počítače pro pracovní uzel clusteru HDInsight. Doporučené velikosti virtuálních počítačů pracovních uzlů najdete v tématu [Vytvoření clusterů Apache Hadoop ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Po vytvoření hraničního uzlu se můžete připojit k hraničnímu uzlu pomocí protokolu SSH a spustit klientské nástroje pro přístup ke clusteru Hadoop ve službě HDInsight.

> [!WARNING]
> Vlastní komponenty, které jsou nainstalované na hraničním uzlu, přijímají komerčně přiměřenou podporu od Microsoftu. To může vést k řešení problémů, ke kterým dochází. Nebo můžete být odkazováni na prostředky komunity, které vám pomohou další pomoc. Níže jsou uvedeny některé z nejaktivnějších webů, které vám pomůžou získat nápovědu od komunity:
>
> * [Microsoft Q&Stránka s otázkou pro HDInsight](/answers/topics/azure-hdinsight.html)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Pokud používáte technologii Apache, může být možné najít pomoc prostřednictvím webů Apache v systému [https://apache.org](https://apache.org) , jako je například web [Apache Hadoop](https://hadoop.apache.org/) .

> [!IMPORTANT]
> Ubuntu images budou k dispozici pro nové vytvoření clusteru HDInsight do 3 měsíců od publikování. Od ledna 2019 **se automaticky** neopravují spuštěné clustery (včetně hraničních uzlů). Pokud chtějí zákazníci opravit běžící cluster, musí použít akce skriptu nebo jiné mechanismy.  Další informace najdete v tématu [opravy operačního systému pro HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Přidání hraničního uzlu do existujícího clusteru

V této části použijete šablonu Správce prostředků k přidání hraničního uzlu do stávajícího clusteru HDInsight.  Šablonu Správce prostředků můžete najít na [GitHubu](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Šablona Správce prostředků volá akci skriptu umístěnou na adrese https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh . Skript neprovede žádné akce.  Je k tomu předvedení volání akce skriptu z Správce prostředků šablony.

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu Azure Resource Manager v Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Nakonfigurujte následující vlastnosti:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|Vyberte předplatné Azure, které se používá k vytvoření clusteru.|
    |Skupina prostředků|Vyberte skupinu prostředků použitou pro existující cluster HDInsight.|
    |Umístění|Vyberte umístění existujícího clusteru HDInsight.|
    |Název clusteru|Zadejte název existujícího clusteru HDInsight.|

1. Zaškrtněte možnost **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a potom výběrem  **koupit** vytvořte hraniční uzel.

> [!IMPORTANT]  
> Ujistěte se, že jste vybrali skupinu prostředků Azure pro existující cluster HDInsight.  V opačném případě se zobrazí chybová zpráva "u vnořeného prostředku nelze provést požadovanou operaci. &lt;Nenašel se nadřazený prostředek> název_clusteru.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Přidat hraniční uzel při vytváření clusteru

V této části použijete šablonu Správce prostředků k vytvoření clusteru HDInsight s hraničním uzlem.  Šablonu Správce prostředků najdete v [galerii šablon pro rychlý Start Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Šablona Správce prostředků volá akci skriptu umístěnou na adrese https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh . Skript neprovede žádné akce.  Je k tomu předvedení volání akce skriptu z Správce prostředků šablony.

1. Vytvořte cluster HDInsight, pokud ho ještě nemáte.  Viz Začínáme [používat Hadoop ve službě HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu Azure Resource Manager v Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Nakonfigurujte následující vlastnosti:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|Vyberte předplatné Azure, které se používá k vytvoření clusteru.|
    |Skupina prostředků|Vytvořte novou skupinu prostředků použitou pro cluster.|
    |Umístění|Vyberte umístění skupiny prostředků.|
    |Název clusteru|Zadejte název nového clusteru, který chcete vytvořit.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno Hadoop HTTP.  Výchozí uživatelské jméno je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo uživatele Hadoop HTTP.|
    |Uživatelské jméno SSH|Zadejte uživatelské jméno SSH. Výchozí název je **sshuser**.|
    |Heslo SSH|Zadejte heslo uživatele SSH.|
    |Nainstalovat akci skriptu|Ponechte výchozí hodnotu pro procházení tohoto článku.|

    V šabloně se pevně zakódované některé vlastnosti: typ clusteru, počet pracovních uzlů clusteru, velikost hraničního uzlu a název hraničního uzlu.

1. Zaškrtněte možnost **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a výběrem  **koupit** vytvoříte cluster s hraničním uzlem.

## <a name="add-multiple-edge-nodes"></a>Přidat více hraničních uzlů

Do clusteru HDInsight můžete přidat několik hraničních uzlů.  Konfiguraci více hraničních uzlů lze provést pouze pomocí šablon Azure Resource Manager.  Podívejte se na ukázku šablony na začátku tohoto článku.  Aktualizujte **targetInstanceCount** tak, aby odráželo počet hraničních uzlů, které byste chtěli vytvořit.

## <a name="access-an-edge-node"></a>Přístup k hraničnímu uzlu

Koncový bod SSH na hraničním uzlu je &lt; EdgeNodeName>. &lt; Název_clusteru> – ssh.azurehdinsight.net:22.  Například new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Hraniční uzel se zobrazí jako aplikace na Azure Portal.  Portál poskytuje informace pro přístup k hraničnímu uzlu pomocí protokolu SSH.

**Ověření koncového bodu SSH hraničního uzlu**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete cluster HDInsight s hraničním uzlem.
3. Vyberte **aplikace**. Zobrazí se hraniční uzel.  Výchozí název je **New-edgenode**.
4. Vyberte hraniční uzel. Zobrazí se koncový bod SSH.

**Použití podregistru na hraničním uzlu**

1. Připojení k hraničnímu uzlu pomocí SSH Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po připojení k hraničnímu uzlu pomocí protokolu SSH pomocí následujícího příkazu otevřete konzolu podregistru:

    ```console
    hive
    ```

3. Spusťte následující příkaz pro zobrazení tabulek podregistru v clusteru:

    ```hiveql
    show tables;
    ```

## <a name="delete-an-edge-node"></a>Odstranění hraničního uzlu

Hraniční uzel můžete z Azure Portal odstranit.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete cluster HDInsight s hraničním uzlem.
3. Vyberte **aplikace**. Zobrazí se seznam hraničních uzlů.  
4. Klikněte pravým tlačítkem myši na hraniční uzel, který chcete odstranit, a vyberte **Odstranit**.
5. Akci potvrďte výběrem **Ano**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak přidat hraniční uzel a jak přistupovat k hraničnímu uzlu. Další informace najdete v těchto článcích:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se, jak nasadit nepublikovanou aplikaci HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](/rest/api/hdinsight/hdinsight-application): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvoření clusterů Apache Hadoop se systémem Linux v HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Naučte se volat šablony Správce prostředků pro vytváření clusterů HDInsight.
