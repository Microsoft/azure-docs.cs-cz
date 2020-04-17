---
title: Použití prázdných hraničních uzlů v clusterech Apache Hadoop v Azure HDInsight
description: Jak přidat prázdný uzel okraje do clusteru HDInsight. Používá se jako klient a pak testujte nebo hostujte aplikace HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537793"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Použití prázdných hraničních uzlů v clusterech Apache Hadoop v HDInsightu

Přečtěte si, jak přidat prázdný hraniční uzel do clusteru HDInsight. Prázdný hraniční uzel je virtuální počítač Linuxu se stejnými klientskými nástroji nainstalovanými a nakonfigurovanými jako v hlavových uzlech. Ale bez spuštěných služeb [Apache Hadoop.](./hadoop/apache-hadoop-introduction.md) Hraniční uzel můžete použít pro přístup ke clusteru, testování klientských aplikací a hostování klientských aplikací.

Prázdný uzel hrany můžete přidat do existujícího clusteru HDInsight do nového clusteru při vytváření clusteru. Přidání prázdného hraničního uzlu se provádí pomocí šablony Azure Resource Manager.  Následující ukázka ukazuje, jak se to provádí pomocí šablony:

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

Jak je znázorněno v ukázce, můžete volitelně volat [akci skriptu](hdinsight-hadoop-customize-cluster-linux.md) pro další konfiguraci. Například instalace [Apache Hue](hdinsight-hadoop-hue-linux.md) v hraničním uzlu. Skript akce skriptu musí být veřejně přístupné na webu.  Například pokud je skript uložený ve službě Azure Storage, použijte veřejné kontejnery nebo veřejné objekty BLOB.

Velikost virtuálního počítače hraničního uzlu musí splňovat požadavky na velikost virtuálního počítače clusteru CLUSTERU HDInsight. Doporučené velikosti virtuálních portů pracovních uzlů najdete v tématu [Vytvoření clusterů Apache Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Po vytvoření hraničního uzlu se můžete připojit k hraničnímu uzlu pomocí SSH a spustit klientské nástroje pro přístup ke clusteru Hadoop v HDInsightu.

> [!WARNING]
> Vlastní součásti, které jsou nainstalovány na hraničním uzlu, získávají komerčně přiměřenou podporu od společnosti Microsoft. To může mít za následek řešení problémů, se kterými se setkáte. Nebo můžete být odkázáni na komunitní zdroje pro další pomoc. Níže jsou uvedeny některé z nejaktivnějších stránek pro získání pomoci od komunity:
>
> * [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Pokud používáte technologii Apache, můžete být schopni najít pomoc [https://apache.org](https://apache.org)prostřednictvím stránek projektu Apache na , jako je například web [Apache Hadoop.](https://hadoop.apache.org/)

> [!IMPORTANT]
> Obrázky Ubuntu budou k dispozici pro vytvoření nového clusteru HDInsight do 3 měsíců od zveřejnění. Od ledna 2019 nejsou spuštěné clustery (včetně hraničních uzlů) **automaticky** opraveny. Zákazníci musí k opravě spuštěného clusteru použít akce skriptu nebo jiné mechanismy.  Další informace naleznete [v tématu Opravy operačního systému pro HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Přidání hraničního uzlu do existujícího clusteru

V této části použijete šablonu Správce prostředků k přidání hraničního uzlu do existujícího clusteru HDInsight.  Šablonu Správce prostředků najdete na [GitHubu](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Šablona Správce prostředků volá akci https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.shskriptu umístěnou na adrese . Skript nedělá žádné akce.  Je to demonstrovat volání skriptakce ze šablony Správce prostředků.

1. Vyberte následující bitovou kopii, kterou chcete přihlásit do Azure a otevřete šablonu Azure Resource Manager na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Nakonfigurujte následující vlastnosti:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|Vyberte předplatné Azure používané k vytvoření clusteru.|
    |Skupina prostředků|Vyberte skupinu prostředků použitou pro existující cluster HDInsight.|
    |Umístění|Vyberte umístění existujícího clusteru HDInsight.|
    |Název clusteru|Zadejte název existujícího clusteru HDInsight.|

1. **Zaškrtněte, souhlasím s výše uvedenými podmínkami**a pak vyberte možnost Koupit **a** vytvořte hraniční uzel.

> [!IMPORTANT]  
> Nezapomeňte vybrat skupinu prostředků Azure pro existující cluster HDInsight.  V opačném případě se zobrazí chybová zpráva "Nelze provést požadovanou operaci na vnořený prostředek. Nadřazený prostředek _&lt;Název_clusteru> nebyl nalezen."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Přidání hraničního uzlu při vytváření clusteru

V této části použijete šablonu Správce prostředků k vytvoření clusteru HDInsight s hraničním uzlem.  Šablonu Správce prostředků najdete v [galerii šablon rychlého startu Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Šablona Správce prostředků volá akci https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.shskriptu umístěnou na adrese . Skript nedělá žádné akce.  Je to demonstrovat volání skriptakce ze šablony Správce prostředků.

1. Pokud cluster HDInsight ještě nemáte, vytvořte ho.  Viz [Začínáme používat Hadoop v HDInsightu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Vyberte následující bitovou kopii, kterou chcete přihlásit do Azure a otevřete šablonu Azure Resource Manager na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Nakonfigurujte následující vlastnosti:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|Vyberte předplatné Azure používané k vytvoření clusteru.|
    |Skupina prostředků|Vytvořte novou skupinu prostředků používanou pro cluster.|
    |Umístění|Vyberte umístění skupiny prostředků.|
    |Název clusteru|Zadejte název nového clusteru, který chcete vytvořit.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno Hadoop HTTP.  Výchozí uživatelské jméno je **admin**.|
    |Heslo přihlášení clusteru|Zadejte uživatelské heslo Hadoop HTTP.|
    |Uživatelské jméno SSH|Zadejte uživatelské jméno SSH. Výchozí název je **sshuser**.|
    |Ssh heslo|Zadejte uživatelské heslo SSH.|
    |Instalace akce skriptu|Zachovat výchozí hodnotu pro procházení tohoto článku.|

    Některé vlastnosti byly v šabloně pevně zakódovány: Typ clusteru, Počet pracovních uzlů clusteru, Velikost uzlu Edge a Název uzlu Edge.

1. **Zaškrtněte, souhlasím s výše uvedenými podmínkami**a pak vyberte možnost **Nákup** a vytvořte cluster s hraničním uzlem.

## <a name="add-multiple-edge-nodes"></a>Přidání více uzlů okrajů

Do clusteru HDInsight můžete přidat více hraničních uzlů.  Konfiguraci více hraničních uzlů lze provést jenom pomocí šablon Azure Resource Manager.  Viz ukázka šablony na začátku tohoto článku.  Aktualizujte **targetInstanceCount** tak, aby odrážel počet hraničních uzlů, které chcete vytvořit.

## <a name="access-an-edge-node"></a>Přístup k hraničnímu uzlu

Koncový bod hraničního uzlu &lt;ssh je EdgeNodeName>. &lt;Název clusteru>-ssh.azurehdinsight.net:22.  Například new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Hraniční uzel se zobrazí jako aplikace na webu Azure Portal.  Portál poskytuje informace pro přístup k hraničnímu uzlu pomocí SSH.

**Ověření koncového bodu Hraniční uzel SSH**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete cluster HDInsight s hraničním uzlem.
3. Vyberte **možnost Aplikace**. Uvidíte uzel okraje.  Výchozí název je **new-edgenode**.
4. Vyberte uzel hrany. Uvidíte koncový bod SSH.

**Použití Hive na hraničním uzlu**

1. Připojení k hraničnímu uzlu pomocí SSH Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po připojení k hraničnímu uzlu pomocí SSH otevřete konzolu Hive pomocí následujícího příkazu:

        hive

3. Spuštěním následujícího příkazu zobrazíte tabulky Hive v clusteru:

        show tables;

## <a name="delete-an-edge-node"></a>Odstranění hraničního uzlu

Hraniční uzel můžete odstranit z webu Azure Portal.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete cluster HDInsight s hraničním uzlem.
3. Vyberte **možnost Aplikace**. Zobrazí se seznam hraničních uzlů.  
4. Klikněte pravým tlačítkem myši na uzel okraje, který chcete odstranit, a pak vyberte **odstranit**.
5. Výběrem **Ano** potvrďte.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak přidat hraniční uzel a jak získat přístup k hraničnímu uzlu. Další informace naleznete v následujících článcích:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Instalace vlastních aplikací HDInsight:](hdinsight-apps-install-custom-applications.md)Přečtěte si, jak nasadit nepublikovanou aplikaci HDInsight do HDInsightu.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvářejte clustery Apache Hadoop založené na Linuxu v HDInsightpomocí šablon Resource Manageru:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)přečtěte si, jak volat šablony Správce prostředků k vytvoření clusterů HDInsight.
