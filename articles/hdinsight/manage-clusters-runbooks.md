---
title: 'Kurz: K vytváření clusterů pomocí runbooků Azure Automation - Azure HDInsight'
description: Zjistěte, jak vytvářet a odstraňovat clustery Azure HDInsight pomocí skriptů, které běží v cloudu pomocí runbooků Azure Automation.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75553510"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Kurz: Vytváření clusterů Azure HDInsight pomocí Azure Automation

Azure Automation umožňuje vytvářet skripty, které běží v cloudu a spravovat prostředky Azure na vyžádání nebo na základě plánu. Tento článek popisuje, jak vytvořit runbooky Prostředí PowerShell k vytvoření a odstranění clusterů Azure HDInsight.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nainstalujte moduly potřebné pro interakci s HDInsight.
> * Vytvořte a uložte pověření potřebná během vytváření clusteru.
> * Vytvořte novou runbook Azure Automation a vytvořte cluster HDInsight.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Existující [účet Azure Automation](../automation/automation-quickstart-create-account.md).
* Existující [účet úložiště Azure](../storage/common/storage-account-create.md), který se bude používat jako úložiště clusteru.

## <a name="install-hdinsight-modules"></a>Instalace modulů HDInsight

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte účty Azure Automation.
1. V části **Sdílené prostředky**vyberte **galerii Modulů** .
1. Do pole zadejte **AzureRM.Profile** a stiskněte klávesu ENTER a vyhledejte. Vyberte dostupný výsledek hledání.
1. Na obrazovce **profilu AzureRM.vyberte** **Importovat**. Zaškrtněte políčko pro aktualizaci modulů Azure a pak vyberte **OK**.

    ![import modulu AzureRM.profile](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Vraťte se do galerie modulů výběrem **galerie Moduly** v části **Sdílené prostředky**.
1. Zadejte **příkaz HDInsight**. Vyberte **AzureRM.HDInsight**.

    ![procházet moduly HDInsight](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. Na panelu **AzureRM.HDInsight** vyberte **Importovat** a **OK**.

    ![import modulu AzureRM.HDInsight](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

1. V části **Sdílené prostředky**vyberte **pověření**.
1. Vyberte **Přidat pověření**.
1. Zadejte požadované informace na panelu **Nové pověření.** Toto pověření je k uložení hesla clusteru, které vám umožní přihlásit se do Ambari.

    | Vlastnost | Hodnota |
    | --- | --- |
    | Name (Název) | `cluster-password` |
    | Uživatelské jméno | `admin` |
    | Heslo | `SECURE_PASSWORD` |
    | Potvrzení hesla | `SECURE_PASSWORD` |

1. Vyberte **Vytvořit**.
1. Opakujte stejný postup pro `ssh-password` nové `sshuser` přihlašovací údaje s uživatelským jménem a heslem dle vašeho výběru. Vyberte **Vytvořit**. Toto pověření je pro uložení hesla SSH pro váš cluster.

    ![vytvoření přihlašovacích údajů](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Vytvoření runbooku pro vytvoření clusteru

1. V části **Automatizace procesů**vyberte **Runbook** .
1. Vyberte **Vytvořit runbook**.
1. Na **panelu Vytvořit runbook** zadejte název runbooku, například `hdinsight-cluster-create`. V rozevíracím seznamu **Typ runbooku** vyberte **Powershell.**
1. Vyberte **Vytvořit**.

    ![vytvořit runbook](./media/manage-clusters-runbooks/create-runbook.png)

1. Na obrazovce Upravit **runbook prostředí PowerShell** zadejte následující kód a vyberte **Publikovat**:

    ![publikovat runbook](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Vytvoření runbooku pro odstranění clusteru

1. V části **Automatizace procesů**vyberte **Runbook** .
1. Vyberte **Vytvořit runbook**.
1. Na **panelu Vytvořit runbook** zadejte název runbooku, například `hdinsight-cluster-delete`. V rozevíracím seznamu **Typ runbooku** vyberte **Powershell.**
1. Vyberte **Vytvořit**.
1. Na obrazovce Upravit **runbook prostředí PowerShell** zadejte následující kód a vyberte **Publikovat**:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Spuštění runbooků

### <a name="create-a-cluster"></a>Vytvoření clusteru

1. Seznam runbooků pro váš účet Automation zobrazíte výběrem sady **Runbook** v části **Automatizace procesů**.
1. Vyberte `hdinsight-cluster-create`nebo název, který jste použili při vytváření clusteru runbook.
1. Chcete-li spustit runbook okamžitě, vyberte **možnost Spustit.** Můžete také naplánovat pravidelné spouštění runbooků. Viz [Plánování runbooku v Azure Automation](../automation/shared-resources/schedules.md)
1. Zadejte požadované parametry skriptu a vyberte **OK**. Tím se vytvoří nový cluster HDInsight s názvem, který jste zadali v parametru **CLUSTERNAME.**

    ![spuštění vytvořit clusteru runbook](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Odstranění clusteru

Odstraňte cluster výběrem `hdinsight-cluster-delete` runbooku, který jste vytvořili. Vyberte **Start**, zadejte parametr **CLUSTERNAME** a vyberte **OK**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, odstraňte účet Azure Automation, který byl vytvořen, aby se zabránilo nezamýšleným poplatkům. Pokud tak chcete provést, přejděte na portál Azure, vyberte skupinu prostředků, do které jste vytvořili účet Azure Automation, vyberte účet Automation a pak **vyberte Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa clusterů Apache Hadoop v HDInsightu pomocí Azure PowerShellu](hdinsight-administer-use-powershell.md)