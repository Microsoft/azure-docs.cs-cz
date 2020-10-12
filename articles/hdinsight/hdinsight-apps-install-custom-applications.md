---
title: Instalace vlastních aplikací Apache Hadoop ve službě Azure HDInsight
description: Naučte se instalovat aplikace HDInsight pro Apache Hadoop clustery ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 9fd8152b4180d44d3b822feef7e74e267b6b948a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086496"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalace vlastních aplikací Apache Hadoop ve službě Azure HDInsight

V tomto článku se dozvíte, jak nainstalovat aplikaci [Apache Hadoop](https://hadoop.apache.org/) do služby Azure HDInsight, která nebyla publikována do Azure Portal. Aplikace, kterou nainstalujete v tomto článku, je [Odstínová](https://gethue.com/).

An HDInsight aplikace je aplikace, kterou uživatelé můžou instalovat v clusteru HDInsight.  Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

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

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu Správce prostředků v Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Šablona Správce prostředků se nachází na adrese [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) .  Další informace o zápisu této šablony Resource Manageru naleznete v části [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

1. Z rozevíracího seznamu vyberte existující **skupinu prostředků** , která obsahuje váš cluster. Je nutné použít stejnou skupinu prostředků jako cluster.

1. Zadejte název clusteru, do kterého chcete aplikaci nainstalovat. Tento cluster musí být existující cluster.

1. Zaškrtněte políčko pro souhlasím **s podmínkami a ujednáními uvedenými nahoře**.

1. Vyberte **Koupit**.

Stav instalace můžete zobrazit z dlaždice připnuté k řídicímu panelu portálu a portálu oznámení (kliknutím na ikonu zvonku v horní části portálu).  Instalace aplikace trvá přibližně 10 minut.

### <a name="to-install-hue-while-creating-a-cluster"></a>Postup instalace aplikace Hue při vytváření clusteru

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu Správce prostředků v Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Šablona Správce prostředků se nachází na adrese [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) .  Další informace o zápisu této šablony Resource Manageru naleznete v části [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

2. Pro vytvoření clusteru a instalaci aplikace Hue postupujte podle pokynů. Další informace o vytváření clusterů HDInsight naleznete v tématu [Vytváření clusterů Hadoop založených na Linuxu v nástroji HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Jiné metody instalace

Kromě portálu Azure můžete pro volání šablon Resource Manageru použít také [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) a [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli).

## <a name="validate-the-installation"></a>Ověření instalace

Stav aplikace můžete zkontrolovat na portálu Azure a ověřit tak instalaci aplikace. Kromě toho můžete také ověřit, že všechny koncové body HTTP byly vydány podle očekávání a webové stránky, pokud existuje.

Pro **odstín**můžete použít následující kroky:

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Vyberte cluster, do kterého jste nainstalovali aplikaci.
1. V nabídce **Nastavení** vyberte **aplikace**.
1. Výběrem možnosti **odstín** ze seznamu zobrazíte vlastnosti.  
1. Vyberte odkaz webová stránka pro ověření webu.

### <a name="azure-cli"></a>Azure CLI

Nahraďte `CLUSTERNAME` a `RESOURCEGROUP` příslušnými hodnotami a potom zadejte následující příkazy:

* Zobrazí seznam všech aplikací pro cluster HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Pro načtení vlastností zadané aplikace.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Řešení potíží instalace

Stav instalace aplikace můžete zkontrolovat na portálu oznámení (kliknutím na ikonu zvonku v horní části portálu).

Pokud se instalace aplikace nezdařila, můžete zobrazit chybové zprávy a informace o ladění ze tří míst:

* Aplikace HDInsight: obecné informace o chybě.

    Otevřete cluster z portálu a vyberte aplikace z nastavení:

    ![hdinsight aplikace aplikace chyba instalace](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Akce skriptu HDInsight: Pokud chybová zpráva aplikace HDInsight značí selhání akce skriptu, zobrazí se další podrobnosti o selhání skriptu v podokně Akce skriptu.

    Vyberte akci skriptu z nastavení. V historii akcí skriptu se zobrazí chybové zprávy

    ![hdinsight aplikace skript akce chyba](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Webové uživatelské rozhraní Apache Ambari: Pokud byl příčinou selhání instalační skript, použijte webové uživatelské rozhraní Ambari ke kontrole úplných protokolů o instalačních skriptech.

    Další informace najdete v tématu [Poradce při potížích s akcemi skriptů](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Odstranění aplikací HDInsight

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Vyberte cluster, do kterého jste nainstalovali aplikaci.
1. V nabídce **Nastavení** vyberte **aplikace**.
1. Klikněte pravým tlačítkem na aplikaci, kterou chcete odebrat, a pak vyberte **Odstranit**.
1. Akci potvrďte výběrem **Ano**.

### <a name="azure-cli"></a>Azure CLI

`NAME`Hodnoty, `CLUSTERNAME` a nahraďte `RESOURCEGROUP` příslušnými hodnotami a potom zadejte následující příkaz:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Další kroky

* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): další informace jak vyvíjet šablony Resource Manageru pro nasazení aplikací HDInsight.
* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvoření clusterů Apache Hadoop se systémem Linux v HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Naučte se volat šablony Správce prostředků pro vytváření clusterů HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.
