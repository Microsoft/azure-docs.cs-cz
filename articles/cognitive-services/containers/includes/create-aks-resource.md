---
title: Vytvoření prostředku clusteru služby Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit prostředek služby Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383421"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Vytvoření prostředku clusteru služby Azure Kubernetes

1. Přejít na [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)a vyberte **vytvořit**.

1. Na kartě **základy** zadejte následující informace:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|
    |Název clusteru Kubernetes|Zadejte název (malými písmeny).|
    |Region (Oblast)|Vyberte okolní umístění.|
    |Verze Kubernetes|Libovolná hodnota je označena jako **(výchozí)** .|
    |Předpona názvu DNS|Vytvoří se automaticky, ale můžete přepsat.|
    |Velikost uzlu|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Počet uzlů|Ponechte posuvník na výchozí hodnotu.|

1. Na kartě **škálování** nechejte **virtuální uzly** a služba **VM Scale Sets** nastavené na výchozí hodnoty.
1. Na kartě **ověřování** ponechte **instanční objekt** a nastavte možnost **RBAC** na výchozí hodnoty.
1. Na kartě **sítě** zadejte následující výběry:

    |Nastavení|Hodnota|
    |--|--|
    |Směrování aplikace HTTP|Ne|
    |Konfigurace sítě|Basic|

1. Na kartě **monitorování** se ujistěte, že je **možnost povolit sledování kontejneru** nastavená na **hodnotu Ano**, a jako výchozí hodnotu ponechte **Log Analytics pracovní prostor** .
1. Na kartě **značky** ponechte páry název/hodnota prázdné pro nyní.
1. Vyberte možnost **zkontrolovat a vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit**.

> [!NOTE]
> Pokud ověření selhalo, může to být způsobeno chybou instančního objektu. Vraťte se na kartu **ověřování** a pak vraťte zpět na **kontrolu + vytvořit**, kde by se mělo ověřování spustit a pak předat.
