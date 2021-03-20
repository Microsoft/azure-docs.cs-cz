---
title: Použití GPU ve službě Azure Kubernetes Service (AKS)
description: Naučte se používat GPU pro vysoce výkonné úlohy náročné na výpočetní výkon nebo grafiku ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929136"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Použití GPU pro úlohy náročné na výpočetní výkon ve službě Azure Kubernetes Service (AKS)

Grafické procesory (GPU) se často používají pro úlohy náročné na výpočetní výkon, jako jsou například úlohy grafiky a vizualizace. AKS podporuje vytváření fondů uzlů s podporou GPU pro spouštění těchto úloh náročných na výpočetní výkon v Kubernetes. Další informace o dostupných virtuálních počítačích s podporou GPU najdete [v tématu velikosti virtuálních počítačů optimalizované pro GPU v Azure][gpu-skus]. Pro uzly AKS doporučujeme minimální velikost *Standard_NC6*.

> [!NOTE]
> Virtuální počítače s podporou GPU obsahují specializovaný hardware, který je předmětem vyšší ceny a dostupnosti oblastí. Další informace najdete v tématu dostupnost nástroje pro [ceny][azure-pricing] a [oblasti][azure-availability].

V současné době jsou fondy uzlů s podporou GPU dostupné jenom pro fondy uzlů Linux.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS s uzly, které podporují GPU. Cluster AKS musí běžet v Kubernetes 1,10 nebo novějším. Pokud potřebujete cluster AKS, který splňuje tyto požadavky, Projděte si první část tohoto článku a [vytvořte cluster AKS](#create-an-aks-cluster).

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Pokud potřebujete cluster AKS, který splňuje minimální požadavky (uzel s podporou GPU a Kubernetes verze 1,10 nebo novější), proveďte následující kroky. Pokud již máte cluster AKS, který splňuje tyto požadavky, [přejděte k další části](#confirm-that-gpus-are-schedulable).

Nejdřív vytvořte skupinu prostředků pro cluster pomocí příkazu [AZ Group Create][az-group-create] . Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v oblasti *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte cluster AKS pomocí příkazu [AZ AKS Create][az-aks-create] . Následující příklad vytvoří cluster s jedním uzlem velikosti `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Pomocí příkazu [AZ AKS Get-credentialss][az-aks-get-credentials] Získejte přihlašovací údaje pro váš cluster AKS:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Nainstalovat modul plug-in zařízení NVIDIA

Než bude možné použít GPU v uzlech, je nutné nasadit DaemonSet pro modul plug-in zařízení NVIDIA. Tento DaemonSet spustí pod každým uzlem, aby poskytoval požadované ovladače pro GPU.

Nejprve vytvořte obor názvů pomocí příkazu [kubectl Create Namespace][kubectl-create] , například *GPU-Resources*:

```console
kubectl create namespace gpu-resources
```

Vytvořte soubor s názvem *NVIDIA-Device-plugin-DS. yaml* a vložte následující manifest YAML. Tento manifest je k dispozici jako součást [modulu plug-in zařízení NVIDIA pro projekt Kubernetes][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Nyní pomocí příkazu [kubectl Apply][kubectl-apply] vytvořte DaemonSet a potvrďte, že se modul plug-in zařízení NVIDIA úspěšně vytvořil, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Použití specializované image GPU AKS (Preview)

Jako alternativu k těmto krokům AKS poskytuje plně nakonfigurovanou AKS bitovou kopii, která již obsahuje [modul plug-in zařízení NVIDIA pro Kubernetes][nvidia-github].

> [!WARNING]
> Neměli byste ručně instalovat sadu démonů modulu plug-in pro zařízení NVIDIA pro clustery pomocí nové AKSy specializované image GPU.


Zaregistrujte `GPUDedicatedVHDPreview` funkci:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](/cli/azure/feature#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

K instalaci rozšíření AKS-Preview rozhraní příkazového řádku použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
az extension add --name aks-preview
```

Pokud chcete aktualizovat rozšíření CLI AKS-Preview, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Použití specializované image GPU AKS pro nové clustery (Preview)    

Nakonfigurujte cluster tak, aby při vytvoření clusteru používal image AKS specializovaného grafického procesoru. Pomocí `--aks-custom-headers` příznaku v uzlech agentů GPU na novém clusteru použijte k použití AKS specializované image GPU.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Pokud chcete vytvořit cluster pomocí běžných imagí AKS, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku. Můžete se také rozhodnout přidat další specializované fondy uzlů GPU podle níže uvedených pokynů.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Použití specializované image GPU AKS na existujících clusterech (Preview)

Konfigurace nového fondu uzlů pro použití specializované image GPU AKS Použijte `--aks-custom-headers` příznak Flag pro uzly agenta GPU v novém fondu uzlů, aby se použila AKS specializovaná image GPU.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Pokud chcete vytvořit fond uzlů pomocí běžných imagí AKS, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku. 

> [!NOTE]
> Pokud vaše SKU GPU vyžaduje virtuální počítače generace 2, můžete vytvořit tento postup:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Potvrďte, že jsou plánovatelná GPU.

S vytvořeným clusterem AKS ověřte, že GPU jsou plánovatelná v Kubernetes. Nejprve seznam uzlů v clusteru pomocí příkazu [kubectl Get Nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Nyní použijte příkaz [kubectl popsat uzel][kubectl-describe] a potvrďte, že GPU jsou plánovatelná. V části *kapacita* by měl grafický procesor vypsat jako `nvidia.com/gpu:  1` .

Následující zhuštěný příklad ukazuje, že grafický procesor je k dispozici na uzlu s názvem *AKS-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Spuštění úlohy s povoleným GPU

Pokud chcete zobrazit GPU v akci, naplánujte úlohu s povoleným GPU pomocí příslušné žádosti o prostředky. V tomto příkladu spustíme úlohu [Tensorflow](https://www.tensorflow.org/) s [datovou sadou mnist ručně zapsaných](http://yann.lecun.com/exdb/mnist/).

Vytvořte soubor s názvem *Samples-TF-mnist ručně zapsaných-demo. yaml* a vložte následující manifest YAML. Následující manifest úlohy zahrnuje omezení prostředků `nvidia.com/gpu: 1` :

> [!NOTE]
> Pokud při volání do ovladačů obdržíte chybu neshody verzí, například, verze ovladače CUDA není dostatečná pro verzi CUDA runtime, Projděte si graf kompatibility matice ovladače NVIDIA – [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Spusťte úlohu pomocí příkazu [kubectl Apply][kubectl-apply] . Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Zobrazení stavu a výstupu úlohy s podporou GPU

Sledujte průběh úlohy pomocí příkazu [kubectl získat úlohy][kubectl-get] s `--watch` argumentem. První načtení obrázku a zpracování datové sady může trvat několik minut. Když se ve sloupci *dokončení* zobrazí *1/1*, úloha se úspěšně dokončila. Ukončete `kubetctl --watch` příkaz pomocí *kombinace kláves CTRL-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Pokud se chcete podívat na výstup úlohy s povoleným grafickým procesorem, nejdřív si načtěte název pod příkazem [kubectl Get lusks][kubectl-get] :

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Nyní použijte příkaz [kubectl logs][kubectl-logs] k zobrazení protokolů pod. V následujícím příkladu se v protokolech potvrdí, že se zjistilo příslušné zařízení GPU `Tesla K80` . Zadejte název pro vlastní pod:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání přidružených objektů Kubernetes vytvořených v tomto článku použijte příkaz [kubectl Delete Job][kubectl delete] :

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Další kroky

Pokud chcete spouštět úlohy Apache Spark, přečtěte si téma [spuštění úloh Apache Spark na AKS][aks-spark].

Další informace o spouštění úloh strojového učení (ML) na Kubernetes najdete v tématu [Kubeflow Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
