---
title: Azure HDInsight SDK na cestách
description: Referenční materiál pro použití clusterů Azure HDInsight SDK for Go a Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 292496c4d458621213fe62105149ac845d78891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479582"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK pro na cestách (náhled)

## <a name="overview"></a>Přehled
Sada HDInsight SDK for Go poskytuje třídy a funkce, které umožňují spravovat clustery HDInsight. Zahrnuje operace pro vytváření, odstraňování, aktualizaci, seznam, změna velikosti, provádění akcí skriptů, monitorování, získávání vlastností clusterů HDInsight a další.

> [!NOTE]  
>GoDoc referenční materiál pro tuto SDK je také [k dispozici zde](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* [ `go get` Nástroj](https://github.com/golang/go/wiki/GoGetTools).
* [Jdi](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalace sady SDK

Z vašeho umístění GOPATH spusťte`go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Ověřování

Sada SDK musí být nejprve ověřena pomocí předplatného Azure.  Postupujte podle následujícího příkladu a vytvořte instanční objekt a použijte jej k ověření. Poté, co to toto provedete, `ClustersClient`budete mít instanci , která obsahuje mnoho funkcí (uvedených v níže uvedených částech), které lze použít k provádění operací správy.

> [!NOTE]  
> Existují i jiné způsoby, jak ověřit kromě níže uvedeného příkladu, které by mohly být vhodnější pro vaše potřeby. Všechny funkce jsou nastíněny zde: [Ověřovací funkce v Azure SDK for Go](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Příklad ověřování pomocí instančního objektu

Nejprve se přihlaste do [služby Azure Cloud Shell](https://shell.azure.com/bash). Ověřte, zda aktuálně používáte předplatné, ve kterém chcete vytvořit instanční objekt.

```azurecli-interactive
az account show
```

Informace o vašem předplatném se zobrazí jako JSON.

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

Pokud nejste přihlášeni ke správnému předplatnému, vyberte správné spuštěním: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Pokud jste ještě nezaregistrovali poskytovatele prostředků HDInsight jinou funkcí (například vytvořením clusteru HDInsight prostřednictvím portálu Azure), musíte to udělat jednou, než se budete moct ověřit. To lze provést z [prostředí Azure Cloud Shell](https://shell.azure.com/bash) spuštěním následujícího příkazu:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Dále zvolte název instančního objektu a vytvořte jej pomocí následujícího příkazu:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Informace o instančním objektu se zobrazí jako JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Zkopírujte níže uvedený výstřižek `CLIENT_SECRET`a `SUBSCRIPTION_ID` vyplňte `TENANT_ID`, `CLIENT_ID`, a řetězce z JSON, který byl vrácen po spuštění příkazu k vytvoření instančního objektu.

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
> Tato část předpokládá, že jste již `ClusterClient` ověřili a vytvořili `client`instanci a uložíte ji do proměnné s názvem . Pokyny pro ověření a `ClusterClient` získání a naleznete v části Ověřování výše.

### <a name="create-a-cluster"></a>Vytvoření clusteru

Nový cluster lze vytvořit `client.Create()`voláním . 

#### <a name="example"></a>Příklad

Tento příklad ukazuje, jak vytvořit cluster [Apache Spark](https://spark.apache.org/) se dvěma hlavní uzly a jeden pracovní uzel.

> [!NOTE]  
> Nejprve je třeba vytvořit skupinu prostředků a účet úložiště, jak je vysvětleno níže. Pokud jste je již vytvořili, můžete tyto kroky přeskočit.

##### <a name="creating-a-resource-group"></a>Vytvoření skupiny prostředků

Skupinu prostředků pomocí [prostředí Azure Cloud Shell](https://shell.azure.com/bash) můžete vytvořit spuštěním

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Vytváření účtu úložiště

Účet úložiště můžete vytvořit pomocí [Azure Cloud Shell](https://shell.azure.com/bash) spuštěním:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Teď spusťte následující příkaz, abyste získali klíč pro váš účet úložiště (budete ho potřebovat k vytvoření clusteru):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Níže go úryvek vytvoří cluster Spark se dvěma hlavní uzly a jeden pracovní uzel. Vyplňte prázdné proměnné, jak je vysvětleno v komentářích, a neváhejte změnit další parametry tak, aby vyhovovaly vašim specifickým potřebám.

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

### <a name="get-cluster-details"></a>Získání podrobností o clusteru

Získání vlastností pro daný cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Příklad

Můžete použít `get` k potvrzení, že jste úspěšně vytvořili cluster.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Výstup by měl vypadat takto:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Seznam clusterů

#### <a name="list-clusters-under-the-subscription"></a>Seznam clusterů v rámci předplatného

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Seznam clusterů podle skupiny prostředků

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Oba `List()` `ListByResourceGroup()` a `ClusterListResultPage` vrátit strukturu. Chcete-li získat další stránku, můžete zavolat `Next()`. To lze opakovat, dokud `ClusterListResultPage.NotDone()` se vrátí `false`, jak je znázorněno v příkladu níže.

#### <a name="example"></a>Příklad

Následující příklad vytiskne vlastnosti všech clusterů pro aktuální předplatné:

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

### <a name="update-cluster-tags"></a>Aktualizace značek clusteru

Značky daného clusteru můžete aktualizovat takto:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Příklad

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Změna velikosti clusteru

Velikost počtu pracovních uzlů daného clusteru můžete změnit zadáním nové velikosti takto:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorování clusteru

Sadu HDInsight Management SDK lze také použít ke správě monitorování v clusterech prostřednictvím sady Operations Management Suite (OMS).

Podobně jako způsob, `ClusterClient` jakým jste vytvořili pro operace `ExtensionClient` správy, je třeba vytvořit pro monitorování operací. Po dokončení výše uvedené části Ověřování můžete `ExtensionClient` vytvořit takto:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Níže uvedené příklady monitorování předpokládají, `ExtensionClient` že `extClient` jste `Authorizer` již inicializovali volaný a nastavte jeho, jak je uvedeno výše.

### <a name="enable-oms-monitoring"></a>Povolení monitorování OMS

> [!NOTE]  
> Chcete-li povolit monitorování OMS, musíte mít existující pracovní prostor Analýzy protokolů. Pokud jste ještě nevytvořili, můžete se dozvědět, jak to udělat tady: [Vytvoření pracovního prostoru Analýzy protokolů na webu Azure Portal](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Povolení monitorování OMS v clusteru:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Zobrazit stav monitorování OMS

Jak získat stav OMS v clusteru:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Zakázání monitorování OMS

Zakázání služby OMS v clusteru:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Akce skriptu

HDInsight poskytuje konfigurační funkci nazvanou akce skriptu, která vyvolá vlastní skripty pro přizpůsobení clusteru.

> [!NOTE]  
> Další informace o používání akcí skriptů naleznete zde: [Přizpůsobení clusterů HDInsight založených na Linuxu pomocí akcí skriptů](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Provádění akcí skriptu

Akce skriptu můžete v daném clusteru provádět takto:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Pro operace Odstranit akci skriptu a Seznam trvalých akcí skriptu `ScriptActionsClient`je třeba vytvořit `ClusterClient` podobně jako způsob, jakým jste je vytvořili pro operace správy. Po dokončení výše uvedené části Ověřování můžete `ScriptActionsClient` vytvořit takto podobné:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Níže uvedené příklady akcí skriptu předpokládají, `scriptActionsClient` že `Authorizer` jste již inicializovali volaný `ScriptActionsClient` a nastavili jeho, jak je uvedeno výše.

### <a name="delete-script-action"></a>Akce Odstranit skript

Chcete-li odstranit zadanou akci trvalého skriptu v daném clusteru,

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Seznam trvalých akcí skriptu

> [!NOTE]  
> Oba `ListByCluster()` vrátí `ScriptActionsListPage` strukturu. Chcete-li získat další stránku, můžete zavolat `Next()`. To lze opakovat, dokud `ClusterListResultPage.NotDone()` se vrátí `false`, jak je znázorněno v příkladu níže.

Chcete-li vypsat všechny trvalé akce skriptu pro zadaný cluster:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Příklad

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

### <a name="list-all-scripts-execution-history"></a>Vypsat historii spuštění všech skriptů

Pro tuto operaci je třeba `ScriptExecutionHistoryClient`vytvořit , podobně `ClusterClient` jako způsob, jakým jste vytvořili pro operace správy. Po dokončení výše uvedené části Ověřování můžete `ScriptActionsClient` vytvořit takto podobné:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Níže předpokládá, že jste již `ScriptExecutionHistoryClient` `scriptExecutionHistoryClient` inicializovali volané a nastavte jeho, `Authorizer` jak je uvedeno výše.

Chcete-li vypsat historii spuštění všech skriptů pro zadaný cluster:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Příklad

Tento příklad vytiskne všechny podrobnosti pro všechny minulé spuštění skriptu.

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

## <a name="next-steps"></a>Další kroky

Prozkoumejte [referenční materiál GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). GoDocs poskytují referenční dokumentaci pro všechny funkce v sdk.
