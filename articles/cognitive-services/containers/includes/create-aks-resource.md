---
title: Vytvoření prostředku clusteru služby Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit prostředek služby Azure Kubernetes Service (AKS).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87298860"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Vytvoření prostředku clusteru služby Azure Kubernetes

1. Přejít na [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)a vyberte **vytvořit**.

1. Na kartě **Základní informace** zadejte následující údaje:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|
    |Název clusteru Kubernetes|Zadejte název (malými písmeny).|
    |Oblast|Vyberte umístění ve svém okolí.|
    |Verze Kubernetes|Libovolná hodnota je označena jako **(výchozí)**.|
    |Předpona názvu DNS|Vytvoří se automaticky, ale můžete přepsat.|
    |Velikost uzlu|DS2 v2 úrovně Standard:<br>`2 vCPUs`, `7 GB`|
    |Počet uzlů|Ponechte posuvník na výchozí hodnotu.|

1. Na kartě **fondy uzlů** ponechte **virtuální uzly** a služba **VM Scale Sets** nastavené na výchozí hodnoty.
1. Na kartě **ověřování** ponechte **instanční objekt** a nastavte možnost **RBAC** na výchozí hodnoty.
1. Na kartě **sítě** zadejte následující výběry:

    |Nastavení|Hodnota|
    |--|--|
    |Směrování aplikace HTTP|No|
    |Konfigurace sítě|Základní|

1. Na kartě **integrace** se ujistěte, že je **monitorování kontejneru** nastaveno na **povoleno**, a jako výchozí hodnotu ponechte **Log Analytics pracovní prostor** .
1. Na kartě **značky** ponechte páry název/hodnota prázdné pro nyní.
1. Vyberte možnost **zkontrolovat a vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit**.

> [!NOTE]
> Pokud ověření selhalo, může to být způsobeno chybou instančního objektu. Vraťte se na kartu **ověřování** a pak vraťte zpět na **kontrolu + vytvořit**, kde by se mělo ověřování spustit a pak předat.
