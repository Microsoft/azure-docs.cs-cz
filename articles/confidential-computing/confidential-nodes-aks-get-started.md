---
title: 'Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure s důvěrnými výpočetními uzly'
description: Naučte se vytvářet cluster AKS s důvěrnými uzly a nasazovat aplikaci Hello World pomocí Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 9343d3fa82302711311d8db3672713fa80fab1f7
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122168"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-using-azure-cli-preview"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) s důvěrnými výpočetními uzly pomocí rozhraní příkazového řádku Azure (Preview)

Tento rychlý Start je určený pro vývojáře nebo operátory clusterů, kteří chtějí rychle vytvořit cluster AKS a nasadit aplikaci pro monitorování aplikací pomocí spravované služby Kubernetes v Azure.

## <a name="overview"></a>Přehled

V tomto rychlém startu se dozvíte, jak nasadit cluster Azure Kubernetes Service (AKS) s důvěrnými výpočetními uzly pomocí Azure CLI a spustit aplikaci Hello World v enklávy. AKS je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. Přečtěte [si další informace o AKS.](https://docs.microsoft.com/azure/aks/intro-kubernetes)

> [!NOTE]
> DCsv2 virtuální počítače s důvěrnými výpočetními využitím specializovaného hardwaru podléhajícího vyšší ceně a dostupnosti oblastí. Další informace najdete na stránce virtuálních počítačů pro [dostupné SKU a podporované oblasti](virtual-machine-solutions.md).

### <a name="deployment-pre-requisites"></a>Požadavky na nasazení

1. Máte aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
1. Ve vašem počítači nasazení musí být nainstalovaná a nakonfigurovaná verze Azure CLI 2.0.64 nebo novější (spuštěním nástroje  `az --version` Najděte verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) .
1. minimální verze [rozšíření AKS-Preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) 0.4.62 
1. Ve vašem předplatném je k dispozici minimálně šest jader DCSv2 pro použití. Ve výchozím nastavení kvóta virtuálních počítačů pro důvěrné výpočetní služby na předplatné Azure 8 jader. Pokud plánujete zřídit cluster, který vyžaduje více než 8 jader, postupujte podle [těchto](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) pokynů a vyvolejte lístek zvýšení kvóty.

### <a name="confidential-computing-node-features"></a>Funkce uzlu důvěrného výpočetního prostředí

1. Linux Worker Nodes podporující pouze kontejnery Linux
1. Ubuntu generace 2 18,04 Virtual Machines
1. PROCESOR založený na procesorech Intel SGX s využitím paměti EPC (Encrypted Page cache). Další informace najdete [tady](https://docs.microsoft.com/azure/confidential-computing/faq) .
1. Kubernetes verze 1.16 +
1. Předem nainstalovaný ovladač Intel SGX DCAP. Další informace najdete [tady](https://docs.microsoft.com/azure/confidential-computing/faq) .
1. Rozhraní příkazového řádku nasazené během verze Preview


## <a name="installing-the-cli-pre-requisites"></a>Instalace požadavků CLI

Pokud chcete nainstalovat rozšíření AKS-Preview 0.4.62 nebo novější, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Pokud chcete aktualizovat rozšíření CLI AKS-Preview, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli-interactive
az extension update --name aks-preview
```

Zaregistrujte Gen2VMPreview:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Může trvat několik minut, než se stav zobrazí jako zaregistrované. Stav registrace můžete zjistit pomocí příkazu AZ Feature list:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci poskytovatele prostředků Microsoft. ContainerService pomocí příkazu AZ Provider Register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Vytvoření clusteru AKS

Pokud už máte cluster AKS, který splňuje výše uvedené požadavky, [přejděte do oddílu existující cluster](#existing-cluster) a přidejte nový fond s důvěrnými výpočetními uzly.

Nejdřív vytvořte skupinu prostředků pro cluster pomocí příkazu AZ Group Create. Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v oblasti *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Nyní vytvořte cluster AKS pomocí příkazu AZ AKS Create. Následující příklad vytvoří cluster s jedním uzlem velikosti `Standard_DC2s_v2` . Můžete zvolit další podporovaný seznam SKU DCsv2 z [tohoto místa](https://docs.microsoft.com/azure/virtual-machines/dcv2-series):

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_DC2s_v2 \
    --node-count 3 \
    --enable-addon confcom \
    --network-plugin azure \
    --vm-set-type VirtualMachineScaleSets \
    --aks-custom-headers usegen2vm=true
```
Výše uvedený příkaz by měl zřídit nový cluster AKS s fondy uzlů DCSv2 a automaticky instalovat dvě sady démonů –[SGX Device Plugin](confidential-nodes-aks-overview.md#sgx-plugin)(  &  [pomocná pomocná](confidential-nodes-aks-overview.md#sgx-quote)sada SGX zařízení Plug-in SGX)

Pomocí příkazu AZ AKS Get-credentialss Získejte přihlašovací údaje pro váš cluster AKS:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Ověřte, zda jsou uzly správně vytvořeny a zda jsou v fondech uzlů DCSv2 spuštěny sady démonů SGX s použitím příkazu kubectl Get lusks & Nodes, jak je uvedeno níže:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Pokud výstup odpovídá výše uvedenému, cluster AKS je teď připravený ke spouštění důvěrných aplikací.

Pokud chcete otestovat aplikaci v enklávy, přečtěte si téma [Hello World v](#hello-world) části věnované nasazení enklávy. Případně postupujte podle pokynů níže v tématu Přidání dalších fondů uzlů na AKS (AKS podporuje kombinování fondů uzlů SGX a fondů uzlů bez SGX).

>Pokud v fondech uzlů DCSv2 nejsou nainstalované sady démonů SGX související s, spusťte následující příkaz.

```azurecli-interactive
az aks update --enable-addons confcom --resource-group myResourceGroup --name myAKSCluster
```

![Vytvoření clusteru DCSv2 AKS](./media/confidential-nodes-aks-overview/CLIAKSProvisioning.gif)

## <a name="adding-confidential-computing-node-to-existing-aks-cluster"></a>Přidání uzlu důvěrného computingu do stávajícího clusteru AKS<a id="existing-cluster"></a>

V této části se předpokládá, že máte spuštěný cluster AKS, který splňuje kritéria uvedená v části požadavky.

Nejdřív umožníte, aby se v existujícím clusteru povolily doplňky AKS související s důvěrným výpočetním prostředím:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Teď do clusteru přidat fond uzlů DCSv2

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
Výstup by měl zobrazovat nově přidané confcompool1 v clusteru AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Pokud výstup odpovídá výše uvedenému, cluster AKS je teď připravený ke spouštění důvěrných aplikací.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World z izolované aplikace enklávy <a id="hello-world"></a>
Vytvořte soubor s názvem *Hello-World-enklávy. yaml* a vložte následující manifest YAML. Tento otevřený enklávy ukázkový kód aplikace najdete v [projektu Open enklávy](https://github.com/openenclave/openenclave/tree/master/samples/helloworld).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Nyní pomocí příkazu kubectl Apply vytvořte ukázkovou úlohu, která se spustí v zabezpečeném enklávy, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Spuštěním následujících příkazů si můžete ověřit, že úlohy úspěšně vytvořila prostředí enklávy (Trusted Execution Environment):

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat přidružené fondy uzlů nebo odstranit cluster AKS, použijte následující příkazy:

Odstranění clusteru AKS
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Další kroky

Spusťte Python, uzel atd. Aplikace, které jsou důvěrné prostřednictvím důvěrných kontejnerů, návštěvou [důvěrných ukázek kontejnerů](https://github.com/Azure-Samples/confidential-container-samples).

Spouštějte enklávy aplikace s podporou enklávy a navštivte [si ukázky kontejnerů Azure s podporou](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).



