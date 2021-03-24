---
title: 'Kurz: použití sad Runbook Azure Automation k vytváření clusterů – Azure HDInsight'
description: Naučte se vytvářet a odstraňovat clustery Azure HDInsight pomocí skriptů, které běží v cloudu, pomocí Azure Automation Runbooky.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 5eb0f353579233041bb5ccba46de2549ada7e9b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864784"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Kurz: vytvoření clusterů Azure HDInsight pomocí Azure Automation

Azure Automation vám umožní vytvářet skripty, které běží v cloudu a spravují prostředky Azure na vyžádání nebo podle plánu. Tento článek popisuje, jak vytvořit Runbooky PowerShellu pro vytváření a odstraňování clusterů Azure HDInsight.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nainstalujte moduly nezbytné pro interakci se službou HDInsight.
> * Vytvořte a uložte přihlašovací údaje potřebné při vytváření clusteru.
> * Vytvořte novou Azure Automation sadu Runbook pro vytvoření clusteru HDInsight.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

* Existující [účet Azure Automation](../automation/automation-quickstart-create-account.md).
* Existující [účet Azure Storage](../storage/common/storage-account-create.md), který se použije jako úložiště clusteru.

## <a name="install-hdinsight-modules"></a>Nainstalovat moduly HDInsight

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
1. Vyberte účty Azure Automation.
1. V části **sdílené prostředky** vyberte **Galerie modulů** .
1. Do pole zadejte **AzureRM. profil** a stiskněte Enter pro hledání. Vyberte dostupný výsledek hledání.
1. Na obrazovce **AzureRM. profil** vyberte **importovat**. Zaškrtněte políčko pro aktualizaci modulů Azure a pak vyberte **OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="importovat modul AzureRM. Profile" border="false":::

1. Vraťte se do galerie modulů tak, že v části **sdílené prostředky** vyberete **Galerie moduly** .
1. Zadejte **HDInsight**. Vyberte **AzureRM. HDInsight**.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="Procházet moduly HDInsight" border="true":::

1. Na panelu **AzureRM. HDInsight** vyberte **importovat** a **OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="importovat modul AzureRM. HDInsight" border="true":::

## <a name="create-credentials"></a>Vytvořit pověření

1. V části **sdílené prostředky** vyberte **přihlašovací údaje**.
1. Vyberte **Přidat pověření**.
1. Zadejte požadované informace na novém panelu **přihlašovací údaje** . Toto pověření slouží k uložení hesla clusteru, které vám umožní přihlásit se k Ambari.

    | Vlastnost | Hodnota |
    | --- | --- |
    | Název | `cluster-password` |
    | Uživatelské jméno | `admin` |
    | Heslo | `SECURE_PASSWORD` |
    | Potvrzení hesla | `SECURE_PASSWORD` |

1. Vyberte **Vytvořit**.
1. Stejný postup opakujte pro nové přihlašovací údaje `ssh-password` s uživatelským jménem `sshuser` a heslem podle vašeho výběru. Vyberte **Vytvořit**. Tento přihlašovací údaj slouží k uložení hesla SSH pro váš cluster.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="vytvoření přihlašovacích údajů" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Vytvoření Runbooku pro vytvoření clusteru

1. V části **Automatizace procesu** vyberte **Runbooky** .
1. Vyberte **vytvořit Runbook**.
1. Na panelu **vytvořit Runbook** zadejte název sady Runbook, například `hdinsight-cluster-create` . V rozevíracím seznamu **typ Runbooku** vyberte **PowerShell** .
1. Vyberte **Vytvořit**.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="vytvořit Runbook" border="true":::

1. Na obrazovce **Upravit powershellový Runbook** zadejte následující kód a vyberte **publikovat**:

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="publikování Runbooku" border="true":::

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

## <a name="create-a-runbook-to-delete-a-cluster"></a>Vytvoření Runbooku pro odstranění clusteru

1. V části **Automatizace procesu** vyberte **Runbooky** .
1. Vyberte **vytvořit Runbook**.
1. Na panelu **vytvořit Runbook** zadejte název sady Runbook, například `hdinsight-cluster-delete` . V rozevíracím seznamu **typ Runbooku** vyberte **PowerShell** .
1. Vyberte **Vytvořit**.
1. Na obrazovce **Upravit powershellový Runbook** zadejte následující kód a vyberte **publikovat**:

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

## <a name="execute-runbooks"></a>Spouštění Runbooků

### <a name="create-a-cluster"></a>Vytvoření clusteru

1. Seznam runbooků pro váš účet Automation zobrazíte tak, že v části **Automatizace procesu** vyberete **Runbooky** .
1. Vyberte `hdinsight-cluster-create` nebo název, který jste použili při vytváření Runbooku pro vytváření clusterů.
1. Vyberte **Spustit** , aby se Runbook spustil hned. Můžete také naplánovat pravidelné spouštění Runbooků. Viz [plánování Runbooku v Azure Automation](../automation/shared-resources/schedules.md)
1. Zadejte požadované parametry skriptu a vyberte **OK**. Tím se vytvoří nový cluster HDInsight s názvem, který jste zadali v parametru **název_clusteru** .

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="spustit příkaz Create cluster Runbook" border="true":::

### <a name="delete-a-cluster"></a>Odstranění clusteru

Odstraňte cluster tak `hdinsight-cluster-delete` , že vyberete Runbook, který jste vytvořili. Vyberte **Start**, zadejte parametr **název_clusteru** a sselect **OK**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte účet Azure Automation, který jste vytvořili, aby nedocházelo k nezamýšleným poplatkům. Provedete to tak, že přejdete na Azure Portal, vyberete skupinu prostředků, ve které jste vytvořili účet Azure Automation, vyberete účet Automation a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure PowerShell](hdinsight-administer-use-powershell.md)