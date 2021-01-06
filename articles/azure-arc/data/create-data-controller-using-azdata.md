---
title: Vytvoření kontroleru dat pomocí Azure Data CLI (azdata)
description: Vytvořte řadič dat ARC Azure na typickém clusteru s více uzly Kubernetes, který jste už vytvořili pomocí Azure Data CLI (azdata).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 19451fb09919238a04ac953c9c38fc70b4744d16
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955293"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Vytvořte řadič dat ARC Azure pomocí [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Požadavky

Přehled informací najdete v tématu [vytvoření řadiče dat ARC Azure](create-data-controller.md) .

Pokud chcete vytvořit řadič dat ARC Azure pomocí nástroje [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , budete ho muset [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] nainstalovat.

   [Nainstalovat [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Bez ohledu na to, kterou cílovou platformu zvolíte, budete muset před vytvořením uživatele správce kontroleru dat nastavit následující proměnné prostředí. Tyto přihlašovací údaje můžete zadat jiným lidem, kteří potřebují mít k řadiči dat přístup správce podle potřeby.

**AZDATA_USERNAME** – uživatelské jméno podle vašeho výběru pro uživatele správce kontroleru dat. Příklad: `arcadmin`

**AZDATA_PASSWORD** – heslo zvolené pro uživatele správce kontroleru dat. Heslo musí obsahovat alespoň osm znaků a musí obsahovat znaky ze tří z následujících čtyř sad: velká písmena, malá písmena, číslice a symboly.

### <a name="linux-or-macos"></a>Linux nebo macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Než začnete vytvářet řadič dat ARC Azure, budete se muset připojit a ověřit ke clusteru Kubernetes a nechat si vybrat existující kontext Kubernetes. Způsob připojení ke clusteru Kubernetes nebo službě se liší. Přečtěte si dokumentaci pro distribuci Kubernetes nebo službu, kterou používáte, v tématu Jak se připojit k serveru rozhraní Kubernetes API.

Můžete zkontrolovat, že máte aktuální připojení Kubernetes a potvrdit aktuální kontext pomocí následujících příkazů.

```console
kubectl get namespace
kubectl config current-context
```

### <a name="connectivity-modes"></a>Režimy připojení

Jak je popsáno v tématu [režimy připojení a požadavky](https://docs.microsoft.com/azure/azure-arc/data/connectivity), může být nasazený řadič dat Azure ARC buď v `direct` `indirect` režimu připojení, nebo. V `direct` režimu připojení se data o využití automaticky a do Azure se odesílají do Azure. V tomto článku příklady určují `direct` režim připojení následujícím způsobem:

   ```console
   --connectivity-mode direct
   ```

   Pokud chcete vytvořit kontrolér s `indirect` režimem připojení, aktualizujte skripty v příkladu, jak je uvedeno níže:

   ```console
   --connectivity-mode indirect
   ```

#### <a name="create-service-principal"></a>Vytvoření instančního objektu

Pokud nasazujete řadič dat ARC Azure pomocí `direct` režimu připojení, jsou pro připojení Azure vyžadovány přihlašovací údaje instančního objektu. Instanční objekt slouží k odesílání dat o využití a metrikách. 

Pomocí těchto příkazů vytvořte objekt služby nahrávání metrik:

> [!NOTE]
> Vytvoření instančního objektu vyžaduje [určitá oprávnění v Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Chcete-li vytvořit instanční objekt, aktualizujte následující příklad. Nahraďte `<ServicePrincipalName>` názvem objektu služby a spusťte příkaz:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Pokud jste instanční objekt dříve vytvořili a stačí získat aktuální přihlašovací údaje, spusťte následující příkaz, kterým přihlašovací údaje resetujete.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Pokud například chcete vytvořit instanční objekt s názvem `azure-arc-metrics` , spusťte následující příkaz.

```console
az ad sp create-for-rbac --name azure-arc-metrics
```

Příklad výstupu:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Uložte `appId` hodnoty, `password` a `tenant` v proměnné prostředí pro pozdější použití. 

#### <a name="save-environment-variables-in-windows"></a>Uložení proměnných prostředí ve Windows

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

#### <a name="save-environment-variables-in-linux-or-macos"></a>Uložení proměnných prostředí v systému Linux nebo macOS

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

#### <a name="save-environment-variables-in-powershell"></a>Uložení proměnných prostředí v PowerShellu

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
$Env:SPN_AUTHORITY="https://login.microsoftonline.com"
```

Po vytvoření instančního objektu přiřaďte instančnímu objektu příslušnou roli. 

### <a name="assign-roles-to-the-service-principal"></a>Přiřazení rolí k instančnímu objektu

Spuštěním tohoto příkazu přiřaďte instanční objekt k `Monitoring Metrics Publisher` roli v předplatném, kde jsou umístěny prostředky vaší instance databáze:

#### <a name="run-the-command-on-windows"></a>Spuštění příkazu ve Windows

> [!NOTE]
> Při spuštění z prostředí systému Windows je třeba použít pro názvy rolí dvojité uvozovky.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-on-linux-or-macos"></a>Spuštění příkazu v systému Linux nebo macOS

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-in-powershell"></a>Spuštění příkazu v PowerShellu

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

S instančním objektem přiřazeným k příslušné roli a nastavením proměnných prostředí můžete pokračovat v vytváření kontroleru dat. 

## <a name="create-the-azure-arc-data-controller"></a>Vytvoření kontroleru dat ARC Azure

> [!NOTE]
> `--namespace`V níže uvedených příkladech můžete použít jinou hodnotu parametru příkazu azdata ARC DC Create, ale nezapomeňte použít tento název oboru názvů pro `--namespace parameter` všechny ostatní příkazy níže.

- [Vytvoření ve službě Azure Kubernetes (AKS)](#create-on-azure-kubernetes-service-aks)
- [Vytvoření v modulu AKS v centru Azure Stack](#create-on-aks-engine-on-azure-stack-hub)
- [Vytvoření AKS v systému Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
- [Vytvoření v Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
- [Vytvoření na platformě Red Hat OpenShift Container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
- [Vytvoření v open source, Kubernetes pro odesílání dat (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [Vytvoření ve službě AWS elastické Kubernetes (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
- [Vytvoření ve službě Google Cloud Kubernetes Engine (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Vytvoření ve službě Azure Kubernetes (AKS)

Ve výchozím nastavení používá profil nasazení AKS `managed-premium` třídu úložiště. `managed-premium`Třída úložiště bude fungovat jenom v případě, že máte virtuální počítače nasazené pomocí imagí virtuálních počítačů, které mají prémiové disky.

Pokud se chystáte použít `managed-premium` jako třídu úložiště, můžete spustit následující příkaz a vytvořit tak řadič dat. Zástupné symboly v příkazu nahraďte názvem skupiny prostředků, ID předplatného a umístěním Azure.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Pokud si nejste jistí, jakou třídu úložiště chcete použít, měli byste použít `default` třídu úložiště, která je podporovaná bez ohledu na typ používaného virtuálního počítače. Neposkytuje jenom nejrychlejší výkon.

Pokud chcete použít `default` třídu úložiště, můžete spustit tento příkaz:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Vytvoření v modulu AKS v centru Azure Stack

Ve výchozím nastavení používá profil nasazení `managed-premium` třídu úložiště. `managed-premium`Třída úložiště bude fungovat jenom v případě, že máte virtuální počítače, které se nasadily pomocí imagí virtuálních počítačů, které mají prémiové disky v centru Azure Stack.

Spuštěním následujícího příkazu můžete vytvořit řadič dat pomocí třídy úložiště Managed-Premium:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Pokud si nejste jistí, jakou třídu úložiště chcete použít, měli byste použít `default` třídu úložiště, která je podporovaná bez ohledu na typ používaného virtuálního počítače. V centru Azure Stack se disky Premium a standardní disky zálohují stejnou infrastrukturou úložiště. Proto se očekává, že budou poskytovat stejný obecný výkon, ale s různými limity IOPS.

Pokud chcete použít `default` třídu úložiště, můžete spustit tento příkaz.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Vytvoření AKS v systému Azure Stack HCI

Ve výchozím nastavení používá profil nasazení třídu úložiště s názvem `default` a typ služby `LoadBalancer` .

Spuštěním následujícího příkazu můžete vytvořit řadič dat pomocí `default` třídy úložiště a typu služby `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Vytvoření v Azure Red Hat OpenShift (ARO)

#### <a name="apply-the-scc"></a>Použít SCC

Před vytvořením řadiče dat v Azure Red Hat OpenShift budete muset použít určitá omezení kontextu zabezpečení (SCC). Pro verzi Preview tyto omezení zabezpečení neuvolní. Budoucí vydání budou poskytovat aktualizované SCC.

1. Stáhněte si omezení vlastního kontextu zabezpečení (SCC). Použijte jednu z následujících možností: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Následující příkaz stáhne oblouk-data-SCC. yaml:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Vytvořte SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Použijte SCC pro účet služby.

   > [!NOTE]
   > Použijte stejný obor názvů a v `azdata arc dc create` níže uvedeném příkazu. Příklad je `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```


#### <a name="create-custom-deployment-profile"></a>Vytvořit vlastní profil nasazení

Použijte profil `azure-arc-azure-openshift` pro Azure RedHat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Vytvoření kontroleru dat

Chcete-li vytvořit řadič dat, můžete spustit následující příkaz:

> [!NOTE]
> Použijte stejný obor názvů, a to v `oc adm policy add-scc-to-user` předchozích příkazech. Příklad je `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Vytvoření na platformě Red Hat OpenShift Container Platform (OCP)

> [!NOTE]
> Pokud používáte platformu Red Hat OpenShift Container Platform v Azure, doporučuje se použít nejnovější dostupnou verzi.

#### <a name="apply-the-scc"></a>Použít SCC

Než vytvoříte řadič dat na Red Hat OCP, budete muset použít určitá omezení kontextu zabezpečení (SCC). Pro verzi Preview tyto omezení zabezpečení neuvolní. Budoucí vydání budou poskytovat aktualizované SCC.

1. Stáhněte si omezení vlastního kontextu zabezpečení (SCC). Použijte jednu z následujících možností: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Následující příkaz stáhne oblouk-data-SCC. yaml:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Vytvořte SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Použijte SCC pro účet služby.

   > [!NOTE]
   > Použijte stejný obor názvů a v `azdata arc dc create` níže uvedeném příkazu. Příklad je `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

#### <a name="determine-storage-class"></a>Určení třídy úložiště

Také budete muset určit, kterou třídu úložiště použít, spuštěním následujícího příkazu.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Vytvořit vlastní profil nasazení

Spuštěním následujícího příkazu vytvořte nový soubor vlastního profilu nasazení na základě `azure-arc-openshift` profilu nasazení. Tento příkaz vytvoří adresář `custom` v aktuálním pracovním adresáři a vlastním souboru profilu nasazení `control.json` v tomto adresáři.

Použijte profil `azure-arc-openshift` pro OpenShift kontejnerové platformy.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Nastavení třídy úložiště 

Teď v následujícím příkazu nastavte požadovanou třídu úložiště tak, že nahradíte `<storageclassname>` název třídy úložiště, která se má použít, a to tak, že se určí spuštěním `kubectl get storageclass` příkazu výše.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Nastavit nástroj pro vyrovnávání zatížení (volitelné)

Ve výchozím nastavení `azure-arc-openshift` používá profil nasazení `NodePort` jako typ služby. Pokud používáte cluster OpenShift, který je integrovaný s nástrojem pro vyrovnávání zatížení, můžete změnit konfiguraci tak, aby používala `LoadBalancer` typ služby, a to pomocí následujícího příkazu:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Ověření zásad zabezpečení

Při použití OpenShift můžete chtít spustit s výchozími zásadami zabezpečení v OpenShift nebo chcete obecně uzamknout prostředí z více než typických. Volitelně můžete zvolit, že chcete zakázat některé funkce pro minimalizaci oprávnění potřebných v době nasazení a v době spuštění spuštěním následujících příkazů.

Tento příkaz zakáže kolekce metrik o luskech. Pokud tuto funkci zakážete, nebudete moct zobrazit metriky o luskech v řídicích panelech Grafana. Platí výchozí hodnota.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Tento příkaz zakáže kolekce metrik o uzlech. Pokud tuto funkci zakážete, nebudete moct zobrazit metriky o uzlech v řídicích panelech Grafana. Platí výchozí hodnota.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Tento příkaz zakáže možnost převzít výpisy paměti pro účely řešení potíží.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Vytvoření kontroleru dat

Nyní jste připraveni vytvořit řadič dat pomocí následujícího příkazu.

> [!NOTE]
>   Použijte stejný obor názvů, a to v `oc adm policy add-scc-to-user` předchozích příkazech. Příklad je `arc` .

> [!NOTE]
>   `--path`Parametr by měl ukazovat na _adresář_ obsahující control.jsv souboru, ne na control.jsv samotném souboru.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Vytvoření v open source, Kubernetes pro odesílání dat (kubeadm)

Profil nasazení kubeadm ve výchozím nastavení používá třídu úložiště nazvanou `local-storage` a typ služby `NodePort` . Pokud je to přijatelné, můžete přeskočit níže uvedené pokyny, které nastaví požadovanou třídu úložiště a typ služby, a hned spustit `azdata arc dc create` níže uvedený příkaz.

Pokud chcete přizpůsobit svůj profil nasazení tak, aby určoval konkrétní třídu úložiště nebo typ služby, Začněte vytvořením nového souboru profilu nasazení na základě profilu nasazení kubeadm spuštěním následujícího příkazu. Tento příkaz vytvoří adresář `custom` v aktuálním pracovním adresáři a vlastním souboru profilu nasazení `control.json` v tomto adresáři.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Dostupné třídy úložiště můžete vyhledat spuštěním následujícího příkazu.

```console
kubectl get storageclass
```

Teď v následujícím příkazu nastavte požadovanou třídu úložiště tak, že nahradíte `<storageclassname>` název třídy úložiště, která se má použít, a to tak, že se určí spuštěním `kubectl get storageclass` příkazu výše.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Ve výchozím nastavení používá profil nasazení kubeadm `NodePort` jako typ služby. Pokud používáte cluster Kubernetes, který je integrovaný s nástrojem pro vyrovnávání zatížení, můžete změnit konfiguraci pomocí následujícího příkazu.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Nyní jste připraveni vytvořit řadič dat pomocí následujícího příkazu.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Vytvoření ve službě AWS elastické Kubernetes (EKS)

Ve výchozím nastavení je třída úložiště EKS `gp2` a typ služby je `LoadBalancer` .

Spuštěním následujícího příkazu vytvořte řadič dat pomocí zadaného profilu nasazení EKS.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Vytvoření ve službě Google Cloud Kubernetes Engine (GKE)

Ve výchozím nastavení je třída úložiště GKE `standard` a typ služby je `LoadBalancer` .

Spuštěním následujícího příkazu vytvořte řadič dat pomocí zadaného profilu nasazení GKE.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Po spuštění příkazu pokračujte a [Monitorujte stav vytváření](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Monitorování stavu vytváření

Dokončení vytvoření kontroleru bude trvat několik minut. Průběh můžete sledovat v jiném okně terminálu pomocí následujících příkazů:

> [!NOTE]
>  V ukázkových příkazech se předpokládá, že jste vytvořili řadič dat a obor názvů Kubernetes s názvem `arc` . Pokud jste použili jiný název oboru názvů nebo data Controller, můžete ho nahradit `arc` vaším jménem.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Stav vytvoření kteréhokoli z nich můžete také ověřit spuštěním příkazu podobného níže. To je užitečné hlavně při řešení problémů.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Řešení potíží s vytvářením

Pokud narazíte na potíže s vytvářením, přečtěte si [příručku pro řešení potíží](troubleshoot-guide.md).
