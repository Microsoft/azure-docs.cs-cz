---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 4c4a5b66fe35da01a3661715e17a9fda20bc2411
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43184808"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Vytvoření clusteru Kubernetes s podporou Azure Dev Spaces

1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.
1. Zvolte **Vytvořit prostředek**, vyhledejte **Kubernetes** a vyberte **Služba Kubernetes** > **Vytvořit**.

   Pod jednotlivými nadpisy formuláře pro vytvoření clusteru AKS proveďte následující kroky.

    - **PODROBNOSTI O PROJEKTU:** Vyberte předplatné Azure a novou nebo existující skupinu prostředků Azure.
    - **PODROBNOSTI O CLUSTERU:** Zadejte název, oblast (v současné době můžete vybrat pouze oblast EastUS, CentralUS, WestEurope, WestUS2, CanadaCentral nebo CanadaEast), verzi a předponu názvu DNS pro cluster AKS.
    - **ŠKÁLOVÁNÍ:** Vyberte velikost virtuálního počítače pro uzly agentů AKS a počet uzlů. Pokud s Azure Dev Spaces začínáte, na prozkoumání všech funkcí vám stačí jeden uzel. Počet uzlů můžete po nasazení clusteru kdykoli snadno upravit. Mějte na paměti, že velikost virtuálního počítače není možné po vytvoření clusteru AKS změnit. Pokud však potřebujete vertikálně navýšit kapacitu, po nasazení clusteru AKS můžete snadno vytvořit nový cluster AKS s většími virtuálními počítači a pomocí Dev Spaces provést opětovné nasazení do tohoto většího clusteru.

   Nezapomeňte vybrat Kubernetes verze 1.9.6 nebo novější.

   ![Nastavení konfigurace Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Po dokončení vyberte **Další: Ověřování**.

1. Vyberte požadované nastavení pro řízení přístupu na základě role (RBAC). Služba Azure Dev Spaces podporuje clustery s vypnutým i zapnutým řízením přístupu na základě role.

    ![Nastavení řízení přístupu na základě role](../media/common/k8s-RBAC.PNG)

1. Ujistěte se, že je povolené směrování aplikace HTTP.

   ![Povolení směrování aplikace HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!Note]
    > Pokud chcete povolit [Směrování aplikace HTTP](/azure/aks/http-application-routing) v existujícím clusteru, použijte tento příkaz: `az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing`

1. Vyberte **Zkontrolovat a vytvořit** a po dokončení vyberte **Vytvořit**.
