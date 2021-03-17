---
title: Principy správy úloh Kubernetes na zařízeních s Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak mohou být Kubernetes úlohy spravovány na zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: b962d66349bbed112114c010e8d185ba16c74c8d
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443076"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Správa úloh Kubernetes na zařízení Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Na zařízení Azure Stack Edge pro se vytvoří cluster Kubernetes při konfiguraci výpočetní role. Po vytvoření clusteru Kubernetes je možné kontejnery aplikace nasadit do clusteru Kubernetes v části lusky. Úlohy můžete do clusteru Kubernetes nasazovat různými způsoby. 

Tento článek popisuje různé metody, které je možné použít k nasazení úloh na zařízení Azure Stack Edge pro.

## <a name="workload-types"></a>Typy úloh

Dva běžné typy úloh, které můžete nasadit na zařízení Azure Stack Edge pro, jsou bezstavové aplikace nebo stavové aplikace.

- **Bezstavové aplikace** neuchovávají svůj stav a neukládají žádná data do trvalého úložiště. Všechna data uživatelů a relací zůstanou u klienta. Některé příklady bezstavových aplikací zahrnují webové front-endy, jako je Nginx a další webové aplikace.

    Nasazení Kubernetes můžete vytvořit pro nasazení bezstavové aplikace v clusteru. 

- **Stavové aplikace** vyžadují, aby jejich stav byl uložen. Stavové aplikace používají trvalé úložiště, jako jsou trvalé svazky, k ukládání dat pro použití serverem nebo jinými uživateli. Příklady stavových aplikací jsou databáze jako [Azure SQL Edge](../azure-sql-edge/overview.md) a MongoDB.

    Pro nasazení stavové aplikace můžete vytvořit nasazení Kubernetes. 

## <a name="deployment-flow"></a>Tok nasazení

K nasazení aplikací na zařízení Azure Stack Edge pro budete postupovat podle těchto kroků: 
 
1. **Konfigurace přístupu**: za prvé budete používat prostředí PowerShell pro vytvoření uživatele, vytvoření oboru názvů a udělení přístupu k tomuto oboru názvů uživateli.
2. **Konfigurace úložiště**: dále budete používat prostředek Azure Stack Edge v Azure Portal k vytvoření trvalých svazků pomocí statického nebo dynamického zřizování pro stavové aplikace, které budete nasazovat.
3. **Konfigurace sítě**: nakonec služby budete používat k vystavování aplikací externě a v rámci clusteru Kubernetes.
 
## <a name="deployment-types"></a>Typy nasazení

Existují tři hlavní způsoby nasazení vašich úloh. Každá z těchto metodologií nasazení vám umožňuje připojit se k samostatnému oboru názvů na zařízení a pak nasadit bezstavové nebo stavové aplikace.

![Nasazení úlohy Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Místní nasazení**: Toto nasazení probíhá prostřednictvím přístupového nástroje příkazového řádku `kubectl` , který umožňuje nasazení Kubernetes `yamls` . Ke clusteru Kubernetes se dostanete na Azure Stack Edge pro pomocí `kubeconfig` souboru. Další informace najdete v pro [přístup ke clusteru Kubernetes prostřednictvím kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Nasazení IoT Edge**: Jedná se prostřednictvím IoT Edge, které se připojují ke službě Azure IoT Hub. Pomocí oboru názvů se připojíte ke clusteru Kubernetes na zařízení Azure Stack Edge pro `iotedge` . Agenti IoT Edge nasazení v tomto oboru názvů zodpovídají za připojení k Azure. Konfiguraci použijete `IoT Edge deployment.json` pomocí Azure DEVOPS CI/CD. Správa oboru názvů a IoT Edge se provádí prostřednictvím operátoru cloudu.

- **Nasazení Kubernetes s povoleným Azure ARC**: Kubernetes ARC Azure je hybridní Nástroj pro správu, který vám umožní nasadit aplikace do clusterů Kubernetes. Připojíte se ke clusteru Kubernetes na zařízení Azure Stack Edge pro pomocí `azure-arc namespace` . Agenti nasazených v tomto oboru názvů zodpovídají za připojení k Azure. Konfiguraci nasazení použijete pomocí správy konfigurace založené na GitOps. 
    
    Služba Azure ARC s povoleným Kubernetes vám také umožní použít Azure Monitor pro kontejnery k zobrazení a monitorování clusteru. Další informace najdete v tom, [co je Azure ARC Enabled Kubernetes?](../azure-arc/kubernetes/overview.md).
    
    Od března 2021 bude Kubernetes s povoleným použitím Azure ARC všeobecně k dispozici pro uživatele a standardní poplatky za používání. Jako zákazník s vyhodnocenou verzí Preview bude pro Azure Stack hraničních zařízení k dispozici bezplatně Kubernetesý Azure ARC. Pokud chcete využít nabídku Preview, vytvořte [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

    1. V seznamu **Typ problému** vyberte **Fakturace**.
    2. V části **Předplatné** vyberte své předplatné.
    3. V části **Služba** vyberte **moje služby** a pak vyberte **Azure Stack Edge**.
    4. V části **prostředek** vyberte svůj prostředek.
    5. V části **Souhrn** zadejte popis problému.
    6. V části **typ problému** vyberte **neočekávané poplatky**.
    7. V části **problémový typ** vyberte možnost **pomáhat mi při bezplatné zkušební verzi pochopit poplatky**.


## <a name="choose-the-deployment-type"></a>Zvolit typ nasazení

Při nasazování aplikací Vezměte v úvahu následující informace:

- **Jeden nebo více typů**: můžete zvolit jednu možnost nasazení nebo kombinaci různých možností nasazení.
- **Cloud versus místní**: v závislosti na vašich aplikacích můžete zvolit místní nasazení prostřednictvím kubectl nebo nasazení v cloudu prostřednictvím IoT Edge a ARC Azure. 
    - Když zvolíte místní nasazení, budete omezeni na síť, ve které je nasazené vaše zařízení Azure Stack Edge pro.
    - Pokud máte cloudového agenta, který můžete nasadit, měli byste nasadit svůj operátor cloudu a používat cloudovou správu.
- **IoT vs Azure ARC**: volba nasazení také závisí na záměru vašeho produktu. Pokud nasazujete aplikace nebo kontejnery, které mají hlubší integraci s ekosystémem IoT nebo IoT, vyberte IoT Edge pro nasazení aplikací. Pokud máte existující nasazení Kubernetes, bude upřednostňovanou volbou Azure ARC.


## <a name="next-steps"></a>Další kroky

Chcete-li nasadit aplikaci místně pomocí kubectl, přečtěte si:

- [Nasaďte bezstavovou aplikaci na Azure Stack Edge pro pomocí kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Pokud chcete nasadit aplikaci prostřednictvím IoT Edge, přečtěte si:

- [Nasaďte vzorový modul na Azure Stack Edge pro pomocí IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Pokud chcete nasadit aplikaci přes Azure ARC, přečtěte si:

- [Nasaďte aplikaci pomocí ARC Azure](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).
