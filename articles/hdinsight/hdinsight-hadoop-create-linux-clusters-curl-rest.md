---
title: Vytvoření clusterů Hadoop pomocí rozhraní Azure REST API – Azure
description: Zjistěte, jak vytvářet clustery HDInsight, odešlete šablon Azure Resource Manageru k rozhraní Azure REST API.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jasonh
ms.openlocfilehash: 79ab8ad9d827b3d2dda8138260b9857f241afc45
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094063"
---
# <a name="create-hadoop-clusters-using-the-azure-rest-api"></a>Vytvoření clusterů Hadoop pomocí rozhraní Azure REST API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Zjistěte, jak vytvořit cluster HDInsight pomocí šablony Azure Resource Manageru a rozhraní Azure REST API.

Rozhraní Azure REST API umožňuje provádět operace správy služeb hostovaných na platformě Azure, včetně vytvoření nové prostředky, například clustery HDInsight.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!NOTE]
> Kroky v tomto dokumentu pomocí [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) nástroj ke komunikaci s rozhraním REST API Azure.

## <a name="create-a-template"></a>Vytvoření šablony

Šablony Azure Resource Manageru jsou dokumenty JSON, které popisují **skupiny prostředků** a všechny prostředky v ní (třeba HDInsight). Tento přístup založený na šabloně můžete zadat prostředky, které potřebujete pro HDInsight v jedné šabloně.

Následující dokument JSON je spojení šablonu a parametry souborů z [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), který vytvoří cluster založených na Linuxu používat heslo k zabezpečení uživatelského účtu SSH.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
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
                   "sshUserName": {
                       "type": "string",
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
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

V tomto příkladu se používá v krocích v tomto dokumentu. V příkladu nahraďte *hodnoty* v **parametry** část s hodnotami pro váš cluster.

> [!IMPORTANT]
> Šablona používá výchozí počet pracovních uzlů (4) pro HDInsight cluster. Pokud máte v plánu na více než 32 uzlů pracovního procesu, musíte vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti ram.
>
> Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

Postupujte podle kroků popsaných v [Začínáme s Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) a připojte se k předplatnému pomocí `az login` příkazu.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

> [!NOTE]
> Tyto kroky jsou zkrácenou verzi *vytvořit instanční objekt s heslem* část [pomocí Azure CLI k vytvoření instančního objektu pro přístup k prostředkům](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentu. Postup vytvoření instančního objektu, který se používá k ověření rozhraní Azure REST API.

1. Z příkazového řádku použijte následující příkaz seznam vašich předplatných Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    V seznamu vyberte předplatné, které chcete použít a Všimněte si, **Subscription_ID** a __Tenant_ID__ sloupce. Uložte tyto hodnoty.

2. Použijte následující příkaz k vytvoření aplikace v Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Nahraďte hodnoty `--display-name`, `--homepage`, a `--identifier-uris` vlastními hodnotami. Zadejte heslo pro novou položku služby Active Directory.

   > [!NOTE]
   > `--home-page` a `--identifier-uris` hodnoty nemusíte odkazují na vlastní webové stránky hostované na Internetu. Musí být jedinečné identifikátory URI.

   Hodnota vrácená z tohoto příkazu je __ID aplikace__ pro novou aplikaci. Uloží tuto hodnotu.

3. Použijte následující příkaz k vytvoření instančního objektu pomocí **ID aplikace**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Hodnota vrácená z tohoto příkazu je __ID objektu__. Uloží tuto hodnotu.

4. Přiřazení **vlastníka** rolí instančního objektu služby pomocí **ID objektu** hodnotu. Použití **ID předplatného** jste získali dříve.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Získání tokenu ověřování

Chcete-li získat ověřovací token, použijte následující příkaz:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Nastavte `$TENANTID`, `$APPID`, a `$PASSWORD` hodnoty získané nebo použili.

Pokud tento požadavek je úspěšné, dostanete odpověď 200 řady a text odpovědi obsahuje dokument JSON.

Dokument JSON vrácený tuto žádost obsahuje element s názvem **access_token**. Hodnota **access_token** slouží k žádosti o ověření rozhraní REST API.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Použijte následující postup a vytvořte skupinu prostředků.

* Nastavte `$SUBSCRIPTIONID` předplatné ID dostali při vytváření instančního objektu.
* Nastavte `$ACCESSTOKEN` na přístupový token získaný v předchozím kroku.
* Nahraďte `DATACENTERLOCATION` s Chcete vytvořit skupinu prostředků a prostředky, v datovém centru. Třeba "střed USA – jih".
* Nastavte `$RESOURCEGROUPNAME` k názvu, kterou chcete použít pro tuto skupinu:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Pokud tento požadavek je úspěšné, dostanete odpověď 200 řady a tělo odpovědi obsahuje dokument JSON obsahující informace o skupině. `"provisioningState"` Prvek obsahuje hodnotu `"Succeeded"`.

## <a name="create-a-deployment"></a>Vytvoření nasazení

Použijte následující příkaz k nasazení šablony do skupiny prostředků.

* Nastavte `$DEPLOYMENTNAME` k názvu, kterou chcete použít pro toto nasazení.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> Pokud šablona se uloží do souboru, můžete použít následující příkaz místo `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Pokud tento požadavek je úspěšné, dostanete odpověď 200 řady a text odpovědi obsahuje dokument JSON obsahující informace o operaci nasazení.

> [!IMPORTANT]
> Nasazení se odeslal, ale nebyl dokončen. Může trvat několik minut, obvykle přibližně 15, pro se nasazení dokončí.

## <a name="check-the-status-of-a-deployment"></a>Zkontrolujte stav nasazení

Pokud chcete zkontrolovat stav nasazení, použijte následující příkaz:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Tento příkaz vrátí dokument JSON obsahující informace o operaci nasazení. `"provisioningState"` Prvek obsahuje stav nasazení. Pokud tento prvek obsahuje hodnotu `"Succeeded"`, pak nasazení byla úspěšně dokončena.

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup

Teď, když úspěšně vytvoříte HDInsight cluster, použijte následující postup, jak pracovat s vaším clusterem.

### <a name="hadoop-clusters"></a>Clustery Hadoop

* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clustery HBase

* [Začínáme s HBase ve službě HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací v Javě pro HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clustery Storm

* [Vývoj topologie Java pro Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent v Pythonu v Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií se Stormem v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
