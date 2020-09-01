---
title: Přehled clusteru Kubernetes na zařízení Microsoft Azure Stack Edge | Microsoft Docs
description: Popisuje, jak se Kubernetes implementuje na zařízení Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: be7e01644d7d1f232c50b1efbadf100671a262fe
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268104"
---
# <a name="kubernetes-on-your-azure-stack-edge-gpu-device"></a>Kubernetes na zařízení GPU na Azure Stack hraničních zařízeních

Kubernetes je oblíbená Open-Source platforma pro orchestraci kontejnerových aplikací. Tento článek obsahuje přehled Kubernetes a potom popisuje, jak Kubernetes funguje na zařízení Azure Stack Edge. 

## <a name="about-kubernetes"></a>O Kubernetes 

Kubernetes poskytuje snadnou a spolehlivou platformu pro správu kontejnerových aplikací a jejich přidružených síťových komponent a součástí úložiště. Pomocí Kubernetes můžete rychle sestavovat, doručovat a škálovat kontejnery aplikací.

Jako otevřená platforma můžete použít Kubernetes k sestavování aplikací s upřednostňovaným programovacím jazykem, knihovnami operačních systémů nebo sběrnicí pro zasílání zpráv. Aby bylo možné naplánovat a nasadit verze, Kubernetes se mohou integrovat s existujícími nástroji pro průběžnou integraci a průběžné doručování.

Další informace najdete v tématu [jak Kubernetes funguje](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge"></a>Kubernetes na Azure Stack Edge

Na Azure Stack hraničním zařízení můžete vytvořit cluster Kubernetes konfigurací výpočtů. Když je nakonfigurovaná role COMPUTE, cluster Kubernetes, včetně hlavních a pracovních uzlů, se nasadí a nakonfiguruje za vás. Tento cluster se pak použije pro nasazení úlohy prostřednictvím `kubectl` , IoT Edge nebo ARC Azure.

Azure Stack hraniční zařízení je k dispozici jako konfigurace s jedním uzlem, která představuje cluster infrastruktury. Cluster Kubernetes je oddělený od clusteru infrastruktury a je nasazený nad cluster infrastruktury. Cluster infrastruktury poskytuje trvalé úložiště pro Azure Stack hraniční zařízení, zatímco cluster Kubernetes zodpovídá výhradně při orchestraci aplikace. 

Cluster Kubernetes v tomto případě má hlavní uzel a pracovní uzel. Uzly Kubernetes v clusteru jsou virtuální počítače, ve kterých běží vaše aplikace a cloudové pracovní postupy. 

Hlavní uzel Kubernetes zodpovídá za údržbu požadovaného stavu clusteru. Hlavní uzel také řídí pracovní uzel, který zase spouští kontejnerové aplikace. 

Následující diagram znázorňuje implementaci Kubernetes na hraničním zařízení s jedním uzlem Azure Stack. Zařízení s jedním uzlem není vysoce dostupné a pokud se jeden uzel nepovede, zařízení se ukončí. Cluster Kubernetes také rozroste.

![Architektura Kubernetes pro zařízení hraničního Azure Stack s jedním uzlem](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Další informace o architektuře clusteru Kubernetes najdete v [části Kubernetes Core koncepty](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Zřizování svazku úložiště

Aby bylo možné podporovat aplikační úlohy, můžete připojit svazky úložiště pro trvalá data na sdílených složkách Azure Stack hraničních zařízení. Je možné použít jak statické, tak dynamické svazky. 

Další informace najdete v tématu možnosti zřizování úložiště pro aplikace v [úložišti Kubernetes pro vaše zařízení Azure Stack Edge](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Sítě

Kubernetes Networks umožňuje konfigurovat komunikaci v rámci vaší Kubernetes sítě, včetně sítě z kontejnerů do kontejnerů, sítě pod sebou, sítě s koncovými službami a sítě Internet-to-Service. Další informace najdete v tématu model sítě v [Kubernetes sítě pro vaše zařízení Azure Stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Aktualizace

Jakmile budou k dispozici nové verze Kubernetes, cluster se dá upgradovat pomocí standardních aktualizací, které jsou k dispozici pro vaše Azure Stack hraniční zařízení. Postup upgradu najdete v tématu [použití aktualizací pro Azure Stack Edge](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Přístup, monitorování

Cluster Kubernetes na vašem zařízení Azure Stack Edge umožňuje řízení přístupu na základě role (RBAC). Další informace najdete v tématu [řízení přístupu na základě role pro cluster Kubernetes na zařízení Azure Stack Edge](azure-stack-edge-gpu-kubernetes-rbac.md).

Stav clusteru a prostředků můžete také monitorovat prostřednictvím řídicího panelu Kubernetes. K dispozici jsou také protokoly kontejneru. Další informace najdete v tématu [Použití řídicího panelu Kubernetes k monitorování stavu clusteru Kubernetes na zařízení Azure Stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Azure Monitor je také k dispozici jako doplněk pro shromažďování dat o stavu z kontejnerů, uzlů a řadičů. Další informace najdete v tématu [přehled Azure monitor](../azure-monitor/overview.md) .

<!--## Private container registry

Kubernetes on Azure Stack Edge device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Správa aplikací

Po vytvoření clusteru Kubernetes na zařízení Azure Stack Edge můžete spravovat aplikace nasazené v tomto clusteru pomocí kterékoli z následujících metod:

- Nativní přístup prostřednictvím `kubectl`
- IoT Edge 
- Azure Arc

Tyto metody jsou vysvětleny v následujících částech.


### <a name="kubernetes-and-kubectl"></a>Kubernetes a kubectl

Po nasazení clusteru Kubernetes můžete spravovat aplikace nasazené v clusteru místně z klientského počítače. K interakci s aplikacemi použijete nativní nástroj, jako je například *kubectl* prostřednictvím příkazového řádku. 

Další informace o nasazení clusteru Kubernetes najdete [v nasazení clusteru Kubernetes na zařízení Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md). Informace o správě najdete na [webu použití kubectl ke správě clusteru Kubernetes na zařízení Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes a IoT Edge

Kubernetes je také možné integrovat s IoT Edge úlohami na Azure Stack hraničních zařízeních, kde Kubernetes zajišťuje škálování a ekosystém a IoT zajišťuje ekosystém orientovaný na IoT. Vrstva Kubernetes se používá jako vrstva infrastruktury pro nasazení Azure IoT Edge úloh. Doba životnosti modulu a vyrovnávání zatížení sítě je spravovaná pomocí Kubernetes, zatímco platforma hraniční aplikace je spravovaná pomocí IoT Edge.

Další informace o nasazení aplikací do clusteru Kubernetes prostřednictvím IoT Edge najdete tady: 

- [Zpřístupňujte bezstavové aplikace na Azure Stack hraničním zařízení prostřednictvím IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes a ARC Azure

Azure ARC je hybridní Nástroj pro správu, který vám umožní nasadit aplikace do clusterů Kubernetes. Azure ARC vám taky umožňuje použít Azure Monitor pro kontejnery k zobrazení a monitorování clusterů. Další informace najdete v tom, [co je Azure-ARC s podporou Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview). Informace o cenách Azure ARC najdete v [cenách Azure ARC](https://azure.microsoft.com/services/azure-arc/#pricing).


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o Kubernetes Storage na [zařízení Azure Stack Edge](azure-stack-edge-gpu-kubernetes-storage.md).
- Princip síťových modelů Kubernetes na [zařízení Azure Stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).
- Nasaďte [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) do Azure Portal.
