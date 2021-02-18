---
title: Azure HDInsight SDK pro cestách
description: Referenční materiál pro použití Azure HDInsight SDK for přejít a Apache Hadoop clusterů
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 09564bd149488d956586c8a0e349cd79eec358a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582759"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK for (Preview)

## <a name="overview"></a>Přehled
Sada HDInsight SDK for přejít poskytuje třídy a funkce, které umožňují spravovat clustery HDInsight. Zahrnuje operace pro vytváření, odstraňování, aktualizaci, vypsání, změnu velikosti, provádění akcí skriptu, monitorování, získávání vlastností clusterů HDInsight a další.

> [!NOTE]  
>Referenční materiál GoDoc pro tuto sadu SDK je také [k dispozici zde](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* [ `go get` Nástroj](https://github.com/golang/go/wiki/GoGetTools).
* [Přejít](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalace sady SDK

Z umístění GOPATH tak spusťte `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Nejdřív je potřeba ověřit sadu SDK s vaším předplatným Azure.  Použijte následující příklad k vytvoření instančního objektu a použijte ho k ověření. Po dokončení budete mít instanci `ClustersClient` , která obsahuje mnoho funkcí (popsaných v níže uvedených částech), které lze použít k provádění operací správy.

> [!NOTE]  
> Kromě níže uvedeného příkladu můžete ověřit jiné způsoby, které by mohly být vhodnější pro vaše potřeby. Zde jsou uvedené všechny funkce: [ověřování funkcí v Azure SDK pro go](/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Příklad ověřování pomocí instančního objektu

Nejdřív se přihlaste k [Azure Cloud Shell](https://shell.azure.com/bash). Ověřte, že aktuálně používáte předplatné, ve kterém chcete objekt služby vytvořit.

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

Pokud nejste přihlášení ke správnému předplatnému, vyberte správnou možnost spuštěním: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Pokud jste ještě nezaregistrovali poskytovatele prostředků služby HDInsight jinou funkcí (například vytvořením clusteru HDInsight prostřednictvím Azure Portal), musíte to provést předtím, než budete moct ověřit. To lze provést z [Azure Cloud Shell](https://shell.azure.com/bash) spuštěním následujícího příkazu:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Pak vyberte název instančního objektu a vytvořte ho pomocí následujícího příkazu:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Hlavní informace o instančním objektu se zobrazí jako JSON.

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

Zkopírujte následující fragment kódu a vyplňte `TENANT_ID` příkazy, `CLIENT_ID` , `CLIENT_SECRET` a `SUBSCRIPTION_ID` pomocí řetězců JSON, které byly vráceny po spuštění příkazu pro vytvoření instančního objektu.

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
> V této části se předpokládá, že už máte ověřenou a vytvořenou `ClusterClient` instanci a uložíte ji do proměnné s názvem `client` . Pokyny pro ověřování a získání `ClusterClient` najdete v části ověřování výše.

### <a name="create-a-cluster"></a>Vytvoření clusteru

Nový cluster lze vytvořit voláním `client.Create()` . 

#### <a name="example"></a>Příklad

Tento příklad ukazuje, jak vytvořit cluster [Apache Spark](https://spark.apache.org/) se dvěma hlavními uzly a jedním pracovním uzlem.

> [!NOTE]  
> Nejprve musíte vytvořit skupinu prostředků a účet úložiště, jak je vysvětleno níže. Pokud jste je již vytvořili, můžete tento postup přeskočit.

##### <a name="creating-a-resource-group"></a>Vytvoření skupiny prostředků

Skupinu prostředků můžete vytvořit pomocí [Azure Cloud Shell](https://shell.azure.com/bash) spuštěním

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště můžete pomocí [Azure Cloud Shell](https://shell.azure.com/bash) vytvořit spuštěním:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Nyní spusťte následující příkaz, který získá klíč pro váš účet úložiště (budete ho potřebovat pro vytvoření clusteru):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Fragment kódu na obrázku níže vytvoří cluster Spark se dvěma hlavními uzly a jedním pracovním uzlem. Vyplňte prázdné proměnné, jak je vysvětleno v komentářích a nebojte se změnit další parametry tak, aby vyhovovaly vašim konkrétním potřebám.

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

### <a name="list-clusters"></a>Výpis clusterů

#### <a name="list-clusters-under-the-subscription"></a>Výpis clusterů v rámci předplatného

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Vypíše clustery podle skupiny prostředků.

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> `List()`A `ListByResourceGroup()` vrací `ClusterListResultPage` strukturu. Chcete-li získat další stránku, můžete zavolat `Next()` . To lze opakovat, dokud se `ClusterListResultPage.NotDone()` nevrátí `false` , jak je znázorněno v následujícím příkladu.

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

Postup odstranění clusteru:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Aktualizace značek clusteru

Značky daného clusteru můžete aktualizovat například takto:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Příklad

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Změna velikosti clusteru

Změnou velikosti daného clusteru můžete určit novou velikost pracovních uzlů, například takto:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorování clusteru

Sadu SDK pro správu HDInsight můžete také použít ke správě monitorování clusterů přes Operations Management Suite (OMS).

Podobně jako u způsobu, jakým jste vytvořili pro `ClusterClient` použití pro operace správy, je potřeba vytvořit, `ExtensionClient` aby se použily k monitorování operací. Po dokončení výše uvedeného oddílu ověření můžete vytvořit `ExtensionClient` podobný postup:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> V níže uvedených příkladech monitorování se předpokládá, že jste již inicializoval `ExtensionClient` volání `extClient` a nastavili `Authorizer` , jak je uvedeno výše.

### <a name="enable-oms-monitoring"></a>Povolit monitorování OMS

> [!NOTE]  
> Chcete-li povolit monitorování OMS, je nutné mít existující Log Analytics pracovní prostor. Pokud jste ho ještě nevytvořili, můžete se dozvědět, jak to udělat: [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Postup povolení monitorování OMS ve vašem clusteru:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Zobrazit stav monitorování OMS

Postup získání stavu OMS ve vašem clusteru:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Zakázat monitorování OMS

Zakázání OMS ve vašem clusteru:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Akce skriptů

HDInsight poskytuje konfigurační funkci nazvanou akce skriptů, které vyvolávají vlastní skripty pro přizpůsobení clusteru.

> [!NOTE]  
> Další informace o tom, jak použít akce skriptů, najdete tady: [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](./hdinsight-hadoop-customize-cluster-linux.md)

### <a name="execute-script-actions"></a>Spustit akce skriptu

V daném clusteru můžete spouštět akce skriptu, například takto:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Pro operace odstranit skript Action a listovat trvalé akce skriptu je potřeba vytvořit `ScriptActionsClient` podobný způsob, jakým jste vytvořili pro `ClusterClient` použití při operacích správy. Po dokončení výše uvedeného oddílu ověření můžete vytvořit `ScriptActionsClient` podobný postup:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Níže uvedené příklady akcí skriptu předpokládají, že jste již inicializoval `ScriptActionsClient` volání `scriptActionsClient` a nastavili `Authorizer` , jak je uvedeno výše.

### <a name="delete-script-action"></a>Akce odstranění skriptu

Odstranění zadané akce trvalého skriptu v daném clusteru:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Vypsat trvalé akce skriptu

> [!NOTE]  
> Obě `ListByCluster()` vrátí `ScriptActionsListPage` strukturu. Chcete-li získat další stránku, můžete zavolat `Next()` . To lze opakovat, dokud se `ClusterListResultPage.NotDone()` nevrátí `false` , jak je znázorněno v následujícím příkladu.

Pro vypsání všech trvalých akcí skriptu pro zadaný cluster:
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

Pro tuto operaci je třeba vytvořit `ScriptExecutionHistoryClient` , podobně jako v případě, že jste vytvořili pro `ClusterClient` použití pro operace správy. Po dokončení výše uvedeného oddílu ověření můžete vytvořit `ScriptActionsClient` podobný postup:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Níže uvedený postup předpokládá, že jste již inicializovali `ScriptExecutionHistoryClient` volání `scriptExecutionHistoryClient` a nastavili `Authorizer` , jak je uvedeno výše.

Výpis historie spouštění všech skriptů pro zadaný cluster:

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

Prozkoumejte [referenční materiál GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight). GoDocs poskytuje referenční dokumentaci pro všechny funkce v sadě SDK.
