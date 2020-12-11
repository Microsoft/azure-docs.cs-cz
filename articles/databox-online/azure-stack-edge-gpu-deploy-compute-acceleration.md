---
title: Použití GPU nebo VPUy výpočetní akcelerace na zařízeních s Azure Stack Edge pro Kubernetes nasazení | Microsoft Docs
description: Popisuje způsob použití GPU nebo VPUy výpočetní akcelerace na grafickém procesoru pro Azure Stack Edge pro, Azure Stack Edge pro R nebo Azure Stack hraniční Mini RI pro nasazení Kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: cf70b24dae70ad2e64f3443e4c4d959d46fb4ea4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095028"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Použití výpočetní akcelerace na Azure Stackovém procesoru Edge pro nasazení Kubernetes

Tento článek popisuje, jak používat výpočetní akceleraci v Azure Stack hraničních zařízeních při použití nasazení Kubernetes. Tento článek se týká Azure Stackch PROCESORů Edge pro, Azure Stack Edge pro R a Azure Stack hraničních zařízení R.


## <a name="about-compute-acceleration"></a>O akceleraci výpočetní technologie 

Pro většinu procesů spuštěných v počítači je střední jednotka pro střední procesor (CPU) vaším výchozím výpočetním účelem pro účely obecného určení. Specializovaný počítačový hardware se často používá k efektivnějšímu provádění některých funkcí, než je spuštění v softwaru v procesoru. Například grafický procesor (GPU) lze použít k urychlení zpracování dat v pixelech.  

Výpočetní akcelerace je pojem, který se používá konkrétně pro Azure Stack hraniční zařízení, kde se pro hardwarovou akceleraci používají grafické procesory (GPU), výpočetní jednotka (VPU) nebo pole programovatelné brány (FPGA). Většina úloh nasazených na Azure Stack hraničních zařízeních zahrnuje kritické časování, několik datových proudů kamery nebo vysoké kmitočty snímků, které vyžadují konkrétní hardwarovou akceleraci.

Článek se zabývá výpočetní akcelerací jenom pomocí GPU nebo VPU pro následující zařízení:

- **Grafický procesor Azure Stack Edge pro** : Tato zařízení můžou mít 1 nebo 2 NVIDIA T4 TENSOR Core GPU. Další informace najdete v tématu [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge pro R** – tato zařízení mají 1 grafický procesor NVIDIA T4 tensor Core. Další informace najdete v tématu [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack hraniční Mini R** – tato zařízení mají 1 Intel Movidius NESČETNÝCH X VPU. Další informace najdete v tématu [Intel Movidius nesčetných X VPU](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>Použití GPU pro nasazení Kubernetes

Následující příklad `yaml` lze použít pro grafický procesor Azure Stack Edge pro nebo pro zařízení Azure Stack Edge pro R s grafickým procesorem.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Použití VPU pro nasazení Kubernetes

Následující příklad `yaml` lze použít pro Azure Stack hraničního zařízení R, které má VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Další kroky

Naučte se [používat kubectl ke spuštění stavové aplikace Kubernetes s PersistentVolume na zařízení GPU Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
