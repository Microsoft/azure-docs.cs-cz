---
title: Dvojité šifrování dat v klidovém umístění
titleSuffix: Azure HDInsight
description: Tento článek popisuje dvě vrstvy šifrování, které jsou k dispozici pro neaktivní data v clusterech Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 9afab87e0d7f0e7a9e5c05b36ace1dfc09c9aa9f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548026"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Dvojité šifrování Azure HDInsight pro neaktivní neaktivní data

Tento článek popisuje metody šifrování neaktivních dat v clusterech Azure HDInsight. Šifrování dat v klidovém umístění odkazuje na šifrování na spravovaných discích (datové disky, disky s operačním systémem a dočasné disky) připojené k virtuálním počítačům clusteru HDInsight. 

Tento dokument neřeší data uložená ve vašem účtu Azure Storage. Clustery můžou mít jeden nebo více připojených účtů Azure Storage, kde by mohly být šifrovací klíče taky spravované Microsoftem nebo spravované zákazníkem, ale šifrovací služba se liší. Další informace o šifrování Azure Storage najdete v části [Azure Storage šifrování pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

## <a name="introduction"></a>Úvod

V Azure existují tři hlavní role spravovaných disků: datový disk, disk s operačním systémem a dočasný disk. Další informace o různých typech spravovaných disků najdete v tématu [Úvod do služby Azure Managed disks](../virtual-machines/managed-disks-overview.md). 

HDInsight podporuje více typů šifrování ve dvou různých vrstvách:

- Šifrování na straně serveru (SSE) – služba úložiště provádí SSE. Ve službě HDInsight se SSE používá k šifrování disků s operačním systémem a datových disků. Ve výchozím nastavení je povolený. SSE je služba šifrování vrstvy 1.
- Šifrování u hostitele pomocí klíče spravovaného platformou, který se podobá SSE, je tento typ šifrování prováděn službou úložiště. Je ale jenom pro dočasné disky a ve výchozím nastavení není povolený. Šifrování na hostiteli je také šifrovací služba vrstvy 1.
- Šifrování v klidovém formátu pomocí spravovaného klíče zákazníka – tento typ šifrování se dá použít na datech a na dočasných discích. Ve výchozím nastavení není povolený a vyžaduje, aby zákazník poskytoval svůj vlastní klíč prostřednictvím trezoru klíčů Azure. Šifrování v klidovém umístění je šifrovací služba vrstvy 2.

Tyto typy jsou shrnuty v následující tabulce.

|Typ clusteru |Disk s operačním systémem (spravovaný disk) |Datový disk (spravovaný disk) |Dočasný datový disk (místní SSD) |
|---|---|---|---|
|Kafka, HBA s akcelerovanými zápisy|LAYER1: [šifrování SSE](../virtual-machines/managed-disks-overview.md#encryption) ve výchozím nastavení|LAYER1: [šifrování SSE](../virtual-machines/managed-disks-overview.md#encryption) ve výchozím nastavení layer2: volitelné šifrování v klidovém stavu s VYUŽITÍm CMK|LAYER1: volitelné šifrování u hostitele pomocí klíče PMK, layer2: volitelné šifrování v klidovém formátu pomocí CMK|
|Všechny ostatní clustery (Spark, Interactive, Hadoop, HBA bez urychleného zápisu)|LAYER1: [šifrování SSE](../virtual-machines/managed-disks-overview.md#encryption) ve výchozím nastavení|–|LAYER1: volitelné šifrování u hostitele pomocí klíče PMK, layer2: volitelné šifrování v klidovém formátu pomocí CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem

Šifrování klíče spravovaného zákazníkem je proces, který se během vytváření clusteru zpracovává bez dalších poplatků. K tomu, abyste mohli autorizovat spravovanou identitu pomocí Azure Key Vault, musíte přidat šifrovací klíč a při vytváření clusteru ho přidat.

Datové disky a dočasné disky v každém uzlu clusteru jsou šifrovány pomocí symetrického šifrovacího klíče (klíč DEK). KLÍČ DEK je chráněný pomocí klíčového šifrovacího klíče (KEK) z vašeho trezoru klíčů. Procesy šifrování a dešifrování jsou zpracovávány výhradně službou Azure HDInsight.

Pro disky s operačním systémem připojené k virtuálním počítačům clusteru je k dispozici pouze jedna vrstva šifrování (PMK). Doporučujeme, aby zákazníci nemuseli kopírovat citlivá data na disky s operačním systémem, pokud se pro jejich scénáře vyžaduje šifrování CMK.

Pokud je povolená brána firewall trezoru klíčů v trezoru klíčů, kde je uložený šifrovací klíč disku, musí se do konfigurace brány firewall trezoru klíčů přidat IP adresy poskytovatele prostředků služby HDInsight pro oblast, kde se cluster bude nasazovat. To je nezbytné, protože HDInsight není důvěryhodná služba trezoru klíčů Azure.

K bezpečnému střídání klíčů v trezoru klíčů můžete použít Azure Portal nebo Azure CLI. Když se klíč otáčí, cluster HDInsight začne používat nový klíč během několika minut. Povolte funkce ochrany před únikem [klíčů, které se budou chránit](../key-vault/general/soft-delete-overview.md) před ransomwaremmi scénáři a náhodným odstraněním. Trezory klíčů bez této funkce ochrany se nepodporují.

### <a name="get-started-with-customer-managed-keys"></a>Začínáme s klíčem spravovaným zákazníkem

Pokud chcete vytvořit cluster HDInsight s povoleným klíčem, Projděte si následující kroky:

1. Vytvoření spravovaných identit pro prostředky Azure
1. Vytvořit Azure Key Vault
1. Vytvořit klíč
1. Vytvořit zásady přístupu
1. Vytvoření clusteru HDInsight s povoleným klíčem spravovaným zákazníkem
1. Otočení šifrovacího klíče

Jednotlivé kroky jsou podrobně vysvětleny v jedné z následujících částí.

### <a name="create-managed-identities-for-azure-resources"></a>Vytvoření spravovaných identit pro prostředky Azure

Vytvoření spravované identity přiřazené uživatelem pro ověření Key Vault.

Konkrétní kroky najdete v tématu [Vytvoření spravované identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) . Další informace o tom, jak spravované identity fungují ve službě Azure HDInsight, najdete v tématu [spravované identity ve službě Azure HDInsight](hdinsight-managed-identities.md). Nezapomeňte uložit ID spravovaného prostředku identity, pokud ho přidáte do zásad přístupu Key Vault.

### <a name="create-azure-key-vault"></a>Vytvořit Azure Key Vault

Vytvořte trezor klíčů. Konkrétní postup najdete v tématu věnovaném [vytvoření Azure Key Vault](../key-vault/secrets/quick-create-portal.md) .

HDInsight podporuje jenom Azure Key Vault. Pokud máte vlastní Trezor klíčů, můžete klíče importovat do Azure Key Vault. Nezapomeňte, že Trezor klíčů musí mít povolené **obnovitelné odstranění** . Další informace o importu existujících klíčů najdete v [informacích o klíčích, tajných klíčích a certifikátech](../key-vault/general/about-keys-secrets-certificates.md).

### <a name="create-key"></a>Vytvořit klíč

1. V novém trezoru klíčů přejděte na **Nastavení**  >  **klíče**  >  **+ Generovat/importovat** .

    ![Vygenerovat nový klíč v Azure Key Vault](./media/disk-encryption/create-new-key.png "Vygenerovat nový klíč v Azure Key Vault")

1. Zadejte název a pak vyberte **vytvořit** . Udržujte výchozí **typ klíče** **RSA** .

    ![vygeneruje název klíče.](./media/disk-encryption/create-key.png "Vygenerovat název klíče")

1. Až se vrátíte na stránku **klíče** , vyberte klíč, který jste vytvořili.

    ![seznam klíčů trezoru klíčů](./media/disk-encryption/key-vault-key-list.png)

1. Výběrem verze otevřete stránku **verze klíče** . Když použijete vlastní klíč pro šifrování clusteru HDInsight, musíte zadat identifikátor URI klíče. Zkopírujte **identifikátor klíče** a uložte ho někam do chvíle, kdy jste připraveni vytvořit cluster.

    ![získat identifikátor klíče](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>Vytvořit zásady přístupu

1. V novém trezoru klíčů přejděte na **Nastavení**  >  **zásady přístupu**  >  **+ Přidat zásady přístupu** .

    ![Vytvořit nové zásady přístupu Azure Key Vault](./media/disk-encryption/key-vault-access-policy.png)

1. Na stránce **Přidat zásady přístupu** zadejte následující informace:

    |Vlastnost |Popis|
    |---|---|
    |Klíčová oprávnění|Vyberte **získat** , **Rozbalit klíč** a **zalomit klíč** .|
    |Tajná oprávnění|Vyberte **získat** , **nastavit** a **Odstranit** .|
    |Vybrat objekt zabezpečení|Vyberte uživatelem přiřazenou spravovanou identitu, kterou jste vytvořili dříve.|

    ![Nastavení výběru objektu zabezpečení pro zásady Azure Key Vaultho přístupu](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Vyberte **Přidat** .

1. Vyberte **Uložit** .

    ![Uložit zásady přístupu Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Vytvoření clusteru s použitím šifrování klíčového disku spravovaného zákazníkem

Nyní jste připraveni vytvořit nový cluster HDInsight. Klíče spravované zákazníkem se dají použít jenom pro nové clustery během vytváření clusteru. Šifrování nejde odebrat z klíčových clusterů spravovaných zákazníkem a klíče spravované zákazníkem se nedají přidat do existujících clusterů.

#### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Během vytváření clusteru zadejte úplný **identifikátor klíče** , včetně verze klíče. Například, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Musíte také přiřadit spravovanou identitu ke clusteru a zadat identifikátor URI klíče.

![Vytvořit nový cluster](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>Použití Azure CLI

Následující příklad ukazuje, jak pomocí rozhraní příkazového řádku Azure vytvořit nový cluster Apache Spark s povoleným šifrováním disku. Další informace najdete v tématu [Azure CLI AZ HDInsight Create](/cli/azure/hdinsight#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru

Následující příklad ukazuje, jak použít šablonu Azure Resource Manager k vytvoření nového clusteru Apache Spark s povoleným šifrováním disku. Další informace najdete v tématu [co jsou šablony ARM](../azure-resource-manager/templates/overview.md).

V tomto příkladu se k volání šablony používá PowerShell.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Obsah šablony správy prostředků `azuredeploy.json` :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>Otočení šifrovacího klíče

Můžou nastat situace, kdy budete možná chtít změnit šifrovací klíče používané clusterem HDInsight po jeho vytvoření. To může být snadné prostřednictvím portálu. Pro tuto operaci musí mít cluster přístup k aktuálnímu klíči i k zamýšlenému novému klíči. v opačném případě se operace otočení klíče nezdaří.

#### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

K otočení klíče potřebujete identifikátor URI trezoru základního klíče. Až to uděláte, přejděte do části vlastnosti clusteru HDInsight na portálu a klikněte na **změnit klíč** pod **adresou URL klíče pro šifrování disku** . Zadejte novou adresu URL klíče a odešlete pro otočení klíče.

![otočit šifrovací klíč disku](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>Použití Azure CLI

Následující příklad ukazuje, jak otočit šifrovací klíč disku pro existující cluster HDInsight. Další informace najdete v tématu [Azure CLI AZ HDInsight otočit-Disk-Encryption-Key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Nejčastější dotazy týkající se šifrování klíčů spravovaných zákazníkem

**Jak cluster HDInsight přistupuje k trezoru klíčů?**

HDInsight přistupuje k vaší instanci Azure Key Vault pomocí spravované identity, kterou přidružíte ke clusteru HDInsight. Tato spravovaná identita se dá vytvořit před nebo během vytváření clusteru. Musíte taky udělit přístup spravované identity k trezoru klíčů, kde je klíč uložený.

**Je tato funkce dostupná pro všechny clustery v prostředí HDInsight?**

Šifrování klíče spravovaného zákazníkem je k dispozici pro všechny typy clusterů kromě Spark 2,1 a 2,2.

**Můžu k šifrování různých disků nebo složek použít více klíčů?**

Ne, všechny spravované disky a disky prostředků jsou šifrovány stejným klíčem.

**Co se stane, když cluster ztratí přístup k trezoru klíčů nebo klíči?**

Pokud cluster ztratí přístup k tomuto klíči, zobrazí se na portálu Apache Ambari upozornění. V tomto stavu se operace **změny klíče** nezdaří. Po obnovení přístupu ke klíčům zmizí Ambari upozornění a operace, jako je například střídání klíčů, je možné úspěšně provést.

![Výstraha Ambari přístupu k klíčům](./media/disk-encryption/ambari-alert.png)

**Jak mohu obnovit cluster, pokud jsou klíče odstraněny?**

Vzhledem k tomu, že se podporují jenom klíče s povoleným obnovitelném odstraněním, měl by cluster znovu získat přístup k klíčům, pokud se klíče obnoví v trezoru klíčů. Pokud chcete obnovit Azure Key Vault klíč, přečtěte si téma [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) nebo [AZ-klíčů trezor-Key-Recovery](/cli/azure/keyvault/key#az-keyvault-key-recover).


**Pokud je cluster škálovatelný, budou nové uzly bezproblémově podporovat klíče spravované zákazníky?**

Yes. Cluster potřebuje během horizontálního navýšení kapacity přístup k klíči v trezoru klíčů. Stejný klíč se používá k šifrování spravovaných disků i disků prostředků v clusteru.

**Jsou v mém umístění dostupné klíče spravované zákazníky?**

Klíče spravované zákazníkem HDInsight jsou k dispozici ve všech veřejných cloudech a národních cloudech.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Šifrování na hostiteli pomocí klíčů spravovaných platformou

### <a name="enable-in-the-azure-portal"></a>Povolit v Azure Portal

Šifrování na hostiteli lze povolit během vytváření clusteru v Azure Portal.

> [!Note]
> Když je povolené šifrování na hostiteli, nemůžete do svého clusteru HDInsight přidávat aplikace z Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Povolte šifrování na hostiteli.":::

Tato možnost povoluje [šifrování na hostiteli](../virtual-machines/disks-enable-host-based-encryption-portal.md) pro virtuální počítače HDInsight s použitím klíče PMK. Šifrování na hostiteli se [podporuje jenom u některých SKU virtuálních počítačů v omezených oblastech](../virtual-machines/disks-enable-host-based-encryption-portal.md) a HDInsight podporuje [následující konfiguraci uzlů a SKU](./hdinsight-supported-node-configuration.md).

Pro pochopení správné velikosti virtuálního počítače pro cluster HDInsight si přečtěte téma [Výběr správné velikosti virtuálního počítače pro cluster Azure HDInsight](hdinsight-selecting-vm-size.md). Výchozí SKU virtuálního počítače pro uzel Zookeeper, pokud je povolené šifrování na hostiteli, bude DS2V2.

### <a name="enable-using-powershell"></a>Povolení s využitím PowerShellu

Následující fragment kódu ukazuje, jak můžete vytvořit nový cluster Azure HDInsight s povoleným šifrováním na hostiteli pomocí PowerShellu. `-EncryptionAtHost $true`K povolení této funkce používá parametr.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Povolení pomocí Azure CLI

Následující fragment kódu ukazuje, jak můžete vytvořit nový cluster Azure HDInsight s povoleným šifrováním na hostiteli pomocí Azure CLI. `--encryption-at-host true`K povolení této funkce používá parametr.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Další kroky

* Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault](../key-vault/general/overview.md).
* [Přehled podnikového zabezpečení ve službě Azure HDInsight](./domain-joined/hdinsight-security-overview.md).