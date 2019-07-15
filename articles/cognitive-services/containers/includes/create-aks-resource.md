---
title: Vytvořte prostředek clusteru služby Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877458"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Vytvořte prostředek clusteru služby Azure Kubernetes

1. Přejděte na [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks) a vyberte **vytvořit**.

1. Na **Základy** kartu, zadejte následující informace:

    |Nastavení|Value|
    |--|--|
    |Subscription|Vyberte odpovídající předplatné|
    |Resource group|Vyberte skupinu prostředků k dispozici|
    |Název clusteru Kubernetes|Zadejte název (malá písmena)|
    |Oblast|Vyberte nejbližší umístění|
    |Verze Kubernetes|1.12.8 (výchozí)|
    |Předpona názvu DNS|Vytvoří automaticky, ale můžete přepsat|
    |Velikost uzlu|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Počet uzlů|Ponechte výchozí hodnota posuvníku|

1. Na **škálování** kartu, ponechte **virtuální uzly** a **škálovacích sad virtuálních počítačů (preview)** nastavené na výchozí hodnoty.
1. Na **ověřování** kartu, ponechte **instanční objekt služby** a **povolit RBAC** nastavené na výchozí hodnoty.
1. Na **sítě** kartu, zadejte následující možnosti:

    |Nastavení|Value|
    |--|--|
    |Směrování aplikace HTTP|Ne|
    |Konfigurace sítě|Basic|

1. Na **monitorování** kartu, ujistěte se, že **povolit monitorování kontejnerů** je nastavena na **Ano**a nechat **pracovní prostor Log Analytics** jako Výchozí hodnota.
1. Na **značky** kartu, zatím nechejte nastavené dvojice název/hodnota prázdná.
1. Vyberte **zkontrolovat a vytvořit**.
1. Jakmile úspěšně proběhne ověření, vyberte **vytvořit**.

> [!NOTE]
> Pokud ověření selže, může to být kvůli chybě "Instančního objektu služby". Přejděte zpět **ověřování** kartu a pak zpátky **revize + vytvořit**, kde ověření by měl spustit a pak předejte.
