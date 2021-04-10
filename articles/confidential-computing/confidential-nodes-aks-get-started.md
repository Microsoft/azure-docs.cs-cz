---
title: 'Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure s důvěrnými výpočetními uzly'
description: V tomto rychlém startu se naučíte vytvořit cluster AKS s důvěrnými uzly a nasadit aplikaci Hello World pomocí Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: 73770acefc8a153e4a2f2fde146f9afd4c319cd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933130"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) s důvěrnými uzly (DCsv2) pomocí rozhraní příkazového řádku Azure

Tento rychlý Start je určený pro vývojáře nebo operátory clusterů, kteří chtějí rychle vytvořit cluster AKS a nasadit aplikaci pro monitorování aplikací pomocí spravované služby Kubernetes v Azure. Cluster můžete také zřídit a přidat k němu důvěrné výpočetní uzly z Azure Portal.

## <a name="overview"></a>Přehled

V tomto rychlém startu se dozvíte, jak nasadit cluster Azure Kubernetes Service (AKS) s důvěrnými výpočetními uzly pomocí Azure CLI a spustit v enklávy jednoduchou aplikaci Hello World. AKS je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. Pokud se chcete dozvědět víc, přečtěte si téma [Úvod do AKS](../aks/intro-kubernetes.md) a [Přehled důvěrných uzlů AKS](confidential-nodes-aks-overview.md).

> [!NOTE]
> DCsv2 virtuální počítače s důvěrnými výpočetními využitím specializovaného hardwaru podléhajícího vyšší ceně a dostupnosti oblastí. Další informace najdete na stránce virtuálních počítačů pro [dostupné SKU a podporované oblasti](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcsv2"></a>Funkce uzlu důvěrného computingu (DCsv2)

1. Linux Worker Nodes podporuje kontejnery Linux.
1. Virtuální počítač 2. generace s Ubuntu 18,04 Virtual Machines uzly.
1. PROCESOR založený na procesorech Intel SGX s využitím paměti EPC (Encrypted Page cache). Další informace si můžete přečíst [zde](./faq.md).
1. Podpora pro Kubernetes verze 1.16 +.
1. Ovladač Intel SGX DCAP je předinstalovaný na uzlech AKS. Další informace si můžete přečíst [zde](./faq.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

1. Aktivní předplatné Azure. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
1. Verze Azure CLI 2.0.64 nebo novější je nainstalovaná a nakonfigurovaná na vašem počítači pro nasazení (spuštěním nástroje `az --version` zjistíte verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
1. Ve vašem předplatném je minimálně šest **DCsv2** jader dostupných k použití. Ve výchozím nastavení je kvóta virtuálních počítačů pro každé předplatné Azure v rámci předplatného pro důvěrný výpočet osm jader. Pokud plánujete zřídit cluster, který vyžaduje více než osm jader, postupujte podle [těchto](../azure-portal/supportability/per-vm-quota-requests.md) pokynů, abyste vyvolali lístek zvýšení kvóty.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Vytvoření nového clusteru AKS s důvěrnými výpočetními uzly a doplňkem

Podle pokynů níže přidejte uzly s podporou důvěrného zpracování s doplňkem.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Vytvoření clusteru AKS s fondem uzlů systému

Pokud už máte cluster AKS, který splňuje výše uvedené požadavky, [přejděte do oddílu existující cluster](#existing-cluster) a přidejte nový fond s důvěrnými výpočetními uzly.

Nejdřív vytvořte skupinu prostředků pro cluster pomocí příkazu [AZ Group Create][az-group-create] . Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v oblasti *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Nyní vytvořte cluster AKS pomocí příkazu [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

Výše uvedený příkaz vytvoří nový cluster AKS s fondem uzlů systému s povoleným doplňkem. V dalším kroku přidejte do clusteru AKS fond uživatelských uzlů s důvěrnými výpočetními funkcemi.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Přidání fondu důvěrných výpočetních uzlů do clusteru AKS 

Spuštěním následujícího příkazu přidejte fond uživatelských uzlů o `Standard_DC2s_v2` velikosti se třemi uzly. Z podporovaného seznamu [SKU a oblastí DCsv2](../virtual-machines/dcv2-series.md)můžete zvolit jinou skladovou jednotku.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Po spuštění by měl být nový fond uzlů s **DCsv2** viditelný s doplňkem daemonsets pro důvěrné výpočetní prostředí ([modul plug-in zařízení SGX](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>Ověření fondu uzlů a doplňku

Pomocí příkazu [AZ AKS Get-credentialss][az-aks-get-credentials] Získejte přihlašovací údaje pro váš cluster AKS:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Ověřte, zda jsou uzly správně vytvořeny a daemonsets s SGX, které jsou spuštěny v fondech uzlů **DCsv2** pomocí příkazu kubectl Get & Nodes, jak je uvedeno níže:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Pokud výstup odpovídá výše uvedenému, cluster AKS je teď připravený ke spouštění důvěrných aplikací.

Pokud chcete otestovat aplikaci v enklávy, přečtěte si část Hello World v části nasazení [enklávy](#hello-world) . Případně postupujte podle pokynů níže v tématu Přidání dalších fondů uzlů na AKS (AKS podporuje kombinování fondů uzlů SGX a fondů uzlů bez SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Přidání fondu důvěrných výpočetních uzlů do stávajícího clusteru AKS<a id="existing-cluster"></a>

V této části se předpokládá, že máte spuštěný cluster AKS, který splňuje kritéria uvedená v oddílu požadavky (platí pro doplněk).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Povolit pro existující cluster doplněk důvěrné výpočetní AKS

Spusťte následující příkaz, který povolí doplněk důvěrného výpočetního prostředí:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Přidání fondu uživatelských uzlů **DCsv2** do clusteru

> [!NOTE]
> Aby bylo možné používat důvěrné výpočetní funkce, musí mít váš stávající cluster AKS minimálně jeden fond uzlů na základě SKU virtuálního počítače **DCsv2** . Další informace o důvěrných skladových položkách SKU virtuálních počítačů, které jsou k dispozici, najdete v tématu [dostupné SKU a podporované oblasti](virtual-machine-solutions.md).

Spuštěním následujícího příkazu vytvořte nový fond uzlů:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Ověřte, že byl vytvořen nový fond uzlů s názvem confcompool1:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Ověřte, že daemonsets běží na fondech důvěrných uzlů.

Přihlaste se ke stávajícímu clusteru AKS a proveďte následující ověření.

```console
kubectl get nodes
```

Výstup by měl zobrazovat nově přidané confcompool1 v clusteru AKS. Můžete se také podívat na další daemonsets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Pokud výstup odpovídá výše uvedenému, cluster AKS je teď připravený ke spouštění důvěrných aplikací. Pomocí níže uvedených pokynů nasaďte testovací aplikaci.

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

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat přidružené fondy uzlů nebo odstranit cluster AKS, použijte následující příkazy:

### <a name="remove-the-confidential-computing-node-pool"></a>Odebrat fond uzlů pro důvěrný výpočetní výkon

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Odstranění clusteru AKS

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Další kroky

* Pomocí důvěrných [ukázek kontejnerů](https://github.com/Azure-Samples/confidential-container-samples)spusťte Python, Node atd. aplikace, které jsou v tajných kontejnerech důvěrné.

* Spouštějte enklávy aplikace s podporou enklávy a navštivte [si ukázky kontejnerů Azure s podporou](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials