---
title: Nasazení samoobslužné brány do služby Azure Kubernetes | Microsoft Docs
description: Přečtěte si, jak nasadit komponentu samoobslužné brány Azure API Management do služby Azure Kubernetes.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015217"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Nasazení do Azure Kubernetes Service

Tento článek popisuje postup nasazení komponenty samoobslužné brány Azure API Management do [služby Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). Další informace o nasazení brány pro místní hostování do clusteru Kubernetes najdete v tomto[dokumentu](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Požadavky

- [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
- [Vytvoření clusteru Azure Kubernetes](../aks/kubernetes-walkthrough-portal.md)
- [Zřízení prostředku brány v instanci API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Nasazení samoobslužné brány do AKS

1. V části **nasazení a infrastruktura**vyberte **brány** .
2. Vyberte prostředek samoobslužné brány, který chcete nasadit.
3. Vyberte **nasazení**.
4. Všimněte si, že nový token v textovém poli **token** byl automaticky vygenerován za použití výchozích hodnot **vypršení platnosti** a **tajného klíče** . V případě potřeby upravte buď nebo, a pokud je to potřeba **, vyberte vytvořit a vytvořte** nový token.
5. Ujistěte se, že je v části **skripty nasazení**vybraná možnost **Kubernetes** .
6. Chcete-li stáhnout soubor, vyberte odkaz soubor **<brány – název>. yml** vedle **nasazení** .
7. Podle potřeby upravte mapování portů a název kontejneru v souboru YML.
8. V závislosti na vašem scénáři možná budete muset změnit [typ služby](../aks/concepts-network.md#services). Výchozí hodnota je `NodePort`.
9. Vyberte ikonu **kopírování** nacházející se na pravém konci textového pole **nasadit** a uložte `kubectl` příkaz do schránky.
10. Vložte příkaz do okna terminálu (nebo příkazu). Všimněte si, že příkaz očekává, že se stažený soubor prostředí nachází v aktuálním adresáři.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Spusťte příkaz. Příkaz nastaví cluster AKS ke spuštění kontejneru pomocí Image brány v místním prostředí staženého z Microsoft Container Registry a ke konfiguraci kontejneru pro vystavení portů HTTP (8080) a HTTPS (443).
12. Spuštěním následujícího příkazu ověřte, že brána pod je spuštěná. Všimněte si, že název pod se bude lišit.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Spuštěním následujícího příkazu zkontrolujete, jestli je služba brány spuštěná. Všimněte si, že název služby a IP adresy se budou lišit.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Vraťte se na Azure Portal a ověřte, že uzel Brána, který jste právě nasadili, hlásí stav v pořádku.

> [!TIP]
> Pomocí <code>kubectl logs <gateway-pod-name></code> příkazu můžete zobrazit snímek protokolu brány pro samoobslužné hostování.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .
* Další informace o [službě Azure Kubernetes](../aks/intro-kubernetes.md)
* Naučte [se konfigurovat a uchovávat protokoly v cloudu](how-to-configure-cloud-metrics-logs.md) .
* * Informace [o tom, jak místně nakonfigurovat a uchovat protokoly](how-to-configure-local-metrics-logs.md)
