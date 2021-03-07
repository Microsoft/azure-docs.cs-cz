---
title: Přehled clusteru Kubernetes na zařízení Microsoft Azure Stack Edge pro | Microsoft Docs
description: Popisuje způsob implementace Kubernetes na zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 72ba07090e6ce67501761d97876aa136f146d61c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437923"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes na zařízení GPU pro Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Kubernetes je oblíbená Open-Source platforma pro orchestraci kontejnerových aplikací. Tento článek obsahuje přehled Kubernetes a potom popisuje, jak Kubernetes funguje na zařízení Azure Stack Edge pro. 

## <a name="about-kubernetes"></a>O Kubernetes 

Kubernetes poskytuje snadnou a spolehlivou platformu pro správu kontejnerových aplikací a jejich přidružených síťových komponent a součástí úložiště. Pomocí Kubernetes můžete rychle sestavovat, doručovat a škálovat kontejnery aplikací.

Jako otevřená platforma můžete použít Kubernetes k sestavování aplikací s upřednostňovaným programovacím jazykem, knihovnami operačních systémů nebo sběrnicí pro zasílání zpráv. Aby bylo možné naplánovat a nasadit verze, Kubernetes se mohou integrovat s existujícími nástroji pro průběžnou integraci a průběžné doručování.

Další informace najdete v tématu [jak Kubernetes funguje](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes na Azure Stack Edge pro

Na zařízení Azure Stack Edge pro můžete vytvořit cluster Kubernetes konfigurací výpočtů. Když je nakonfigurovaná role COMPUTE, cluster Kubernetes, včetně hlavních a pracovních uzlů, se nasadí a nakonfiguruje za vás. Tento cluster se pak použije pro nasazení úlohy prostřednictvím `kubectl` , IoT Edge nebo ARC Azure.

Zařízení Azure Stack Edge pro je k dispozici jako konfigurace s jedním uzlem, která představuje cluster infrastruktury. Cluster Kubernetes je oddělený od clusteru infrastruktury a je nasazený nad cluster infrastruktury. Cluster infrastruktury poskytuje trvalé úložiště pro zařízení Azure Stack Edge pro, zatímco cluster Kubernetes zodpovídá výhradně za orchestraci aplikací. 

Cluster Kubernetes v tomto případě má hlavní uzel a pracovní uzel. Uzly Kubernetes v clusteru jsou virtuální počítače, ve kterých běží vaše aplikace a cloudové pracovní postupy. 

Hlavní uzel Kubernetes zodpovídá za údržbu požadovaného stavu clusteru. Hlavní uzel také řídí pracovní uzel, který zase spouští kontejnerové aplikace. 

Následující obrázek znázorňuje implementaci Kubernetes na zařízení Azure Stack Edge pro s 1 uzlem. Zařízení s jedním uzlem není vysoce dostupné a pokud se jeden uzel nepovede, zařízení se ukončí. Cluster Kubernetes také rozroste.

![Architektura Kubernetes pro zařízení s Azure Stack Edge pro 1 uzel](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Další informace o architektuře clusteru Kubernetes najdete v [části Kubernetes Core koncepty](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Zřizování svazku úložiště

Aby bylo možné podporovat aplikační úlohy, můžete připojit svazky úložiště pro trvalá data na sdílených složkách zařízení Azure Stack Edge pro. Je možné použít jak statické, tak dynamické svazky. 

Další informace najdete v tématu možnosti zřizování úložiště pro aplikace v [úložišti Kubernetes pro zařízení Azure Stack Edge](azure-stack-edge-gpu-kubernetes-storage.md)pro.

## <a name="networking"></a>Sítě

Kubernetes Networks umožňuje konfigurovat komunikaci v rámci vaší Kubernetes sítě, včetně sítě z kontejnerů do kontejnerů, sítě pod sebou, sítě s koncovými službami a sítě Internet-to-Service. Další informace najdete v tématu model sítě v [Kubernetes sítě pro vaše zařízení Azure Stack Edge](azure-stack-edge-gpu-kubernetes-networking.md)pro.

## <a name="updates"></a>Aktualizace

Jakmile budou k dispozici nové verze Kubernetes, cluster se dá upgradovat pomocí standardních aktualizací, které jsou dostupné pro vaše zařízení Azure Stack Edge pro. Postup upgradu najdete v tématu věnovaném [instalaci aktualizací pro Azure Stack Edge pro](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Přístup, monitorování

Cluster Kubernetes na vašem zařízení Azure Stack Edge pro umožňuje Kubernetes řízení přístupu na základě role (Kubernetes RBAC). Další informace najdete v tématu [Kubernetes řízení přístupu na základě role na zařízení GPU Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-rbac.md).

Stav clusteru a prostředků můžete také monitorovat prostřednictvím řídicího panelu Kubernetes. K dispozici jsou také protokoly kontejneru. Další informace najdete v tématu [Použití řídicího panelu Kubernetes k monitorování stavu clusteru Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Azure Monitor je také k dispozici jako doplněk pro shromažďování dat o stavu z kontejnerů, uzlů a řadičů. Další informace najdete v tématu [přehled Azure monitor](../azure-monitor/overview.md) .

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Správa aplikací

Po vytvoření clusteru Kubernetes na zařízení Azure Stack Edge pro můžete spravovat aplikace nasazené v tomto clusteru pomocí kterékoli z následujících metod:

- Nativní přístup prostřednictvím `kubectl`
- IoT Edge 
- Azure Arc

Tyto metody jsou vysvětleny v následujících částech.


### <a name="kubernetes-and-kubectl"></a>Kubernetes a kubectl

Po nasazení clusteru Kubernetes můžete spravovat aplikace nasazené v clusteru místně z klientského počítače. K interakci s aplikacemi použijete nativní nástroj, jako je například *kubectl* prostřednictvím příkazového řádku. 

Další informace o nasazení clusteru Kubernetes najdete [v nasazení clusteru Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Informace o správě najdete na [webu použití kubectl ke správě clusteru Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes a IoT Edge

Kubernetes je také možné integrovat s IoT Edge úlohami na zařízení Azure Stack Edge pro, kde Kubernetes zajišťuje škálování a ekosystém a IoT zajišťuje ekosystém orientovaný na IoT. Vrstva Kubernetes se používá jako vrstva infrastruktury pro nasazení Azure IoT Edge úloh. Doba životnosti modulu a vyrovnávání zatížení sítě je spravovaná pomocí Kubernetes, zatímco platforma hraniční aplikace je spravovaná pomocí IoT Edge.

Další informace o nasazení aplikací do clusteru Kubernetes prostřednictvím IoT Edge najdete tady: 

- [Zpřístupňujte bezstavové aplikace na zařízení Azure Stack Edge pro prostřednictvím IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes a ARC Azure

Azure ARC je hybridní Nástroj pro správu, který vám umožní nasadit aplikace do clusterů Kubernetes. Azure ARC vám taky umožňuje použít Azure Monitor pro kontejnery k zobrazení a monitorování clusterů. Další informace najdete v Azure-Arc s [povoleným Kubernetes?](../azure-arc/kubernetes/overview.md). Informace o cenách Azure ARC najdete v [cenách Azure ARC](https://azure.microsoft.com/services/azure-arc/#pricing).

Od března 2021 bude Kubernetes s povoleným použitím Azure ARC všeobecně k dispozici pro uživatele a standardní poplatky za používání. Jako zákazník s vyhodnocenou verzí Preview bude pro Azure Stack hraničních zařízení k dispozici bezplatně Kubernetesý Azure ARC. Pokud chcete využít nabídku Preview, vytvořte [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

1. V seznamu **Typ problému** vyberte **Fakturace**.
2. V části **Předplatné** vyberte své předplatné.
3. V části **Služba** vyberte **moje služby** a pak vyberte **Azure Stack Edge**.
4. V části **prostředek** vyberte svůj prostředek.
5. V části **Souhrn** zadejte popis problému.
6. V části **typ problému** vyberte **neočekávané poplatky**.
7. V části **problémový typ** vyberte možnost **pomáhat mi při bezplatné zkušební verzi pochopit poplatky**.


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o Kubernetes Storage na [zařízení Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md).
- Pochopení síťového modelu Kubernetes na [zařízení Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-networking.md).
- Nasaďte [Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md) v Azure Portal.
