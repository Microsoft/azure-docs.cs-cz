---
title: Vytváření clusterů Apache Hadoop pomocí rozhraní Azure REST API – Azure
description: Zjistěte, jak vytvořit clustery HDInsight odesláním šablon Azure Resource Manager do rozhraní AZURE REST API.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 2680304bd73bdbae35b29b89f38ae2665615f5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239925"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Vytvoření clusterů Apache Hadoop pomocí rozhraní Azure REST API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Zjistěte, jak vytvořit cluster HDInsight pomocí šablony Azure Resource Manager a rozhraní API Azure REST.

Rozhraní Azure REST API umožňuje provádět operace správy na službách hostovaných v platformě Azure, včetně vytváření nových prostředků, jako jsou clustery HDInsight.

> [!NOTE]  
> Kroky v tomto dokumentu používají [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) nástroj pro komunikaci s rozhraním API Azure REST.

## <a name="create-a-template"></a>Vytvoření šablony

Šablony Azure Resource Manageru jsou dokumenty JSON, které popisují **skupinu prostředků** a všechny prostředky v ní (například HDInsight).) Tento přístup založený na šabloně umožňuje definovat prostředky, které potřebujete pro HDInsight v jedné šabloně.

Následující dokument JSON je sloučení souborů šablony a [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)parametrů z , který vytvoří cluster založený na Linuxu pomocí hesla k zabezpečení uživatelského účtu SSH.

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
                                   "vmSize": "{}" 
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
                                   "vmSize": "{}"
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

Tento příklad se používá v krocích v tomto dokumentu. Nahraďte ukázkové *hodnoty* v části **Parametry** hodnotami pro váš cluster.

> [!IMPORTANT]  
> Šablona používá výchozí počet pracovních uzlů (4) pro cluster HDInsight. Pokud plánujete na více než 32 pracovních uzlů, pak je nutné vybrat velikost hlavního uzlu s alespoň 8 jader a 14 GB RAM.
>
> Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

Postupujte podle kroků zdokumentovaných v [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) a připojte se k předplatnému pomocí příkazu. `az login`

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

> [!NOTE]  
> Tyto kroky jsou zkrácená verze *vytvořit instanční objekt s heslem* části [použití nastavení příkazu k řešení Azure k vytvoření instančního objektu pro přístup k prostředkům](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentu. Tyto kroky vytvořit instanční objekt, který se používá k ověření rozhraní API Azure REST.

1. Z příkazového řádku můžete pomocí následujícího příkazu vypsat předplatná Azure.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    V seznamu vyberte předplatné, které chcete použít, a poznamenejte si **sloupce Subscription_ID** a __Tenant_ID.__ Uložte tyto hodnoty.

2. Pomocí následujícího příkazu vytvořte aplikaci ve službě Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Nahraďte hodnoty `--display-name` `--homepage`pro `--identifier-uris` , a vlastní mise. Zadejte heslo pro novou položku služby Active Directory.

   > [!NOTE]  
   > Hodnoty `--home-page` `--identifier-uris` a nemusí odkazovat na aktuální webovou stránku hostovací na internetu. Musí se jednat o jedinečné identifikátory URI.

   Hodnota vrácená z tohoto příkazu je __ID aplikace__ pro novou aplikaci. Uložte tuto hodnotu.

3. Pomocí následujícího příkazu vytvořte instanční objekt pomocí **ID aplikace**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Hodnota vrácená z tohoto příkazu je __ID objektu__. Uložte tuto hodnotu.

4. Přiřaďte roli **Vlastník** instančnímu objektu pomocí hodnoty **ID objektu.** Použijte **ID předplatného,** které jste získali dříve.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Získání ověřovacího tokenu

K načtení ověřovacího tokenu použijte následující příkaz:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Nastavte `$TENANTID` `$APPID`, `$PASSWORD` a na hodnoty získané nebo použité dříve.

Pokud je tento požadavek úspěšný, obdržíte odpověď řady 200 a tělo odpovědi obsahuje dokument JSON.

Dokument JSON vrácený tímto požadavkem obsahuje prvek s názvem **access_token**. Hodnota **access_token** se používá pro požadavky na ověření rozhraní REST API.

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

K vytvoření skupiny prostředků použijte následující postup.

* Nastavte `$SUBSCRIPTIONID` na ID předplatného přijaté při vytváření instančního objektu.
* Nastavte `$ACCESSTOKEN` přístupový token přijatý v předchozím kroku.
* Nahraďte `DATACENTERLOCATION` datovým centrem, ve které chcete vytvořit skupinu prostředků a prostředky. Například "Jižní střed USA".
* Nastavte `$RESOURCEGROUPNAME` název, který chcete použít pro tuto skupinu:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Pokud je tento požadavek úspěšný, obdržíte odpověď řady 200 a tělo odpovědi obsahuje dokument JSON obsahující informace o skupině. Prvek `"provisioningState"` obsahuje hodnotu `"Succeeded"`.

## <a name="create-a-deployment"></a>Vytvoření nasazení

Pomocí následujícího příkazu nasadit šablonu do skupiny prostředků.

* Nastavte `$DEPLOYMENTNAME` název, který chcete použít pro toto nasazení.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Pokud jste šablonu uložili do souboru, můžete `-d "{ template and parameters}"`místo následujícího příkazu použít následující příkaz:
>
> `--data-binary "@/path/to/file.json"`

Pokud je tento požadavek úspěšný, obdržíte odpověď řady 200 a tělo odpovědi obsahuje dokument JSON obsahující informace o operaci nasazení.

> [!IMPORTANT]  
> Nasazení bylo odesláno, ale nebylo dokončeno. Dokončení nasazení může trvat několik minut, obvykle kolem 15.

## <a name="check-the-status-of-a-deployment"></a>Kontrola stavu nasazení

Chcete-li zkontrolovat stav nasazení, použijte následující příkaz:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Tento příkaz vrátí dokument JSON obsahující informace o operaci nasazení. Prvek `"provisioningState"` obsahuje stav nasazení. Pokud tento prvek obsahuje `"Succeeded"`hodnotu , bylo nasazení úspěšně dokončeno.

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně vytvořili cluster HDInsight, použijte následující informace o tom, jak s clusterem pracovat.

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití mapreduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBase

* [Začínáme s Apache HBase na HDInsightu](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj java aplikací pro Apache HBase na HDInsightu](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clustery Apache Storm

* [Vývoj java topologie pro Apache Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent Pythonu v Apache Storm na HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a sledování topologie pomocí Apache Storm na HDInsightu](storm/apache-storm-deploy-monitor-topology-linux.md)
