---
title: Nasazení brány Azure API Management v místním prostředí do Kubernetes | Microsoft Docs
description: Postup nasazení brány Azure API Management v místním prostředí do Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513832"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Nasazení brány Azure API Management v místním prostředí do Kubernetes

Tento článek popisuje postup nasazení brány Azure API Management v místním prostředí do clusteru Kubernetes.

> [!NOTE]
> Funkce brány pro samoobslužné hostování je ve verzi Preview. V rámci verze Preview je brána v místním prostředí dostupná jenom pro vývojáře a úrovně Premium bez dalších poplatků. Úroveň pro vývojáře je omezená na jediné nasazení samoobslužné brány.


## <a name="prerequisites"></a>Předpoklady

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Vytvořte cluster Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) je dobrou možností pro účely vývoje a vyhodnocení. V případě produkčních úloh můžete použít [službu Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) nebo cluster Kubernetes v cizím cloudu.
- [Zřízení prostředku brány v instanci API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Nasazení brány do Kubernetes

1. V části **Nastavení**vyberte **brány** .
2. Vyberte prostředek brány, který hodláte nasadit.
3. Vyberte **nasazení**.
4. Všimněte si, že nový token v textovém poli **token** byl automaticky vygenerován za použití výchozích hodnot **vypršení platnosti** a **tajného klíče** . V případě potřeby upravte buď nebo, a pokud je to potřeba **, vyberte vytvořit a vytvořte** nový token.
5. Ujistěte se, že je v části **skripty nasazení**vybraná možnost **Kubernetes** .
6. Chcete-li stáhnout soubor, vyberte odkaz soubor **< brány – název >. yml** vedle **nasazení** .
7. Podle potřeby upravte mapování portů a název kontejneru v souboru YML.
8. Vyberte ikonu **kopírování** nacházející se na pravém konci textového pole **nasadit** a uložte příkaz `kubectl` do schránky. 
9. Vložte příkaz do okna terminálu (nebo příkazu). Všimněte si, že příkaz očekává, že se stažený soubor prostředí nachází v aktuálním adresáři.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Spusťte příkaz. Příkaz nastaví cluster Kubernetes ke spuštění kontejneru pomocí Image brány v místním prostředí staženého z Microsoft Container Registry a ke konfiguraci kontejneru pro vystavení portů HTTP (8080) a HTTPS (443).
11. Spuštěním následujícího příkazu ověřte, že brána pod je spuštěná. Všimněte si, že název pod se bude lišit. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Spuštěním následujícího příkazu zkontrolujete, jestli je služba brány spuštěná. Všimněte si, že název služby se bude lišit. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Vraťte se na Azure Portal a ověřte, že uzel Brána, který jste právě nasadili, hlásí stav v pořádku.

![Stav brány](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Pomocí příkazu <code>kubectl logs <gateway-pod-name></code> můžete zobrazit snímek protokolu brány v místním prostředí.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .
* Další informace o [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)


