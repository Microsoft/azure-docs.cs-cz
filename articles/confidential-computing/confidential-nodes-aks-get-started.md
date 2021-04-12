---
title: 'Rychlý Start: nasazení clusteru AKS s důvěrnými výpočetními uzly pomocí Azure CLI'
description: Naučte se vytvářet clustery Azure Kubernetes Service (AKS) s důvěrnými uzly a nasazovat Hello World aplikaci pomocí Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: b012a8a5856b344b366f1ddd89fc5059a6f3c8ae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283520"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Rychlý Start: nasazení clusteru AKS s důvěrnými výpočetními uzly pomocí Azure CLI

V tomto rychlém startu použijete rozhraní příkazového řádku Azure CLI k nasazení clusteru služby Azure Kubernetes (AKS) s uzly s důvěrným výpočetním prostředím (DCsv2). Pak na enklávy spustíte jednoduchou aplikaci Hello World. Můžete také zřídit cluster a přidat důvěrné výpočetní uzly z Azure Portal, ale tento rychlý Start se zaměřuje na rozhraní příkazového řádku Azure CLI.

AKS je spravovaná služba Kubernetes, která umožňuje vývojářům nebo operátorům clusteru rychle nasazovat a spravovat clustery. Pokud se chcete dozvědět víc, přečtěte si [Úvod k AKS](../aks/intro-kubernetes.md) a [Přehled AKSch důvěrných uzlů](confidential-nodes-aks-overview.md).

Mezi funkce důvěrného výpočetního uzlu patří:

- Linux Worker Nodes podporuje kontejnery Linux.
- Virtuální počítač generace 2 s uzly virtuálních počítačů s Ubuntu 18,04.
- Procesor s technologií Intel SGX podporuje spouštění vašich kontejnerů v chráněných důvěrnosti enklávy využitím šifrované paměti stránkovaného cache (EPC). Další informace najdete v tématu [Nejčastější dotazy týkající se důvěrného výpočetního prostředí Azure](./faq.md).
- Ovladač Intel SGX DCAP je předinstalovaný na uzlech s důvěrnými výpočetními prostředími. Další informace najdete v tématu [Nejčastější dotazy týkající se důvěrného výpočetního prostředí Azure](./faq.md).

> [!NOTE]
> Virtuální počítače s DCsv2 používají specializovaný hardware, který je předmětem vyšší ceny a dostupnosti oblastí. Další informace najdete v tématu [dostupné SKU a podporované oblasti](virtual-machine-solutions.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

- Musíte mít aktivní předplatné Azure. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Verze Azure CLI 2.0.64 nebo novější je nainstalovaná a nakonfigurovaná na vašem počítači nasazení. 

  Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
- Ve vašem předplatném je minimálně šest DCsv2 jader. 

  Ve výchozím nastavení je kvóta pro důvěrné výpočty v rámci předplatného Azure osm jader virtuálních počítačů. Pokud plánujete zřídit cluster, který vyžaduje více než osm jader, postupujte podle [těchto pokynů](../azure-portal/supportability/per-vm-quota-requests.md) , abyste vyvolali lístek zvýšení kvóty.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Vytvoření clusteru AKS s důvěrnými výpočetními uzly a doplňkem

Pomocí následujících pokynů vytvořte cluster AKS s povoleným doplňkem pro důvěrné výpočty, přidejte do clusteru fond uzlů a ověřte, co jste vytvořili.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Vytvoření clusteru AKS s fondem uzlů systému

> [!NOTE]
> Pokud už máte cluster AKS, který splňuje výše uvedená kritéria splnění požadavků, [přejděte k další části](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) , kde můžete přidat fond uzlů pro důvěrný výpočetní výkon.

Nejdřív vytvořte skupinu prostředků pro cluster pomocí příkazu [AZ Group Create][az-group-create] . Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Teď vytvořte cluster AKS s povoleným doplňkem důvěrného zpracování pomocí příkazu [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Přidání fondu uživatelských uzlů s možnostmi důvěrného výpočetního prostředí do clusteru AKS 

Spuštěním následujícího příkazu přidejte fond uživatelských uzlů o `Standard_DC2s_v2` velikosti se třemi uzly do clusteru AKS. V [seznamu podporovaných SKU a oblastí DCsv2](../virtual-machines/dcv2-series.md)můžete zvolit jinou skladovou položku.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Po spuštění příkazu by měl být nový fond uzlů s DCsv2 viditelný s doplňkem DaemonSets ([modul plug-in pro zařízení SGX](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)) s důvěrným výpočetním prostředím.

### <a name="verify-the-node-pool-and-add-on"></a>Ověření fondu uzlů a doplňku

Přihlašovací údaje pro cluster AKS získáte pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pomocí `kubectl get pods` příkazu ověřte, zda jsou uzly správně vytvořeny a DaemonSets související s SGX jsou spuštěny v fondech uzlů DCsv2:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Pokud výstup odpovídá předchozímu kódu, váš cluster AKS je teď připravený ke spouštění důvěrných aplikací.

Pokud chcete otestovat aplikaci v enklávy, můžete přejít na [Hello World nasazení z izolovaného enklávy aplikace](#hello-world) v tomto rychlém startu. Pokud chcete přidat další fondy uzlů na AKS, použijte následující pokyny. (AKS podporuje kombinování fondů uzlů SGX a jiných než SGX fondů uzlů.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Přidání fondu důvěrných výpočetních uzlů do stávajícího clusteru AKS<a id="existing-cluster"></a>

V této části se předpokládá, že už máte spuštěný cluster AKS, který splňuje kritéria pro splnění požadavků uvedená dříve v tomto rychlém startu.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Povolit pro existující cluster doplněk důvěrné výpočetní AKS

Spusťte následující příkaz, který povolí doplněk důvěrného výpočetního prostředí:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Přidání fondu uživatelských uzlů DCsv2 do clusteru

> [!NOTE]
> Aby bylo možné používat důvěrné výpočetní funkce, musí mít váš stávající cluster AKS minimálně jeden fond uzlů, který je založený na SKU virtuálního počítače DCsv2. Další informace o SKU virtuálních počítačů řadičů domény a v2 pro důvěrné výpočetní prostředí najdete v tématu [dostupné SKU a podporované oblasti](virtual-machine-solutions.md).

Spuštěním následujícího příkazu vytvořte fond uzlů:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Ověřte, že byl vytvořen nový fond uzlů s názvem *confcompool1* :

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Ověřte, že DaemonSets běží na fondech důvěrných uzlů.

Přihlaste se ke stávajícímu clusteru AKS a proveďte následující ověření:

```console
kubectl get nodes
```

Výstup by měl zobrazit nově přidaný *confcompool1* fond v clusteru AKS. Můžete se také podívat na další DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Pokud výstup odpovídá předchozímu kódu, váš cluster AKS je teď připravený ke spouštění důvěrných aplikací. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Nasazení Hello World z izolované aplikace enklávy <a id="hello-world"></a>
Nyní jste připraveni nasadit testovací aplikaci. 

Vytvořte soubor s názvem *Hello-World-enklávy. yaml* a vložte ho do následujícího manifestu YAML. Tento kód ukázkové aplikace najdete v [otevřeném projektu enklávy](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Toto nasazení předpokládá, že jste nasadili doplněk *confcom* .

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
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Nyní pomocí `kubectl apply` příkazu vytvořte ukázkovou úlohu, která se otevře v zabezpečeném enklávy, jak je znázorněno v následujícím příkladu výstupu:

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

Fond uzlů pro důvěrný výpočetní výkon, který jste vytvořili v tomto rychlém startu, odeberete pomocí následujícího příkazu: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Pokud chcete odstranit cluster AKS, použijte následující příkaz: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Další kroky

* Pomocí důvěrných [kontejnerů v GitHubu](https://github.com/Azure-Samples/confidential-container-samples)spusťte Python, Node nebo jiné aplikace prostřednictvím důvěrných kontejnerů.

* Spouštění aplikací pracujících s enklávy pomocí [ukázek kontejnerů Azure s podporou enklávy na GitHubu](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
