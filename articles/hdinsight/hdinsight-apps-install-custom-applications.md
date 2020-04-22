---
title: Instalace vlastních aplikací Apache Hadoop na Azure HDInsight
description: Přečtěte si, jak nainstalovat hdinsight ové aplikace pro clustery Apache Hadoop v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: b25e9d860f118c1b044b13e01a80aaf7a24963cf
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768245"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalace vlastních aplikací Apache Hadoop na Azure HDInsight

V tomto článku se dozvíte, jak nainstalovat aplikaci [Apache Hadoop](https://hadoop.apache.org/) na Azure HDInsight, která nebyla publikována na webu Azure Portal. Aplikace, kterou nainstalujete v tomto článku je [Hue](https://gethue.com/).

Aplikace HDInsight je aplikace, kterou mohou uživatelé nainstalovat do clusteru HDInsight.  Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

## <a name="prerequisites"></a>Požadavky

Pokud chcete instalovat aplikace HDInsight na stávající cluster HDInsight, musí mít cluster služby HDInsight. Chcete-li jeden vytvořit, prostudujte si část [Tvorba clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md). Aplikace HDInsight můžete také nainstalovat při vytváření clusteru HDInsight.

## <a name="install-hdinsight-applications"></a>Instalace aplikací HDInsight

Aplikace HDInsight lze nainstalovat při vytvoření clusteru nebo do existujícího clusteru HDInsight. Postup definování šablon Azure Resource Manageru, viz část [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

Soubory potřebné pro nasazení této aplikace (Hue):

* [azuredeploy.JSON](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): Šablona Resource Manageru pro instalaci aplikace HDInsight. Viz [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) pro vývoj vlastní šablony Resource Manageru.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Akce skriptu volaná šablonou Resource Manageru pro konfiguraci hraničního uzlu.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Binární soubor hue volaný ze souboru hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Binární soubor hue volaný ze souboru hui-install_v0.sh.
* [webwasb tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Ukázková webová aplikace (Tomcat) volaná ze souboru hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Postup instalace aplikace Hue do stávajícího clusteru HDInsight

1. Vyberte následující bitovou kopii, kterou chcete přihlásit do Azure a otevřete šablonu Správce prostředků na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Šablona Správce prostředků [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)je umístěna na adrese .  Další informace o zápisu této šablony Resource Manageru naleznete v části [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

1. V rozevíracím seznamu vyberte existující **skupinu prostředků,** která obsahuje váš cluster. Je nutné použít stejnou skupinu prostředků jako cluster.

1. Zadejte název clusteru, do kterého chcete aplikaci nainstalovat. Tento cluster musí být existující cluster.

1. Zaškrtněte **políčko, protože souhlasím s výše uvedenými podmínkami**.

1. Vyberte **Koupit**.

Stav instalace můžete zobrazit z dlaždice připnuté k řídicímu panelu portálu a portálu oznámení (kliknutím na ikonu zvonku v horní části portálu).  Instalace aplikace trvá přibližně 10 minut.

### <a name="to-install-hue-while-creating-a-cluster"></a>Postup instalace aplikace Hue při vytváření clusteru

1. Vyberte následující bitovou kopii, kterou chcete přihlásit do Azure a otevřete šablonu Správce prostředků na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Šablona Správce prostředků [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)je umístěna na adrese .  Další informace o zápisu této šablony Resource Manageru naleznete v části [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

2. Pro vytvoření clusteru a instalaci aplikace Hue postupujte podle pokynů. Další informace o vytváření clusterů HDInsight naleznete v tématu [Vytváření clusterů Hadoop založených na Linuxu v nástroji HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Jiné metody instalace

Kromě portálu Azure můžete pro volání šablon Resource Manageru použít také [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) a [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli).

## <a name="validate-the-installation"></a>Ověření instalace

Stav aplikace můžete zkontrolovat na portálu Azure a ověřit tak instalaci aplikace. Kromě toho můžete také ověřit všechny koncové body PROTOKOLU HTTP přišel podle očekávání a webové stránky, pokud existuje.

Pro **Hue**můžete použít následující kroky:

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte cluster, do kterého jste aplikaci nainstalovali.
1. V nabídce **Nastavení** vyberte **Aplikace**.
1. **Chcete-li** zobrazit vlastnosti, vyberte ze seznamu odstín.  
1. Vyberte odkaz webová stránka pro ověření webu.

### <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME`Nahraďte `RESOURCEGROUP` a zadejte příslušné hodnoty a zadejte následující příkazy:

* Chcete-li seznam všech aplikací pro cluster HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Chcete-li načíst vlastnosti zadané aplikace.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Řešení potíží instalace

Stav instalace aplikace můžete zkontrolovat na portálu oznámení (kliknutím na ikonu zvonku v horní části portálu).

Pokud se instalace aplikace nezdařila, zobrazí se chybové zprávy a informace o ladění ze tří míst:

* Aplikace HDInsight: obecné informace o chybě.

    Otevřete cluster z portálu a vyberte Aplikace z nastavení:

    ![hdinsight aplikace aplikace chyba instalace](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Akce skriptu HDInsight: Pokud chybová zpráva aplikace HDInsight značí selhání akce skriptu, zobrazí se další podrobnosti o selhání skriptu v podokně Akce skriptu.

    V nastavení vyberte Akce skriptu. V historii akcí skriptu se zobrazí chybové zprávy

    ![hdinsight aplikace skript akce chyba](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari Web UI: If the install script was the cause of the failure, use Ambari Web UI to check full logs about the install scripts.

    Další informace naleznete [v tématu Poradce při potížích s akcemi skriptu](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Odstranění aplikací HDInsight

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte cluster, do kterého jste aplikaci nainstalovali.
1. V nabídce **Nastavení** vyberte **Aplikace**.
1. Klikněte pravým tlačítkem myši na aplikaci, kterou chcete odebrat, a pak vyberte **odstranit**.
1. Výběrem **Ano** potvrďte.

### <a name="azure-cli"></a>Azure CLI

Nahraďte `NAME`, `CLUSTERNAME`a `RESOURCEGROUP` s příslušnými hodnotami a zadejte následující příkaz:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Další kroky

* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): další informace jak vyvíjet šablony Resource Manageru pro nasazení aplikací HDInsight.
* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvářejte clustery Apache Hadoop založené na Linuxu v HDInsightpomocí šablon Resource Manageru:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)přečtěte si, jak volat šablony Správce prostředků k vytvoření clusterů HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.
