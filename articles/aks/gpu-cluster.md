---
title: Použití gpu ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak používat grafické procesory pro vysoce výkonné výpočetní nebo graficky náročné úlohy ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/27/2020
ms.openlocfilehash: 242fefb3b153d11e23d66f26049d0b68c0a4bf4a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383986"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Použití gpu pro úlohy náročné na výpočetní výkon ve službě Azure Kubernetes Service (AKS)

Grafické procesory (GPU) se často používají pro úlohy náročné na výpočetní výkon, jako je grafika a vizualizační úlohy. AKS podporuje vytváření fondů uzlů s podporou GPU pro spuštění těchto úloh náročných na výpočetní výkon v Kubernetes. Další informace o dostupných virtuálních počítačích s podporou GPU najdete [v tématu velikosti virtuálních zařízení optimalizované pro GPU v Azure][gpu-skus]. Pro uzly AKS doporučujeme minimální velikost *Standard_NC6*.

> [!NOTE]
> Virtuální moduly s podporou GPU obsahují specializovaný hardware, který podléhá vyšším cenám a dostupnosti oblasti. Další informace naleznete v [nástroji pro stanovení cen][azure-pricing] a [dostupnosti oblasti][azure-availability].

V současné době pomocí fondů uzlů s podporou GPU je k dispozici pouze pro fondy uzlů Linux.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS s uzly, které podporují gpu. Cluster AKS musí spustit Kubernetes 1.10 nebo novější. Pokud potřebujete cluster AKS, který splňuje tyto požadavky, naleznete v první části tohoto článku [k vytvoření clusteru AKS](#create-an-aks-cluster).

Potřebujete také nainstalované a nakonfigurované a nakonfigurované azure CLI verze 2.0.64 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Pokud potřebujete cluster AKS, který splňuje minimální požadavky (uzel s podporou GPU a Kubernetes verze 1.10 nebo novější), proveďte následující kroky. Pokud již máte cluster AKS, který splňuje tyto požadavky, [přejděte k další části](#confirm-that-gpus-are-schedulable).

Nejprve vytvořte skupinu prostředků pro cluster pomocí příkazu [az group create.][az-group-create] Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v oblasti *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte cluster AKS pomocí příkazu [az aks create.][az-aks-create] Následující příklad vytvoří cluster s jedním uzlem o velikosti `Standard_NC6`:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Získejte pověření pro cluster AKS pomocí příkazu [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-drivers"></a>Instalace ovladačů NVIDIA

Před gpu v uzlech je nutné nasadit DaemonSet pro plugin zařízení NVIDIA. Tato sada daemonset spouští pod na každém uzlu poskytnout požadované ovladače pro gpu.

Nejprve vytvořte obor názvů pomocí příkazu [create namespace kubectl,][kubectl-create] například *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Vytvořte soubor s názvem *nvidia-device-plugin-ds.yaml* a vložte následující manifest YAML. Tento manifest je součástí [pluginu zařízení NVIDIA pro projekt Kubernetes][nvidia-github].

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
      - image: nvidia/k8s-device-plugin:1.11
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

Nyní použijte příkaz [kubectl apply][kubectl-apply] k vytvoření sady DaemonSet a potvrďte, že plugin zařízení NVIDIA je úspěšně vytvořen, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Zkontrolujte, zda jsou gpu schedulable

Když je váš cluster AKS vytvořen, potvrďte, že gpu jsou schepitelné v Kubernetes. Nejprve uveďte uzly v clusteru pomocí příkazu [kubectl get uzly:][kubectl-get]

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Nyní použijte příkaz [ubectl describe node][kubectl-describe] k potvrzení, že GPU jsou schedulable. V části *Kapacita* by měl GPU uvést jako `nvidia.com/gpu:  1`.

Následující zkrácený příklad ukazuje, že gpu je k dispozici na uzlu s názvem *aks-nodepool1-18821093-0*:

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

## <a name="run-a-gpu-enabled-workload"></a>Spuštění úlohy s podporou GPU

Chcete-li zobrazit GPU v akci, naplánujte úlohu s podporou GPU s příslušným požadavkem na prostředky. V tomto příkladu spusťme úlohu [Tentenzorflow](https://www.tensorflow.org/) proti [datové sadě MNIST](http://yann.lecun.com/exdb/mnist/).

Vytvořte soubor s názvem *samples-tf-mnist-demo.yaml* a vložte následující manifest YAML. Následující manifest úlohy zahrnuje `nvidia.com/gpu: 1`limit prostředků ve výši :

> [!NOTE]
> Pokud se při volání do ovladačů zobrazí chyba neshody verzí verzí, například verze ovladače CUDA je nedostatečná pro verzi runtime CUDA, přečtěte si graf kompatibility matic ovladačů NVIDIA -[https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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

Ke spuštění úlohy použijte příkaz [kubectl apply.][kubectl-apply] Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Zobrazení stavu a výstupu úlohy s podporou GPU

Sledujte průběh úlohy pomocí příkazu [kubectl get jobs][kubectl-get] s argumentem. `--watch` Může trvat několik minut nejprve vytáhnout obrázek a zpracovat datovou sadu. Pokud se ve sloupci *DOKONČENÍ* zobrazí *1/1*, úloha byla úspěšně dokončena. Ukončete `kubetctl --watch` příkaz *pomocí kláves Ctrl-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Chcete-li se podívat na výstup úlohy s podporou GPU, nejprve získejte název podu pomocí příkazu [kubectl get pods:][kubectl-get]

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Nyní použijte příkaz [kubectl logs][kubectl-logs] k zobrazení protokolů pod. Následující ukázkové protokoly podu potvrzují, že bylo `Tesla K80`zjištěno příslušné zařízení GPU . Zadejte název pro svůj vlastní pod:

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

Chcete-li odebrat přidružené objekty Kubernetes vytvořené v tomto článku, použijte příkaz [kubectl delete delete][kubectl delete] takto:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Další kroky

Pokud chcete spustit úlohy Apache Spark, přečtěte si informace o [spuštění úloh Apache Spark na AKS][aks-spark].

Další informace o spuštění úloh strojového učení (ML) na Kubernetes najdete v [tématu Kubeflow Labs][kubeflow-labs].

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
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
