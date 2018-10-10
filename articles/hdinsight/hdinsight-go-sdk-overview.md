---
title: Azure HDInsight Go SDK
description: Referenční informace pro Azure HDInsight Go SDK
services: hdinsight
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 9/21/2018
ms.author: tyfox
ms.openlocfilehash: 8beb75748c2e9fe3f71ad321c4cd523e344fb90c
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901902"
---
# <a name="hdinsight-go-management-sdk-preview"></a>HDInsight Go Management SDK ve verzi Preview

## <a name="overview"></a>Přehled
HDInsight Go SDK poskytuje třídy a funkce, které vám pomohou se správou clusterů HDInsight. Zahrnuje operace se mají vytvořit, odstranit, aktualizovat, seznam, změna velikosti, spustit skript akce, monitorování, získá vlastnosti clusterů HDInsight a dalších.

> [!NOTE]
>Referenční materiál GoDoc pro tuto sadu SDK je také [tady k dispozici](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Požadavky

* Účet Azure. Pokud ho nemáte, [získat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* [Go](https://golang.org/dl/)

## <a name="sdk-installation"></a>Instalace sady SDK

Z umístění GOPATH, spusťte `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Sady SDK nejprve musí být ověřený ve vašem předplatném Azure.  Postupujte podle příkladu níže k vytvoření instančního objektu a použít ho k ověření. Po dokončení budete mít instanci `ClustersClient`, který obsahuje mnoho funkcí (uvedených v následující části), které lze použít k provádění operací správy.

> [!NOTE]
> Existují jiné způsoby, jak ověřit kromě následujícím příkladu, který může potenciálně být lépe vyhovuje vašim potřebám. Všechny funkce jsou uvedeny zde: [funkce ověřování v sadě Azure SDK for Go](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Příklad ověřování pomocí instančního objektu

První, přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash). Ověřte, že používáte předplatné, ve kterém chcete, aby se vytvořil objekt služby. 

```azurecli-interactive
az account show
```

Informace o vašem předplatném se zobrazí jako dokumenty JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Pokud nejste přihlášení správné předplatné, vyberte tu správnou spuštěním: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]
> Pokud jste ještě nezaregistrovali poskytovatele prostředků HDInsight pomocí jiné funkci (například tak, že vytvoříte HDInsight Cluster prostřednictvím webu Azure Portal), je potřeba to udělat po předtím, než může ověřit. To můžete udělat z [Azure Cloud Shell](https://shell.azure.com/bash) spuštěním následujícího příkazu:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

V dalším kroku vyberte název instančního objektu služby a vytvořte jej pomocí následujícího příkazu:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Informací o instančním objektu služby se zobrazí jako dokumenty JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Zkopírujte následující fragment kódu a vyplňte `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET`, a `SUBSCRIPTION_ID` s řetězci z formátu JSON, který byl vrácen po spuštění příkazu k vytvoření instančního objektu služby.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"    
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Správa clusteru

> [!NOTE]
> V této části se předpokládá již ověřen a konstruovány `ClusterClient` instance a uložte ho proměnnou s názvem `client`. Pokyny k ověřování a získání `ClusterClient` najdete v části ověřování výše.

### <a name="create-a-cluster"></a>Vytvoření clusteru

Nový cluster lze vytvořit voláním `client.Create()`. 

#### <a name="example"></a>Příklad:

Tento příklad ukazuje, jak vytvořit Spark cluster s 2 hlavní uzly a 1 pracovního uzlu.

> [!NOTE]
> Nejprve musíte vytvořit skupinu prostředků a účtu úložiště, jak je popsáno níže. Pokud jste již vytvořili tyto, můžete přeskočit tyto kroky.

##### <a name="creating-a-resource-group"></a>Vytváří se skupina prostředků

Můžete vytvořit skupinu prostředků pomocí [Azure Cloud Shell](https://shell.azure.com/bash) spuštěním
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Vytváření účtu úložiště

Můžete vytvořit účet úložiště pomocí [Azure Cloud Shell](https://shell.azure.com/bash) spuštěním:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Nyní spusťte následující příkaz k získání klíče účtu úložiště (ho budete potřebovat k vytvoření clusteru):
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
Níže Go fragment kódu vytvoří Spark cluster s 2 hlavní uzly a 1 pracovního uzlu. Vyplnit prázdné proměnné, jak je vysvětleno v komentářích a můžete změnit další parametry tak, aby vyhovovala vašim konkrétním potřebám.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Získat podrobnosti o clusteru

Pokud chcete získat vlastnosti pro daný cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Příklad:

Můžete použít `get` potvrďte, že úspěšně jste vytvořili svůj cluster.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Výstup by měl vypadat:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Výpis clusterů

#### <a name="list-clusters-under-the-subscription"></a>Výpis clusterů v rámci předplatného
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>Výpis clusterů ve skupině prostředků
```golang
client.ListByResourceGroup("<Resource Group Name>")
```
> [!NOTE]
> Obě `List()` a `ListByResourceGroup()` vrátit `ClusterListResultPage` struktury. Pokud chcete získat další stránku, můžete volat `Next()`. To můžete opakovat až do `ClusterListResultPage.NotDone()` vrátí `false`, jak je znázorněno v následujícím příkladu.

#### <a name="example"></a>Příklad:
Následující příklad vytiskne vlastnosti všech clusterech pro aktuální předplatné:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Odstranění clusteru

Odstranění clusteru:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Aktualizace clusteru značky

Můžete aktualizovat značky daného clusteru takto:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Příklad:

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Změnit velikost clusteru

Velikost clusteru daný počet uzlů pracovního procesu můžete změnit tak, že zadáte novou velikost takto:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorování clusteru

HDInsight Management SDK lze použít také ke správě, monitorování v clusterech služby prostřednictvím Operations Management Suite (OMS).

Podobně jako na způsobu vytvoření `ClusterClient` Pokud chcete používat pro operace správy, je potřeba vytvořit `ExtensionClient` pro monitorování provozu. Po dokončení ověřování části výše, můžete vytvořit `ExtensionClient` takto:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Níže monitorování příklady předpokládají, budete mít již byl inicializován `ExtensionClient` volá `extClient` a nastavte jeho `Authorizer` jak je uvedeno výše.

### <a name="enable-oms-monitoring"></a>Povolit monitorování OMS

> [!NOTE]
> Pokud chcete povolit monitorování OMS, musíte mít existující pracovní prostor Log Analytics. Pokud jste jednu ještě nevytvořili, se dozvíte, jak to udělat tady: [vytvořit pracovní prostor Log Analytics na portálu Azure portal](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Povolení monitorování OMS ve vašem clusteru:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Zobrazit stav monitorování OMS

Pokud chcete získat stav OMS ve vašem clusteru:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Zakázat monitorování OMS

Chcete-li zakázat OMS ve vašem clusteru:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Akce skriptů

HDInsight poskytuje konfigurace funkci akce skriptu, který vyvolá vlastní skripty pro přizpůsobení clusteru.
> [!NOTE]
> Další informace o tom, jak pomocí akcí skriptů najdete tady: [HDInsight založených na Linuxu přizpůsobit clustery pomocí skriptových akcí](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Spustit akce skriptu

Akce se skripty můžete spustit v daném clusteru takto:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Pro operace odstranění akce skriptu a "Seznam trvalá akce skriptu", je potřeba vytvořit `ScriptActionsClient`, podobně jako na způsobu vytvoření `ClusterClient` pro operace správy. Po dokončení ověřování části výše, můžete vytvořit `ScriptActionsClient` takto:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Pod akcí skriptů příklady předpokládají, je již inicializován `ScriptActionsClient` volá `scriptActionsClient` a nastavte jeho `Authorizer` jak je znázorněno výše.

### <a name="delete-script-action"></a>Odstranit akci se skripty

Pokud chcete odstranit zadané trvalá akce se skripty akce v daném clusteru:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Seznam trvalé akce se skripty

> [!NOTE]
> Obě `ListByCluster()` vrátí `ScriptActionsListPage` struktury. Pokud chcete získat další stránku, můžete volat `Next()`. To můžete opakovat až do `ClusterListResultPage.NotDone()` vrátí `false`, jak je znázorněno v následujícím příkladu.

Chcete-li vypsat všechny trvalé akce se skripty pro zadaný cluster:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Příklad:

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }    
}
```

### <a name="list-all-scripts-execution-history"></a>Seznam historie provádění všechny skripty

Pro tuto operaci je potřeba vytvořit `ScriptExecutionHistoryClient`, podobně jako na způsobu vytvoření `ClusterClient` pro operace správy. Po dokončení ověřování části výše, můžete vytvořit `ScriptActionsClient` takto:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Níže předpokládá již inicializován `ScriptExecutionHistoryClient` volá `scriptExecutionHistoryClient` a nastavte jeho `Authorizer` jak je znázorněno výše.

Do seznamu historie spuštění všech skriptů pro zadaný cluster:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Příklad:

Tento příklad vytiskne všechny podrobnosti pro všechny po spuštění skriptu.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }       
}
```

## <a name="next-steps"></a>Další postup

* Prozkoumejte [GoDoc referenční materiál](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). GoDocs zadejte referenční dokumentaci pro všechny funkce v sadě SDK.
