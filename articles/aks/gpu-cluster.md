---
title: Použít GPU ve službě Azure Kubernetes Service (AKS)
description: Informace o používání GPU pro vysokovýkonné výpočetní prostředí nebo úlohy náročné na grafiku ve službě Azure Kubernetes Service (AKS)
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/25/2018
ms.author: laevenso
ms.openlocfilehash: 683abd9bad93bff51bea84c8081d2b8f9d300cd4
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419244"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Použití GPU pro úlohy náročné na výpočetní prostředky ve službě Azure Kubernetes Service (AKS)

Grafických procesorů (GPU) se často používají pro úlohy náročné na výpočetní prostředky, jako jsou grafiky a vizualizace úloh. AKS podporuje vytváření fondů uzlu s podporou grafického procesoru pro spuštění těchto úloh náročných na výpočetní v Kubernetes. Další informace o dostupných virtuálních počítačů s podporou grafického procesoru, naleznete v tématu [GPU optimalizované velikosti virtuálních počítačů v Azure][gpu-skus]. Pro uzly AKS, doporučujeme minimální velikost *Standard_NC6*.

> [!NOTE]
> Virtuální počítače s podporou grafického procesoru obsahovat specializovaném hardwaru, který je v souladu s vyšší ceny a regionální dostupnosti. Další informace najdete v tématu [ceny] [ azure-pricing] nástroj a [dostupnost v oblastech][azure-availability].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS pomocí uzlů, které podporují GPU. AKS cluster musí používat Kubernetes 1.10 nebo novější. Pokud potřebujete cluster AKS, který splňuje tyto požadavky, najdete v první části tohoto článku [vytvoření clusteru AKS](#create-an-aks-cluster).

Také nutné mít Azure CLI verze 2.0.49 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Pokud potřebujete AKS cluster, který splňuje minimální požadavky (s podporou grafického procesoru uzlu a Kubernetes verze 1.10 nebo novější), proveďte následující kroky. Pokud už máte cluster AKS, který splňuje tyto požadavky, přejděte k další části.

Nejprve vytvořte skupinu prostředků s využitím clusteru [vytvořit skupiny az] [ az-group-create] příkazu. Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v *eastus* oblasti:

```azurecli
az group create --name myResourceGroup --location eastus
```

Teď vytvořte cluster AKS pomocí [az aks vytvořit] [ az-aks-create] příkazu. Následující příklad vytvoří cluster s jeden uzel velikosti `Standard_NC6`, a spouští Kubernetes verze 1.10.8:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.10.8
```

Získat přihlašovací údaje pro váš cluster AKS pomocí [az aks get-credentials] [ az-aks-get-credentials] příkaz:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Ověřte, zda jsou plánovatelná GPU

Se vytvoří cluster AKS ověřte, zda GPU plánovatelná v Kubernetes. Nejprve vypište uzly v clusteru pomocí [kubectl get uzly] [ kubectl-get] příkaz:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-18821093-0   Ready    agent   6m    v1.10.8
```

Teď pomocí [kubectl popisují uzel] [ kubectl-describe] příkazu ověřte, že jsou plánovatelná GPU. V části *kapacity* části GPU zveřejnit jako `nvidia.com/gpu:  1`. Pokud se nezobrazí GPU, přečtěte si článek [dostupnost řešení potíží s GPU](#troubleshoot-gpu-availability) oddílu.

Následujícímu zhuštěnému příkladu ukazuje, že je k dispozici na uzel s názvem grafického procesoru *aks nodepool1 18821093 0*:

```
$ kubectl describe node aks-nodepool1-18821093-0

Name:               aks-nodepool1-18821093-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713824Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5940m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             53417120Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 688e083d19554d4a9563bd138f4ca98b
 System UUID:                08162568-B987-A84D-8865-98D6EFC64B32
 Boot ID:                    7b440249-8a96-42eb-950f-08c9a3c530b7
 Kernel Version:             4.15.0-1023-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.10.8
 Kube-Proxy Version:         v1.10.8
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myGPUCluster_myGPUCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-18821093-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                    CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                  ----                                    ------------  ----------  ---------------  -------------
  gpu-resources              nvidia-device-plugin-9cfcf              0 (0%)        0 (0%)      0 (0%)           0 (0%)

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Spuštění úlohy s podporou grafického procesoru

Zobrazíte GPU v akci Naplánujte úlohu s podporou grafického procesoru u žádosti odpovídající prostředek. V tomto příkladu, můžeme spustit [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) úlohy proti [datovou sadu mnist ručně](http://yann.lecun.com/exdb/mnist/).

Vytvořte soubor s názvem *ukázky tf mnist ručně demo.yaml* a vložte následující YAML manifestu. Následující úloha manifest obsahuje omezení prostředků `nvidia.com/gpu: 1`:

> [!NOTE]
> Pokud se zobrazí chybová zpráva o neshodě verze při volání do ovladače, například verze ovladače CUDA není dostatečná pro verzi modulu runtime CUDA, podívejte se na nVidia ovladač matice kompatibility graf- [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Použití [použití kubectl] [ kubectl-apply] příkaz ke spuštění úlohy. Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Zobrazit výstup úlohy s podporou grafického procesoru a stav

Sledovat průběh úlohy pomocí [kubectl get úlohy] [ kubectl-get] příkazů `--watch` argument. Může trvat několik minut na první o přijetí změn na obrázku a zpracovat datovou sadu. Když *dokončování* sloupec zobrazuje *1/1*, úloha byla úspěšně dokončena:

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Podívat se na výstup úlohy s podporou grafického procesoru, nejdřív získejte název podů s [kubectl get pods] [ kubectl-get] příkaz:

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Teď použijte [kubectl protokoly] [ kubectl-logs] příkazu zobrazte protokoly pod. Následující příklad pod protokoly potvrďte, že příslušné zařízení GPU zjištění `Tesla K80`. Zadejte název pro vlastní podu:

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání přidružené Kubernetes objekty vytvořené v tomto článku, použijte [kubectl odstranit úlohu] [ kubectl delete] takto:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>Řešení potíží s dostupností GPU

Pokud nevidíte GPU jako dostupné na uzly, budete muset nasadit DaemonSet pro modul plug-in nVidia zařízení. Tato DaemonSet spustí pod v každém uzlu a poskytovat požadované ovladače GPU.

Nejprve vytvořte obor názvů pomocí [kubectl vytvoření oboru názvů] [ kubectl-create] příkazu, jako například *gpu prostředky*:

```console
kubectl create namespace gpu-resources
```

Vytvořte soubor s názvem *nvidia zařízení modulu plug-in ds.yaml* a vložte následující YAML manifestu. Aktualizace `image: nvidia/k8s-device-plugin:1.10` poloviny dolů manifestu tak, aby odpovídala verzi Kubernetes. Například pokud váš cluster AKS používá Kubernetes verze 1.11, aktualizujte značky `image: nvidia/k8s-device-plugin:1.11`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
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
      containers:
      - image: nvidia/k8s-device-plugin:1.10 # Update this tag to match your Kubernetes version
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
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Teď použijte [použití kubectl] [ kubectl-apply] příkazu vytvořte DaemonSet:

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Spustit [kubectl popisují uzel] [ kubectl-describe] příkazu ověřte, zda GPU je nyní k dispozici na uzlu.

## <a name="next-steps"></a>Další postup

Chcete-li spouštět úlohy Apache Sparku, přečtěte si téma [Apache Spark spuštění úloh v AKS][aks-spark].

Další informace o spouštění machine learning (ML) úloh v Kubernetes najdete v tématu [Kubeflow Labs][kubeflow-labs].

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

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
