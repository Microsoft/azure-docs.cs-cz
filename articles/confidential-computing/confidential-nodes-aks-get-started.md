---
title: 'Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure s důvěrnými výpočetními uzly'
description: Naučte se vytvářet cluster AKS s důvěrnými uzly a nasazovat aplikaci Hello World pomocí Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/8/2020
ms.author: amgowda
ms.openlocfilehash: 866c8340cf9c16d768f4035326aa2ec52dbf1401
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653359"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) s důvěrnými uzly (DCsv2) pomocí rozhraní příkazového řádku Azure

Tento rychlý Start je určený pro vývojáře nebo operátory clusterů, kteří chtějí rychle vytvořit cluster AKS a nasadit aplikaci pro monitorování aplikací pomocí spravované služby Kubernetes v Azure. Cluster můžete také zřídit a přidat k němu důvěrné výpočetní uzly z Azure Portal.

## <a name="overview"></a>Přehled

V tomto rychlém startu se dozvíte, jak nasadit cluster Azure Kubernetes Service (AKS) s důvěrnými výpočetními uzly pomocí Azure CLI a spustit v enklávy jednoduchou aplikaci Hello World. AKS je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. Přečtěte [si další informace o AKS.](../aks/intro-kubernetes.md)

> [!NOTE]
> DCsv2 virtuální počítače s důvěrnými výpočetními využitím specializovaného hardwaru podléhajícího vyšší ceně a dostupnosti oblastí. Další informace najdete na stránce virtuálních počítačů pro [dostupné SKU a podporované oblasti](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcxs-v2"></a>Funkce důvěrného výpočetního uzlu (DC <x> s-v2)

1. Linux Worker Nodes podporující pouze kontejnery Linux
1. Virtuální počítač 2. generace s Ubuntu 18,04 uzly Virtual Machines
1. PROCESOR založený na procesorech Intel SGX s využitím paměti EPC (Encrypted Page cache). Další informace najdete [tady](./faq.md) .
1. Podpora Kubernetes verze 1.16 +
1. Ovladač Intel SGX DCAP je předinstalovaný na uzlech AKS. Další informace najdete [tady](./faq.md) .

## <a name="deployment-prerequisites"></a>Požadavky nasazení
Návod k nasazení vyžaduje následující:

1. Aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
1. Verze Azure CLI 2.0.64 nebo novější je nainstalovaná a nakonfigurovaná na vašem počítači pro nasazení (spuštěním nástroje `az --version` zjistíte verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](../container-registry/container-registry-get-started-azure-cli.md) .
1. Minimální verze [rozšíření Azure AKS-Preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) 0.5.0
1. Minimálně šest jader **DC <x> s-v2** dostupných v předplatném pro použití. Ve výchozím nastavení kvóta virtuálních počítačů pro důvěrné výpočetní služby na předplatné Azure 8 jader. Pokud plánujete zřídit cluster, který vyžaduje více než 8 jader, postupujte podle [těchto](../azure-portal/supportability/per-vm-quota-requests.md) pokynů a vyvolejte lístek zvýšení kvóty.

## <a name="cli-based-preparation-steps-required-for-add-on-in-preview---optional-but-recommended"></a>Přípravné kroky založené na rozhraní příkazového řádku (povinné pro doplněk ve verzi Preview – volitelné, ale doporučené)
Pomocí následujících pokynů povolte v AKS doplněk pro důvěrné výpočetní operace.

### <a name="step-1-installing-the-cli-prerequisites"></a>Krok 1: instalace požadovaných součástí CLI

Pokud chcete nainstalovat rozšíření AKS-Preview 0.5.0 nebo novější, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Pokud chcete aktualizovat rozšíření CLI AKS-Preview, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="step-2-azure-confidential-computing-addon-feature-registration-on-azure"></a>Krok 2: registrace funkce doplňku důvěrných výpočetních funkcí Azure v Azure
Registruje se AKS-ConfidentialComputingAddon v předplatném Azure. Tato funkce přidá daemonset modulu plug-in zařízení SGX, jak [je popsáno v podrobnostech](./confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks):
1. Modul plug-in ovladače zařízení SGX
```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Může trvat několik minut, než se stav zobrazí jako zaregistrované. Stav registrace můžete zjistit pomocí příkazu AZ Feature list. Tato registrace funkce se provádí jenom jednou pro každé předplatné. Pokud jste to předtím zaregistrovali, můžete předchozí krok přeskočit:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
```
Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci poskytovatele prostředků Microsoft. ContainerService pomocí příkazu AZ Provider Register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```
## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Vytváření nového clusteru AKS s důvěrnými výpočetními uzly a doplňkem
Podle pokynů níže přidejte uzly s podporou důvěrného zpracování s doplňkem.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>Krok 1: Vytvoření clusteru AKS s fondem systémových uzlů

Pokud už máte cluster AKS, který splňuje výše uvedené požadavky, [přejděte do oddílu existující cluster](#existing-cluster) a přidejte nový fond s důvěrnými výpočetními uzly.

Nejdřív vytvořte skupinu prostředků pro cluster pomocí příkazu AZ Group Create. Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v oblasti *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Nyní vytvořte cluster AKS pomocí příkazu AZ AKS Create.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
Výše uvedený příkaz vytvoří nový cluster AKS s fondem systémových uzlů s povoleným doplňkem. Nyní přejděte do AKS (DCsv2) a přidejte do něj uživatelský uzel důvěrné výpočetní Nodepool typu.

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>Krok 2: Přidání fondu důvěrných výpočetních uzlů do clusteru AKS 

Spusťte následující příkaz pro uživatele nodepool o `Standard_DC2s_v2` velikosti se 3 uzly. Můžete zvolit další podporovaný seznam SKU a oblastí [DCsv2:](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
Výše uvedený příkaz je dokončen. nový fond uzlů s **řadičem domény <x> s-v2** by měl být viditelný s doplňkem pro důvěrné výpočetní Daemonsets ([modul plug-in zařízení SGX](confidential-nodes-aks-overview.md#sgx-plugin)
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>Krok 3: ověření fondu uzlů a doplňku
Pomocí příkazu AZ AKS Get-credentialss Získejte přihlašovací údaje pro váš cluster AKS:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Ověřte, zda jsou uzly správně vytvořeny a daemonsets s SGX, které jsou spuštěny ve fondech **DC <x> s-v2** pomocí příkazu kubectl get lusks & Nodes, jak je uvedeno níže:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Pokud výstup odpovídá výše uvedenému, cluster AKS je teď připravený ke spouštění důvěrných aplikací.

Pokud chcete otestovat aplikaci v enklávy, přečtěte si téma [Hello World v](#hello-world) části věnované nasazení enklávy. Případně postupujte podle pokynů níže v tématu Přidání dalších fondů uzlů na AKS (AKS podporuje kombinování fondů uzlů SGX a fondů uzlů bez SGX).

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Přidání fondu důvěrných výpočetních uzlů do stávajícího clusteru AKS<a id="existing-cluster"></a>

V této části se předpokládá, že máte spuštěný cluster AKS, který splňuje kritéria uvedená v oddílu požadavky (platí pro doplněk).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Krok 1: Povolení doplňku důvěrné výpočetní AKS ve stávajícím clusteru

Spuštěním následujícího příkazu povolte doplněk důvěrného výpočetního prostředí.

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>Krok 2: Přidání fondu uživatelských uzlů **DC <x> s-v2** do clusteru
    
> [!NOTE]
> Pokud chcete používat funkci důvěrného výpočetního prostředí, váš stávající cluster AKS musí mít minimálně jeden fond uzlů na základě SKU virtuálního počítače ve verzi **<x> v2** . Další informace o dostupných položkách SKU [a podporovaných oblastech](virtual-machine-solutions.md)pro DCsv2 virtuální počítače s podporou důvěrného zpracování
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
výše uvedený příkaz by měl vypsat poslední fond uzlů, který jste přidali s názvem confcompool1.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Krok 3: Ověřte, že daemonsets běží na fondech důvěrných uzlů.

Přihlaste se ke stávajícímu clusteru AKS a proveďte níže uvedené ověření. 

```console
kubectl get nodes
```
Výstup by měl zobrazovat nově přidané confcompool1 v clusteru AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Pokud výstup odpovídá výše uvedenému, cluster AKS je teď připravený ke spouštění důvěrných aplikací. Postupujte prosím podle níže uvedeného testovacího nasazení aplikace.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World z izolované aplikace enklávy <a id="hello-world"></a>
Vytvořte soubor s názvem *Hello-World-enklávy. yaml* a vložte následující manifest YAML. Tento otevřený enklávy ukázkový kód aplikace najdete v [projektu Open enklávy](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Následující nasazení předpokládá, že jste nasadili doplněk "confcom".

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
