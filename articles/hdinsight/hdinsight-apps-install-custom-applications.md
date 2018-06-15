---
title: Instalace vlastních aplikací Hadoop v Azure HDInsight | Dokumentace Microsoftu
description: Informace o instalaci aplikací HDInsight na aplikace HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e556b29c-8176-4bc5-a90b-aa01abfd3aee
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: ab0395f9ee9a8950b439f19502ca6cef6c4f4cf2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201631"
---
# <a name="install-custom-hadoop-applications-on-azure-hdinsight"></a>Instalace vlastních aplikací Hadoop v Azure HDInsight

V tomto článku se dozvíte, jak v Azure HDInsight nainstalovat aplikaci Hadoop, která nebyla publikována na webu Azure Portal. Aplikace, kterou v tomto článku nainstalujete, se jmenuje [Hue](http://gethue.com/).

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight se systémem Linux.  Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými.  

Další související články:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.

## <a name="prerequisites"></a>Požadavky
Pokud chcete instalovat aplikace HDInsight na stávající cluster HDInsight, musí mít cluster služby HDInsight. Chcete-li jeden vytvořit, prostudujte si část [Tvorba clusterů](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Aplikace HDInsight můžete také nainstalovat při vytváření clusteru HDInsight.

## <a name="install-hdinsight-applications"></a>Instalace aplikací HDInsight
Aplikace HDInsight lze nainstalovat při vytvoření clusteru nebo do existujícího clusteru HDInsight. Postup definování šablon Azure Resource Manageru, viz část [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

Soubory potřebné pro nasazení této aplikace (Hue):

* [azuredeploy.JSON](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): Šablona Resource Manageru pro instalaci aplikace HDInsight. Viz [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) pro vývoj vlastní šablony Resource Manageru.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Akce skriptu volaná šablonou Resource Manageru pro konfiguraci hraničního uzlu.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Binární soubor hue volaný ze souboru hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Binární soubor hue volaný ze souboru hui-install_v0.sh.
* [webwasb tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Ukázková webová aplikace (Tomcat) volaná ze souboru hui-install_v0.sh.

**Postup instalace aplikace Hue do stávajícího clusteru HDInsight**

1. Klikněte na následující obrázek pro přihlášení do Azure a otevřete šablonu Resource Manageru na portálu Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Toto tlačítko otevře šablonu Resource Manageru na portálu Azure.  Šablony Resource Manageru se nachází v [ https://github.com/hdinsight/Iaas-Applications/tree/master/Hue ](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Další informace o zápisu této šablony Resource Manageru naleznete v části [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
2. Z okna **Parametry** zadejte následující údaje:

   * **Název clusteru**: Zadejte název clusteru, do kterého chcete aplikaci nainstalovat. Tento cluster musí být existující cluster.
3. Klikněte na možnost **OK** a uložte parametry.
4. Z okna **Vlastní nasazení** zadejte **Skupinu prostředků**.  Skupina prostředků je kontejner, který seskupuje cluster, účet závislého úložiště a další prostředky. Je nezbytná k použití stejné skupiny prostředků jako cluster.
5. Klikněte na tlačítko **Smluvní podmínky** a pak klikněte na tlačítko **Vytvořit**.
6. Ověřte zaškrtnutí políčka **Kód pin pro řídicí panel** a pak klikněte na tlačítko **Vytvořit**. Stav instalace můžete zobrazit z dlaždice připnuté k řídicímu panelu portálu a portálu oznámení (kliknutím na ikonu zvonku v horní části portálu).  Instalace aplikace trvá přibližně 10 minut.

**Postup instalace aplikace Hue při vytváření clusteru**

1. Klikněte na následující obrázek pro přihlášení do Azure a otevřete šablonu Resource Manageru na portálu Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Toto tlačítko otevře šablonu Resource Manageru na portálu Azure.  Šablony Resource Manageru se nachází v [ https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json ](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Další informace o zápisu této šablony Resource Manageru naleznete v části [MSDN: instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
2. Pro vytvoření clusteru a instalaci aplikace Hue postupujte podle pokynů. Další informace o vytváření clusterů HDInsight naleznete v tématu [Vytváření clusterů Hadoop založených na Linuxu v nástroji HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Kromě portálu Azure můžete pro volání šablon Resource Manageru použít také [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) a [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-cli).

## <a name="validate-the-installation"></a>Ověření instalace
Stav aplikace můžete zkontrolovat na portálu Azure a ověřit tak instalaci aplikace. Kromě toho můžete také ověřit, zda všechny koncové body HTTP vychází dle očekávání a webovou stránku, pokud existuje:

**Postup otevření portál Hue**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **Clustery HDInsight** v levé nabídce.  Pokud ho nevidíte, klikněte na tlačítko **Procházet** a pak klikněte na tlačítko **Clustery HDInsight**.
3. Klikněte na cluster, kde je nainstalovaná aplikace.
4. Z okna **Nastavení** klikněte na tlačítko **Aplikace** pod kategorií **Obecné**. Měli byste vidět položku **hue** uvedenou v okně **Nainstalované aplikace**.
5. Klikněte na položku **hue** na seznamu a zobrazte seznam vlastností.  
6. Klikněte na odkaz webové stránky a ověřte webovou stránku; koncový bod HTTP, otevřete v prohlížeči a ověřte uživatelské rozhraní webu Hue, otevřete koncový bod SSH pomocí protokolu SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="troubleshoot-the-installation"></a>Řešení potíží instalace
Stav instalace aplikace můžete zkontrolovat na portálu oznámení (kliknutím na ikonu zvonku v horní části portálu).

Pokud se instalace aplikace nezdařila, můžete zobrazit chybové zprávy a informace ladění ze 3 míst:

* Aplikace HDInsight: obecné informace o chybě.

    Otevřete cluster z portálu a v okně Nastavení klikněte na tlačítko Aplikace:

    ![hdinsight aplikace aplikace chyba instalace](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* Akce skriptu HDInsight: Pokud chybová zpráva aplikace HDInsight značí selhání akce skriptu, zobrazí se další podrobnosti o selhání skriptu v podokně Akce skriptu.

    V okně Nastavení klikněte na tlačítko Akce skriptu. V historii akcí skriptu se zobrazí chybové zprávy

    ![hdinsight aplikace skript akce chyba](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Webové uživatelské rozhraní Ambari: Pokud byl příčinou selhání instalační skript, použijte webové uživatelské rozhraní Ambari ke kontrole úplných protokolů týkajících se skriptů instalace.

    Další informace naleznete v tématu [Poradce při potížích](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Odstranění aplikací HDInsight
Existuje několik způsobů jak odstranit aplikace HDInsight.

### <a name="use-portal"></a>Použít portál
**Postup odebrání aplikace pomocí portálu**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **Clustery HDInsight** v levé nabídce.  Pokud ho nevidíte, klikněte na tlačítko **Procházet** a pak klikněte na tlačítko **Clustery HDInsight**.
3. Klikněte na cluster, kde je nainstalovaná aplikace.
4. Z okna **Nastavení** klikněte na tlačítko **Aplikace** pod kategorií **Obecné**. Zobrazí se seznam nainstalovaných aplikací. Pro toto školení je položka **hue** uvedena v okně **Nainstalované aplikace**.
5. Klikněte pravým tlačítkem na aplikaci, kterou chcete odebrat, a pak klikněte na tlačítko **Odstranit**.
6. Pro potvrzení klikněte na tlačítko **Ano**.

Z portálu můžete také odstranit cluster nebo odstranit skupinu prostředků, která obsahuje aplikaci.

### <a name="use-azure-powershell"></a>Použití Azure Powershell
Pomocí Azure PowerShell můžete odstranit cluster nebo skupinu prostředků. Viz téma [Odstranění clusterů pomocí Azure PowerShell](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Použití Azure CLI
Pomocí Azure CLI můžete odstranit cluster nebo skupinu prostředků. Viz téma [Odstranění clusterů pomocí Azure CLI](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>Další postup
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): další informace jak vyvíjet šablony Resource Manageru pro nasazení aplikací HDInsight.
* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytváření clusterů Hadoop na systému Linux v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Zjistěte, jak voláním šablon Resource Manageru vytvoříte clustery HDInsight.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Zjistěte, jak lze pomocí prázdných hraničních uzlů přistupovat ke clusteru HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.
