---
title: Principy správy úloh Kubernetes na zařízení Azure Stack Edge | Microsoft Docs
description: Popisuje, jak mohou být Kubernetes úlohy spravovány na zařízení Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 2e2a41f797c6c58597e90ef6bd6e373ab7408a7b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182050"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Správa úloh Kubernetes na zařízení Azure Stack Edge

V Azure Stack hraničních zařízeních se vytvoří cluster Kubernetes při konfiguraci výpočetní role. Po vytvoření clusteru Kubernetes je možné kontejnery aplikace nasadit do clusteru Kubernetes v části lusky. Existují různé způsoby, jak nasazovat úlohy do clusteru Kubernetes. 

Tento článek popisuje různé metody, které je možné použít k nasazení úloh na Azure Stack hraničních zařízeních.

## <a name="workload-types"></a>Typy úloh

Mezi dva běžné typy úloh, které můžete nasadit na Azure Stack hraniční zařízení, patří bezstavové aplikace nebo stavové aplikace.

- **Bezstavové aplikace** neuchovávají svůj stav a neukládají žádná data do trvalého úložiště. Všechna data uživatelů a relací zůstanou u klienta. Některé příklady bezstavových aplikací zahrnují webové front-endy, jako je Nginx a další webové aplikace.

    Nasazení Kubernetes můžete vytvořit pro nasazení bezstavové aplikace v clusteru. 

- **Stavové aplikace** vyžadují, aby jejich stav byl uložen. Stavové aplikace používají trvalé úložiště, jako jsou trvalé svazky, k ukládání dat pro použití serverem nebo jinými uživateli. Příklady stavových aplikací jsou databáze, jako je MongoDB.

    Pro nasazení stavové aplikace můžete vytvořit nasazení Kubernetes. 

## <a name="deployment-flow"></a>Tok nasazení

K nasazení aplikací na Azure Stack hraniční zařízení budete postupovat podle těchto kroků: 
 
1. **Konfigurace přístupu**: za prvé budete používat prostředí PowerShell pro vytvoření uživatele, vytvoření oboru názvů a udělení přístupu k tomuto oboru názvů uživateli.
2. **Konfigurace úložiště**: dále budete používat prostředek Azure Stack Edge v Azure Portal k vytvoření trvalých svazků pomocí statického nebo dynamického zřizování pro stavové aplikace, které budete nasazovat.
3. **Konfigurace sítě**: nakonec služby budete používat k vystavování aplikací externě a v rámci clusteru Kubernetes.
 
## <a name="deployment-types"></a>Typy nasazení

Existují tři hlavní způsoby nasazení vašich úloh. Každá z těchto metodologií nasazení vám umožňuje připojit se k samostatnému oboru názvů na zařízení a pak nasadit bezstavové nebo stavové aplikace.

![Nasazení úlohy Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Místní nasazení**: Jedná se prostřednictvím přístupového nástroje příkazového řádku, jako je například `kubectl` , který umožňuje nasazení K8 `yamls` . Připojíte se ke clusteru K8 na okraji Azure Stack, který vytvoříte pomocí `kubeconfig` souboru. Další informace najdete v pro [přístup ke clusteru Kubernetes prostřednictvím kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Nasazení IoT Edge**: Jedná se prostřednictvím IoT Edge, které se připojují ke službě Azure IoT Hub. Připojíte se ke clusteru K8 na zařízení Azure Stack Edge přes `iotedge` obor názvů. Agenti IoT Edge nasazená v tomto oboru názvů zodpovídají za připojení k Azure. Konfiguraci použijete `IoT Edge deployment.json` pomocí Azure DEVOPS CI/CD. Správa oboru názvů a IoT Edge se provádí prostřednictvím operátoru cloudu.

- **Nasazení Azure/ARC**: Azure ARC je hybridní Nástroj pro správu, který vám umožní nasadit aplikace do clusterů K8. Cluster K8 se připojujete na zařízení Azure Stack Edge přes `azure-arc namespace` . V tomto oboru názvů se nasazují agenti, kteří zodpovídají za připojení k Azure. Konfiguraci nasazení použijete pomocí správy konfigurace založené na GitOps. Azure ARC vám také umožní použít Azure Monitor pro kontejnery k zobrazení a monitorování clusterů. Další informace najdete v tom, [co je Azure-ARC s podporou Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Zvolit typ nasazení

Při nasazování aplikací Vezměte v úvahu následující informace:

- **Jeden nebo více typů**: můžete zvolit jednu možnost nasazení nebo kombinaci různých možností nasazení.
- **Cloud versus místní**: v závislosti na vašich aplikacích můžete zvolit místní nasazení prostřednictvím kubectl nebo nasazení v cloudu prostřednictvím IoT Edge a ARC Azure. 
    - Místní nasazení je pro vývojové scénáře více vhodné. Když zvolíte místní nasazení, budete omezeni na síť, ve které je nasazené vaše Azure Stack hraniční zařízení.
    - Pokud máte cloudového agenta, který můžete nasadit, měli byste nasadit svůj operátor cloudu a používat cloudovou správu.
- **IoT vs Azure ARC**: volba nasazení také závisí na záměru vašeho produktu. Pokud nasazujete aplikace nebo kontejnery, které mají hlubší integraci s ekosystémem IoT nebo IoT, měli byste vybrat IoT Edge způsob nasazení aplikací. Pokud máte existující nasazení Kubernetes, bude upřednostňovanou volbou Azure ARC.


## <a name="next-steps"></a>Další kroky

Chcete-li nasadit aplikaci místně pomocí kubectl, přečtěte si:

- [Nasaďte bezstavovou aplikaci na Azure Stack Edge přes kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Pokud chcete nasadit aplikaci prostřednictvím IoT Edge, přečtěte si:

- [Nasaďte vzorový modul na Azure Stack Edge přes IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Pokud chcete nasadit aplikaci přes Azure ARC, přečtěte si:

- [Nasaďte aplikaci pomocí ARC Azure](azure-stack-edge-gpu-deploy-sample-module.md).
