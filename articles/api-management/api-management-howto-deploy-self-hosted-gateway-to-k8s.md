---
title: Nasazení brány azure api pro správu s vlastním hostitelem do Kubernetes | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nasadit do Kubernetes samoobslužnou bránu správy rozhraní Azure API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513832"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Nasazení brány azure api managementu s vlastním hostitelem do Kubernetes

Tento článek obsahuje postup nasazení brány pro správu rozhraní Azure Azure hostovaného vlastním hostitelem do clusteru Kubernetes.

> [!NOTE]
> Funkce brány s vlastním hostitelem je ve verzi Preview. Během předběžné verze je brána s vlastním hostitelem dostupná pouze na úrovních Vývojář a Premium bez dalších poplatků. Úroveň pro vývojáře je omezena na jedno nasazení brány hostované sami sebou.


## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Vytvořte cluster Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) je dobrou volbou pro účely vývoje a hodnocení. Pro produkční úlohy můžete použít [službu Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) nebo cluster Kubernetes v cizím cloudu.
- [Zřízení prostředku brány v instanci správy rozhraní API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Nasazení brány do Kubernetes

1. V části **Nastavení**vyberte **Brány.**
2. Vyberte prostředek brány, který chcete nasadit.
3. Vyberte **možnost Nasazení**.
4. Všimněte si, že nový token v textovém poli **tokenu** byl automaticky vygenerován pro vás pomocí výchozí **hodnoty vypršení platnosti** a **tajný klíč.** V případě potřeby upravte jeden nebo oba a vyberte **Generovat** a vytvořte nový token.
5. Ujistěte se, že je v části **Skripty nasazení**vybraná možnost **Kubernetes** .
6. Vyberte<název **brány>.yml** odkaz na soubor vedle **nasazení** ke stažení souboru.
7. Podle potřeby upravte mapování portů a název kontejneru v souboru yml.
8. Vyberte ikonu **kopírování** umístěnou na pravém konci textového pole **Nasazení,** chcete-li `kubectl` příkaz uložit do schránky. 
9. Vložte příkaz do okna terminálu (nebo příkazu). Všimněte si, že příkaz očekává, že stažený soubor prostředí bude přítomen v aktuálním adresáři.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Spusťte příkaz. Příkaz instruuje cluster Kubernetes ke spuštění kontejneru pomocí bitové kopie brány s vlastním hostitelem stažené z registru kontejnerů Microsoft a ke konfiguraci kontejneru tak, aby zpřístupňoval porty HTTP (8080) a HTTPS (443).
11. Spusťte níže uvedený příkaz a zkontrolujte, zda je pod brány spuštěn. Všimněte si, že název podu se bude lišit. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Spusťte níže uvedený příkaz a zkontrolujte, zda je spuštěna služba brány. Všimněte si, že název služby se bude lišit. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Vraťte se na portál Azure a potvrďte, že uzel brány, který jste právě nasadili, hlásí stav v pořádku.

![stav brány](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Pomocí <code>kubectl logs <gateway-pod-name></code> příkazu můžete zobrazit snímek samoobslužného protokolu brány.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v [tématu Přehled samoobslužné brány správy rozhraní Azure API.](self-hosted-gateway-overview.md)
* Další informace o [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)


