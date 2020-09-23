---
title: Pochopení sítě Kubernetes na zařízení Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak Kubernetes síť funguje na zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 001304ad6eda27db2285aaa9ad8b28929e2a04f8
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899322"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes sítě v zařízení GPU pro Azure Stack Edge pro

Na zařízení Azure Stack Edge pro se vytvoří cluster Kubernetes při konfiguraci výpočetní role. Po vytvoření clusteru Kubernetes je možné kontejnery aplikace nasadit do clusteru Kubernetes v části lusky. Existují různé způsoby použití sítě pro lusky v clusteru Kubernetes. 

Tento článek podrobně popisuje sítě v clusteru Kubernetes obecně a konkrétně v kontextu zařízení Azure Stack Edge pro. 

## <a name="networking-requirements"></a>Požadavky na síť

Tady je příklad typické aplikace 2 vrstvy, která je nasazená do clusteru Kubernetes.

- Aplikace má front-end webového serveru a databázovou aplikaci v back-endu. 
- Každé z nich se přiřazuje IP adresa, ale tyto IP adresy se můžou změnit při restartování a převzetí služeb při selhání. 
- Každá aplikace se skládá z několika lusků a vyrovnávání zatížení napříč všemi replikami pod. 

![Kubernetes požadavky na síť](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

Výsledkem výše uvedeného scénáře jsou následující požadavky na síť:

 - Je potřeba, aby k externí aplikaci měl přístup uživatel aplikace mimo cluster Kubernetes prostřednictvím názvu nebo IP adresy. 
 - Aplikace v rámci clusteru Kubernetes, například front-end a části back-endu, by měly být schopné vzájemně komunikovat.

Abychom mohli vyřešit jak výše uvedené potřeby, zavádíme službu Kubernetes. 


## <a name="networking-services"></a>Síťové služby

Existují dva typy služeb Kubernetes: 

- **Služba IP adres clusteru** – Zamyslete se nad tím, že poskytujete konstantní koncový bod pro lusky aplikace. K jakémukoli poli pod přidruženým k těmto službám nelze přicházet z vnějšku clusteru Kubernetes. IP adresa, která se používá pro tyto služby, přichází z adresního prostoru v privátní síti. 
- **IP adresa nástroje pro vyrovnávání zatížení** , jako je služba IP adresy clusteru, ale přidružená IP adresa pochází z externí sítě a je dostupná z vnějšku clusteru Kubernetes.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Kubernetes síť na Azure Stack Edge pro

Calico, Metallb a základní DNS jsou všechny součásti, které jsou nainstalované pro sítě na Azure Stack Edge pro. 

- **Calico** přiřadí IP adresu z rozsahu PRIVÁTNÍch IP adres do každého dne a nakonfiguruje sítě pro tyto lusky tak, aby pod uzlem v jednom uzlu komunikovaly pod jiným uzlem. 
- **Metallb** běží na clusteru pod a přiřazuje IP adresu službám typu služba Vyrovnávání zatížení. IP adresy nástroje pro vyrovnávání zatížení se volí z rozsahu IP adres služby poskytnutého prostřednictvím místního uživatelského rozhraní. 
- **Základní DNS** – tento doplněk nakonfiguruje název služby Mapování záznamů DNS na IP adresu clusteru.

IP adresy, které se používají pro uzly Kubernetes a externí služby, jsou k dispozici prostřednictvím stránky **výpočetní síť** v místním uživatelském rozhraní zařízení.

![Kubernetes přiřazení IP adresy v místním uživatelském rozhraní](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

Přiřazení IP adresy je pro:

- **IP adresy uzlu Kubernetes**: Tento rozsah IP adres se používá pro hlavní a pracovní uzly Kubernetes. Tato IP adresa se používá, když uzly Kubernetes vzájemně komunikují.

- **IP adresy externích služeb Kubernetes**: Tento rozsah IP adres se používá pro externí služby (označované také jako Load Balancer služby), které jsou vystaveny mimo cluster Kubernetes.  


## <a name="next-steps"></a>Další kroky

Konfigurace sítě Kubernetes na Azure Stack Edge pro najdete v tématu:

- [Zpřístupněte bezstavovou aplikaci externě na Azure Stack Edge pro prostřednictvím IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Vystavte bezstavovou aplikaci externě na Azure Stack Edge pro prostřednictvím kuebctl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
