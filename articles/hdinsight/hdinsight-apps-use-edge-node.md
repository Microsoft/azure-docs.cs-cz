---
title: Použít prázdný edge uzly na clustery systému Hadoop v HDInsight - Azure | Microsoft Docs
description: Postup přidání prázdný hraniční uzel clusteru služby HDInsight, který může být použit jako klient a pak testovacího nebo hostitele aplikace HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jgao
ms.openlocfilehash: 95ffc033a442fcf6074998398104ccb01e7a01a7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170267"
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Použít prázdný edge uzly na clustery systému Hadoop v HDInsight

Informace o postupu přidání prázdný hraniční uzel do clusteru HDInsight. Prázdný hraniční uzel je virtuální počítač s Linuxem pomocí stejných nástrojů klient nainstalován a nakonfigurován jako headnodes, ale žádné služby Hadoop systémem. Hraničního uzlu můžete použít pro přístup ke clusteru, testování vaší klientské aplikace a hostování vaší klientské aplikace. 

Prázdný hraniční uzel můžete přidat do existujícího clusteru HDInsight, do nového clusteru při vytváření clusteru. Přidání prázdný hraniční uzel se provádí pomocí šablony Azure Resource Manager.  Následující příklad ukazuje, jak se provádí pomocí šablony:

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
                            "vmSize": "Standard_D3"
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

Jak je znázorněno v ukázce, můžete volitelně volat [skript akce](hdinsight-hadoop-customize-cluster-linux.md) provést další konfiguraci, například při instalaci [Apache Hue](hdinsight-hadoop-hue-linux.md) v uzlu edge. Skript akce skriptu musí být veřejně přístupné na webu.  Pokud tento skript je uložený v úložišti Azure, použijte například veřejné kontejnery nebo veřejné objekty BLOB.

Velikost virtuálního počítače uzlu edge musí splňovat požadavky HDInsight clusteru pracovní uzel virtuální počítač velikost. Doporučené pracovní uzel velikosti virtuálních počítačů, najdete v části [vytvoření Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Po vytvoření hraniční uzel, můžete připojit k uzlu edge pomocí protokolu SSH a spuštění nástrojů pro klientský přístup ke clusteru Hadoop v HDInsight.

> [!WARNING] 
> Vlastní komponenty, které jsou nainstalovány na uzlu edge získání vyvineme podpory společnosti Microsoft. Může to způsobit řešení problémů, na které narazíte. Nebo může označovat komunitní zdroje pro další pomoc. Tady jsou některé z nejvíc aktivní lokality pro získání nápovědy od komunity:
>
> * [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com).
>
> Pokud používáte technologie Apache, bude pravděpodobně možné najít pomoc prostřednictvím Apache projektu lokalit na [ http://apache.org ](http://apache.org), například [Hadoop](http://hadoop.apache.org/) lokality.

> [!NOTE]
> Stejné jako clustery, edge uzly jsou i oprava spravované.  Další informace najdete v tématu [opravy operačního systému pro HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Přidat k existujícímu clusteru hraniční uzel
V této části použijte šablonu Resource Manager přidat hraniční uzel do existujícího clusteru HDInsight.  Šablony Resource Manageru naleznete v [Githubu](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Šablony Resource Manageru volá akci skriptu nacházející se v https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Skript nebude provádět žádné akce.  Je k předvedení volání akce skriptu z šablony Resource Manageru.

**Chcete-li přidat prázdný hraniční uzel do existujícího clusteru**

1. Kliknutím na následující obrázek otevřete šablonu Azure Resource Manageru na portálu Azure a přihlaste se k Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Nakonfigurujte následující vlastnosti:
   
   * **Předplatné**: Vyberte předplatné Azure, použít pro vytvoření clusteru.
   * **Skupina prostředků**: Vyberte skupinu prostředků, použít pro existující cluster HDInsight.
   * **Umístění**: Vyberte umístění existujícího clusteru HDInsight.
   * **Název clusteru**: Zadejte název existujícího clusteru HDInsight.
   * **Okraj velikost uzlu**: vyberte jednu z velikosti virtuálních počítačů. Velikost virtuálního počítače musí splňovat požadavky velikost pracovního procesu uzlu virtuálního počítače. Doporučené pracovní uzel velikosti virtuálních počítačů, najdete v části [vytvoření Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Okraj uzlu předpony**: výchozí hodnota je **nové**.  Pomocí výchozí hodnoty, je název uzlu edge **nové edgenode**.  Můžete upravit předponu z portálu. Můžete také upravit úplný název ze šablony.

4. Zkontrolujte **souhlasím s podmínkami a ujednáními výše uvedených**a potom klikněte na **nákupu** vytvořit hraničního uzlu.

>[!IMPORTANT]
> Ujistěte se, že vyberte skupinu prostředků Azure pro existující cluster HDInsight.  Jinak zobrazí chybová zpráva "nelze provést požadovanou operaci na vnořeného prostředku. Nadřazený prostředek '&lt;ClusterName >' nebyl nalezen. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Přidat hraniční uzel, při vytváření clusteru
V této části použijte k vytvoření clusteru HDInsight s hraniční uzel, na šablony Resource Manageru.  Šablony Resource Manageru najdete v [galerii šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Šablony Resource Manageru volá akci skriptu nacházející se v https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Skript nebude provádět žádné akce.  Je k předvedení volání akce skriptu z šablony Resource Manageru.

**Vytvoření clusteru HDInsight pomocí hraniční uzel**

1. Pokud ještě nemáte, vytvořte cluster služby HDInsight.  V tématu [začněte s Hadoop v HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Kliknutím na následující obrázek otevřete šablonu Azure Resource Manageru na portálu Azure a přihlaste se k Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Nakonfigurujte následující vlastnosti:
   
   * **Předplatné**: Vyberte předplatné Azure, použít pro vytvoření clusteru.
   * **Skupina prostředků**: Vytvořte novou skupinu prostředků používat pro cluster.
   * **Umístění:** Vyberte umístění pro skupinu prostředků.
   * **Název clusteru**: Zadejte název pro nový cluster vytvořit.
   * **Uživatelské jméno přihlášení clusteru**: Zadejte uživatelské jméno Hadoop HTTP.  Výchozí název je **správce**.
   * **Heslo pro přihlášení clusteru**: Zadejte heslo k uživatelskému Hadoop HTTP.
   * **SSH uživatelské jméno**: Zadejte uživatelské jméno SSH. Výchozí název je **sshuser**.
   * **SSH heslo**: Zadejte heslo uživatele SSH.
   * **Instalace akce skriptu**: ponechte výchozí hodnotu pro procházení tohoto kurzu.
     
     Některé vlastnosti byly pevně kódovaný v šabloně: typ clusteru, počet uzlů pracovního procesu clusteru, velikost uzlu Edge a název uzlu Edge.
4. Zkontrolujte **souhlasím s podmínkami a ujednáními výše uvedených**a potom klikněte na **nákupu** k vytvoření clusteru s hraničního uzlu.

## <a name="add-multiple-edge-nodes"></a>Přidání více uzlů edge

Můžete přidat více edge uzly do clusteru HDInsight.  Konfigurace více uzly okraj lze provést pouze pomocí šablon Azure Resource Manageru.  Viz ukázka šablony na začátku tohoto článku.  Je potřeba aktualizovat **targetInstanceCount** tak, aby odrážela počet uzlů edge, kterou chcete vytvořit.

## <a name="access-an-edge-node"></a>Přístup uzlu edge
Hraničního uzlu ssh koncový bod je &lt;EdgeNodeName >.&lt; Název clusteru >-ssh.azurehdinsight.net:22.  Například nové edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Hraničního uzlu se zobrazí jako aplikace na portálu Azure.  Portál obsahuje informace, přístup k uzlu edge pomocí protokolu SSH.

**Chcete-li ověřit koncový bod SSH uzlu edge**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete HDInsight cluster se hraniční uzel.
3. Klikněte na tlačítko **aplikace**. Zobrazí se hraničního uzlu.  Výchozí název je **nové edgenode**.
4. Klikněte na tlačítko hraničního uzlu. Zobrazí se koncový bod SSH.

**Používání Hive v uzlu edge**

1. Připojení k hraničnímu uzlu pomocí SSH Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po připojení k uzlu edge pomocí protokolu SSH, použijte následující příkaz pro otevření konzoly Hive:
   
        hive
3. Spusťte následující příkaz k zobrazení tabulek Hive v clusteru:
   
        show tables;

## <a name="delete-an-edge-node"></a>Odstranit hraniční uzel
Hraniční uzel můžete odstranit z portálu Azure.

**Pro přístup uzlu edge**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete HDInsight cluster se hraniční uzel.
3. Klikněte na tlačítko **aplikace**. Zobrazí se seznam uzlů okraj.  
4. Klikněte pravým tlačítkem na hraniční uzel, který chcete odstranit a potom klikněte na **odstranit**.
5. Pro potvrzení klikněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili postup přidání hraniční uzel a jak získat přístup k uzlu edge. Další informace naleznete v následujících článcích:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): naučit se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytváření clusterů Hadoop na systému Linux v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Zjistěte, jak voláním šablon Resource Manageru vytvoříte clustery HDInsight.

