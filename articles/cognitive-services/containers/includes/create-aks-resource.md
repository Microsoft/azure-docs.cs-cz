---
title: Vytvoření prostředku clusteru služby Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit prostředek služby Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377443"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Vytvoření prostředku clusteru služby Azure Kubernetes

1. Přejít na [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)a vyberte **vytvořit**.

1. Na kartě **základy** zadejte následující informace:

    |Nastavení|Value|
    |--|--|
    |Subscription|Vyberte odpovídající předplatné.|
    |Resource group|Vyberte dostupnou skupinu prostředků.|
    |Název clusteru Kubernetes|Zadejte název (malými písmeny).|
    |Oblast|Vyberte okolní umístění.|
    |Verze Kubernetes|1.12.8 (výchozí).|
    |Předpona názvu DNS|Vytvoří se automaticky, ale můžete přepsat.|
    |Velikost uzlu|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Počet uzlů|Ponechte posuvník na výchozí hodnotu.|

1. Na kartě **škálování** ponechte **virtuální uzly** a **Virtual Machine Scale Sets (Preview)** nastavené na výchozí hodnoty.
1. Na kartě **ověřování** ponechte **instanční objekt** a **povolte nastavení RBAC** na výchozí hodnoty.
1. Na kartě **sítě** zadejte následující výběry:

    |Nastavení|Value|
    |--|--|
    |Směrování aplikace HTTP|Ne|
    |Konfigurace sítě|Basic|

1. Na kartě **monitorování** se ujistěte, že je **možnost povolit sledování kontejneru** nastavená na **hodnotu Ano**, a jako výchozí hodnotu ponechte **Log Analytics pracovní prostor** .
1. Na kartě **značky** ponechte páry název/hodnota prázdné pro nyní.
1. Vyberte možnost **zkontrolovat a vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit**.

> [!NOTE]
> Pokud ověření selhalo, může to být způsobeno chybou instančního objektu. Vraťte se na kartu **ověřování** a pak vraťte zpět na **kontrolu + vytvořit**, kde by se mělo ověřování spustit a pak předat.
